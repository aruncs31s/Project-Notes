# Enrollment Model

## Structure

```go
type Enrollment struct {
    UserID             uuid.UUID        `gorm:"type:uuid;primaryKey"`
    CourseID           uuid.UUID        `gorm:"type:uuid;primaryKey"`
    Status             EnrollmentStatus `gorm:"default:'active'"`
    ProgressPercentage float64         `gorm:"default:0"`
    EnrolledAt         time.Time
    Course            Course         `gorm:"foreignKey:CourseID"`
}
```

## Enrollment Status

| Status | Description |
|--------|------------|
| `active` | Currently enrolled |
| `completed` | Course completed |

## Fields

| Field | Type | Description |
|-------|------|------------|
| `UserID` | UUID | Student reference |
| `CourseID` | UUID | Course reference |
| `Status` | EnrollmentStatus | Active or completed |
| `ProgressPercentage` | float64 | Completion percentage |
| `EnrolledAt` | timestamp | Enrollment time |

## Progress Calculation

Progress is calculated as:
```
(completed_modules / total_modules) * 100
```