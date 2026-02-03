# Quick Start Guide

A concise guide to get you started with the Project Notes repository structure.

## 📋 For New Projects

### Step 1: Choose a Category

Place your project in the appropriate category:

| Category | Path | Use For |
|----------|------|---------|
| Electronics | `01 Electronics/` | Hardware, IoT, embedded systems |
| Web | `02 Web Based/` | Web apps, websites, APIs |
| Linux | `03 Linux/` | Linux tools, configurations |
| AI & ML | `04 AI & ML/` | Machine learning, AI projects |
| Robotics | `05 Robotics/` | Robotics projects |
| Others | `06 Others/` | Miscellaneous projects |
| Software | `07 Software/` | General software, CLI tools |

### Step 2: Create Project Directory

```bash
# Navigate to category
cd "01 Electronics"  # or your category

# Create project directory (use Title Case)
mkdir "Your Project Name"
cd "Your Project Name"
```

### Step 3: Use a Template

Copy the appropriate template:

```bash
# For Electronics
cp ../../templates/Electronics-Project-Template.md "Your Project Name.md"

# For Web
cp ../../templates/Web-Project-Template.md "Your Project Name.md"

# For AI/ML
cp ../../templates/AI-ML-Project-Template.md "Your Project Name.md"

# For Software
cp ../../templates/Software-Project-Template.md "Your Project Name.md"
```

### Step 4: Create Directory Structure

```bash
# Basic structure
mkdir -p attachments/images
mkdir -p attachments/diagrams
mkdir -p code  # if you have code

# For versions (optional)
mkdir -p versions
```

### Step 5: Update Frontmatter

Edit your project file and update the frontmatter:

```yaml
---
id: Your_Project_Name  # Use underscores, no spaces
tags:
  - project
  - electronics  # or website, ai, ml, linux, robotics, software
dg-publish: true
---
```

### Step 6: Start Documenting!

Fill in the sections as you work on your project:
1. Overview - What is it?
2. Objectives - What are you trying to achieve?
3. Technical Details - How does it work?
4. Implementation - How did you build it?
5. Add images, diagrams, code as you go

## 📝 Quick Reference

### Naming Conventions

✅ **Good**:
- Project directories: `Solar Battery Monitor`
- Files: `Solar Battery Monitor.md`
- Images: `circuit-diagram-v2.png`

❌ **Bad**:
- `solar_battery_monitor` (use spaces, not underscores)
- `SolarBatteryMonitor` (not CamelCase)
- `IMG_1234.png` (not descriptive)

### Required Tags

Always include these tags in frontmatter:

```yaml
tags:
  - project           # REQUIRED for all projects
  - [category]        # electronics, website, ai, ml, etc.
  - [custom tags]     # Optional: add more descriptive tags
```

### File Organization

```
Your Project/
├── Your Project.md           # Main documentation
├── attachments/
│   ├── images/              # Photos, screenshots
│   ├── diagrams/            # Excalidraw, draw.io files
│   └── docs/                # PDFs, datasheets, bills
├── code/                    # Source code (if applicable)
│   ├── firmware/            # For electronics
│   ├── frontend/            # For web
│   └── backend/             # For web
└── versions/                # Version history (optional)
```

## 🎯 Essential Sections

### Minimum Required Sections

Every project should have at least:

1. **Frontmatter** - Metadata (id, tags)
2. **Title** - `# Project Name`
3. **Overview** - Brief description
4. **Technical Details** - Components/tech stack
5. **Resources** - Links and references

### Recommended Sections

Add these for complete documentation:

6. **Objectives** - Goals and requirements
7. **Implementation** - How you built it
8. **Challenges & Solutions** - Problems faced
9. **Future Improvements** - Enhancement ideas
10. **Photos/Screenshots** - Visual documentation

## 🖼️ Adding Images

### In Obsidian

```markdown
![[image-name.png]]
![[diagram.excalidraw.md]]
```

### Standard Markdown

```markdown
![Description](attachments/images/image-name.png)
```

### Where to Store

- Photos/Screenshots → `attachments/images/`
- Diagrams → `attachments/diagrams/`
- Documents → `attachments/docs/`

## 🔗 Linking Projects

### Link to Other Projects

```markdown
See also: [[Related Project Name]]
Based on: [[Earlier Project]]
```

### Link to Resources

```markdown
[External Link](https://example.com)
[Datasheet](attachments/docs/datasheet.pdf)
```

## 📊 For Obsidian Users

### Install Recommended Plugins

1. **Dataview** - For dynamic queries
2. **Excalidraw** - For diagrams
3. **Tasks** - For task management

### Use Dataview Queries

List all projects in a category:

```dataview
TABLE file.name as "Project", file.mtime as "Last Modified"
FROM #project and #electronics
SORT file.mtime DESC
```

## 🚀 Quick Commands

```bash
# Check what category you're in
pwd

# List all projects in current category
ls -1

# Create basic structure
mkdir -p "New Project"/{attachments/{images,diagrams,docs},code,versions}

# Copy template
cp ../../templates/Electronics-Project-Template.md "New Project/New Project.md"

# Open in Obsidian (if installed)
open "New Project/New Project.md"
```

## 💡 Tips

1. **Start Simple**: Don't try to fill everything at once
2. **Document as You Go**: Update while building
3. **Use Checklists**: Track your progress
4. **Add Visuals**: Pictures are worth 1000 words
5. **Link Related Content**: Connect related projects
6. **Update Regularly**: Keep documentation current

## 📚 Common Patterns

### For Electronics Projects

**Must include**:
- Block diagram
- Component list
- Circuit explanation
- Code (if applicable)

### For Web Projects

**Must include**:
- Tech stack
- Setup instructions
- Screenshots
- Deployment info

### For AI/ML Projects

**Must include**:
- Dataset description
- Model architecture
- Training results
- Evaluation metrics

### For Software Projects

**Must include**:
- Installation instructions
- Usage examples
- Configuration options
- API docs (if library)

## 🆘 Common Issues

### Issue: Don't know where to start

**Solution**: 
1. Choose closest template
2. Fill in Overview first
3. Add sections as needed

### Issue: Template has too many sections

**Solution**: 
- Remove sections you don't need
- Mark others as "TBD" to fill later

### Issue: Project doesn't fit any category

**Solution**: 
- Put in `06 Others/` or closest match
- Tag appropriately for search

## 📖 Full Documentation

For detailed information, see:

- **[README.md](README.md)** - Repository overview
- **[STRUCTURE.md](STRUCTURE.md)** - Detailed structure guidelines
- **[CONTRIBUTING.md](CONTRIBUTING.md)** - How to contribute
- **[templates/](templates/)** - Project templates

## 🎬 Example Workflow

Here's a complete example of starting a new electronics project:

```bash
# 1. Navigate to category
cd "01 Electronics"

# 2. Create project
mkdir "LED Cube"
cd "LED Cube"

# 3. Create structure
mkdir -p attachments/{images,diagrams} code

# 4. Copy template
cp ../../templates/Electronics-Project-Template.md "LED Cube.md"

# 5. Edit the file
# - Update frontmatter (id: LED_Cube, tags: project, electronics)
# - Fill in Overview: "An 8x8x8 LED cube display"
# - List Objectives
# - Add component list as you go
# - Document build process
# - Add photos to attachments/images/
# - Add circuit diagrams to attachments/diagrams/

# 6. As you code, add files to code/
# code/led-cube.ino

# 7. Update documentation regularly
```

## ✅ Checklist for New Project

Use this checklist when starting a new project:

- [ ] Choose appropriate category
- [ ] Create project directory (Title Case)
- [ ] Create subdirectories (attachments, code, etc.)
- [ ] Copy appropriate template
- [ ] Update frontmatter (id, tags)
- [ ] Write overview section
- [ ] List objectives/goals
- [ ] Document as you build
- [ ] Add images and diagrams
- [ ] Include code/snippets
- [ ] Document challenges faced
- [ ] List resources used
- [ ] Link to related projects
- [ ] Update project index if needed

## 🤝 Need Help?

- Check existing projects for examples
- Read [STRUCTURE.md](STRUCTURE.md) for details
- Look at templates in `templates/` directory
- Open an issue if you have questions

---

**Remember**: The goal is documentation that helps you and others understand the project. Don't stress about perfection - start simple and improve over time!

*Last Updated: 2025-10-23*
