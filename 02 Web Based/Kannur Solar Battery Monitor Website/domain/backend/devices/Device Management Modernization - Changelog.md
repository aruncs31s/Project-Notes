---
title: Device Management Modernization - Changelog
date: 2026-09-16
tags:
  - project
  - backend
  - frontend
  - devices
  - architecture
  - changelog
---

# Device Management Modernization — Architectural Changelog

**Date:** September 16, 2026  
**Scope:** Full-stack modernization of device identity, ownership synchronization, atomic updates, unified provisioning UX, and telemetry-driven lifecycle state transitions.

---

## Executive Summary

Prior to this work, device management suffered from:
1. **Ownership Desynchronization:** Queries filtered by `d.created_by`, causing devices transferred to a new owner via `device_ownerships` to disappear from their dashboards.
2. **Mutation Duplication & Data Loss:** `PUT /api/devices/:id` and `PUT /api/devices/:id/full` had divergent logic, silently dropping location assignments upon edit.
3. **Frontend Modal Sprawl:** Device creation was fragmented across 4 disparate modals (`AddDeviceModal`, `AdvancedDeviceAddModal`, `AddSolarDeviceModal`, `AddSensorDeviceModal`) with duplicate states and inconsistent validation.
4. **State Machine Disconnect:** Devices in `Initialized` (State 5) required manual actuation rather than automatically activating upon receiving telemetry.

All four areas have been refactored, tested, and verified with zero breaking changes to existing routes or components.

---

## 1. Backend Ownership & Authorization Reconciliation (P0)

### Problem
- `devices.created_by` tracked the original creator, while `device_ownerships.owner_user_id` tracked the current legal owner.
- Queries like `ListDevicesByUser` and `ListMicrocontrollers` queried `WHERE d.created_by = ?`, meaning transferred devices remained with the creator and never showed up for the transferee.
- In `internal/repository/device_repository.go:529`, a SQL syntax typo (`dt.harware_type ? =`) caused query failures on hardware-filtered scans.

### Solution & Changes
- **Reconciled Repository Queries:** Updated `ListDevicesByUser`, `GetDevicesByHardwareTypeAndUser`, `GetDevicesWithLocationByHardwareTypeAndUser`, and `ListMicrocontrollers` to prioritize `device_ownerships.owner_user_id` with backwards-compatible fallback to `d.created_by`:
  ```sql
  WHERE (
      devices.id IN (SELECT do.device_id FROM device_ownerships do WHERE do.owner_user_id = ?)
      OR (devices.created_by = ? AND NOT EXISTS (SELECT 1 FROM device_ownerships do WHERE do.device_id = devices.id))
  )
  ```
- **Startup Migration:** Added an ANSI-standard backfill in `internal/database/db.go` that automatically inserts `device_ownerships` records for any legacy devices.
- **Bug Fixes:**
  - Corrected `dt.harware_type ? =` typo to `dt.hardware_type = ?`.
  - Corrected `zap.String("voltage_meter_id", string(voltageMeterID))` in `solar_repository.go` to `zap.Uint` (preventing uint-to-rune corruption).
- **Automated Tests:** Created `internal/repository/device_ownership_test.go` verifying creator visibility, post-transfer visibility, and legacy fallback via in-memory SQLite.

---

## 2. Backend API Consolidation (`PUT /devices/:id`) (P1)

### Problem
- The partial update `PUT /api/devices/:id` passed `nil` for device assignment, causing edits on the device detail page to strip site assignments.
- `PUT /api/devices/:id/full` duplicated logic and required a rigid payload that frontends struggled to compose.

### Solution & Changes
- **Expanded DTO:** Added optional pointer fields (`CurrentState`, `LocationID`, `Address`, `City`) to `dto.UpdateDeviceRequest`.
- **Atomic Repository Upsert:** Updated `UpdateDevice` in `device_repository.go` to transactionally upsert `DeviceDetails` and `DeviceAssignment` even if record IDs were not pre-loaded.
- **Preload Hardening:** Added `.Preload("Assignment")` to `GetDevice`.
- **Unified Writer Service:** `DeviceWriter.UpdateDevice` now updates core entity, network details, and assignment in a single database transaction. `FullUpdateDevice` delegates directly to `UpdateDevice`.
- **Automated Tests:** Added `TestUpdateDevice_UnifiedDetailsAndAssignment` in `device_ownership_test.go`.

---

## 3. Frontend Multi-Step Device Provisioning Wizard (P1)

### Problem
- 4 separate modals duplicated form logic, styles, and API calls across `/devices`, `/my-devices`, `/my-microcontrollers`, `/solar-devices`, and `/admin`.

### Solution & Changes
- **New Component:** Built `src/components/DeviceProvisioningWizard.tsx`:
  - **Step 1: Identity & Profile:** Name, Hardware Category switcher (Gateway/ESP32, Solar MPPT, Sensor), Type selector, and Firmware Version.
  - **Step 2: Network & Credentials:** IPv4 format check, MAC hardware address format validation (`XX:XX:XX:XX:XX:XX`).
  - **Step 3: Topology & Hierarchy:** Toggle between Standalone Gateway vs Sub-device / Attached Sensor with live search and selection of parent controller.
  - **Step 4: Site & Location:** Selection of registered facility sites (auto-populating address/city) or custom location tags.
  - **Step 5: Completion & Credentials:** Success card with Device ID, 1-click Ingestion Token generation with copy button, and direct navigation links.
- **Zero-Breaking Consolidation:** Replaced the internals of `AddDeviceModal.tsx`, `AdvancedDeviceAddModal.tsx`, `AddSolarDeviceModal.tsx`, and `AddSensorDeviceModal.tsx` to delegate to `DeviceProvisioningWizard` with appropriate category presets (`initialCategory="solar"`, etc.).
- **Type Hardening:** Added `device_state?: number;` and `is_configured?: boolean;` to `DeviceResponseDTO` in `src/application/types/devices/device.ts`.
- **Build Verification:** `npm run build` compiles with exit code 0.

---

## 4. Ingestion Pipeline & Lifecycle State Auto-Activation (P2)

### Problem
- Newly created devices remained stuck in state `Initialized` (5) or `Inactive` (2) until a user manually actuated a control command, even after the device started sending telemetry.

### Solution & Changes
- **Telemetry Hook:** In `internal/service/reading_service.go`, incoming telemetry packets via `RecordEssentialReadings` now trigger an asynchronous handler:
  1. Updates `last_seen_at` on `device_details`.
  2. If the device is in state `Initialized` (5) or `Inactive` (2), it automatically executes `s.device.ControlDevice(ctx, deviceID, model.ActionTurnOn, 1)`.
  3. Transitions the device to `Active` (1) with an audit record in `device_state_history`.
- **Automated Tests:** Created `internal/service/reading_service_test.go` (`TestRecordEssentialReadings_AutoActivatesInitializedDevice`), verifying state transition from 5 $\rightarrow$ 1 and timestamp stamping.

---

## File Modification Index

### Backend (`skvms/`)
| File | Action | Description |
|------|--------|-------------|
| `internal/database/db.go` | Modified | Added startup backfill for `device_ownerships`. |
| `internal/repository/device_repository.go` | Modified | Reconciled ownership filtering, added `Preload("Assignment")`, hardened transactional `UpdateDevice`. |
| `internal/repository/microcontrollers_repository.go` | Modified | Reconciled ownership filtering for microcontroller lists. |
| `internal/repository/solar_repository.go` | Modified | Fixed `zap.Uint` conversion bug. |
| `internal/dto/device.go` | Modified | Added `CurrentState`, `LocationID`, `Address`, `City` to `UpdateDeviceRequest`. |
| `internal/service/device/writer/writer.go` | Modified | Unified `UpdateDevice` and `FullUpdateDevice`. |
| `internal/service/reading_service.go` | Modified | Added telemetry ingestion auto-activation hook and `last_seen_at` touch. |
| `internal/handler/http/device/writer/device.go` | Modified | Hardened claim casting and error handling. |
| `internal/repository/device_ownership_test.go` | **New** | Unit tests for ownership reconciliation and unified device updates. |
| `internal/service/reading_service_test.go` | **New** | Unit tests for telemetry auto-activation. |

### Frontend (`Kannur-Solar-Battery-Monitoring-System-Website-FrontEnd-React/`)
| File | Action | Description |
|------|--------|-------------|
| `src/components/DeviceProvisioningWizard.tsx` | **New** | 4-step unified device onboarding wizard with token generator. |
| `src/components/AddDeviceModal.tsx` | Modified | Delegated to `DeviceProvisioningWizard`. |
| `src/components/AdvancedDeviceAddModal.tsx` | Modified | Delegated to `DeviceProvisioningWizard`. |
| `src/components/AddSolarDeviceModal.tsx` | Modified | Delegated to `DeviceProvisioningWizard` with `initialCategory="solar"`. |
| `src/components/AddSensorDeviceModal.tsx` | Modified | Delegated to `DeviceProvisioningWizard` with `initialCategory="sensor"`. |
| `src/components/index.ts` | Modified | Exported `DeviceProvisioningWizard` and modals. |
| `src/application/types/devices/device.ts` | Modified | Added `device_state` and `is_configured` to `DeviceResponseDTO`. |
| `src/components/DeviceHeader.tsx` | Modified | Added fallback for `device_state`. |
| `src/components/DeviceInfoCard.tsx` | Modified | Made optional fields optional and added `status`. |
| `src/pages/device-detail/DeviceDetail.tsx` | Modified | Cleaned up unused variables. |
| `src/pages/map-view/MapView.tsx` | Modified | Provided fallback for `device_state`. |
| `src/pages/profile/DeviceCard.tsx` | Modified | Provided fallback for `device_state`. |
| `src/pages/solar-devices/SolarDeviceDetail.tsx` | Modified | Provided fallback for `device_state`. |
| `src/pages/microcontroller-detail/MicrocontrollerDetail.tsx` | Modified | Fixed `DeviceInfoCard` and `DeviceControlPanel` prop types. |

---

## Verification Summary
- **Backend Tests:** `go test ./...` $\rightarrow$ 100% PASS (code 0).
- **Backend Build:** `go build ./...` $\rightarrow$ SUCCESS (code 0).
- **Frontend Bundle:** `npm run build` $\rightarrow$ SUCCESS (code 0).
