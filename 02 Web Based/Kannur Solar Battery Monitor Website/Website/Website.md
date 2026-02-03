# Website

Based on our conversation, I implemented a many-to-many relationship between [Version](vscode-file://vscode-app/opt/visual-studio-code/resources/app/out/vs/code/electron-browser/workbench/workbench.html) and `Feature` models using a junction table. Here's a comprehensive documentation of all the changes made:

## Summary of Changes: Implementing Many-to-Many Relationship Between Versions and Features

### Problem Addressed

- Previously, features were duplicated for each version (one-to-many relationship)
- A feature couldn't be shared across multiple versions
- Inefficient data storage and maintenance

### Solution Implemented

- Converted to many-to-many relationship using a junction table `version_features`
- Features can now be shared across multiple versions
- Eliminated feature duplication

---

## 1. Model Changes ([versions.go](vscode-file://vscode-app/opt/visual-studio-code/resources/app/out/vs/code/electron-browser/workbench/workbench.html))

### Before:

```go
type Version struct {
    // ... other fields
    Features []Feature `json:"Features" gorm:"foreignKey:VersionID;constraint:OnUpdate:CASCADE,OnDelete:CASCADE"`
}

type Feature struct {
    ID          uint   `json:"ID" gorm:"column:id;primaryKey;autoIncrement"`
    VersionID   uint   `json:"VersionID" gorm:"column:version_id;not null;index"`
    FeatureName string `json:"FeatureName" gorm:"column:feature_name;unique;not null"`
    Enabled     bool   `json:"Enabled" gorm:"column:enabled;not null;default:false"`
}
```
### After:

```go
type Version struct {
    // ... other fields
    Features []Feature `json:"Features" gorm:"many2many:version_features;"`
}

type Feature struct {
    ID          uint      `json:"ID" gorm:"column:id;primaryKey;autoIncrement"`
    FeatureName string    `json:"FeatureName" gorm:"column:feature_name;unique;not null"`
    Enabled     bool      `json:"Enabled" gorm:"column:enabled;not null;default:false"`
    Versions    []Version `json:"Versions" gorm:"many2many:version_features;"`
}

// New junction table model
type VersionFeature struct {
    VersionID uint `json:"VersionID" gorm:"column:version_id;primaryKey;not null"`
    FeatureID uint `json:"FeatureID" gorm:"column:feature_id;primaryKey;not null"`
}
```
---

## 2. Database Migration ([db.go](vscode-file://vscode-app/opt/visual-studio-code/resources/app/out/vs/code/electron-browser/workbench/workbench.html))

### Added:

- [&model.VersionFeature{}](vscode-file://vscode-app/opt/visual-studio-code/resources/app/out/vs/code/electron-browser/workbench/workbench.html) to the `AutoMigrate` call
- Moved the device state fix **after** AutoMigrate to prevent "table doesn't exist" errors

### Before:
```go
// Fix existing devices with invalid device_state before migration
if err := db.Exec("UPDATE devices SET current_state = 1 WHERE current_state = 0").Error; err != nil {
    return nil, fmt.Errorf("failed to update device states: %w", err)
}

if err := db.AutoMigrate(/* models */); err != nil {
    return nil, err
}
```
### After:

```go
if err := db.AutoMigrate(/* models including VersionFeature */); err != nil {
    return nil, err
}

// Fix existing devices with invalid device_state after migration
if err := db.Exec("UPDATE devices SET current_state = 1 WHERE current_state = 0").Error; err != nil {
    return nil, fmt.Errorf("failed to update device states: %w", err)
}
```

---

## 3. Repository Changes ([version_repository.go](vscode-file://vscode-app/opt/visual-studio-code/resources/app/out/vs/code/electron-browser/workbench/workbench.html))

### Updated `CreateNewDeviceVersion`:

- **Before**: Created duplicate features for each version
- **After**: Associates existing features with the new version using GORM associations

### Before:


```go
// Create New Features for the new version
var newFeatures []model.Feature
for _, f := range allFeatures {
    newFeature := model.Feature{
        VersionID:   version.ID,
        FeatureName: f.FeatureName,
        Enabled:     f.Enabled,
    }
    newFeatures = append(newFeatures, newFeature)
}
if err := tx.Create(&newFeatures).Error; err != nil {
    return err
}
```

### After:

```go
// Associate features with the new version via many-to-many
if err := tx.Model(&version).Association("Features").Append(allFeatures); err != nil {
    return err
}
```

### Updated `GetFeaturesByVersion`:

- **Before**: Queried features by `version_id` foreign key
- **After**: Uses GORM preload to get associated features

### Before:

```go
err := r.db.Where("version_id = ?", versionID).Find(&features).Error
```

### After:

```go
var version model.Version
err := r.db.Preload("Features").First(&version, versionID).Error
return version.Features, nil
```

### Added `AssociateFeatureWithVersion` method:

```go
func (r *versionRepository) AssociateFeatureWithVersion(ctx context.Context, versionID, featureID uint) error {
    var version model.Version
    var feature model.Feature
    if err := r.db.First(&version, versionID).Error; err != nil {
        return err
    }
    if err := r.db.First(&feature, featureID).Error; err != nil {
        return err
    }
    return r.db.Model(&version).Association("Features").Append(&feature)
}
```
---

## 4. Service Changes ([version_service.go](vscode-file://vscode-app/opt/visual-studio-code/resources/app/out/vs/code/electron-browser/workbench/workbench.html))

### Updated `CreateFeature`:

- **Before**: Created feature with [VersionID](vscode-file://vscode-app/opt/visual-studio-code/resources/app/out/vs/code/electron-browser/workbench/workbench.html)
- **After**: Creates feature independently, then associates it with version

### Before:


```go
feature := &model.Feature{
    VersionID:   versionID,
    FeatureName: featureName,
    Enabled:     enabled,
}
```

### After:

```go
feature := &model.Feature{
    FeatureName: featureName,
    Enabled:     enabled,
}
err = s.repo.AssociateFeatureWithVersion(ctx, versionID, feature.ID)
```
### Updated Feature Response Mappings:

- Removed [VersionID](vscode-file://vscode-app/opt/visual-studio-code/resources/app/out/vs/code/electron-browser/workbench/workbench.html) from `dto.FeatureResponse` since features can belong to multiple versions

---

## 5. DTO Changes ([generic.go](vscode-file://vscode-app/opt/visual-studio-code/resources/app/out/vs/code/electron-browser/workbench/workbench.html))

### Updated `FeatureResponse`:

```go
type FeatureResponse struct {
    ID          uint   `json:"ID"`
    // Removed: VersionID uint `json:"VersionID"`
    FeatureName string `json:"FeatureName"`
    Enabled     bool   `json:"Enabled"`
}
```

---

## 6. Seed Changes ([seed.go](vscode-file://vscode-app/opt/visual-studio-code/resources/app/out/vs/code/electron-browser/workbench/workbench.html))

### Updated [seedVersions](vscode-file://vscode-app/opt/visual-studio-code/resources/app/out/vs/code/electron-browser/workbench/workbench.html):

- **Before**: Created features with [VersionID](vscode-file://vscode-app/opt/visual-studio-code/resources/app/out/vs/code/electron-browser/workbench/workbench.html)
- **After**: Creates features separately, then associates them

### Before:

```go
features := []model.Feature{
    {VersionID: v2.ID, FeatureName: "remote-control", Enabled: true},
    // ...
}
```
### After:
```go
features := []model.Feature{
    {FeatureName: "remote-control", Enabled: true},
    // ...
}
// Then associate:
if err := db.Model(&v2).Association("Features").Append(existingFeatures); err != nil {
    return err
}
```
---

## Benefits of These Changes

1. **Data Efficiency**: No more feature duplication across versions
2. **Flexibility**: Features can be reused across multiple versions
3. **Maintainability**: Changes to a feature affect all versions that use it
4. **Scalability**: Better performance for queries involving features
5. **Data Integrity**: Proper referential integrity with junction table

## Database Schema Impact

- **New Table**: `version_features` with columns `version_id` and `feature_id` (composite primary key)
- **Modified Table**: [features](vscode-file://vscode-app/opt/visual-studio-code/resources/app/out/vs/code/electron-browser/workbench/workbench.html) - removed `version_id` column
- **Existing Tables**: [versions](vscode-file://vscode-app/opt/visual-studio-code/resources/app/out/vs/code/electron-browser/workbench/workbench.html) remains unchanged structurally

## API Behavior Changes

- `CreateNewDeviceVersion`: Now accepts existing feature IDs instead of duplicating features
- `CreateFeature`: Associates features with versions after creation
- Response formats: `FeatureResponse` no longer includes [versionID](vscode-file://vscode-app/opt/visual-studio-code/resources/app/out/vs/code/electron-browser/workbench/workbench.html) since it's many-to-many

The application now properly supports sharing features across multiple versions while maintaining data consistency and efficiency.