# Backend API Documentation

> `http://localhost:8080/api`

## Authentication
Protected routes require a Bearer token in the `Authorization` header:
`Authorization: Bearer <your_jwt_token>`

## Core Routes Overview & Payloads

### Health & System
- `GET /api/health` - Server health check
  ```json
  // Response
  { "status": "ok" }
  ```
- `GET /api/version` - API version info
  ```json
  // Response
  { "version": "1.0.0" }
  ```
- `GET /ws/chat` - WebSocket connection for real-time chat

### Authentication
- `POST /api/register` - Register a new user
  ```json
  // Request
  {
      "first_name": "John",
      "last_name": "Doe",
      "email": "student@example.com",
      "password": "securepassword",
      "role": "student" // "student", "teacher", "admin"
  }
  ```
  ```json
  // Response
  {
      "token": "ey...",
      "user": {
          "id": "uuid",
          "first_name": "John",
          "last_name": "Doe",
          "email": "student@example.com",
          "role": "student"
      }
  }
  ```
- `POST /api/login` - Authenticate and receive a JWT
  ```json
  // Request
  {
      "email": "student@example.com",
      "password": "securepassword"
  }
  // Response returns the same Auth payload as register.
  ```

### Users
- `PUT /api/users/profile` - Update user profile
  ```json
  // Request
  {
      "first_name": "John",
      "last_name": "Doe",
      "bio": "Lifelong learner",
      "avatar_url": "https://..."
  }
  ```

### Courses
**Teacher/Admin Only:**
- `POST /api/courses` - Create a new course
  ```json
  // Request
  {
      "title": "Go for Beginners",
      "description": "Learn Golang...",
      "thumbnail_url": "https://...",
      "price": 49.99,
      "type": "paid", // "free", "paid"
      "format": "course", // "course", "project"
      "status": "active", // "coming soon", "active", "ended"
      "duration": "10h",
      "is_certificate_available": true
  }
  ```
  ```json
  // Response (Course details)
  {
      "id": "uuid",
      "teacher_id": "uuid",
      "title": "Go for Beginners",
      // ... fields matching request ...
      "student_count": 0,
      "likes_count": 0
  }
  ```

- `POST /api/courses/:id/modules` - Add a new module to a course
  ```json
  // Request
  {
      "title": "Introduction",
      "description": "Welcome to the course",
      "type": "video", // "video", "chapter"
      "video_url": "https://...",
      "points": 10,
      "is_free": true
  }
  ```

**General Course Interactive:**
- `POST /api/courses/:id/reviews` - Add a review
  ```json
  // Request
  {
      "rating": 5,
      "comment": "Excellent course!"
  }
  ```
- `GET /api/courses/:id`
  Returns Course details along with nested Modules and Teacher info.

### Assignments (Standard)
**Teacher/Admin:**
- `POST /api/courses/:id/assignments` - Create assignment
  ```json
  // Request
  {
      "title": "Build an API",
      "description": "Create a CRUD API in Go",
      "max_score": 100,
      "due_date": "2026-12-31T23:59:59Z"
  }
  ```
- `PUT /api/courses/:id/assignments/:assignmentId/submissions/:submissionId/grade` - Grade submission
  ```json
  // Request
  {
      "score": 95,
      "feedback": "Great work!"
  }
  ```

**Student:**
- `POST /api/courses/:id/assignments/:assignmentId/submit` - Submit an assignment
  ```json
  // Request
  {
      "file_url": "https://.../submission.pdf"
  }
  ```

### Coding Assignments
**Teacher/Admin:**
- `POST /api/courses/:id/coding-assignments` - Create coding assignment
  ```json
  // Request
  {
      "title": "Two Sum",
      "description": "Find two numbers that add up to target.",
      "language": "python", // "python", "javascript"
      "starter_code": "def two_sum(nums, target):",
      "max_score": 100,
      "test_cases": [
          {
              "description": "Basic test",
              "input": "[2,7,11,15]\n9",
              "expected_output": "[0,1]",
              "is_hidden": false
          }
      ]
  }
  ```

**Student:**
- `POST /api/courses/:id/coding-assignments/:codingAssignmentId/submit` - Submit code
  ```json
  // Request
  {
      "code": "def two_sum(nums, target):\n    pass"
  }
  ```
  ```json
  // Response includes grading results
  {
      "passed": true,
      "test_results": [
          {
              "test_case_id": "uuid",
              "actual": "[0,1]",
              "passed": true,
              "execution_time_ms": 12
          }
      ]
  }
  ```

### Code Execution
- `POST /api/code/run` - Execute code in sandbox
  ```json
  // Request
  {
      "code": "print('Hello World')",
      "language": "python",
      "input": "stdin data if any"
  }
  ```
  ```json
  // Response
  {
      "output": "Hello World\n",
      "stderr": "",
      "execution_time_ms": 15
  }
  ```

### Chat
- `POST /api/chat/conversations` - Create a new conversation
  ```json
  // Request
  {
      "type": "direct", // "direct", "group"
      "participant_ids": ["user-uuid-1", "user-uuid-2"]
  }
  ```
- Send message (Typically via Websocket `GET /ws/chat`, but payload shape is equivalent to standard send)
  ```json
  // Payload
  {
      "content": "Hello there!"
  }
  ```

### Notifications
- `GET /api/notifications` returns:
  ```json
  [
      {
          "id": "uuid",
          "title": "Assignment Graded",
          "message": "Your submission was graded: 95/100",
          "is_read": false,
          "type": "assignment_graded",
          "created_at": "..."
      }
  ]
  ```

---
*Note: This document covers the most common request/response DTO structures. For complete fields, refer to `internal/dto/` packages.*
