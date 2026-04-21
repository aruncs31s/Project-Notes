# Course Model

## Structure

```go
type Course struct {
    ID                     uuid.UUID  `gorm:"type:uuid;primaryKey"`
    PrevID                 string    `gorm:"type:varchar(255)"`
    TeacherID              uuid.UUID  `gorm:"index"`
    Title                 string    `gorm:"not null"`
    Description           string    `gorm:"type:text"`
    ThumbnailURL          string
    Price                 float64   `gorm:"default:0"`
    Type                  string    `gorm:"default:'paid'"`
    Format                string    `gorm:"default:'course'"`
    Status                string    `gorm:"default:'coming soon'"`
    Duration              string
    IsCertificateAvailable bool     `gorm:"default:false"`
    StartDate             *time.Time
    CreatedAt             time.Time
    UpdatedAt             time.Time
    DeletedAt             gorm.DeletedAt `gorm:"index"`

    Teacher      User         `gorm:"foreignKey:TeacherID"`
    Modules     []Module     `gorm:"foreignKey:CourseID"`
    Enrollments []Enrollment `gorm:"foreignKey:CourseID"`
}
```

## Fields

| Field | Type | Description |
|-------|------|------------|
| `ID` | UUID | Primary key |
| `PrevID` | string | URL-friendly slug |
| `TeacherID` | UUID | Course owner reference |
| `Title` | string | Course title |
| `Description` | text | Course description |
| `ThumbnailURL` | string | Course thumbnail |
| `Price` | decimal | Course price |
| `Type` | string | `free` or `paid` |
| `Format` | string | `course` or `project` |
| `Status` | string | `coming soon`, `active`, or `ended` |
| `Duration` | string | Estimated duration |
| `IsCertificateAvailable` | bool | Certificate enabled |

## Course Types

| Type | Description |
|------|------------|
| `free` | No cost |
| `paid` | Requires payment |

## Course Formats

| Format | Description |
|--------|------------|
| `course` | Standard course |
| `project` | Project-based learning |

## Course Status

| Status | Description |
|--------|------------|
| `coming soon` | Not yet started |
| `active` | Currently available |
| `ended` | Past course |

## Related

- [[module-model]]
- [[enrollment-model]]