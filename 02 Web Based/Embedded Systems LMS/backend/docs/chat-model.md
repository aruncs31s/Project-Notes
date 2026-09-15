# Chat Model

## Conversation

```go
type Conversation struct {
    ID        uuid.UUID `gorm:"type:uuid;primaryKey"`
    Type     string    `gorm:"type:varchar(50)"`
    CreatedAt time.Time
    UpdatedAt time.Time

    Participants []Participant `gorm:"foreignKey:ConversationID"`
    Messages    []Message    `gorm:"foreignKey:ConversationID"`
}
```

## Participant

```go
type Participant struct {
    ConversationID uuid.UUID `gorm:"type:uuid;primaryKey"`
    UserID         uuid.UUID `gorm:"type:uuid;primaryKey"`
    JoinedAt       time.Time
}
```

## Message

```go
type Message struct {
    ID             uuid.UUID  `gorm:"type:uuid;primaryKey"`
    ConversationID uuid.UUID  `gorm:"type:uuid;index"`
    SenderID       uuid.UUID  `gorm:"type:uuid;index"`
    Content        string    `gorm:"type:text"`
    AttachmentURL string
    CreatedAt     time.Time
}
```

## Conversation Types

| Type | Description |
|------|------------|
| `direct` | Two users |
| `group` | Multiple users |

## Related

- [[chat-handler]] - HTTP handlers
- [[chat-service]] - Business logic