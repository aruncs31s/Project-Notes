# Project Structure Guidelines

This document provides detailed guidelines for organizing and documenting projects in this repository.

## Table of Contents

1. [Naming Conventions](#naming-conventions)
2. [Directory Structure](#directory-structure)
3. [Project Types](#project-types)
4. [File Organization](#file-organization)
5. [Documentation Standards](#documentation-standards)
6. [Asset Management](#asset-management)
7. [Version Control](#version-control)

## Naming Conventions

### Project Names

- **Format**: Use Title Case (first letter of each word capitalized)
- **Examples**: 
  - ✅ `Kannur Solar Monitor`
  - ✅ `GCEK Weather Station`
  - ✅ `AI Robot`
  - ❌ `kannur-solar-monitor`
  - ❌ `GCEK_weather_station`

### Directory Names

- Use spaces in directory names for readability
- Match the project name exactly
- Example: `01 Electronics/Kannur Solar Battery Monitor/`

### File Names

- **Main documentation files**: Title Case with spaces
  - Example: `Kannur Solar Battery Monitor.md`
- **Supporting files**: Descriptive, lowercase with hyphens or spaces
  - Example: `version-control.md` or `Version Control.md`
- **Code files**: Follow language-specific conventions
  - Example: `solar_monitor.ino`, `app.py`, `main.js`

## Directory Structure

### Root Level Categories

```
Project-Notes/
├── 01 Electronics/     # Hardware, embedded systems, IoT
├── 02 Web Based/      # Web applications, websites
├── 03 Linux/          # Linux tools, configurations
├── 04 AI & ML/        # AI/ML projects
├── 05 Robotics/       # Robotics projects
├── 06 Others/         # Miscellaneous projects
├── 07 Software/       # General software projects
└── Common/            # Shared resources
```

### Individual Project Structure

Each project should follow this structure:

```
Project Name/
├── README.md or [Project Name].md     # Main project documentation
├── Documents.md                       # Additional documentation index
├── attachments/                       # Images, PDFs, reference docs
│   ├── images/                       # Screenshots, photos
│   ├── diagrams/                     # Excalidraw, drawio files
│   └── docs/                         # PDFs, receipts, bills
├── code/                             # Source code (if applicable)
│   ├── microcontroller/              # MCU code
│   ├── backend/                      # Backend code
│   ├── frontend/                     # Frontend code
│   └── scripts/                      # Utility scripts
├── schematics/                       # Circuit diagrams (electronics)
├── versions/                         # Version history
│   ├── Version 1.md
│   ├── Version 2.md
│   └── ...
└── research/                         # R&D notes, experiments
```

## Project Types

### 1. Electronics Projects

**Required Components:**
- Block diagram of the system
- Schematic diagram (if applicable)
- Component list with specifications
- Circuit explanation
- Code files (if microcontroller is used)
- Testing and calibration notes

**Example Structure:**
```
Solar Battery Monitor/
├── Solar Battery Monitor.md           # Main documentation
├── attachments/
│   ├── circuit-diagram.png
│   ├── block-diagram.excalidraw.md
│   └── pcb-layout.png
├── code/
│   ├── esp32-firmware.ino
│   └── libraries/
├── schematics/
│   └── battery-monitor-v2.kicad_pro
├── component-list.md
└── calibration-notes.md
```

**Documentation Template:**

````markdown
---
id: Project_Name
tags:
  - project
  - electronics
dg-publish: true
---

# Project Name

## Overview
Brief description of the project and its purpose.

## Objectives
- What problem does it solve?
- Key goals and requirements

## System Architecture

### Block Diagram
![[block-diagram.excalidraw.md]]

### Components
1. **Microcontroller**: ESP32
2. **Sensors**: ACS712 current sensor
3. **Display**: OLED 128x64
4. ...

## Circuit Design

### Schematic
![[circuit-diagram.png]]

### Circuit Explanation
Detailed explanation of how the circuit works...

## Implementation

### Hardware Setup
Step-by-step assembly instructions...

### Software/Firmware
```cpp
// Key code snippets with explanations
```

## Testing
Test results, calibration data...

## Challenges & Solutions
Problems encountered and their solutions...

## Future Improvements
- Enhancement idea 1
- Enhancement idea 2

## Resources
- [Datasheet Link]
- [Reference Project]
````

### 2. Web-Based Projects

**Required Components:**
- Project overview and goals
- Technology stack
- Architecture diagram
- API documentation (if applicable)
- Setup and deployment instructions
- Screenshots or demo links

**Example Structure:**
```
Weather Dashboard/
├── Weather Dashboard.md               # Main documentation
├── attachments/
│   ├── screenshots/
│   │   ├── home-page.png
│   │   └── dashboard-view.png
│   └── architecture-diagram.excalidraw.md
├── code/
│   ├── frontend/
│   │   ├── src/
│   │   └── package.json
│   └── backend/
│       ├── api/
│       └── requirements.txt
├── api-documentation.md
└── deployment-notes.md
```

**Documentation Template:**

````markdown
---
id: Project_Name
tags:
  - project
  - website
dg-publish: true
---

# Project Name

## Overview
Brief description of the web application.

## Tech Stack
- **Frontend**: React, TailwindCSS
- **Backend**: Node.js, Express
- **Database**: PostgreSQL
- **Deployment**: Vercel, Railway

## Features
- Feature 1
- Feature 2
- Feature 3

## Architecture
![[architecture-diagram.excalidraw.md]]

## Setup Instructions

### Prerequisites
- Node.js v18+
- PostgreSQL 14+

### Installation
```bash
# Clone repository
git clone [repo-url]

# Install dependencies
npm install

# Configure environment
cp .env.example .env

# Run development server
npm run dev
```

## API Documentation
Key endpoints and their usage...

## Deployment
Deployment steps and considerations...

## Screenshots
![[home-page.png]]
![[dashboard-view.png]]

## Challenges & Solutions
...

## Future Enhancements
...
````

### 3. AI & ML Projects

**Required Components:**
- Problem statement
- Dataset description
- Model architecture
- Training process and results
- Evaluation metrics
- Usage instructions

**Example Structure:**
```
Weather Prediction/
├── Weather Prediction.md
├── data/
│   ├── raw/
│   ├── processed/
│   └── data-description.md
├── models/
│   ├── trained-model.h5
│   └── model-architecture.md
├── notebooks/
│   ├── data-exploration.ipynb
│   ├── training.ipynb
│   └── evaluation.ipynb
├── code/
│   ├── preprocessing.py
│   ├── train.py
│   └── predict.py
└── results/
    ├── metrics.md
    └── visualizations/
```

### 4. Linux/Software Projects

**Required Components:**
- Purpose and functionality
- Dependencies and requirements
- Installation instructions
- Configuration examples
- Usage guide

## File Organization

### Main Documentation File

Each project must have a primary documentation file:
- Named after the project or simply `README.md`
- Contains frontmatter with metadata:
  ```yaml
  ---
  id: Unique_Project_ID
  tags:
    - project
    - [category]
  dg-publish: true
  ---
  ```

### Attachments Directory

Use the `attachments/` directory for:
- **images/**: Photos, screenshots, renders
- **diagrams/**: Excalidraw files, draw.io diagrams
- **docs/**: PDFs, receipts, datasheets, reference materials

Best practices:
- Use descriptive filenames
- Organize in subdirectories by type
- Keep file sizes reasonable (compress images if needed)

### Code Directory

If your project includes code:
- Keep it in a `code/` subdirectory
- Use appropriate subdirectories (`frontend/`, `backend/`, `firmware/`)
- Include README files in code directories
- Add comments and documentation

### Versions Directory

For projects with multiple iterations:
```
versions/
├── Version 1.md              # Initial version
├── Version 2.md              # Second iteration
├── Version 2 with MQTT.md    # Variants
└── Version 3.excalidraw.md   # Latest design
```

## Documentation Standards

### Required Sections

Every project documentation should include:

1. **Frontmatter** (metadata)
2. **Title** (# Project Name)
3. **Overview** (brief description)
4. **Objectives** (goals and requirements)
5. **Technical Details** (architecture, components, tech stack)
6. **Implementation** (how it was built)
7. **Testing/Results** (validation and results)
8. **Challenges & Solutions** (problems and fixes)
9. **Future Improvements** (enhancement ideas)
10. **Resources** (links, references, related materials)

### Writing Style

- Use clear, concise language
- Include diagrams and visuals
- Add code snippets with explanations
- Link to related projects and resources
- Update documentation as the project evolves

### Markdown Best Practices

- Use headings hierarchically (# → ## → ###)
- Include table of contents for long documents
- Use code blocks with language specification
- Add alt text to images
- Use lists for better readability
- Include links with descriptive text

## Asset Management

### Images

- **Screenshots**: PNG format, reasonable resolution
- **Photos**: JPEG format, compressed appropriately
- **Diagrams**: Excalidraw (.excalidraw.md) or SVG format
- Store in `attachments/images/` or `attachments/diagrams/`

### Diagrams

Preferred tools:
- **Excalidraw**: For sketches and block diagrams
- **Draw.io**: For flowcharts and network diagrams
- **Mermaid**: For inline diagrams in Markdown

### Documents

- **Datasheets**: Store in `attachments/docs/`
- **Bills/Receipts**: Keep for project cost tracking
- **Reference materials**: PDFs, links to external resources

## Version Control

### Git Best Practices

1. **Commit messages**: Clear and descriptive
   - ✅ "Add circuit diagram for battery monitor v2"
   - ❌ "Update"

2. **File tracking**: Avoid committing:
   - Binary files (when possible)
   - Generated files
   - Temporary files
   - Large datasets

3. **Branching**: Use branches for major changes
   - `main` or `master`: Stable, working version
   - `feature/*`: New features or projects
   - `fix/*`: Bug fixes and corrections

### Project Versions

Document versions clearly:
- Use semantic versioning when applicable (v1.0.0)
- Maintain a changelog for significant changes
- Archive old versions in `versions/` directory
- Link between versions in documentation

### Version Documentation Template

```markdown
# Version X

## Date
YYYY-MM-DD

## Changes
- Change 1
- Change 2
- Change 3

## Improvements
- Improvement 1
- Improvement 2

## Issues Fixed
- Issue 1
- Issue 2

## Known Issues
- Issue A
- Issue B

## Notes
Additional notes about this version...
```

## Templates

### Quick Start Template

For quickly starting a new project:

```markdown
---
id: Project_Name
tags:
  - project
  - [category]
dg-publish: true
---

# Project Name

## Status
🚧 In Progress / ✅ Completed / 🔄 On Hold

## Overview
[Brief description]

## Objectives
- [ ] Objective 1
- [ ] Objective 2

## Technical Details
**Tech Stack/Components:**
- Item 1
- Item 2

## Progress Log

### [Date]
- Work done today

## Resources
- [Link 1]
- [Link 2]
```

## Dataview Integration

For Obsidian users, leverage dataview queries:

### List All Projects in a Category

```dataview
TABLE file.name as "Project", file.mtime as "Last Modified"
FROM #project and #electronics
SORT file.mtime DESC
```

### Track Project Status

```dataview
TASK
FROM #project
WHERE status = "in-progress"
```

### Link Related Projects

```dataview
LIST
FROM [[Current Project Name]]
```

## Summary

Following this structure ensures:
- ✅ Consistency across all projects
- ✅ Easy navigation and discovery
- ✅ Comprehensive documentation
- ✅ Better collaboration
- ✅ Professional presentation
- ✅ Easier maintenance and updates

For questions or suggestions about this structure, please open an issue in the repository.

---

*Last Updated: 2025-10-23*
