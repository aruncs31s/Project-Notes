# Profile Model

## Structure

```go
type Profile struct {
    ID          uuid.UUID `gorm:"type:uuid;primaryKey"`
    UserID      uuid.UUID `gorm:"type:uuid;uniqueIndex;not null"`
    FirstName   string   `gorm:"type:varchar(100)"`
    LastName    string   `gorm:"type:varchar(100)"`
    AvatarURL   string   `gorm:"type:varchar(255)"`
    Bio        string   `gorm:"type:text"`
    Points     int      `gorm:"default:0"`
    CreatedAt  time.Time
    UpdatedAt  time.Time
}
```

## Fields

| Field | Type | Description |
|-------|------|------------|
| `UserID` | UUID | Reference to User |
| `FirstName` | string | First name |
| `LastName` | string | Last name |
| `AvatarURL` | string | Profile picture |
| `Bio` | text | User bio |
| `Points` | int | Gamification points |