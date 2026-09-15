# Module Model

## Structure

```go
type Module struct {
    ID          uuid.UUID  `gorm:"type:uuid;primaryKey"`
    CourseID    uuid.UUID  `gorm:"type:uuid;index"`
    ParentID    *uuid.UUID `gorm:"type:uuid;index"`
    Title       string    `gorm:"size:255;not null"`
    Description string    `gorm:"type:text"`
    Type        string    `gorm:"default:'video'"`
    VideoURL    string
    PDFURL      string
    Points      int      `gorm:"default:0"`
    IsFree      bool     `gorm:"default:false"`
    OrderIndex  int      `gorm:"not null"`
    CreatedAt   time.Time
    UpdatedAt   time.Time
}
```

## Module Types

| Type | Description |
|------|------------|
| `video` | Video content |
| `chapter` | Text chapter |
| `pdf` | PDF document |

## Fields

| Field | Type | Description |
|-------|------|------------|
| `ID` | UUID | Primary key |
| `CourseID` | UUID | Parent course reference |
| `ParentID` | UUID? | Parent module (for nested) |
| `Title` | string | Module title |
| `Description` | text | Module description |
| `Type` | string | Content type |
| `VideoURL` | string | Video URL |
| `PDFURL` | string | PDF URL |
| `Points` | int | Completion points |
| `IsFree` | bool | Free preview |
| `OrderIndex` | int | Sort order |

## Module Progress

```go
type ModuleProgress struct {
    UserID      uuid.UUID `gorm:"type:uuid;primaryKey"`
    ModuleID    uuid.UUID `gorm:"type:uuid;primaryKey"`
    Completed   bool
    CompletedAt *time.Time
}
```