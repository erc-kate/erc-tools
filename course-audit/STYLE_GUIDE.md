# Course Audit App — Style Guide & Codebook

## Overview

This app is built with **R Shiny** using the **bslib** package for modern Bootstrap 5 theming.
All visual styles follow **Texas A&M University brand guidelines** (marcomm.tamu.edu).

---

## File Structure

| File | Purpose |
|------|---------|
| `app.R` | Entry point — never changes, just calls `shinyApp(ui, server)` |
| `ui.R` | All layout, styling (CSS), and HTML structure |
| `server.R` | All logic: filtering, reactivity, table rendering |
| `global.R` | Runs once on startup: loads packages, reads data, defines helper functions |
| `course_audit.csv` | Primary data — one row per course |
| `theme.csv` | Theme names and definitions |
| `subtheme.csv` | Subtheme names and definitions, linked to themes |

---

## Color Palette

All colors are drawn from the official Texas A&M brand palette.

| Name | Hex | Where Used |
|------|-----|-----------|
| Aggie Maroon | `#500000` | Header bar, pill tab active state, row expand arrows, left-border accents |
| Deep Maroon | `#3C0000` | Available for hover/dark states if needed |
| White | `#FFFFFF` | Page background, active pill tab text |
| Dark Charcoal | `#202020` | Default body text (`fg` in bs_theme) |
| Charcoal | `#3E3E3E` | Page description paragraphs |
| Medium Gray | `#707070` | Filter labels, table header text, count text, hint text |
| Light Gray | `#D1D1D1` | Table border, table header bottom border |
| Warm Off-White | `#f5f1f0` | Table column header background |
| Row Hover | `#EBEBEB` | Table row hover state |
| Detail Panel BG | `#fdf8f8` | Expanded course detail background (inline in global.R) |
| Rule Lines | `#f0f0f0` | Between-row borders in the table |

---

## Typography

Fonts are loaded from **Google Fonts** via `bslib::font_google()` in the `bs_theme()` call in `ui.R`.

| Font | Role | Where Used |
|------|------|-----------|
| **Oswald** | Headings | App title ("GRADUATE COURSE AUDIT"), set via `heading_font` |
| **Open Sans** | Body | All body text, filter labels, table content, descriptions, set via `base_font` |

To change fonts, update the `bs_theme()` call in `ui.R`:
```r
bs_theme(
  base_font    = font_google("Open Sans"),
  heading_font = font_google("Oswald")
)
```

---

## bslib Theme Configuration (`ui.R`)

```r
bs_theme(
  version      = 5,          # Bootstrap version
  primary      = "#500000",  # Aggie Maroon — drives btn-primary, focus rings, etc.
  bg           = "white",    # Page background
  fg           = "#202020",  # Default text color (Dark Charcoal)
  base_font    = font_google("Open Sans"),
  heading_font = font_google("Oswald")
)
```

Setting `primary` to Aggie Maroon means any bslib/Bootstrap component that uses
the primary color (buttons, focus outlines, active states) automatically uses maroon
without extra CSS.

---

## CSS Classes Reference (`ui.R`)

### Layout

| Class | Description |
|-------|-------------|
| `.app-header` | Full-width maroon header bar containing the title and pill tabs |
| `.content-area` | Padding wrapper (`24px 8px 20px 8px`) applied inside each tab panel |
| `.layout-col-filters` | Left sidebar column (filters) — collapses to full width on mobile |
| `.layout-col-table` | Right main column (table) — collapses to full width on mobile |

### Navigation

| Class | Description |
|-------|-------------|
| `.pill-tabs` | Flex container for tab buttons |
| `.pill-tab` | Individual tab button — white outlined pill on maroon background |
| `.pill-tab.active` | Active tab — filled white with maroon text |
| `.tab-panel` | Hidden by default (`display: none`) |
| `.tab-panel.active` | Visible tab content (`display: block`) |

Tab switching is handled by the `switchTab(tab)` JavaScript function at the bottom of `ui.R`.

### Table

| Selector | Description |
|----------|-------------|
| `.dataTables_wrapper` | Outer border and rounded corners around the DT table |
| `table.dataTable thead th` | Column headers — `#f5f1f0` background, uppercase, `#707070` text |
| `table.dataTable tbody td` | Data cells — 9px vertical padding |
| `table.dataTable tbody tr:hover` | Row hover — `#EBEBEB` background |
| `.toggle-col` | The `▶` expand arrow in column 2 — rotates 90° when row is open |
| `.row-open .toggle-col` | Rotated arrow state for expanded rows |
| `.dataTables_info` | Hidden (the "Showing X of Y entries" text is suppressed) |

### Text & Labels

| Class | Description |
|-------|-------------|
| `.filter-label` | Small uppercase label above each filter input |
| `.page-description` | Introductory paragraph under the tabs |
| `.course-total` | Right-aligned count below the table |
| `.filter-hint` | Small help text at the bottom of the filter sidebar |

---

## Data Codebook

### `course_audit.csv`

One row per course. 166 courses total.

| Column | Type | Description | Example |
|--------|------|-------------|---------|
| `course_name` | character | Department abbreviation | `ACCT` |
| `course_num` | integer | Course number | `658` |
| `title` | character | Full course title | `Advanced Accounting Data Analytics` |
| `department` | character | Full department name | `Accounting` |
| `credit_hours` | integer | Credit hours | `3` |
| `course_description` | character | Catalog description | `Conceptual and practical...` |
| `prereq` | character | Prerequisite text (may be NA) | `Graduate classification` |
| `theme` | character | Primary audit theme (links to `theme.csv`) | `Research Skills` |
| `subtheme` | character | Audit subtheme (links to `subtheme.csv`) | `Data Analytics & Computational Methods` |

### `theme.csv`

| Column | Type | Description |
|--------|------|-------------|
| `theme` | character | Theme name (matches `courses$theme`) |
| `theme_definition` | character | Plain-language definition of the theme |

### `subtheme.csv`

| Column | Type | Description |
|--------|------|-------------|
| `theme` | character | Parent theme name (links to `theme.csv`) |
| `subtheme` | character | Subtheme name (matches `courses$subtheme`) |
| `subtheme_definition` | character | Plain-language definition of the subtheme |

---

## Helper Functions (`global.R`)

### `make_detail_html(row)`
Takes one row from `courses` and returns an HTML string for the expandable row detail panel.
Called inside `server.R` via `sapply()` when building the table.

**Inline style values to know:**
- Left border: `4px solid #500000` (Aggie Maroon)
- Background: `#fdf8f8` (warm off-white)
- Margin: `4px 24px 8px 48px` (indented from the row)

### `build_themes_tab(themes_df, subthemes_df)`
Builds the entire Themes & Definitions tab as a list of bslib/HTML elements.
Returns a `tagList` — one block per theme, each containing its subthemes.

**Inline style values to know:**
- Left border: `4px solid #500000` (Aggie Maroon)
- Background: `#fdf8f8` (warm off-white)

> **Note:** These inline styles live in `global.R` (not `ui.R`) because they are
> generated programmatically in R, not as static CSS. If you update brand colors,
> remember to update both `ui.R` **and** the inline styles in `global.R`.

---

## To Update Data

No code changes needed. Just replace the CSV files:

- **Add/remove courses** → edit `course_audit.csv`
- **Add/rename a theme** → edit `theme.csv` and update `courses$theme` values in `course_audit.csv`
- **Add/rename a subtheme** → edit `subtheme.csv` and update `courses$subtheme` values in `course_audit.csv`

After editing CSVs, redeploy the app (the data is read fresh on each startup).
