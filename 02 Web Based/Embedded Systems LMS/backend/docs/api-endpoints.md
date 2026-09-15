# API Endpoints

Complete list of REST API endpoints.

## Authentication

| Method | Endpoint | Description | Auth |
|--------|---------|------------|------|
| POST | `/api/register` | Register new user | No |
| POST | `/api/login` | User login | No |

## Health

| Method | Endpoint | Description | Auth |
|--------|---------|------------|------|
| GET | `/api/health` | Health check | No |
| GET | `/api/version` | API version | No |

## Courses

| Method | Endpoint | Description | Auth |
|--------|---------|------------|------|
| GET | `/api/courses` | List all courses | Optional |
| GET | `/api/courses/trending` | Trending courses | Optional |
| GET | `/api/courses/search` | Search courses | Optional |
| GET | `/api/courses/:id` | Get course by ID | Optional |
| POST | `/api/courses` | Create course | Teacher/Admin |
| PUT | `/api/courses/:id` | Update course | Teacher/Admin |
| DELETE | `/api/courses/:id` | Delete course | Teacher/Admin |

### Course Modules

| Method | Endpoint | Description | Auth |
|--------|---------|------------|------|
| POST | `/api/courses/:id/modules` | Create module | Teacher/Admin |
| PUT | `/api/courses/:id/modules/:moduleId` | Update module | Teacher/Admin |
| DELETE | `/api/courses/:id/modules/:moduleId` | Delete module | Teacher/Admin |
| POST | `/api/courses/:id/modules/reorder` | Reorder modules | Teacher/Admin |

### Course Interactions

| Method | Endpoint | Description | Auth |
|--------|---------|------------|------|
| POST | `/api/courses/:id/like` | Like course | Yes |
| DELETE | `/api/courses/:id/like` | Unlike course | Yes |
| POST | `/api/courses/:id/enroll` | Enroll in course | Yes |
| GET | `/api/courses/:id/enrollment` | Get enrollment status | Yes |
| POST | `/api/courses/:id/reviews` | Add review | Yes |
| GET | `/api/courses/:id/reviews` | Get reviews | Yes |
| POST | `/api/courses/:id/modules/:moduleId/complete` | Complete module | Yes |

## Assignments

| Method | Endpoint | Description | Auth |
|--------|---------|------------|------|
| GET | `/api/courses/:id/assignments` | List assignments | Yes |
| GET | `/api/courses/:id/assignments/:assignmentId` | Get assignment | Yes |
| POST | `/api/courses/:id/assignments` | Create assignment | Teacher/Admin |
| PUT | `/api/courses/:id/assignments/:assignmentId` | Update assignment | Teacher/Admin |
| DELETE | `/api/courses/:id/assignments/:assignmentId` | Delete assignment | Teacher/Admin |
| POST | `/api/courses/:id/assignments/:assignmentId/submit` | Submit assignment | Yes |
| GET | `/api/courses/:id/assignments/:assignmentId/submissions/me` | Get own submission | Yes |
| GET | `/api/courses/:id/assignments/:assignmentId/submissions` | Get all submissions | Teacher/Admin |
| PUT | `/api/courses/:id/assignments/:assignmentId/submissions/:submissionId/grade` | Grade submission | Teacher/Admin |

## Coding Assignments

| Method | Endpoint | Description | Auth |
|--------|---------|------------|------|
| GET | `/api/courses/:id/coding-assignments` | List coding assignments | Yes |
| GET | `/api/courses/:id/coding-assignments/:id` | Get coding assignment | Yes |
| POST | `/api/courses/:id/coding-assignments` | Create coding assignment | Teacher/Admin |
| PUT | `/api/courses/:id/coding-assignments/:id` | Update coding assignment | Teacher/Admin |
| DELETE | `/api/courses/:id/coding-assignments/:id` | Delete coding assignment | Teacher/Admin |
| POST | `/api/courses/:id/coding-assignments/:id/submit` | Submit code | Yes |
| GET | `/api/courses/:id/coding-assignments/:id/submissions/me` | Get own submission | Yes |
| POST | `/api/code/run` | Run code in sandbox | Yes |

## Uploads

| Method | Endpoint | Description | Auth |
|--------|---------|------------|------|
| POST | `/api/upload/video` | Upload video | Teacher/Admin |
| POST | `/api/upload/image` | Upload image | All |
| POST | `/api/upload/pdf` | Upload PDF | Teacher/Admin |
| POST | `/api/upload/attachment` | Upload attachment | All |

## Certificates

| Method | Endpoint | Description | Auth |
|--------|---------|------------|------|
| POST | `/api/certificates/generate` | Generate certificate | Yes |
| GET | `/api/certificates/download` | Download certificate | No |

## Chat

| Method | Endpoint | Description | Auth |
|--------|---------|------------|------|
| WS | `/ws/chat` | WebSocket connection | Yes |
| GET | `/api/chat/conversations` | List conversations | Yes |
| POST | `/api/chat/conversations` | Create conversation | Yes |
| GET | `/api/chat/conversations/:id/messages` | Get messages | Yes |

## Notifications

| Method | Endpoint | Description | Auth |
|--------|---------|------------|------|
| GET | `/api/notifications` | List notifications | Yes |
| GET | `/api/notifications/unread-count` | Unread count | Yes |
| PUT | `/api/notifications/:id/read` | Mark as read | Yes |

## Users

| Method | Endpoint | Description | Auth |
|--------|---------|------------|------|
| GET | `/api/users` | List users | Yes |
| GET | `/api/users/search` | Search users | Yes |
| PUT | `/api/users/profile` | Update profile | Yes |
| GET | `/api/users/:id/enrolments` | Get user enrollments | Yes |

## Leaderboard

| Method | Endpoint | Description | Auth |
|--------|---------|------------|------|
| GET | `/api/leaderboard` | Get leaderboard | No |