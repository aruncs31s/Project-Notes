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
