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
| [[get]] | GET | Get device by ID |
| [[my]] | GET | Get my devices |
| [[my-stats]] | GET | Get my device stats |
| [[search]] | GET | Search devices |
| [[create]] | POST | Create device |
| [[update]] | PUT | Update device |
| [[full-update]] | PUT | Full update device |
| [[delete]] | DELETE | Delete device |
| [[control]] | POST | Control device |

## Device Types

| Endpoint | Method | Description |
|----------|:------:|-------------|
| [[types-list]] | GET | List device types |
| [[types-create]] | POST | Create device type |
| [[types-hardware]] | GET | Get hardware types |
| [[types-sensors]] | GET | Get sensor types |

## Sensors

See: [[sensors/README|Sensors]]

## Microcontrollers

See: [[microcontrollers/README|Microcontrollers]]

## Readings

See: [[readings/README|Readings]]

## Common Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `id` | uint | Device ID |
| `name` | string | Device name |
| `device_type` | uint | Device type ID |
| `current_state` | uint | Current state (1=Active, 2=Inactive, etc.) |
| `created_at` | datetime | Creation time |
| `updated_at` | datetime | Last update time |