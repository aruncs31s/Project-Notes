# Assignment Model

## Assignment

```go
type Assignment struct {
    ID          uuid.UUID `gorm:"type:uuid;primaryKey"`
    CourseID    uuid.UUID `gorm:"type:uuid;index"`
    Title       string   `gorm:"type:varchar(255);not null"`
    Description string   `gorm:"type:text"`
    MaxScore    int      `gorm:"default:100"`
    DueDate     *time.Time
    CreatedAt   time.Time
    UpdatedAt   time.Time
    DeletedAt   gorm.DeletedAt `gorm:"index"`

    Course      Course                 `gorm:"foreignKey:CourseID"`
    Submissions []AssignmentSubmission `gorm:"foreignKey:AssignmentID"`
}
```

## Assignment Submission

```go
type AssignmentSubmission struct {
    ID            uuid.UUID `gorm:"type:uuid;primaryKey"`
    AssignmentID  uuid.UUID `gorm:"type:uuid;index"`
    UserID        uuid.UUID `gorm:"type:uuid;index"`
    FileURL       string   `gorm:"type:varchar(255);not null"`
    ExtractedText string   `gorm:"type:text"`
    Score         *int
    Feedback      string   `gorm:"type:text"`
    SubmittedAt   time.Time
    GradedAt      *time.Time
}
```

## Fields

### Assignment

| Field | Type | Description |
|-------|------|------------|
| `ID` | UUID | Primary key |
| `CourseID` | UUID | Course reference |
| `Title` | string | Assignment title |
| `Description` | text | Instructions |
| `MaxScore` | int | Maximum points |
| `DueDate` | timestamp | Due date |

### Submission

| Field | Type | Description |
|-------|------|------------|
| `FileURL` | string | Uploaded file URL |
| `ExtractedText` | text | OCR extracted text |
| `Score` | int? | Graded score |
| `Feedback` | text | Teacher feedback |