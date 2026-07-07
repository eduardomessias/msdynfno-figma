# Grid — control

> **Microsoft references:**
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/get-started/grid-capabilities
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/get-started/whats-new-platform-update-21 (sticky default actions)
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/general-form-guidelines (Grid section)
>
> **AOT control class:** `Grid` — with `Style = Tabular` (default) or `Style = List`
> **Figma component:** `Control/Grid`
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-05-21

---

## 1. Purpose

The **Grid** renders a table of records — the most common control in D365FO. It appears in two styles:

1. **Tabular grid** (default) — Excel-like rows × columns. Used in List Page, Simple List, Simple List and Details (Tabular variant), Details Transaction main grid, Details Master main grid, FactBox Grid.
2. **List style grid** (`Style = List`) — multi-line "card" rows, ≤3 lines, 2–5 fields per row. Used as the **NavigationList** in Details Master, Details Transaction, Simple List and Details (List Grid variant).

The grid supports inline editing, multi-select, column filtering, sorting, grouping, freeze, export, personalisation, and bulk-edit-via-paste from Excel.

---

## 2. Anatomy

```
Grid (Style=Tabular | List)
├── Column 1 (FormStringControl | FormRealControl | FormDateControl | FormReferenceGroup | …)
├── Column 2
├── …
├── DefaultAction (CommandButton)            — hyperlinked column / row default action
└── DefaultLabelAction                       — label shown in the grid context menu
```

| Node              | Purpose                                                       | Notes                                                                  |
|-------------------|---------------------------------------------------------------|------------------------------------------------------------------------|
| Column            | One field per column. Type matches the bound EDT.             | First textual column carries the hyperlink to Details (when there is one) |
| `DefaultAction`   | The button to invoke when the row is "opened"                  | Bind to a `MenuItemButton` that navigates to the Details form           |
| `DefaultLabelAction` | Label exposed in the context menu                           | E.g. "Customer details"                                                 |

---

## 3. Key properties

| Property              | Notes                                                                                                       |
|-----------------------|-------------------------------------------------------------------------------------------------------------|
| `DataSource`          | Bound data source (the table or view)                                                                       |
| `Style`               | `Tabular` (default) or `List`                                                                               |
| `AllowEdit`           | Inherits from data source. List Page primary data source must have `AllowEdit = No`                          |
| `DefaultAction`       | The button that opens Details when the row is clicked (hyperlink on first text column)                       |
| `DefaultLabelAction`  | Label in the right-click menu                                                                                |
| `Multiselect`         | `Yes` to allow multi-row selection                                                                          |
| `ShowRowLabels`       | For List style grids — show the label for each line                                                          |
| `FrozenColumnCount`   | How many leading columns stay pinned during horizontal scroll                                                |

Per-column properties of note: `EditMode` (read-only override), `Mandatory`, `DisplayLength`, `AllowEdit`, `Skip`.

---

## 4. States / variants

- **Row states**: Rest / Hover / Selected / Edited (italics during "typing ahead of system" save). Group header rows have an expanded/collapsed indicator.
- **Cell states**: Rest / Focus / Edit / Disabled / ReadOnly / Validation (warning/error/success border).
- **Modes**: Light / Dark / HighContrast.
- **Densities**: Compact (default — 28px row) / Comfortable.
- **Sticky default action** (Platform update 21+, off by default but enabled in most environments) keeps the hyperlink on the same column regardless of personalisation. Behave as if it's on.

Bound tokens (`component.tokens.json → grid`):
- `headerBackground`, `headerForeground`, `headerBorderBottom`
- `rowBackground`, `rowBackgroundAlt`, `rowBackgroundHover`, `rowBackgroundSelected`
- `cellForeground`, `cellForegroundLink`, `cellBorder`
- `geometry.headerHeight`, `rowHeightCompact`, `rowHeightDefault`

---

## 5. UX guidelines (consolidated from MS Learn)

### Tabular grid

1. **≤15 columns**.
2. First textual / data column is a hyperlink → Details form (set via `DefaultAction`).
3. Column ordering:
   - **Transactional entities**: `ID` first, then master entity `ID` + `Name`.
   - **Master entities**: `Name` first, then `ID`.
4. Include all mandatory fields so records can be created **directly** in the grid.
5. By default, the [QuickFilter](./filter-pane.md#quickfilter) targets the most-likely-filtered column.
6. **Empty grid** must **not** auto-insert a new record.
7. **No editable Tabular grids** in the Simple List and Details — Tabular variant (the parent pattern forbids it).

### List style grid (NavigationList)

1. Rows span **at most 3 lines**.
2. **≥2 fields** per row. Typically just ID + Description.
3. In Details Transaction, the **last field** is the transaction **total** (lets users scan financial impact).

### Inline editing

- "Typing ahead of system" allows users to type before validation completes for a row. Status indicators appear at the start of the row (Pending / Validated / Failed / Paused).
- Users can **paste from Excel** directly into the grid (matching column count). Excess columns are skipped; excess rows are inserted.
- **Bulk edit** (Feature management 10.0.38+) — multi-select rows then `Grid options > Edit selected rows` to update a column for all selected rows.

### Grouping / sorting / freeze

- Users can group on any column; grouping persists in personalisation, expand/collapse state does not.
- Sorting on a non-grouped column keeps grouping intact.
- Date/DateTime columns can be grouped by year/month/day/hour/minute/second.
- Freeze pane via right-click on the column header.

---

## 6. Behavior

- The grid supports right-click context menus with **View details**, **Filter by selection**, **Copy**, **Add to filter**, etc.
- `Ctrl+Shift+G` switches to grid view in patterns that have a grid/details split (Details Master, Details Transaction).
- Default action hyperlink is **sticky** (Platform update 21+ feature; treat as on by default).
- Validation runs cell-by-cell. Failed validations are highlighted by the cell border tokens.

---

## 7. Do / Don't

**Do**
- Set `DefaultAction` on every Grid that opens Details — without it, the hyperlink doesn't render correctly.
- Provide a label via `DefaultLabelAction` so the grid's right-click menu makes sense.
- Use a **List style** grid for NavigationLists; **Tabular** for the bulk-edit grid in the same pattern.
- Allow multi-select where users actually need bulk operations.

**Don't**
- Don't exceed 15 columns — push extra info into FactBoxes or the Details form.
- Don't auto-insert a row when the grid is empty.
- Don't disable inline editing on a Details Transaction main grid — the pattern is built around it.
- Don't put computed (code-driven) columns where filters or sorts are expected — only SQL-backed columns support that.

---

## 8. AOT mapping (dev handoff)

| Spec field                | AOT location                                                       | Notes                                          |
|---------------------------|--------------------------------------------------------------------|------------------------------------------------|
| Grid container             | `Form > Design > … > Grid`                                         | `Style = Tabular` or `List`                    |
| Data source                | `Grid.DataSource = <DS>`                                           | Required by BP                                 |
| Columns                    | `Grid > <FormControl bound to DS.field>`                            | ≤15 for tabular; specific count for List style |
| Default action             | `Grid.DefaultAction = <button name>` + the button is a `MenuItemButton` opening Details | Drives the hyperlink            |
| Default label              | `Grid.DefaultLabelAction = <label>`                                | Shown in right-click context menu              |
| Multi-select               | `Grid.Multiselect = Yes`                                           | Enable for bulk-edit scenarios                 |
| Freeze columns             | `Grid.FrozenColumnCount = <n>`                                     | Pins the first n columns                       |

---

## 9. Figma component definition

- **Name:** `Control/Grid`
- **Slots:**
  - `Slot — header row` (column header instances)
  - `Slot — data rows` (repeating row instance)
  - `Slot — footer` (optional totals / aggregates)
- **Variants:**
  - `Style`: Tabular / List
  - `State`: Filled / Empty / Loading / Error
  - `Density`: Compact / Comfortable
  - `Edit mode`: ReadOnly / Editable
  - `Mode`: Light / Dark / HighContrast

A **row** is its own component (`Control/Grid/Row`) with variants for `Rest / Hover / Selected / Edited` and `Style: Tabular / List`.

---

## 10. References

- Microsoft Learn — [Grid capabilities](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/get-started/grid-capabilities)
- Microsoft Learn — [General form guidelines — Grid](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/general-form-guidelines)
- Microsoft Learn — [Sticky default actions in grids (Platform update 21)](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/get-started/whats-new-platform-update-21)
