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
| [[02 Web Based/Kannur Solar Battery Monitor Website/domain/backend/users/list]] | GET | List all users |
| [[02 Web Based/Kannur Solar Battery Monitor Website/domain/backend/users/get]] | GET | Get user by ID |
| [[profile]] | GET | Get current user profile |
| [[user-profile]] | GET | Get user profile by ID |
| [[02 Web Based/Kannur Solar Battery Monitor Website/domain/backend/users/create]] | POST | Create new user |
| [[02 Web Based/Kannur Solar Battery Monitor Website/domain/backend/users/update]] | PUT | Update user |
| [[02 Web Based/Kannur Solar Battery Monitor Website/domain/backend/users/delete]] | DELETE | Delete user |

## Quick Links

- [[02 Web Based/Kannur Solar Battery Monitor Website/domain/backend/users/list|List Users]] - Get all users
- [[02 Web Based/Kannur Solar Battery Monitor Website/domain/backend/users/get|Get User]] - Get user by ID
- [[02 Web Based/Kannur Solar Battery Monitor Website/domain/backend/users/create|Create User]] - Create new user
- [[02 Web Based/Kannur Solar Battery Monitor Website/domain/backend/users/update|Update User]] - Update user details
- [[02 Web Based/Kannur Solar Battery Monitor Website/domain/backend/users/delete|Delete User]] - Delete user
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