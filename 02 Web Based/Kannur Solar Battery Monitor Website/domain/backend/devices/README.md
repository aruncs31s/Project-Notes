---
title: Devices
tags:
  - api
  - devices
  - documentation
---

# Devices

> Device management endpoints for the SKVMS API.

## Core Endpoints

| Endpoint | Method | Description |
|----------|:------:|-------------|
| [[list-all]] | GET | List all devices |
| [[recent]] | GET | List recent devices |
| [[02 Web Based/Kannur Solar Battery Monitor Website/domain/backend/devices/get]] | GET | Get device by ID |
| [[02 Web Based/Kannur Solar Battery Monitor Website/domain/backend/devices/my]] | GET | Get my devices |
| [[02 Web Based/Kannur Solar Battery Monitor Website/domain/backend/devices/my-stats]] | GET | Get my device stats |
| [[02 Web Based/Kannur Solar Battery Monitor Website/domain/backend/devices/search]] | GET | Search devices |
| [[02 Web Based/Kannur Solar Battery Monitor Website/domain/backend/devices/create]] | POST | Create device |
| [[02 Web Based/Kannur Solar Battery Monitor Website/domain/backend/devices/update]] | PUT | Update device |
| [[full-update]] | PUT | Full update device |
| [[02 Web Based/Kannur Solar Battery Monitor Website/domain/backend/devices/delete]] | DELETE | Delete device |
| [[control]] | POST | Control device |

## Device Types

| Endpoint | Method | Description |
|----------|:------:|-------------|
| [[types-list]] | GET | List device types |
| [[types-create]] | POST | Create device type |
| [[types-hardware]] | GET | Get hardware types |
| [[types-sensors]] | GET | Get sensor types |

## Sensors

See: [[02 Web Based/Kannur Solar Battery Monitor Website/domain/backend/devices/sensors/README|Sensors]]

## Microcontrollers

See: [[02 Web Based/Kannur Solar Battery Monitor Website/domain/backend/devices/microcontrollers/README|Microcontrollers]]

## Readings

See: [[02 Web Based/Kannur Solar Battery Monitor Website/domain/backend/devices/readings/README|Readings]]

## Common Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `id` | uint | Device ID |
| `name` | string | Device name |
| `device_type` | uint | Device type ID |
| `current_state` | uint | Current state (1=Active, 2=Inactive, etc.) |
| `created_at` | datetime | Creation time |
| `updated_at` | datetime | Last update time |