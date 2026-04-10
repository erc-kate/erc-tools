# Course Audit App — Style Guide & Codebook

## Overview

This app is a **vanilla HTML/CSS/JavaScript** single-page application.
All visual styles follow **Texas A&M University brand guidelines** (marcomm.tamu.edu).

External libraries are loaded via CDN — no build step or install required.

| Library | Version | Purpose |
|---------|---------|---------|
| Bootstrap | 5.3.3 | Layout, responsive grid, base components |
| DataTables | 1.13.8 | Sortable, filterable, paginated table |
| DataTables FixedHeader | 3.4.0 | Keeps table header visible on scroll |
| Google Fonts | — | Work Sans + Open Sans |

---

## File Structure

| File | Purpose |
|------|---------|
| `index.html` | Everything — HTML structure, CSS styles, and JavaScript logic in one file |
| `course_audit.csv` | Primary data — one row per course |
| `theme.csv` | Theme names and definitions |
| `subtheme.csv` | Subtheme names and definitions, linked to themes |

---

## Color Palette

All colors are drawn from the official Texas A&M brand palette.

| Name | Hex | Where Used |
|------|-----|-----------|
| Aggie Maroon | `#500000` | Table header background, active tab underline, course code text, detail panel left border, focus rings, pagination active state, reset button border |
| White | `#FFFFFF` | Page background, table header text |
| Dark Text | `#333333` | Body text, table cell text, headings |
| Medium Gray | `#666666` | Body color, intro text, tab button default color |
| Hint Gray | `#999999` | Filter labels, meta labels, count text, prereq text |
| Border Gray | `#E2E2E2` | Tab nav border, filter bar border, filter inputs, table footer border |
| Rule Gray | `#F2F2F2` | Between-row borders in the table |
| Row Hover | `#FAFAFA` | Table row hover state |
| Open Row BG | `#F6F6F6` | Expanded row background + table footer background |
| Detail Panel BG | `#FFFFFF` | Expanded course detail panel background |

---

## Typography

Fonts are loaded from **Google Fonts** via a `<link>` tag in `<head>`.

| Font | Weights | Role | Where Used |
|------|---------|------|-----------|
| **Work Sans** | 400, 500, 600, 700 | Headings & UI labels | Tab buttons, filter labels, table column headers, course count, all `h1–h6` |
| **Open Sans** | 400, 600 | Body text | Body copy, filter inputs, table cell text, pagination |

To change fonts, update the Google Fonts `<link>` in `<head>` and the `font-family` declarations in the CSS.

---

## CSS Classes Reference

### Layout & Page

| Class | Description |
|-------|-------------|
| `body` | Open Sans, `#666`, 14px, white background |
| `h1–h6` | Work Sans, `#333` |
| `.intro-text` | Intro paragraph — `#666`, 0.93rem, max-width 760px |

### Tab Navigation

| Class | Description |
|-------|-------------|
| `.tab-nav` | Flex container with `border-bottom: 2px solid #e2e2e2` |
| `.tab-btn` | Work Sans, 0.875rem, `#666` — underline tab style |
| `.tab-btn:hover` | Color changes to `#500000` |
| `.tab-btn.active` | `#500000` text + `3px solid #500000` bottom border |
| `.tab-panel` | Hidden by default (`display: none`) |
| `.tab-panel.active` | Visible (`display: block`) |

Tab switching is handled by the `switchTab(tabId)` JavaScript function in `index.html`.

### Filter Bar

| Class | Description |
|-------|-------------|
| `.filter-bar` | White card with `1px solid #e2e2e2` border, `border-radius: 4px`, flex layout |
| `.filter-group` | Column flex container for a label + input pair |
| `.filter-group.search` | Flex-grows to fill available space |
| `.filter-group.dropdown` | Min-width 130px |
| `.filter-label` | Work Sans, 0.68rem, uppercase, `#999` |
| `.filter-bar input / select` | Open Sans, 0.85rem, `1px solid #e2e2e2` border |
| `.filter-bar input:focus / select:focus` | `border-color: #500000`, maroon glow shadow |
| `.btn-reset` | Maroon outline button — fills maroon on hover |

### Table

| Selector | Description |
|----------|-------------|
| `.table-card` | Outer wrapper — `1px solid #e2e2e2` border, `border-radius: 4px` |
| `table.dataTable thead th` | Maroon (`#500000`) background, white text, Work Sans, uppercase, 0.7rem |
| `table.dataTable tbody td` | 0.875rem, `#333`, `10px 14px` padding |
| `table.dataTable tbody tr:hover > td` | `#fafafa` background |
| `table.dataTable tbody tr.row-open > td` | `#f6f6f6` background |
| `.course-code` | Maroon, bold, no-wrap — used for the `DEPT 000` identifier |

### Toggle Arrow & Detail Panel

| Class | Description |
|-------|-------------|
| `.toggle-arrow` | Maroon `▶` arrow, rotates 90° when row is open |
| `tr.row-open .toggle-arrow` | `transform: rotate(90deg)` |
| `.detail-wrap` | Expanded row container — `margin-left: 36px`, `#f6f6f6` background |
| `.detail-panel` | White card with `3px solid #500000` left border, `border-radius: 0 3px 3px 0` |
| `.detail-title` | 0.95rem, bold, `#333` |
| `.detail-meta` | 4-column grid: Department / Credits / Theme / Subtheme |
| `.meta-label` | 0.75rem, `#999` |
| `.meta-value` | 0.875rem, `#333`, bold |
| `.detail-desc` | Course description — 0.875rem, `#555` |
| `.detail-prereq` | Prerequisites — 0.875rem, `#999` |

### Footer & Pagination

| Class | Description |
|-------|-------------|
| `.table-footer` | Flex row — count on left, pagination on right, `#f6f6f6` background |
| `.course-count` | Work Sans, 0.75rem, `#999` — shows filtered course count |
| `.page-link` | Maroon text and border |
| `.page-item.active .page-link` | Maroon fill, white text |

### Themes Tab

| Class | Description |
|-------|-------------|
| `.themes-grid` | 2-column grid, `gap: 18px` |
| `.theme-card` | White card, `1px solid #ddd` border, `4px solid #500000` top border, `border-radius: 6px` |

---

## JavaScript Logic

All JavaScript lives at the bottom of `index.html`. Key functions:

### `Papa.parse()` — CSV Loading
Loads all three CSVs (`course_audit.csv`, `theme.csv`, `subtheme.csv`) using PapaParse.
Waits for all three to complete before initializing the DataTable.

### `initTable(courses, themes, subthemes)`
Initializes the DataTables instance. Builds the expandable row detail HTML inline.
Populates the Theme and Department filter dropdowns dynamically from the data.

### `switchTab(tabId)`
Shows the selected `.tab-panel` and updates `.tab-btn` active state.

### `applyFilters()`
Reads values from the search box and dropdown filters, then applies them as DataTables column filters. Called on every `input`/`change` event.

### `resetFilters()`
Clears all filter inputs and resets the DataTable. Called by the Reset button.

---

## Data Codebook

### `course_audit.csv`

One row per course.

| Column | Type | Description | Example |
|--------|------|-------------|---------|
| `course_name` | string | Department abbreviation | `ACCT` |
| `course_num` | integer | Course number | `658` |
| `title` | string | Full course title | `Advanced Accounting Data Analytics` |
| `department` | string | Full department name | `Accounting` |
| `credit_hours` | integer | Credit hours | `3` |
| `course_description` | string | Catalog description | `Conceptual and practical...` |
| `prereq` | string | Prerequisite text (may be blank) | `Graduate classification` |
| `theme` | string | Primary audit theme (links to `theme.csv`) | `Research Skills` |
| `subtheme` | string | Audit subtheme (links to `subtheme.csv`) | `Data Analytics & Computational Methods` |

### `theme.csv`

| Column | Type | Description |
|--------|------|-------------|
| `theme` | string | Theme name (matches `course_audit.csv → theme`) |
| `theme_definition` | string | Plain-language definition of the theme |

### `subtheme.csv`

| Column | Type | Description |
|--------|------|-------------|
| `theme` | string | Parent theme name (links to `theme.csv`) |
| `subtheme` | string | Subtheme name (matches `course_audit.csv → subtheme`) |
| `subtheme_definition` | string | Plain-language definition of the subtheme |

---

## To Update Data

No code changes needed. Just replace the CSV files:

- **Add/remove courses** → edit `course_audit.csv`
- **Add/rename a theme** → edit `theme.csv` and update the `theme` column in `course_audit.csv`
- **Add/rename a subtheme** → edit `subtheme.csv` and update the `subtheme` column in `course_audit.csv`

After editing CSVs, re-upload the files to the server. The app fetches them fresh on every page load.
