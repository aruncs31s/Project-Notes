---
title: Readings
tags:
  - api
  - readings
  - documentation
---

# Readings

> Device readings endpoints for the SKVMS API.

## Overview

All readings are stored and returned in **UTC timezone** (RFC3339 format). See [[common]] for shared request/response types.

## Endpoints (Device-level)

| Endpoint | Method | Description |
|----------|:------:|-------------|
| [[list]] | GET | List readings for device |
| [[range]] | GET | Get readings by date range |
| [[interval]] | GET | Get readings with interval |
| [[progressive]] | GET | Get with running averages |
| [[connected]] | GET | Get readings of connected device |

## Endpoints (Global)

| Endpoint | Method | Description |
|----------|:------:|-------------|
| [[advanced]] | GET | Advanced readings with filters |
| [[create]] | POST | Create reading (device auth) |

## Common Types

- [[common|Device Readings]] - Shared types and parameters
- [[common#datetype|Date Formats]] - RFC3339 vs YYYY-MM-DD

## Quick Links (Device)

- [[list|Device Readings]] - Get device readings
- [[range|Date Range]] - Get readings by date range
- [[interval|Interval]] - Get readings at intervals
- [[progressive|Progressive]] - Get with running averages
- [[connected|Connected Device]] - Get child device readings

## Quick Links (Global)

- [[advanced|Advanced Filter]] - Filter readings globally
- [[create|Create Reading]] - Submit new reading