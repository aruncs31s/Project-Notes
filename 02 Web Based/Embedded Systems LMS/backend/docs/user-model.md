# User Model

## Structure

```go
type User struct {
    ID            uuid.UUID   `gorm:"type:uuid;primaryKey"`
    Email         string    `gorm:"uniqueIndex;not null"`
    PasswordHash  string    `gorm:"not null"`
    Role         Role      `gorm:"not null"`
    CreatedAt    time.Time
    UpdatedAt    time.Time
    DeletedAt    gorm.DeletedAt `gorm:"index"`

    Profile      Profile     `gorm:"foreignKey:UserID"`
    Enrollments  []Enrollment `gorm:"foreignKey:UserID"`
}
```

## Roles

```go
const (
    RoleStudent Role = "student"
    RoleTeacher Role = "teacher"
    RoleAdmin   Role = "admin"
)
```

## Fields

| Field | Type | Description |
|-------|------|------------|
| `ID` | UUID | Primary key |
| `Email` | string | Unique email address |
| `PasswordHash` | string | Bcrypt hashed password |
| `Role` | Role | User role (student/teacher/admin) |
| `CreatedAt` | timestamp | Account creation time |
| `UpdatedAt` | timestamp | Last update time |
| `DeletedAt` | timestamp | Soft delete time |

## Relationships

- **Profile** - One-to-one with extended user data
- **Enrollments** - One-to-many with course enrollments

## Methods

```go
func (u *User) IsAdmin() bool
func (u *User) IsTeacher() bool
func (u *User) IsStudent() bool
```

## Related

- [[user-model]]
- [[profile-model]]
- [[dto-user]] - Data transfer objects