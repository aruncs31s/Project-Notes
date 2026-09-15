# Uploaded Media Model

## Structure

```go
type UploadedMedia struct {
    ID           uuid.UUID `gorm:"type:uuid;primaryKey"`
    UserID       uuid.UUID `gorm:"type:uuid;index"`
    Type        string  `gorm:"type:varchar(50);index"`
    OriginalName string  `gorm:"type:varchar(255)"`
    FileName    string  `gorm:"type:varchar(255)"`
    FilePath    string  `gorm:"type:varchar(512)"`
    FileSize   int64   `gorm:"default:0"`
    MimeType   string  `gorm:"type:varchar(100)"`
    CreatedAt  time.Time
}
```

## Media Types

| Type | Description | Extensions |
|------|------------|-----------|
| `video` | Video files | mp4, webm, mov |
| `image` | Images | jpg, png, webp, gif |
| `pdf` | Documents | pdf |
| `attachment` | General files | any |

## Fields

| Field | Type | Description |
|-------|------|------------|
| `UserID` | UUID | Uploader |
| `Type` | string | Media type |
| `OriginalName` | string | Original filename |
| `FileName` | string | Stored filename |
| `FilePath` | string | Storage path |
| `FileSize` | int64 | Size in bytes |
| `MimeType` | string | MIME type |

## Storage

Files are stored in `/uploads/` directory with subdirectories:
- `videos/`
- `images/`
- `pdfs/`
- `attachments/`