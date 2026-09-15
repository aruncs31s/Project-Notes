# GCEK LMS Style Documentation

## Overview

This document outlines the visual design system, color palette, typography, spacing, and component styling patterns used throughout the GCEK LMS application.

## Tech Stack

- **Framework**: React 19.2.0 with TypeScript
- **Build Tool**: Vite 7.3.1
- **Icons**: `@heroicons/react` (outline & solid variants), `react-icons` (feather-icons)
- **Fonts**: Inter (via Google Fonts)

---

## Color Palette

### Dark Theme (Default)

| Token | Value | Usage |
|-------|-------|-------|
| `--bg-primary` | `#000000` | Base background |
| `--bg-secondary` | `#0a0a0a` | Card backgrounds |
| `--bg-tertiary` | `#141414` | Elevated elements |
| `--bg-separator` | `#262626` | Borders/separators |
| `--text-primary` | `#ffffff` | Primary text |
| `--text-secondary` | `#a3a3a3` | Subtext |
| `--text-muted` | `#737373` | Muted text |
| `--brand-primary` | `#fc9a00` | Primary brand (yellow) |
| `--brand-primary-hover` | `#eab308` | Hover state |
| `--brand-secondary` | `#fcd34d` | Lighter brand |
| `--brand-glow` | `rgba(252, 154, 0, 0.3)` | Glow effects |
| `--brand-glow-subtle` | `rgba(252, 154, 0, 0.15)` | Subtle glow |
| `--brand-glow-subtler` | `rgba(252, 154, 0, 0.08)` | Barely visible glow |
| `--danger` | `#ef4444` | Error states |
| `--success` | `#22c55e` | Success states |
| `--warning` | `#f59e0b` | Warning states |
| `--border-color` | `#262626` | Default border |

### Light Theme

| Token | Value | Usage |
|-------|-------|-------|
| `--bg-primary` | `#ffffff` | Base background |
| `--bg-secondary` | `#f8fafc` | Card backgrounds |
| `--bg-tertiary` | `#f1f5f9` | Elevated elements |
| `--text-primary` | `#0f172a` | Primary text |
| `--text-secondary` | `#475569` | Subtext |
| `--text-muted` | `#64748b` | Muted text |
| `--brand-primary` | `#eab308` | Primary brand |
| `--brand-secondary` | `#facc15` | Lighter brand |
| `--border-color` | `#e2e8f0` | Default border |

---

## Typography

### Font Family

```css
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap');

--font-family: 'Inter', sans-serif;
```

### Font Sizes

| Token | Value | Approx. | Usage |
|-------|-------|---------|-------|
| `--text-xs` | 0.65rem | 10px | Labels (FREE PREVIEW, CURRENT) |
| `--text-sm` | 0.75rem | 12px | Badges |
| `--text-base` | 0.875rem | 14px | Form inputs, metadata |
| `--text-body` | 0.95rem | 15px | Body text, descriptions |
| `--text-default` | 1rem | 16px | Default text |
| `--text-lg` | 1.25rem | 20px | Card titles |
| `--text-xl` | 1.5rem | 24px | Section headings |
| `--text-2xl` | 1.75rem | 28px | Mobile headings |
| `--text-3xl` | 2rem | 32px | Hero text (mobile) |
| `--text-4xl` | 2.5rem | 40px | Tablet hero text |
| `--text-5xl` | 4.5rem | 72px | Desktop hero heading |

### Font Weights

| Weight | Value | Usage |
|--------|-------|-------|
| Light | 300 | Rarely used |
| Regular | 400 | Default body text |
| Medium | 500 | Secondary text, buttons |
| Semi-bold | 600 | Navigation, badges |
| Bold | 700 | Headings, emphasis |

---

## Spacing

Base unit: `4px` (0.25rem)

| Token | Value | Usage |
|-------|-------|-------|
| `space-1` | 0.25rem | 4px | Minimal gaps |
| `space-2` | 0.5rem | 8px | Icon buttons, badges |
| `space-3` | 0.75rem | 12px | Small padding |
| `space-4` | 1rem | 16px | Base unit |
| `space-6` | 1.5rem | 24px | Card padding |
| `space-8` | 2rem | 32px | Container padding |
| `space-10` | 2.5rem | 40px | Section padding |
| `space-12` | 3rem | 48px | Large gaps |
| `space-16` | 4rem | 64px | Hero margins |

---

## Border Radius

| Token | Value | Usage |
|-------|-------|-------|
| `--border-radius-sm` | 4px | Minimal rounding |
| `--border-radius-md` | 8px | Inputs, buttons |
| `--border-radius-lg` | 12px | Cards, panels |
| `--border-radius-xl` | 16px | Large cards |
| `--border-radius-2xl` | 24px | Hero sections |
| `--border-radius-full` | 999px | Pills, avatars |

---

## Components

### Buttons

**Primary Button**
```css
.btn-primary {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  padding: 0.75rem 1.5rem;
  border-radius: 8px;
  background: linear-gradient(135deg, var(--brand-primary), var(--brand-secondary));
  color: #4a4141;
  font-weight: 600;
  box-shadow: 0 4px 14px 0 var(--brand-glow);
  transition: all 0.2s ease-in-out;
}

.btn-primary:hover {
  transform: translateY(-2px);
  color: #ffffff;
}
```

**Secondary Button**
```css
.btn-secondary {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  padding: 0.75rem 1.5rem;
  border-radius: 8px;
  background: transparent;
  color: var(--text-primary);
  border: 1px solid var(--border-color);
  font-weight: 500;
  transition: all 0.2s ease-in-out;
}

.btn-secondary:hover {
  background: var(--bg-tertiary);
}
```

**Button Sizes**
- Small: `0.6rem 1rem`
- Default: `0.75rem 1.5rem`
- Large: `1rem 2.5rem` (hero CTAs)

---

### Glass Panel

```css
.glass-panel {
  background: var(--bg-secondary);
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
  border: 1px solid var(--border-color);
  box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1),
              0 2px 4px -1px rgba(0, 0, 0, 0.06);
  border-radius: 12px;
}
```

---

### Form Inputs

```css
.input-field,
.form-input {
  width: 100%;
  padding: 0.75rem 1rem;
  border-radius: 8px;
  background-color: var(--bg-secondary);
  border: 1px solid var(--border-color);
  color: var(--text-primary);
  font-family: inherit;
  transition: all 0.2s;
}

.form-input {
  padding: 0.75rem 1.25rem;
  border-radius: 12px;
  background-color: var(--bg-tertiary);
  font-size: 0.95rem;
}

.input-field:focus,
.form-input:focus {
  outline: none;
  border-color: var(--brand-primary);
  box-shadow: 0 0 0 2px var(--brand-glow-subtle);
}
```

---

### Badges

```css
.badge {
  display: inline-flex;
  align-items: center;
  padding: 0.25rem 0.75rem;
  font-size: 0.75rem;
  font-weight: 600;
  border-radius: 999px;
  text-transform: uppercase;
  letter-spacing: 0.05em;
}

.badge-primary {
  background: var(--brand-glow-subtle);
  color: var(--brand-primary);
  border: 1px solid var(--brand-glow);
}

.badge-success {
  background: rgba(166, 227, 161, 0.15);
  color: var(--success);
  border: 1px solid rgba(166, 227, 161, 0.3);
}

.badge-blur {
  backdrop-filter: blur(8px);
  background: rgba(0, 0, 0, 0.5);
  color: #fff;
  border: 1px solid rgba(255, 255, 255, 0.2);
}
```

---

### Cards

**Course Card**
```css
.course-card {
  transition: all 0.3s cubic-bezier(0.25, 0.8, 0.25, 1);
  border: 1px solid var(--border-color);
  background: var(--bg-secondary);
  border-radius: 16px;
  overflow: hidden;
  display: flex;
  flex-direction: column;
}

.course-card:hover {
  transform: translateY(-8px);
  box-shadow: 0 20px 40px -10px rgba(0, 0, 0, 0.3),
              0 0 20px var(--brand-glow-subtle);
  border-color: var(--brand-primary);
}
```

**Stat Box**
```css
.stat-box {
  background: var(--bg-secondary);
  backdrop-filter: blur(10px);
  border: 1px solid var(--border-color);
  border-radius: 16px;
  padding: 1.5rem;
  transition: all 0.3s ease;
  text-align: center;
}

.stat-box:hover {
  transform: translateY(-5px);
  border-color: var(--brand-primary);
  box-shadow: 0 10px 30px -10px var(--brand-glow-subtle);
}
```

**Feature Card**
```css
.feature-card {
  background: var(--bg-secondary);
  border-radius: 16px;
  padding: 2rem;
  transition: all 0.3s ease;
  border: 1px solid var(--border-color);
}

.feature-card:hover {
  transform: translateY(-8px);
  border-color: var(--brand-secondary);
  box-shadow: 0 10px 30px -10px var(--brand-glow-subtler);
}
```

---

### Navigation

```css
.navbar {
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: space-between;
  padding: 1rem 2rem;
  border-radius: 0 0 12px 12px;
  position: sticky;
  top: 0;
  z-index: 50;
}

.nav-link {
  color: var(--text-secondary);
  font-weight: 600;
  text-decoration: none;
  transition: color 0.2s;
}

.nav-link:hover {
  color: var(--brand-primary);
}
```

---

### Profile Components

```css
.profile-avatar {
  width: 36px;
  height: 36px;
  border-radius: 50%;
  object-fit: cover;
  border: 2px solid var(--brand-primary);
}

.profile-dropdown {
  position: absolute;
  top: calc(100% + 10px);
  right: 0;
  width: 200px;
  background: var(--bg-secondary);
  backdrop-filter: blur(12px);
  border: 1px solid var(--border-color);
  border-radius: 12px;
  padding: 0.5rem;
  box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.4);
  z-index: 100;
}
```

---

## Visual Effects

### Text Gradient

```css
.text-gradient {
  background: linear-gradient(135deg, var(--brand-primary), var(--brand-secondary));
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}
```

### Hero Gradient Background

```css
.hero-gradient {
  background: linear-gradient(-45deg,
    var(--bg-primary),
    var(--brand-glow-subtle),
    var(--brand-glow-subtler),
    var(--bg-primary));
  background-size: 400% 400%;
  animation: gradientBG 15s ease infinite;
}

@keyframes gradientBG {
  0% { background-position: 0% 50%; }
  50% { background-position: 100% 50%; }
  100% { background-position: 0% 50%; }
}
```

### Card Glow on Hover

```css
.card-glow {
  box-shadow: 0 20px 40px -10px rgba(0, 0, 0, 0.3),
              0 0 20px var(--brand-glow-subtle);
}
```

---

## Animations

### Fade In

```css
@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.animate-fade-in {
  animation: fadeIn 0.4s ease-out forwards;
}
```

### Common Transitions

```css
transition: all 0.2s ease;
transition: all 0.2s ease-in-out;
transition: all 0.3s ease;
transition: all 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
```

---

## Responsive Breakpoints

| Breakpoint | Width | Usage |
|------------|-------|-------|
| Mobile | 0-480px | Single column layout |
| Small Mobile | 481-600px | Compact mobile |
| Tablet | 481-768px | Two column grids |
| Small Laptop | 769-1024px | Three column grids |
| Desktop | 1025px+ | Full layout |

---

## Z-Index Scale

| Layer | Value |
|-------|-------|
| Base | `0` |
| Navbar | `50` |
| Dropdowns | `100` |
| Modals/Overlays | `1000` |

---

## Usage Guidelines

1. **Dark-first**: Design primarily for dark mode; light mode is secondary
2. **Brand consistency**: Always use `--brand-primary` for primary actions and highlights
3. **Glassmorphism**: Apply `backdrop-filter: blur()` to elevated components like panels and dropdowns
4. **Hover states**: Include lift animations (`translateY`) and glow effects on interactive cards
5. **Gradients**: Use `linear-gradient` with brand colors for primary buttons and text highlights
6. **Shadows**: Prefer brand-colored glows over neutral black shadows
7. **Border radius**: Use consistent values from the spacing scale