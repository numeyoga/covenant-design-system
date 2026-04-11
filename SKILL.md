---
name: vanilla-design-system
description: >
  Design system for Vanilla Web backoffice/dashboard applications (HTML5, CSS natif, JS vanilla).
  ALWAYS use this skill whenever you must create, modify, style, structure, or review any UI element —
  including but not limited to: HTML pages, components, layouts, forms, tables, modals, drawers,
  navigation, toasts, alerts, badges, tabs, dropdowns, tooltips, empty states, loading states,
  error states, icons, CSS tokens, color themes, spacing, typography, accessibility (WCAG),
  responsive behavior, or any visual/frontend code. Also use this skill when generating code that
  will be rendered in a browser, when reviewing UI code for conformity, or when answering questions
  about UI conventions, design tokens, or component APIs. If the task touches anything visual or
  structural in the frontend, read this skill FIRST. Even partial UI tasks (e.g. "add a button",
  "style this table", "fix the layout") require this skill.
---

# Vanilla Web Design System — Skill Guide

## Purpose

This skill enforces a comprehensive design system for **personal-use backoffice/dashboard SPAs** built with the **Vanilla Web** stack (HTML5, native CSS custom properties, vanilla JS ES2024+). No framework, preprocessor, or transpiler in the production bundle.

The design system is documented across 9 reference files in the `references/` directory. This SKILL.md contains the critical rules that apply to **every** task, plus a routing table to load only the specific reference files needed.

---

## Critical Rules — Always Apply

These rules are non-negotiable. Apply them to every piece of UI code you produce, regardless of which reference files you read.

### Stack

- **HTML5 sémantique, CSS natif (custom properties), JavaScript vanilla (ES2024+)**
- Zero frameworks, preprocessors, or transpilers in the delivered code
- Web Platform **Baseline "Newly Available"** as the compatibility floor
- Target browsers: Chrome and Opera (Chromium-based)

### Design Tokens — Mandatory

Every color, spacing, font-size, border-radius, shadow, z-index, and animation duration **must** use a CSS custom property token. No raw hex, px, or ms values in application CSS.

Token architecture (two levels):

- **Primitive tokens** (`--palette-*`, `--font-*`, `--spacing-*`, etc.) — defined in `tokens.css`, **never consumed directly** in component/application CSS.
- **Semantic tokens** (`--color-*`, `--text-*`, `--radius-*`, etc.) — reference primitives and are the **only** tokens used in component and application code.

### Accessibility — WCAG 2.2 AA

| Criterion               | Requirement                               |
| ----------------------- | ----------------------------------------- |
| Text contrast           | ≥ 4.5:1 (normal text)                     |
| Large text contrast     | ≥ 3:1 (≥ 18pt or 14pt bold)               |
| UI element contrast     | ≥ 3:1                                     |
| Keyboard navigation     | All interactive elements                  |
| Visible focus           | Mandatory, styled via tokens              |
| ARIA attributes         | When native HTML semantics are not enough |
| Interactive target size | Minimum 24×24 CSS pixels                  |

### Typography

- Root font-size: `16px` (do not change)
- Base interface text: `0.875rem` (14px) via `--text-base`
- Font stack: system-ui based (see tokens)

### Layout — Desktop-First

- Primary design viewport: **2048 × 1056 CSS px** (QHD at 125% scaling)
- Media queries use `max-width` (desktop-first)
- App Shell pattern: topbar + optional sidebar + main content area
- Grid system based on CSS Grid, not flexbox (flexbox for alignment within cells)

### CSS Conventions

- **BEM** naming: `block__element--modifier` for structural classes
- **`data-*` attributes** for variant/state modifiers (not BEM modifier classes)
- **`data-js-*` attributes** as JS hooks (never use CSS classes as JS selectors)
- One component per CSS file (`css/components/button.css`, etc.)

### HTML Conventions

- Semantic elements (`<header>`, `<nav>`, `<main>`, `<section>`, `<dialog>`, etc.)
- Native `<dialog>` for modals and drawers (focus trap, backdrop, Escape built-in)
- `accent-color` for checkbox/radio styling
- `lang="fr"` and `data-theme="light"` on `<html>`

### JS Conventions

- ES modules (`type="module"`)
- `data-js-*` attributes for DOM queries
- No inline event handlers; use `addEventListener`

---

## Reference File Routing

Read **only** the files relevant to your current task. Always start with the routing table below.

> **How to read**: Use the `view` tool on the file path shown. The files are in the `references/` subdirectory relative to this SKILL.md.

| If your task involves…                                               | Read these files (in order)     |
| -------------------------------------------------------------------- | ------------------------------- |
| **Any UI task** (baseline context)                                   | `00-fondations.md` (skim §2–§3) |
| Design tokens, colors, spacing, typography, shadows, z-index, themes | `01-design-tokens.md`           |
| Page structure, app shell, grid, containers                          | `02-layout.md`                  |
| Buttons                                                              | `03-composants.md` §2           |
| Inputs, textarea, select                                             | `03-composants.md` §3–§5        |
| Checkbox, radio, toggle                                              | `03-composants.md` §6–§7        |
| Cards                                                                | `03-composants.md` §8           |
| Data tables                                                          | `03-composants.md` §9           |
| Badges                                                               | `03-composants.md` §10          |
| Alerts                                                               | `03-composants.md` §11          |
| Dropdowns / menus                                                    | `03-composants.md` §12          |
| Tabs                                                                 | `03-composants.md` §13          |
| Tooltips                                                             | `03-composants.md` §14          |
| Page headers                                                         | `04-patterns.md` §2             |
| Toolbars                                                             | `04-patterns.md` §3             |
| Forms (create/edit patterns)                                         | `04-patterns.md` §4             |
| List with search/filters                                             | `04-patterns.md` §5             |
| Modals (dialogs)                                                     | `04-patterns.md` §6             |
| Drawers (side panels)                                                | `04-patterns.md` §7             |
| Toasts / notifications                                               | `04-patterns.md` §8             |
| Empty states                                                         | `04-patterns.md` §9             |
| Sidebar navigation                                                   | `04-patterns.md` §10            |
| Loading states, skeletons, spinners                                  | `05-etats-feedback.md` §3       |
| Success states                                                       | `05-etats-feedback.md` §4       |
| Error states, validation                                             | `05-etats-feedback.md` §5       |
| Empty/disabled/data states                                           | `05-etats-feedback.md` §6–§8    |
| Feedback animations                                                  | `05-etats-feedback.md` §9       |
| Icons (Lucide SVG, sprite, usage)                                    | `06-iconographie.md`            |
| HTML/CSS/JS code conventions                                         | `07-regles-code.md`             |
| File/project structure                                               | `07-regles-code.md` §5–§6       |
| **Final review / conformity audit**                                  | `08-checklist.md`               |

### Reading Strategy

1. **Simple task** (e.g., "add a button"): Read the specific component section only. The critical rules above give you enough context.
2. **Medium task** (e.g., "build a form page"): Read `02-layout.md` + the relevant sections of `03-composants.md` + `04-patterns.md` §4.
3. **Full page or new project**: Read `00-fondations.md`, `01-design-tokens.md`, `02-layout.md`, `07-regles-code.md`, then the component/pattern files as needed.
4. **Code review or audit**: Read `08-checklist.md` and cross-reference with the specific documents for any failing items.

---

## File Structure Reference (Quick)

A conforming project follows this structure:

```
project/
├── index.html
├── css/
│   ├── tokens.css          ← All design tokens
│   ├── reset.css            ← CSS reset
│   ├── base.css             ← Base element styles
│   ├── layout.css           ← App shell + grid
│   ├── utilities.css        ← Utility classes
│   └── components/
│       ├── button.css
│       ├── input.css
│       └── …
├── js/
│   ├── main.js              ← Entry point (type="module")
│   └── components/
│       └── …
├── icons/
│   └── sprite.svg           ← Lucide SVG sprite
└── package.json             ← Browserslist + dev-only tooling
```

---

## Component Quick Reference

| Component  | CSS Class     | Key Element          | Variants via `data-*`       |
| ---------- | ------------- | -------------------- | --------------------------- |
| Button     | `.btn`        | `<button>`           | `data-variant`, `data-size` |
| Input      | `.input`      | `<input>`            | `data-size`                 |
| Textarea   | `.textarea`   | `<textarea>`         | —                           |
| Select     | `.select`     | `<select>`           | `data-size`                 |
| Checkbox   | `.checkbox`   | `<input type>`       | styled via `accent-color`   |
| Radio      | `.radio`      | `<input type>`       | styled via `accent-color`   |
| Toggle     | `.toggle`     | `<button>`           | `data-size`                 |
| Card       | `.card`       | `<article>`          | `data-variant`              |
| Data Table | `.data-table` | `<table>`            | `data-density`              |
| Badge      | `.badge`      | `<span>`             | `data-variant`, `data-size` |
| Alert      | `.alert`      | `<div role=alert>`   | `data-variant`              |
| Dropdown   | `.dropdown`   | `<div>` + `<menu>`   | `data-position`             |
| Tabs       | `.tabs`       | `<div role=tablist>` | —                           |
| Tooltip    | `.tooltip`    | `<span>` / CSS       | `data-position`             |

---

## Anti-Patterns — Never Do This

- ❌ Raw color values (`#fff`, `rgb(...)`) in component/application CSS
- ❌ Raw spacing values (`margin: 8px`) — use `var(--space-*)`
- ❌ BEM modifier classes for states — use `data-*` attributes
- ❌ CSS classes as JS hooks — use `data-js-*`
- ❌ Framework-specific code (React, Vue, Svelte) in the delivered bundle
- ❌ `<div>` where a semantic element exists (`<nav>`, `<main>`, `<section>`, `<dialog>`)
- ❌ Custom modal/drawer implementations — use native `<dialog>`
- ❌ Inline styles or `!important` (except documented utility exceptions)
- ❌ Z-index values not from the token layer system
- ❌ Animations without `prefers-reduced-motion` respect

---

## Post-Production Checklist Reminder

After completing any UI task, mentally walk through `08-checklist.md`. For significant deliverables (full pages, new components), read the checklist file explicitly and verify each applicable item.
