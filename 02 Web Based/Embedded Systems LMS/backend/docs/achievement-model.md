# Achievement Model

## Structure

```go
type Achievement struct {
    ID          uuid.UUID `gorm:"type:uuid;primaryKey"`
    Title      string   `gorm:"type:varchar(255);not null"`
    Description string   `gorm:"type:text"`
    IconURL    string   `gorm:"type:varchar(255)"`
    Points     int      `gorm:"default:0"`
    CreatedAt  time.Time
}

type UserAchievement struct {
    UserID         uuid.UUID `gorm:"type:uuid;primaryKey"`
    AchievementID  uuid.UUID `gorm:"type:uuid;primaryKey"`
    EarnedAt      time.Time `gorm:"autoCreateTime"`
}
```

## Fields

| Field | Type | Description |
|-------|------|------------|
| `Title` | string | Achievement name |
| `Description` | text | How to earn |
| `IconURL` | string | Badge image |
| `Points` | int | Points awarded |

## Related

- [[gamification-system]] - Gamification overview