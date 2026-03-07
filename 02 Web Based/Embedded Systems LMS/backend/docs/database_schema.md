# Database Schemas & Domain Models

The application models its domain entities using GORM. Below are the primary entities and their relationships.

## Core Entities

### User
Users represent all actors in the system.
- Roles: Admin, Teacher, Student
- Relationships: One-to-Many with Courses (created), Enrollments, Achievements, Submissions, Messages.

### Course
Core educational content container.
- Includes details like Title, Description, Thumbnail, Price, and Status (Published/Draft).
- Relationships: One-to-Many with Modules, Assignments, Coding Assignments, Reviews, Likes.

### Course Module
Sections inside a course containing the actual content (videos, text).

### Assignment & Submission
Standard assignments where users can upload documents or submit text.
- Grading is handled by teachers, updating the `Score` and `Feedback`.

### Coding Assignment
Specialized assignments evaluated via a code-runner execution environment.
- Contains predefined test cases, constraints (time, memory), and starter code.

### Certificate
Awarded upon course completion.
- Tracks the generated PDF file path and unique certificate ID.

### Notifications & Chat Message
- **Notifications**: System alerts for users (e.g., assignment graded, new course).
- **Chat Messages**: Direct messaging or course group messaging payloads, managed via websockets and stored here.

*(All models use standard GORM conventions, typically implementing `gorm.Model` for ID, CreatedAt, UpdatedAt, and DeletedAt soft deletes.)*

## Entity-Relationship Diagram

```mermaid
erDiagram
    User ||--|| Profile : has
    User ||--o{ Course : teaches
    User ||--o{ Enrollment : creates
    Course ||--o{ Enrollment : has
    User ||--o{ Achievement : earns
    Course ||--o{ Module : contains
    Module ||--o{ Module : parent-child
    User ||--o{ ModuleProgress : tracks
    Module ||--o{ ModuleProgress : tracked_by
    Course ||--o{ Assignment : has
    Assignment ||--o{ AssignmentSubmission : receives
    User ||--o{ AssignmentSubmission : submits
    Course ||--o{ CodingAssignment : has
    CodingAssignment ||--o{ CodingSubmission : receives
    User ||--o{ CodingSubmission : submits
    Course ||--o{ CourseReview : receives
    User ||--o{ CourseReview : writes
    User |o--o{ CourseLike : likes
    Course |o--o{ CourseLike : liked_by
    User |o--o{ WatchLater : saves
    Course |o--o{ WatchLater : saved_by
    User ||--o{ Certificate : receives
    Course ||--o{ Certificate : grants
    Conversation ||--o{ ConversationParticipant : has
    User ||--o{ ConversationParticipant : participates_in
    Conversation ||--o{ Message : contains
    User ||--o{ Message : sends
    User ||--o{ Notification : receives

    User {
        uuid ID PK
        string Role
        string Email
    }
    Profile {
        uuid UserID PK, FK
        string FirstName
        string LastName
    }
    Course {
        uuid ID PK
        uuid TeacherID FK
        string Title
    }
    Module {
        uuid ID PK
        uuid CourseID FK
        string Title
    }
    Enrollment {
        uuid UserID PK, FK
        uuid CourseID PK, FK
        string Status
    }
    Assignment {
        uuid ID PK
        uuid CourseID FK
        string Title
    }
    AssignmentSubmission {
        uuid ID PK
        uuid AssignmentID FK
        uuid UserID FK
        int Score
    }
    CodingAssignment {
        uuid ID PK
        uuid CourseID FK
        string Language
    }
    CodingSubmission {
        uuid ID PK
        uuid CodingAssignmentID FK
        uuid UserID FK
        bool Passed
    }
    Certificate {
        uuid ID PK
        uuid UserID FK
        uuid CourseID FK
    }
    Conversation {
        uuid ID PK
        string Type
    }
    ConversationParticipant {
        uuid ConversationID PK, FK
        uuid UserID PK, FK
    }
    Message {
        uuid ID PK
        uuid ConversationID FK
        uuid SenderID FK
        string Content
    }
    Notification {
        uuid ID PK
        uuid UserID FK
        string Message
        bool IsRead
    }
```
