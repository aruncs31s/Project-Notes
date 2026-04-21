# Course Interaction Model

## CourseReview

```go
type CourseReview struct {
    ID        uuid.UUID `gorm:"type:uuid;primaryKey"`
    CourseID  uuid.UUID `gorm:"type:uuid;index"`
    UserID    uuid.UUID `gorm:"type:uuid;index"`
    Rating    int      `gorm:"check:rating >= 1 AND rating <= 5"`
    Comment   string   `gorm:"type:text"`
    CreatedAt time.Time
}
```

## CourseLike

```go
type CourseLike struct {
    UserID    uuid.UUID `gorm:"type:uuid;primaryKey"`
    CourseID  uuid.UUID `gorm:"type:uuid;primaryKey"`
    CreatedAt time.Time
}
```

## WatchLater

```go
type WatchLater struct {
    UserID    uuid.UUID `gorm:"type:uuid;primaryKey"`
    CourseID  uuid.UUID `gorm:"type:uuid;primaryKey"`
    CreatedAt time.Time
}
```

## Fields

### CourseReview

| Field | Type | Description |
|-------|------|------------|
| `Rating` | int | 1-5 stars |
| `Comment` | text | Review text |

## Constraints

- Rating must be between 1 and 5
- One review per user per course