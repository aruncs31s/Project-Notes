# Project Templates

This directory contains templates for different types of projects. Use these templates as a starting point for documenting new projects in the repository.

## Available Templates

### 1. Electronics Project Template
**File**: `Electronics-Project-Template.md`

Use this template for:
- Hardware projects
- Embedded systems
- IoT devices
- Microcontroller-based projects
- Circuit design projects

**Includes**:
- System architecture and block diagrams
- Component lists and specifications
- Circuit schematics
- Code/firmware sections
- Testing and calibration notes
- Bill of Materials (BOM)

### 2. Web Project Template
**File**: `Web-Project-Template.md`

Use this template for:
- Web applications
- Websites
- Full-stack projects
- Frontend/Backend projects
- API services

**Includes**:
- Tech stack details
- Architecture diagrams
- API documentation
- Setup instructions
- Deployment guides
- Screenshots and demos

### 3. AI & ML Project Template
**File**: `AI-ML-Project-Template.md`

Use this template for:
- Machine Learning projects
- Deep Learning models
- Data science projects
- Predictive models
- Computer vision / NLP projects

**Includes**:
- Dataset description
- EDA and preprocessing
- Model architecture
- Training process
- Evaluation metrics
- Deployment instructions

### 4. Software Project Template
**File**: `Software-Project-Template.md`

Use this template for:
- Command-line tools
- Desktop applications
- Libraries/packages
- Utilities and scripts
- General software projects

**Includes**:
- Installation instructions
- Usage examples
- API documentation
- Development setup
- Testing and CI/CD
- Deployment guides

## How to Use Templates

### Method 1: Copy Template File

1. Choose the appropriate template
2. Copy it to your project directory:
   ```bash
   cp templates/Electronics-Project-Template.md "01 Electronics/Your Project Name/Your Project Name.md"
   ```
3. Edit the copied file and fill in your project details

### Method 2: Use Template Content

1. Create a new file in your project directory
2. Copy the content from the appropriate template
3. Fill in the sections with your project information

### Method 3: Use as Reference

- Review the template structure
- Create your own file following the same sections
- Include the sections most relevant to your project

## Customizing Templates

### Required Sections

Every project should include:
- **Frontmatter** (YAML metadata at the top)
- **Overview** (project description)
- **Objectives** (goals and requirements)
- **Technical Details** (how it works)
- **Implementation** (how it was built)
- **Challenges & Solutions** (problems and fixes)
- **Resources** (references and links)

### Optional Sections

You can add, remove, or modify sections based on your project needs:
- Remove sections that don't apply
- Add custom sections specific to your project
- Reorganize sections for better flow
- Combine related sections

## Template Guidelines

### Frontmatter

Always include proper frontmatter:

```yaml
---
id: Unique_Project_ID
tags:
  - project
  - category_tag
dg-publish: true
---
```

**Required fields**:
- `id`: Unique identifier (use underscores, no spaces)
- `tags`: At least `project` and one category tag
- `dg-publish`: Set to `true` for Obsidian publish

**Category tags**:
- `#electronics` - Electronics projects
- `#website` - Web projects
- `#ai` - AI projects
- `#ml` - Machine Learning projects
- `#linux` - Linux projects
- `#robotics` - Robotics projects
- `#software` - Software projects

### File Naming

- Use Title Case for project name
- Example: `Solar Battery Monitor.md`
- Match the project directory name

### Placeholder Text

Templates use placeholders you should replace:
- `Project Name` → Your actual project name
- `YYYY-MM-DD` → Actual dates
- `[Description]` → Your descriptions
- `Your Name` → Your actual name
- `[link]` → Actual links

### Images and Diagrams

Templates reference images with Obsidian syntax:
```markdown
![[image-name.png]]
![[diagram.excalidraw.md]]
```

Store images in:
```
Your Project/
├── attachments/
│   ├── images/
│   └── diagrams/
```

### Code Blocks

Always specify the language:
````markdown
```python
# Python code
```

```javascript
// JavaScript code
```

```cpp
// C++ code
```
````

## Examples

### Starting an Electronics Project

```bash
# 1. Create project directory
mkdir "01 Electronics/Weather Station"
cd "01 Electronics/Weather Station"

# 2. Copy template
cp ../../templates/Electronics-Project-Template.md "Weather Station.md"

# 3. Create directory structure
mkdir -p attachments/images attachments/diagrams code

# 4. Edit the file and start documenting
```

### Starting a Web Project

```bash
# 1. Create project directory
mkdir "02 Web Based/Todo App"
cd "02 Web Based/Todo App"

# 2. Copy template
cp ../../templates/Web-Project-Template.md "Todo App.md"

# 3. Create directories
mkdir -p attachments/screenshots code

# 4. Start documenting
```

## Template Maintenance

### Updating Templates

If you find improvements to the templates:
1. Update the template file
2. Document the change
3. Consider if existing projects need updates

### Suggesting Changes

To suggest template improvements:
1. Create an issue describing the suggestion
2. Explain the benefit
3. Provide examples if possible

## Tips for Using Templates

1. **Don't Feel Constrained**: Templates are guidelines, not rules
2. **Start Simple**: Fill in basic sections first, expand later
3. **Use Checklists**: Track what sections you've completed
4. **Link Related Content**: Reference other projects and resources
5. **Update Regularly**: Keep documentation current as project evolves
6. **Include Visuals**: Add diagrams, screenshots, photos
7. **Document Problems**: Include challenges and solutions
8. **Think About Others**: Write as if explaining to someone else

## Quick Start Checklist

When starting a new project:

- [ ] Choose appropriate template
- [ ] Create project directory in correct category
- [ ] Copy and rename template file
- [ ] Update frontmatter (id, tags)
- [ ] Fill in overview and objectives
- [ ] Create subdirectories (attachments, code, etc.)
- [ ] Start documenting as you build
- [ ] Add images and diagrams
- [ ] Update regularly
- [ ] Link to related projects

## Additional Resources

- See [STRUCTURE.md](../STRUCTURE.md) for detailed structure guidelines
- See [CONTRIBUTING.md](../CONTRIBUTING.md) for contribution guidelines
- See [README.md](../README.md) for repository overview

## Questions?

If you have questions about using templates:
1. Check the main [STRUCTURE.md](../STRUCTURE.md) document
2. Look at existing projects for examples
3. Open an issue for clarification

---

*Last Updated: 2025-10-23*
