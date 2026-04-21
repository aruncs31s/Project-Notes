# Notification Model

## Structure

```go
type Notification struct {
    ID        uuid.UUID `gorm:"type:uuid;primaryKey"`
    UserID    uuid.UUID `gorm:"type:uuid;index"`
    Type     string   `gorm:"type:varchar(50)"`
    Title    string   `gorm:"type:varchar(255)"`
    Message  string   `gorm:"type:text"`
    IsRead   bool     `gorm:"default:false"`
    CreatedAt time.Time
}
```

## Notification Types

| Type | Description |
|------|------------|
| `enrollment` | New enrollment |
| `submission` | Assignment submitted |
| `grade` | Assignment graded |
| `achievement` | Achievement earned |
| `certificate` | Certificate ready |
| `announcement` | Course announcement |

## Fields

| Field | Type | Description |
|-------|------|------------|
| `UserID` | UUID | Recipient |
| `Type` | string | Notification type |
| `Title` | string | Title |
| `Message` | string | Content |
| `IsRead` | bool | Read status |