# Definitive rules for admin UI screen construction

Modern admin interfaces from Linear, Stripe, Vercel, GitHub, and Notion have converged on a remarkably consistent set of layout and interaction patterns. **The core principle is spatial predictability**: primary actions always top-right, destructive actions always bottom-isolated, toolbars always sticky, and content zones always stacked in a fixed vertical order. This report codifies these patterns into prescriptive rules drawn from real product analysis and six major design systems (Ant Design, Material Design, Carbon, Polaris, Atlassian, PatternFly). Every rule below is specific enough to be programmatically enforced by an AI coding assistant.

---

## 1. Button placement follows four context-dependent rulesets

Admin interfaces use four distinct button placement contexts, each with its own spatial conventions. Mixing these contexts is the most common source of layout inconsistency.

### Page-level actions: top-right, always

Every major admin product places the primary page action in the **top-right of the content header**, on the same horizontal line as the page title. Stripe puts "Create payment link" there. GitHub puts "New repository" there. Linear puts "New issue" there. Vercel puts "Add New…" there. No exceptions exist among modern admin tools.

**Rules:**

- Place the page title top-left and the primary action button top-right, forming a visual frame
- Limit to **one primary (filled) button** in the page header; additional actions use secondary (outlined) style or go into a "More" dropdown
- Secondary page actions sit left of the primary button, ordered by decreasing importance right-to-left
- Never place page-level actions at the bottom of the page — that position is reserved for form submission or danger zones

### Form actions: context determines alignment

Form button placement is the area of greatest nuance, and the rules differ based on whether the form is a full page or a modal dialog.

**Full-page forms** place the submit button **left-aligned with the form fields' left edge**. Eye-tracking research confirms this maintains the strong vertical scanning axis users follow down a form. The button order is **[Primary Action] [Cancel]** left-to-right, where cancel is styled as a text link or ghost button — never as a filled button. Atlassian's design system, PatternFly, and Adam Silver's forms research all converge on this convention. For forms longer than the viewport, a **sticky bottom bar** keeps actions always visible.

**Modal/dialog forms** flip the alignment: buttons go **bottom-right**, with the order **[Cancel] [Primary Action]** left-to-right. This follows macOS/mobile convention and is codified in Material Design, Atlassian, and PatternFly. The affirmative action is always rightmost in a dialog.

**Prescriptive rules:**

- Full-page form: submit left-aligned, order `[Save changes] [Cancel]`, cancel styled as ghost/text
- Modal form: buttons right-aligned, order `[Cancel] [Save]`, primary rightmost
- Forms exceeding one viewport height: use `position: sticky; bottom: 0` action bar with subtle top shadow
- Disable submit until changes are detected; re-enable when form is dirty
- Use **verb+noun labels** ("Save changes", "Create project") — never "OK", "Submit", or "Yes"

### Table row actions: three-dot menu in the last column

The universal convention is a **kebab menu (⋮) or three-dot menu as the rightmost column**. Shadcn/ui's data table, which has become the de facto React pattern, implements this as a `DropdownMenu` triggered by a `MoreHorizontal` icon with `id: "actions"` and no header text. Stripe makes entire rows clickable to navigate to detail pages, minimizing per-row actions. GitHub uses kebab menus on file and issue lists. Linear reveals actions on hover plus supports right-click context menus.

**Rules:**

- Place row actions in the **last (rightmost) column**, right-aligned
- For **≤3 frequent actions**: show inline icon buttons with tooltips, plus a kebab for overflow
- For **4+ actions or data-dense tables**: use only a kebab menu — no inline actions
- When the primary action is viewing details, make the **entire row clickable** and minimize visible row actions
- In kebab dropdown menus, place destructive actions **last**, below a visual separator, in red text
- Hover-reveal is acceptable for secondary actions, but keep the kebab icon always visible

### Toolbar actions: search left, primary action right

The toolbar sits between the page header and the content table. Its layout follows a consistent split-alignment pattern across all products studied.

**Rules:**

- Left side: search input, filter controls, active filter chips
- Right side: sort control, column visibility toggle, view switcher (list/grid), primary action button
- Maximum **3 visible action buttons** in the toolbar; overflow into "More" dropdown
- Toolbar height: **48–56px**
- When bulk selection is active, the toolbar either transforms (Gmail/GitHub pattern) or a floating bar appears (Linear/Jira pattern)

---

## 2. One primary action per screen, then strict visual hierarchy

The **single most universal rule** across all six design systems studied is this: **never show more than one primary (filled) button per visible section**. Ant Design states it explicitly. Polaris states it explicitly. Carbon, Atlassian, Material Design, and PatternFly all state it explicitly. Stripe's own component docs say the same. If no action is clearly primary, use all secondary (outlined) buttons rather than arbitrarily promoting one.

### The four-tier action hierarchy

| Tier            | Visual treatment                     | When to use                                  | Example                               |
| --------------- | ------------------------------------ | -------------------------------------------- | ------------------------------------- |
| **Primary**     | Filled background, brand color       | The single most important action per section | "Create project", "Save changes"      |
| **Secondary**   | Outlined/bordered, no fill           | Important but non-primary actions            | "Export", "Cancel", "Filter"          |
| **Tertiary**    | Text-only, no border or fill         | Low-emphasis and inline actions              | Table row actions, "Show more", links |
| **Destructive** | Red/danger color, filled or outlined | Irreversible actions                         | "Delete", "Remove", "Revoke"          |

Ant Design adds a useful fifth type: **dashed buttons** specifically for "Add another item" actions within forms. This is worth adopting as a convention.

### Destructive actions require physical separation and scaled friction

Destructive buttons must never sit adjacent to primary/confirmatory buttons — Nielsen Norman Group identifies this proximity as a top-10 application design mistake. The rules for destructive actions are the most consistently codified across all systems:

**Placement rules:**

- In settings pages: isolate in a **"Danger Zone" section** at page bottom with red border (GitHub's pattern, now industry standard)
- In dropdown menus: always **last item**, below a divider, in red text
- In confirmation modals: destructive button goes on the right as the "primary" of that dialog, styled red
- Never place a delete button within **100px** of a save button

**Confirmation friction scales to severity:**

| Severity                      | Pattern                                                                  | Example                                  |
| ----------------------------- | ------------------------------------------------------------------------ | ---------------------------------------- |
| Low (easily undone)           | Execute immediately + toast with "Undo" (5–10s window)                   | Removing a label, archiving              |
| Medium (single record)        | Confirmation modal with danger button, specific label ("Delete project") | Deleting a record                        |
| High (cascading/irreversible) | Type-to-confirm modal — user types resource name to enable button        | Deleting a repository, account deletion  |
| Critical (financial/security) | Type-to-confirm + re-authentication (password/2FA)                       | Ownership transfer, bank account changes |

### When to use dropdowns vs button groups

Use **button groups (segmented controls)** only for mutually exclusive view toggles (List/Grid/Board). Use **dropdown menus** when there are 4+ actions or one action is clearly more common. When a default action exists with alternatives, use a **split button**: the visible portion shows the default ("Save"), and the dropdown arrow reveals variants ("Save as draft", "Save and publish"). The canonical toolbar pattern from Ant Design is: `[Primary Button] [Default Button] [Dropdown: More ▾]`.

---

## 3. Bulk actions use floating bottom bars with tiered confirmation

### Selection mechanics

Every system places the **checkbox column first (leftmost)**, at 40–48px width. The header checkbox controls page-level select-all, and when checked, a banner offers global select-all: _"All 25 on this page selected. Select all 2,340 results?"_ — this two-step Gmail pattern prevents accidental mass operations and is the gold standard.

**Required interactions:**

- Shift-click for range selection (expected by every power user)
- Cmd/Ctrl-click for toggling individual items
- Cmd/Ctrl+A for select-all on current view
- Escape to clear all selections
- Indeterminate state (dash icon) on header checkbox when partial selection exists
- **Selected rows highlighted** with a distinct background color (typically light blue/primary at 8% opacity)

### The floating bottom bar has won

Three patterns compete for where bulk actions appear. The **floating bottom bar** — used by Linear, Jira, ClickUp, Calendly, and Adobe Spectrum — has emerged as the modern best practice over the older toolbar-replacement pattern (Gmail, GitHub). UX Movement's research explains why: users interact with rows in the middle and bottom of the screen, so placing actions at the bottom **reduces cursor travel distance** and avoids cluttering the already-busy toolbar area.

**Floating bar specifications:**

- `position: fixed; bottom: 24px` with centered alignment (accounting for sidebar width)
- Slides up with **200ms ease animation** when first item is selected
- Contains: selection count ("12 selected") as leftmost element → action buttons (3–5 max) → "More" overflow menu → clear/dismiss button
- Solid background with elevation shadow, **z-index above content but below modals**
- Persists after non-destructive actions; dismisses only on manual clear or when destructive actions remove all selected items
- On mobile, hide the "selected" label text and show only the count

**Confirmation tiers for bulk actions** mirror the single-action tiers: non-destructive bulk actions (label, assign, status change) execute immediately with an undo toast. Destructive bulk actions (delete) require a modal clearly stating the count: "Permanently delete 23 items? This cannot be undone." For operations affecting **100+ items**, show a progress indicator and a results summary (X succeeded, Y failed with reasons).

---

## 4. Settings pages use sidebar navigation with card-based sections

### Layout: sidebar navigation dominates

**Sidebar navigation (240–280px) + content area** is the standard for any settings interface with 5+ categories. GitHub, Linear, Vercel, Notion, Stripe, Slack, and GitLab all use this pattern. Tabs work only for 3–7 categories at the same hierarchy level (Stripe uses tabs for sub-navigation within sidebar sections). Single-scroll pages work for fewer than 15 total settings but fail at scale.

The sidebar should collapse to a **64px icon-only rail** at ≤1280px viewport width and become a hamburger overlay at ≤768px. Each sidebar item navigates to a distinct page. Shopify's Polaris adds a useful twist: a **two-column layout** within each settings page, with a thin left column for section labels/descriptions and a wider right column for the actual settings cards.

### Grouping: cards with per-section save

Within each settings page, related settings are grouped into **card-based sections** — bordered containers with a heading, optional description, form fields, and a section-level save button. GitHub's repository settings exemplify this perfectly: each card (Repository name, Features, Danger Zone) is visually distinct and independently savable.

**Save behavior rules are strict:**

- **Default to explicit save per section** — a "Save changes" button at the bottom-right of each card, disabled until changes are detected
- **Auto-save is acceptable only for standalone toggle switches** that are low-risk, non-sensitive, and self-contained
- **Never mix auto-save and manual save** within the same form or page section (GitLab's Pajamas design system makes this an explicit rule)
- Implement `beforeunload` warning when unsaved changes exist
- Show clear success feedback after saving: inline "Saved" text with checkmark, or a brief toast
- On save error: preserve user input, show inline error message, provide retry

### Toggle/form hybrids use progressive disclosure

Feature-flag toggles that reveal additional configuration when enabled (GitHub's Issues/Wikis toggles, Stripe's payment method settings) follow consistent rules: the toggle sits at the top of the section, enabling it reveals nested configuration with a **150–200ms slide-down animation**, and disabling the toggle preserves (not deletes) the sub-settings. Revealed configuration is visually indented or placed in a sub-card to show the parent-child relationship.

### The danger zone lives at the bottom, in red

GitHub pioneered the red-bordered "Danger Zone" section at the bottom of settings pages, and it has become the universal standard. Every destructive/irreversible action (delete, transfer ownership, change visibility) goes here, visually separated from regular settings by **significant whitespace (48px+ gap)**, a red border or red-tinted section header, and dedicated confirmation flows per action. The confirmation friction follows the same severity tiers described above.

---

## 5. Screen anatomy follows a fixed four-zone vertical stack

### The zones, top to bottom

Every admin content area divides into four predictable vertical zones:

**Zone 1 — Header** (page title 20–24px semibold top-left, optional breadcrumbs above, optional description below at 14px secondary color). Padding: 24–32px from top.

**Zone 2 — Toolbar** (search and filters left-aligned, sort/view-toggle/primary-action right-aligned). Height: 48–56px. Separated from content by 16–24px margin.

**Zone 3 — Content** (tables at full container width, forms constrained to 640–800px centered, card grids using CSS Grid with 24px gutters).

**Zone 4 — Pagination** (page numbers and items-per-page selector right-aligned, total count "Showing 1–25 of 342"). Height: ~48px.

### Sticky behavior follows a strict hierarchy

The sidebar is **always fixed** (full viewport height). The toolbar becomes **sticky when it scrolls out of view** (`position: sticky; top: 0; z-index: 50`) with a solid background and subtle shadow. Table column headers are **sticky below the toolbar** for any table exceeding ~10 rows. Form action bars are **sticky to the viewport bottom** on forms longer than one screen. Maximum two simultaneous sticky zones to avoid claustrophobia.

**Z-index hierarchy**: Sidebar (z-40) < Sticky toolbar (z-50) < Floating bulk action bar (z-55) < Modals and drawers (z-60+).

### Content width has clear defaults

| Context                           | Max width                   | Notes                                     |
| --------------------------------- | --------------------------- | ----------------------------------------- |
| Overall content container         | **1200–1440px**             | Vercel uses 1280px (`max-w-7xl`)          |
| Data tables                       | **Full width** of container | Tables expand to use all available space  |
| Single-column forms               | **640–800px**               | Centered within content area              |
| Multi-column forms                | **960–1200px**              | Two-column layout                         |
| Text-heavy pages (settings, docs) | **720–800px**               | Optimized for 60–80 character line length |

Design at **1440px** reference artboard, test at 1366px and 1920px. Tables use horizontal scroll with a frozen first column on smaller screens — never stack table rows into cards unless mobile is the primary use case. Column alignment: **text left-aligned, monetary/variable numeric values right-aligned** (Carbon, Material Design, Shadcn).

### Pagination beats infinite scroll for admin tables

Strong consensus across NN/g, UX Planet, and every admin design system: **use pagination for admin data tables**. Admin users need stable orientation ("page 3, row 7"), bookmarkable URLs, predictable server performance, and reliable row comparison. Default to **10–25 rows per page** with a selector offering 10/25/50/100. Reserve infinite scroll exclusively for activity feeds, notification logs, and chat histories.

---

## 6. View composition follows a decision tree

### List-to-detail transitions depend on detail complexity

The right transition pattern follows a clear decision tree:

**Side drawer (400–600px, or 40–50% of screen)** is the modern default for list→detail, used by Linear, Notion, and most recent SaaS tools. It maintains list context, enables fast switching between items, and feels lightweight. Linear's two-tier approach is best-in-class: clicking an issue opens a peek side panel; clicking the title within that panel opens the full-page view.

**Full page navigation** is correct when the detail view is complex with its own tabs, sub-sections, or sub-navigation (GitHub issues→issue detail). Always provide breadcrumb navigation back to the list and **restore scroll position** when returning.

**Master-detail split view** (1/3 list + 2/3 detail, or resizable) suits scan-and-inspect workflows like email, CRM contacts, or event logs. Collapse to stacked mode below ~840px. Minimum list width: 280px. Minimum detail width: 480px.

**Inline expansion** works only when the additional detail is fewer than ~5 fields of metadata.

### Empty states are never blank

An empty state must always contain a **headline** ("No orders yet"), a **supporting description** ("Orders will appear here once customers make purchases"), and a **call-to-action button** ("Create your first order"). Three distinct empty state types need different treatments: **no data** (educational — explain and invite creation), **no results from filter/search** (reassuring — suggest adjusting filters, provide "Clear filters" CTA), and **error state** (informative — explain what failed, provide "Retry" button). For tables, the empty state **replaces the entire table including headers** — never show an empty grid with column headers and a message below.

### Loading states use skeletons by default, spinners for actions

| Load time    | Pattern                                                                    |
| ------------ | -------------------------------------------------------------------------- |
| <1 second    | No loading indicator (skeleton flash would be more distracting)            |
| 1–5 seconds  | **Skeleton screen** mimicking actual content layout with shimmer animation |
| 2–10 seconds | **Spinner** for single-component loading or action processing              |
| >10 seconds  | **Progress bar** with percentage for uploads, imports, batch operations    |

Every dashboard component needs **three independent states**: loading (skeleton), empty (message + CTA), and error (amber/red banner with retry). If one API fails, show an error boundary on that component only — never block the entire page. Use **optimistic updates** (immediate UI update, revert on error) for high-confidence actions like toggles, status changes, and drag-and-drop reordering — but only when actions succeed >99% of the time.

---

## Codifiable ruleset for an AI coding assistant

These **40 rules** are concrete enough to be programmatically enforced during code generation:

**Action placement (10 rules):**

1. One primary (filled) button maximum per visible section
2. Page-level primary action: top-right of content header, same row as page title
3. Full-page form submit: left-aligned with form fields; order `[Primary] [Cancel]`
4. Modal buttons: right-aligned; order `[Cancel] [Primary]`
5. Cancel buttons styled as ghost/text — never filled
6. Long forms (>1 viewport): sticky bottom action bar
7. Table row actions: last column, right-aligned; ≤3 inline icons or kebab menu only
8. Toolbar layout: `[Search | Filters]` left, `[Sort | View toggle | Primary action]` right
9. Max 3 visible buttons in any toolbar or action group; overflow into dropdown
10. Button labels use verb+noun format: "Save changes", "Delete project" — never "OK" or "Submit"

**Destructive actions (6 rules):**

11. Destructive buttons: red/danger color + trash icon, physically separated from constructive actions
12. In dropdown menus: destructive actions last, below a divider, in red
13. In settings pages: isolated "Danger Zone" section at page bottom with red border
14. Low-severity deletion: execute + undo toast (5–10s)
15. Medium-severity deletion: confirmation modal with danger button and specific label
16. High-severity deletion: type-to-confirm modal (user types resource name)

**Bulk actions (8 rules):**

17. Selection checkbox in first (leftmost) column, 40–48px wide
18. Header checkbox controls page-level select; banner offers global select-all
19. Support shift-click range selection and Cmd/Ctrl+click toggle
20. Selected rows: distinct background highlight color
21. Bulk action bar: floating, `position: fixed; bottom: 24px`, centered, 200ms slide-up
22. Bar contents: `[count "selected"] [action buttons ×3-5] [More ⋯] [Clear ×]`
23. Non-destructive bulk actions: execute immediately + undo toast
24. Destructive bulk actions: modal confirmation stating exact count and consequences

**Settings pages (8 rules):**

25. Sidebar navigation (240–280px) for 5+ setting categories; collapsible to 64px
26. Card-based sections within each page: heading + description + fields + save button
27. Explicit save per section by default; auto-save only for standalone low-risk toggles
28. Never mix auto-save and manual save in the same form or section
29. Feature toggles reveal nested configuration with 150–200ms slide animation
30. Danger zone at page bottom, red-bordered, with per-action confirmation flows
31. Implement `beforeunload` warning when unsaved changes exist
32. Disable save button when no changes detected; enable on form dirty state

**Layout and zones (8 rules):**

33. Content zones stack: Header → Toolbar → Content → Pagination
34. Sidebar: always fixed. Toolbar: sticky on scroll. Table headers: sticky below toolbar
35. Sticky elements: solid backgrounds, subtle shadow on scroll, z-index hierarchy (sidebar 40, toolbar 50, floating bars 55, modals 60+)
36. Tables: full container width. Forms: 640–800px centered. Content container: 1200–1440px max
37. Pagination for admin tables (default 10–25 rows); never infinite scroll
38. Column alignment: text left, monetary/numeric right
39. Side drawer as default list→detail transition; full page for complex details
40. Every component needs three states: loading (skeleton), empty (headline + description + CTA), error (banner + retry)
