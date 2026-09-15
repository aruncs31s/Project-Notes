# Certificate Model

## Structure

```go
type Certificate struct {
    ID        uuid.UUID `gorm:"type:uuid;primaryKey"`
    UserID    uuid.UUID `gorm:"type:uuid;index"`
    CourseID  uuid.UUID `gorm:"type:uuid;index"`
    FileURL   string   `gorm:"type:varchar(255)"`
    Layout    int      `gorm:"default:1"`
    IssuedAt  time.Time `gorm:"autoCreateTime"`
}
```

## Fields

| Field | Type | Description |
|-------|------|------------|
| `UserID` | UUID | Recipient |
| `CourseID` | UUID | Course reference |
| `FileURL` | string | PDF file path |
| `Layout` | int | Design template (1-10) |
| `IssuedAt` | timestamp | Issue date |

## Certificate Generation

1. User completes all course modules
2. User or admin requests certificate
3. PDF generated with course/ user details
4. Certificate record saved

## Related

- [[certificate-handler]] - HTTP handlers
- [[certgen-package]] - PDF generation