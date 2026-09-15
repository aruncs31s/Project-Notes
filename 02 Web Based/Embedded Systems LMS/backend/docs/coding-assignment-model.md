# Coding Assignment Model

## Structure

```go
type CodingAssignment struct {
    ID          uuid.UUID  `gorm:"type:uuid;primaryKey"`
    CourseID    uuid.UUID `gorm:"type:uuid;index"`
    Title       string   `gorm:"type:varchar(255);not null"`
    Description string   `gorm:"type:text"`
    Language    string   `gorm:"type:varchar(50);not null"`
    StarterCode string   `gorm:"type:text"`
    MaxScore    int     `gorm:"default:100"`
    DueDate     *time.Time
    CreatedAt   time.Time
    UpdatedAt   time.Time
    DeletedAt   gorm.DeletedAt `gorm:"index"`

    TestCases  []CodingTestCase  `gorm:"foreignKey:AssignmentID"`
    Course    Course          `gorm:"foreignKey:CourseID"`
    Submissions []CodingSubmission `gorm:"foreignKey:AssignmentID"`
}
```

## Coding Submission

```go
type CodingSubmission struct {
    ID                  uuid.UUID  `gorm:"type:uuid;primaryKey"`
    CodingAssignmentID    uuid.UUID `gorm:"type:uuid;index"`
    UserID              uuid.UUID `gorm:"type:uuid;index"`
    Code               string   `gorm:"type:text;not null"`
    Score              *int
    Passed             bool
    Feedback          string   `gorm:"type:text"`
    TestResults        JSON    `gorm:"type:jsonb"`
    SubmittedAt        time.Time `gorm:"autoCreateTime"`
    GradedAt          *time.Time
}
```

## Supported Languages

| Language | Extension |
|----------|----------|
| `python` | `.py` |
| `javascript` | `.js` |

## Test Case

```go
type CodingTestCase struct {
    ID             uuid.UUID `gorm:"type:uuid;primaryKey"`
    AssignmentID   uuid.UUID `gorm:"type:uuid;index"`
    Input         string   `gorm:"type:text"`
    ExpectedOutput string   `gorm:"type:text"`
    IsHidden      bool     `gorm:"default:false"`
    Description   string   `gorm:"type:text"`
}
```

## Fields

| Field | Type | Description |
|-------|------|------------|
| `Language` | string | Programming language |
| `StarterCode` | text | Initial code template |
| `MaxScore` | int | Maximum points |
| `TestCases` | array | Hidden/public test cases |