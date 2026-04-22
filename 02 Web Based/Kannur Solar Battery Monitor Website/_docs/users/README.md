---
title: Users
tags:
  - api
  - users
  - documentation
---

# Users

> User management endpoints for the SKVMS API.

## Endpoints

| Endpoint | Method | Description |
|----------|:------:|-------------|
| [[list]] | GET | List all users |
| [[get]] | GET | Get user by ID |
| [[profile]] | GET | Get current user profile |
| [[user-profile]] | GET | Get user profile by ID |
| [[create]] | POST | Create new user |
| [[update]] | PUT | Update user |
| [[delete]] | DELETE | Delete user |

## Quick Links

- [[list|List Users]] - Get all users
- [[get|Get User]] - Get user by ID
- [[create|Create User]] - Create new user
- [[update|Update User]] - Update user details
- [[delete|Delete User]] - Delete user
- [[profile|Get Profile]] - Get current user profile
- [[user-profile|Get User Profile]] - Get specific user profile

## Common Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `id` | uint | User ID |
| `name` | string | Display name |
| `username` | string | Unique username |
| `email` | string | Email address |
| `role` | string | User role (admin, user, etc.) |
| `created_at` | datetime | Account creation time |