# API Documentation

The ESDC LMS Backend serves a RESTful API heavily utilizing JSON.

## Base URL
Typically `http://localhost:8080/api`

## Authentication
Protected routes require a Bearer token in the `Authorization` header:
`Authorization: Bearer <your_jwt_token>`

## Core Routes Overview

### Health & System
- `GET /api/health` - Server health check
- `GET /api/version` - API version info
- `GET /ws/chat` - WebSocket connection for real-time chat

### Authentication
- `POST /api/register` - Register a new user
- `POST /api/login` - Authenticate and receive a JWT

### Users
- `GET /api/users` - List users (Admin/Teacher)
- `PUT /api/users/profile` - Update user profile
- `GET /api/users/:id/enrolments` - Get a specific user's enrollments

### Courses
- `GET /api/courses` - List all published courses
- `GET /api/courses/trending` - Get trending courses
- `GET /api/courses/search` - Search courses by query
- `GET /api/courses/:id` - Get specific course details
- `GET /api/courses/:id/reviews` - Get course reviews

**Protected Course Actions:**
- `POST /api/courses/:id/like` - Like a course
- `DELETE /api/courses/:id/like` - Unlike a course
- `POST /api/courses/:id/enroll` - Enroll in a course
- `GET /api/courses/:id/enrollment` - Check enrollment status
- `POST /api/courses/:id/modules/:moduleId/complete` - Mark a specific module as completed

**Teacher/Admin Only:**
- `POST /api/courses` - Create a new course
- `PUT/PATCH /api/courses/:id` - Update existing course
- `DELETE /api/courses/:id` - Delete a course
- `POST /api/courses/:id/modules` - Add a new module to a course
- `PUT/PATCH/DELETE /api/courses/:id/modules/:moduleId` - Update/Delete module
- `PUT /api/courses/:id/modules/reorder` - Reorder course modules

### Assignments (Standard & Coding)
Assignments belong to courses.

**Teacher/Admin Actions**:
- `POST /api/courses/:id/assignments` - Create assignment
- `PUT/DELETE /api/courses/:id/assignments/:assignmentId` - Modify assignment
- `GET /api/courses/:id/assignments/:assignmentId/submissions` - View all submissions
- `PUT /api/courses/:id/assignments/:assignmentId/submissions/:submissionId/grade` - Grade a submission

*(Coding Assignments follow the identical pattern under `/api/courses/:id/coding-assignments`)*

**Student Actions**:
- `GET /api/courses/:id/assignments` - View assignments for course
- `POST /api/courses/:id/assignments/:assignmentId/submit` - Submit an assignment
*(Coding Assignments use `/api/courses/:id/coding-assignments/.../submit`)*

### Code Execution
- `POST /api/code/run` - Execute code in a sandboxed environment

### Uploads
- `POST /api/upload/image` - Upload an image file
- `POST /api/upload/video` - Upload a video (Teacher/Admin only)
- `POST /api/upload/attachment` - Upload a general attachment

### Certificates
- `GET /api/certificates/download` - Public download link for generated certificates
- `POST /api/certificates/generate` - Generate a certificate (Internal/Protected)

### Chat
- `GET /api/chat/conversations` - List user conversations
- `POST /api/chat/conversations` - Create a new conversation
- `GET /api/chat/conversations/:id/messages` - Get messages in a conversation

### Notifications
- `GET /api/notifications` - Get user notifications
- `GET /api/notifications/unread-count` - Get count of unread notifications
- `PUT /api/notifications/:id/read` - Mark specific notification as read

---
*Note: Refer to the specific `internal/dto` structs or the Postman collection (if available) for precise payload and response shapes.*
