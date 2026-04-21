# DTOs - Data Transfer Objects

## User DTOs

### RegisterRequest

```go
type RegisterRequest struct {
    Email     string `json:"email" binding:"required,email"`
    Password string `json:"password" binding:"required,min=6"`
    FirstName string `json:"first_name" binding:"required"`
    LastName string `json:"last_name" binding:"required"`
    Role     string `json:"role" binding:"required,oneof=student teacher admin"`
}
```

### LoginRequest

```go
type LoginRequest struct {
    Email    string `json:"email" binding:"required,email"`
    Password string `json:"password" binding:"required"`
}
```

### AuthResponse

```go
type AuthResponse struct {
    Token string       `json:"token"`
    User  UserResponse `json:"user"`
}
```

### UserResponse

```go
type UserResponse struct {
    ID        string `json:"id"`
    Email     string `json:"email"`
    FirstName string `json:"first_name"`
    LastName string `json:"last_name"`
    Role    string `json:"role"`
    AvatarURL string `json:"avatar_url"`
    Bio     string `json:"bio"`
}
```

## Course DTOs

### CreateCourseRequest

```go
type CreateCourseRequest struct {
    Title                  string  `json:"title" binding:"required"`
    Description            string  `json:"description"`
    Price                  float64 `json:"price"`
    Type                  string  `json:"type"`
    Format                string  `json:"format"`
    Status                string  `json:"status"`
    ThumbnailURL           string  `json:"thumbnail_url"`
    Duration              string  `json:"duration"`
    StartDate             string  `json:"start_date"`
    IsCertificateAvailable bool    `json:"is_certificate_available"`
}
```

### CourseResponse

```go
type CourseResponse struct {
    ID                     string `json:"id"`
    Title                  string `json:"title"`
    Description            string `json:"description"`
    Price                  float64 `json:"price"`
    Type                  string `json:"type"`
    Format                string `json:"format"`
    Status                string `json:"status"`
    ThumbnailURL           string `json:"thumbnail_url"`
    Duration              string `json:"duration"`
    StartDate             string `json:"start_date"`
    IsCertificateAvailable bool `json:"is_certificate_available"`
    TeacherID             string `json:"teacher_id"`
    TeacherName           string `json:"teacher_name"`
    LikesCount           int `json:"likes_count"`
    StudentCount        int `json:"student_count"`
    Progress            float64 `json:"progress"`
    IsLiked            bool `json:"is_liked"`
    Modules            []ModuleResponse `json:"modules"`
}
```

## Module DTOs

### CreateModuleRequest

```go
type CreateModuleRequest struct {
    Title       string  `json:"title" binding:"required"`
    Type        string  `json:"type" binding:"required"`
    Description string  `json:"description"`
    VideoURL    string  `json:"video_url"`
    PDFURL      string  `json:"pdf_url"`
    Points      int     `json:"points"`
    IsFree      bool    `json:"is_free"`
    ParentID    *string `json:"parent_id"`
}
```

### ModuleResponse

```go
type ModuleResponse struct {
    ID          string `json:"id"`
    CourseID    string `json:"course_id"`
    Title       string `json:"title"`
    Type       string `json:"type"`
    Description string `json:"description"`
    VideoURL   string `json:"video_url"`
    PDFURL     string `json:"pdf_url"`
    Points     int `json:"points"`
    IsFree     bool `json:"is_free"`
    OrderIndex int `json:"order_index"`
    ParentID  *string `json:"parent_id"`
    IsCompleted bool `json:"is_completed"`
}
```

## Assignment DTOs

### CreateAssignmentRequest

```go
type CreateAssignmentRequest struct {
    Title       string  `json:"title" binding:"required"`
    Description string  `json:"description"`
    MaxScore    int     `json:"max_score"`
    DueDate     *string `json:"due_date"`
}
```

### SubmitAssignmentRequest

```go
type SubmitAssignmentRequest struct {
    FileURL string `json:"file_url" binding:"required"`
}
```

### GradeSubmissionRequest

```go
type GradeSubmissionRequest struct {
    Score    int `json:"score" binding:"required"`
    Feedback string `json:"feedback"`
}
```

## Coding DTOs

### CreateCodingAssignmentRequest

```go
type CreateCodingAssignmentRequest struct {
    Title       string      `json:"title" binding:"required"`
    Description string      `json:"description"`
    Language   string      `json:"language" binding:"required"`
    MaxScore   int        `json:"max_score"`
    StarterCode string    `json:"starter_code"`
    TestCases  []TestCase `json:"test_cases" binding:"required"`
    DueDate   *string   `json:"due_date"`
}
```

### TestCase

```go
type TestCase struct {
    ID             string `json:"id"`
    Input         string `json:"input"`
    ExpectedOutput string `json:"expected_output"`
    Description  string `json:"description"`
    IsHidden     bool   `json:"is_hidden"`
}
```

### RunCodeRequest

```go
type RunCodeRequest struct {
    Code     string `json:"code" binding:"required"`
    Language string `json:"language" binding:"required"`
    Input    string `json:"input"`
}
```

### RunCodeResponse

```go
type RunCodeResponse struct {
    Output         string `json:"output"`
    Error         string `json:"error"`
    Stderr       string `json:"stderr"`
    ExecutionTimeMs int    `json:"execution_time_ms"`
}
```

## Chat DTOs

### CreateConversationRequest

```go
type CreateConversationRequest struct {
    Type           string   `json:"type" binding:"required"`
    ParticipantIDs []string `json:"participant_ids" binding:"required"`
}
```

### MessageResponse

```go
type MessageResponse struct {
    ID             string `json:"id"`
    ConversationID string `json:"conversation_id"`
    SenderID      string `json:"sender_id"`
    Content       string `json:"content"`
    AttachmentURL string `json:"attachment_url"`
    CreatedAt    string `json:"created_at"`
}
```