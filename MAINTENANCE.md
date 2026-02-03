# Repository Maintenance Guide

This guide covers routine maintenance tasks for keeping the Project-Notes repository clean and organized.

## 🧹 Cleanup Tasks

### Remove Tracked System Files

The `.gitignore` file now prevents system files like `.DS_Store` from being tracked in new commits. However, some system files were already committed before the `.gitignore` was added.

#### Option 1: Remove .DS_Store Files (Recommended)

To remove all `.DS_Store` files from git tracking:

```bash
# Find all .DS_Store files
find . -name ".DS_Store" -type f

# Remove them from git tracking (keeps local files)
find . -name ".DS_Store" -type f -exec git rm --cached {} \;

# Commit the removal
git commit -m "Remove .DS_Store files from tracking"

# Push changes
git push
```

After this, future `.DS_Store` files won't be tracked thanks to `.gitignore`.

#### Option 2: Keep Existing Files

If you prefer to keep existing `.DS_Store` files tracked (not recommended), the `.gitignore` will still prevent new ones from being added.

### Verify .gitignore is Working

Test that new system files are properly ignored:

```bash
# Create a test .DS_Store
touch test/.DS_Store

# Check git status - it should NOT show the file
git status

# Clean up test
rm -rf test/
```

## 📋 Regular Maintenance Checklist

### Monthly Tasks

- [ ] Review and update project statuses
- [ ] Check for broken links in documentation
- [ ] Update index files (Projects.md, Electronics Projects.md, etc.)
- [ ] Archive completed projects if needed
- [ ] Update README.md if structure changes

### Quarterly Tasks

- [ ] Review and update templates based on feedback
- [ ] Check all projects have proper tags
- [ ] Ensure consistent naming across projects
- [ ] Update documentation based on lessons learned
- [ ] Review and improve STRUCTURE.md guidelines

### As Needed

- [ ] Add new project categories if needed
- [ ] Update CONTRIBUTING.md with new guidelines
- [ ] Create new templates for emerging project types
- [ ] Add examples for complex scenarios

## 🏷️ Tag Consistency

### Check Tag Usage

Ensure all projects have proper tags:

```bash
# For Obsidian users, use dataview to find untagged projects:
# Projects without the #project tag won't show up in indexes
```

### Standard Tags

Required tags:
- `#project` - All projects must have this

Category tags (choose one or more):
- `#electronics` - Hardware/embedded systems
- `#website` - Web applications
- `#ai` - Artificial intelligence
- `#ml` - Machine learning
- `#linux` - Linux configurations
- `#robotics` - Robotics projects
- `#software` - General software

## 📁 File Organization

### Check for Misplaced Files

```bash
# Find markdown files not in proper project directories
find . -maxdepth 2 -name "*.md" -type f

# Find images not in attachments directories
find . -name "*.png" -o -name "*.jpg" | grep -v "attachments"
```

### Standardize Directory Names

Ensure all projects use Title Case:
```bash
# Good examples:
01 Electronics/Solar Battery Monitor/
02 Web Based/Weather Dashboard/

# Fix if needed:
# Instead of: 01 Electronics/solar-battery-monitor/
# Use: 01 Electronics/Solar Battery Monitor/
```

## 🔗 Link Maintenance

### Check for Broken Links

#### In Obsidian
- Use the "Unresolved links" panel
- Check for broken internal links
- Verify image links work

#### Using Command Line
```bash
# Find markdown files with links
grep -r "\[\[" --include="*.md" .

# Find image references
grep -r "!\[\[" --include="*.md" .
```

### Update Links After Renaming

If you rename a project:
1. Update all internal links to that project
2. Update references in index files
3. Update related project links

## 📊 Statistics and Reports

### Count Projects by Category

```bash
# Count projects in each category
echo "Electronics: $(ls -1 '01 Electronics' | wc -l)"
echo "Web: $(ls -1 '02 Web Based' | wc -l)"
echo "Linux: $(ls -1 '03 Linux' | wc -l)"
echo "AI & ML: $(ls -1 '04 AI & ML' | wc -l)"
echo "Robotics: $(ls -1 '05 Robotics' | wc -l)"
echo "Others: $(ls -1 '06 Others' | wc -l)"
echo "Software: $(ls -1 '07 Software' | wc -l)"
```

### Repository Size

```bash
# Check total repository size
du -sh .

# Check size by category
du -sh 0*/

# Find largest files
find . -type f -exec du -h {} \; | sort -rh | head -20
```

## 🗄️ Archive Old Projects

### When to Archive

Consider archiving projects that are:
- Completed and no longer being updated
- Deprecated or superseded by newer versions
- Experimental and abandoned

### How to Archive

Create an archive directory:
```bash
mkdir -p "06 Others/Archive"
mv "01 Electronics/Old Project" "06 Others/Archive/"
```

Update the project's status to indicate it's archived:
```yaml
## Status
📦 Archived - See [[New Project Name]] for current version
```

## 🔄 Version Control

### Clean Up Branches

```bash
# List all branches
git branch -a

# Delete merged branches (locally)
git branch -d branch-name

# Clean up remote tracking branches
git remote prune origin
```

### Optimize Repository

```bash
# Run git garbage collection
git gc --aggressive --prune=now

# Check repository size
git count-objects -vH
```

## 📝 Documentation Updates

### Keep Documentation Current

When making changes:
1. Update README.md if structure changes
2. Update STRUCTURE.md if guidelines change
3. Update templates if standards change
4. Update QUICK-START.md if process changes
5. Update this MAINTENANCE.md as needed

### Documentation Review Checklist

- [ ] All links work
- [ ] Screenshots are current
- [ ] Code examples work
- [ ] Instructions are clear
- [ ] Examples are relevant

## 🔍 Quality Checks

### Project Quality Checklist

For each project, verify:
- [ ] Has proper frontmatter with id and tags
- [ ] Title matches directory name
- [ ] Has overview section
- [ ] Includes technical details
- [ ] Has at least one visual (diagram/photo)
- [ ] Code is documented (if applicable)
- [ ] Has resources/links section
- [ ] Files are organized in correct directories

### Use This Script

```bash
#!/bin/bash
# check-project-quality.sh
# Checks if a project has minimum required elements

PROJECT_DIR="$1"
if [ -z "$PROJECT_DIR" ]; then
    echo "Usage: $0 <project-directory>"
    exit 1
fi

# Check for main markdown file
MAIN_MD=$(find "$PROJECT_DIR" -maxdepth 1 -name "*.md" | head -1)
if [ -z "$MAIN_MD" ]; then
    echo "❌ No main markdown file found"
    exit 1
fi

# Check for frontmatter
if grep -q "^---" "$MAIN_MD"; then
    echo "✅ Has frontmatter"
else
    echo "❌ Missing frontmatter"
fi

# Check for tags
if grep -q "tags:" "$MAIN_MD"; then
    echo "✅ Has tags"
else
    echo "❌ Missing tags"
fi

# Check for images
if find "$PROJECT_DIR" -name "*.png" -o -name "*.jpg" | grep -q .; then
    echo "✅ Has images"
else
    echo "⚠️  No images found"
fi
```

## 🚀 Performance

### Optimize Large Files

If repository becomes large:

1. **Compress images**:
```bash
# Find large images
find . -name "*.jpg" -size +1M -o -name "*.png" -size +1M

# Compress using imagemagick (if installed)
mogrify -quality 85 -resize '1920>' *.jpg
```

2. **Use Git LFS for large files**:
```bash
git lfs track "*.jpg"
git lfs track "*.png"
git lfs track "*.pdf"
```

3. **Remove large files from history**:
```bash
# Only if absolutely necessary - rewrites history!
git filter-branch --tree-filter 'rm -f path/to/large/file' HEAD
```

## 📖 Template Maintenance

### Update Templates

When updating templates:

1. Edit the template file in `templates/`
2. Document the change in template's README
3. Consider updating existing projects
4. Announce changes to contributors

### Template Change Log

Keep track of template updates:

```markdown
## Template Updates

### 2025-10-23
- Added comprehensive templates for all project types
- Created Electronics, Web, AI/ML, and Software templates
- Added template usage guide

### [Future Date]
- Note changes here
```

## 🛠️ Tools and Scripts

### Useful Maintenance Scripts

Create these in a `scripts/` directory:

1. **check-links.sh** - Verify all links work
2. **update-indexes.sh** - Regenerate index files
3. **find-duplicates.sh** - Find duplicate content
4. **standardize-names.sh** - Check naming conventions

## 📞 Getting Help

If you encounter issues:

1. Check this MAINTENANCE.md guide
2. Review [STRUCTURE.md](STRUCTURE.md) for guidelines
3. Search existing issues on GitHub
4. Open a new issue if needed

## 🎯 Best Practices

1. **Commit Often**: Small, focused commits are better
2. **Write Clear Messages**: Explain what and why
3. **Review Before Push**: Check what you're committing
4. **Keep It Clean**: Remove temporary files
5. **Update Documentation**: Keep docs current
6. **Test Links**: Verify links work before committing
7. **Use Templates**: Maintain consistency

## 📅 Maintenance Schedule

### Weekly
- Quick review of new commits
- Check for obvious issues

### Monthly
- Run quality checks
- Update project statuses
- Review and fix broken links

### Quarterly
- Deep review of structure
- Update templates if needed
- Clean up archives
- Optimize repository

### Annually
- Major structure review
- Template overhaul if needed
- Archive completed projects
- Update all documentation

---

*Last Updated: 2025-10-23*

For questions about maintenance, open an issue or contact the maintainers.
