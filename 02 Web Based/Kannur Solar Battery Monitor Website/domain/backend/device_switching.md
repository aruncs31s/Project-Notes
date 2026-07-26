# Device Ownership, Switching, and Privacy Guide

This document explains how to manage device ownership, transfer devices between users (switching), and toggle public/private visibility in the SKVMS system.

## 1. Device Ownership
Every device in SKVMS is owned by a specific user. The owner has full control over the device, including:
- Updating device details (name, location, etc.)
- Controlling the device (turning on/off, configuring)
- Generating access tokens
- Managing connected sensors
- Toggling public/private visibility
- Transferring ownership to another user

### Initial Ownership
When a device is created, the user who creates it is automatically assigned as the owner.

---

## 2. Device Switching (Ownership Transfer)
"Switching" a device refers to the process of transferring its ownership from one user to another. This is typically done when a device is handed over to a new user or a different department.

### How it Works
- **Authorization:** Only the **Current Owner** or a **System Admin** can initiate a transfer.
- **Process:**
  1. The initiator specifies the `to_user_id` of the new owner.
  2. Optionally, a handover note can be provided.
  3. The system revokes all previous Casbin permissions for the old owner.
  4. The system grants full owner permissions to the new owner.
  5. Both users receive a notification about the transfer.

### API Endpoint
- **POST** `/api/devices/:id/transfer`
- **Payload:**
  ```json
  {
    "to_user_id": 3,
    "note": "Transferred for maintenance"
  }
  ```

---

## 3. Privacy Settings (Public vs. Private)
Devices can be set to either **Public** or **Private** visibility.

### Private Devices (Default)
- **Visibility:** Visible only to the **Owner** and **System Admins**.
- **Access:** Only the owner and admins can view readings, logs, and control the device.
- **Use Case:** Personal or sensitive monitoring units.

### Public Devices
- **Visibility:** Visible to **All registered users** in the system.
- **Access:** Any user can view the device's information and readings. However, **control actions** (like turning it off) are still restricted to the Owner and Admins.
- **Use Case:** Shared infrastructure, public solar units, or demo devices.

### Toggling Visibility
- **Action:** The Owner or Admin can toggle the `is_public` flag.
- **API Endpoint:**
  - **PUT** `/api/devices/:id/public`
  - **Payload:** `{"is_public": true}` (or `false`)

---

## 4. History and Audit
All ownership transfers are logged in the `ownership_transfer_logs` table. This allows admins to track the complete "life cycle" of a device as it moves between users.

### Viewing History
- **API Endpoint:** **GET** `/api/devices/:id/transfer-history`
- **Provides:**
  - Who the device was transferred from/to.
  - Who initiated the transfer.
  - The timestamp of the transfer.
  - Any associated notes.

---

## 5. Technical Details & Security
The system uses a combination of database ownership records and **Casbin RBAC (Role-Based Access Control)** to enforce these policies.

### Casbin Integration
When a transfer occurs:
1. The `service.Transfer` method is called.
2. It explicitly calls `casbin.RevokeAllDevicePermissions` for the previous owner.
3. It updates the `device_ownerships` table.
4. The internal repository logic (or subsequent middleware) ensure that the new owner is granted the `owner` role for that specific device resource (`device:<id>`).

### Visibility Enforcement
- **Public:** Middleware and Repository queries are modified to include devices where `is_public = true` regardless of ownership, but only for **read-only** operations.
- **Private:** Resource access is strictly limited to the `OwnerUserID` or users with an `admin` role.


# Front End Implementation 
## Implementation Plan: Device Ownership and Visibility Management

The objective is to **implement frontend support for managing device ownership** and visibility settings. Currently, the backend supports these features, but the frontend lacks the UI to initiate a transfer or toggle whether a device is public or private.

### User Review Required

> [!IMPORTANT]
> - **Permissions**: Only the device owner or an admin will be able to see and use the ownership management tools.
> - **Transfer Process**: Transferring ownership is a destructive action for the current owner (they will lose write access unless they are an admin).
> - **UI Placement**: I propose adding a new "Ownership & Privacy" card on the standard Device Detail page (`/devices/:id`).

## Proposed Changes

### Domain Layer

#### [entities](file:///home/aruncs/Projects/Smart-City/Kannur-Solar-Battery-Monitoring-System-Website-FrontEnd-React/src/domain/entities/Device.ts)
- Add `DeviceOwnership` interface: `id: number`, `owner_id: number`, `owner_name: string`, `is_public: boolean`.
- Add `TransferOwnershipDTO`: `to_user_id: number`, `note: string`.

#### [repositories](file:///home/aruncs/Projects/Smart-City/Kannur-Solar-Battery-Monitoring-System-Website-FrontEnd-React/src/domain/repositories/IDeviceRepository.ts)
- Add `getOwnership(deviceId: number): Promise<DeviceOwnership>`.
- Add `transferOwnership(deviceId: number, data: TransferOwnershipDTO): Promise<void>`.
- Add `setVisibility(deviceId: number, isPublic: boolean): Promise<void>`.

### Application Layer

#### [usecases](file:///home/aruncs/Projects/Smart-City/Kannur-Solar-Battery-Monitoring-System-Website-FrontEnd-React/src/application/usecases/devices/)
- Create `GetDeviceOwnershipUseCase`.
- Create `TransferDeviceOwnershipUseCase`.
- Create `SetDeviceVisibilityUseCase`.

### Infrastructure Layer

#### [repositories](file:///home/aruncs/Projects/Smart-City/Kannur-Solar-Battery-Monitoring-System-Website-FrontEnd-React/src/infrastructure/repositories/DeviceRepository.ts)
- Implement the new ownership and visibility methods using `httpClient`.

### API & DI Layer

#### [api](file:///home/aruncs/Projects/Smart-City/Kannur-Solar-Battery-Monitoring-System-Website-FrontEnd-React/src/api/devices.ts)
- Expose the new use cases in the `devicesAPI` object.

#### [di](file:///home/aruncs/Projects/Smart-City/Kannur-Solar-Battery-Monitoring-System-Website-FrontEnd-React/src/application/di/container.ts)
- Register the new use cases in the dependency injection container.

### UI Components

#### [NEW] [DeviceOwnershipCard.tsx](file:///home/aruncs/Projects/Smart-City/Kannur-Solar-Battery-Monitoring-System-Website-FrontEnd-React/src/pages/device-detail/components/DeviceOwnershipCard.tsx)
- Display the current owner's name.
- Show the current visibility status (Public vs Private) with icons.
- Provide a toggle for visibility.
- Provide a "Transfer Ownership" button.

#### [NEW] [TransferOwnershipModal.tsx](file:///home/aruncs/Projects/Smart-City/Kannur-Solar-Battery-Monitoring-System-Website-FrontEnd-React/src/components/TransferOwnershipModal.tsx)
- A modal that allows selecting a user from a dropdown or list (fetched via `usersAPI.getAll()`).
- Input for an optional handover note.
- Confirmation step before finalizing the transfer.

### Page Integration

#### [DeviceDetail.tsx](file:///home/aruncs/Projects/Smart-City/Kannur-Solar-Battery-Monitoring-System-Website-FrontEnd-React/src/pages/device-detail/DeviceDetail.tsx)
- Add the `DeviceOwnershipCard` to the sidebar or main grid.
- Integrate logic to trigger the transfer modal.

#### [useDeviceDetailData.ts](file:///home/aruncs/Projects/Smart-City/Kannur-Solar-Battery-Monitoring-System-Website-FrontEnd-React/src/pages/device-detail/hooks/useDeviceDetailData.ts)
- Add states for ownership data and visibility status.
- Add handlers for `handleTransferOwnership` and `handleToggleVisibility`.

## Open Questions

- **User Search**: Should we implement a real-time search for users in the transfer modal, or is a simple dropdown of all users sufficient for now? Given the expected number of users, a dropdown with search-filtering (e.g., using a library or simple local filter) seems best.

## Verification Plan

### Automated Tests
- Build verification using `npm run build`.

### Manual Verification
1. Navigate to a device you own.
2. Verify the "Ownership & Privacy" card is visible.
3. Toggle visibility and check if it persists after refresh.
4. Attempt to transfer ownership to another user.
5. Verify that you can no longer edit the device (unless you are an admin).
6. Check the "Transfer History" page to see if the record appeared correctly.
