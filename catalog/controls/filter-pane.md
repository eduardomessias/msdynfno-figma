# Filter pane and friends — controls

> **Microsoft references:**
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/filtering
> - https://learn.microsoft.com/dynamics365/fin-ops-core/fin-ops/get-started/advanced-filtering-query-options
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/custom-filter-group-subpattern
>
> **AOT control class(es):** Filter Pane (system-provided), `QuickFilter` (modelled), Grid column filter dialog (system), Advanced filter dialog (system)
> **Figma component(s):** `Pattern/FilterPane`, `Control/QuickFilter`, `Composite/GridColumnFilter`, `Pattern/AdvancedFilterDialog`
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-05-21

---

## 1. Purpose

D365FO offers **four** filtering surfaces — pick the right one per scenario.

| Mechanism            | When to use                                                                                          | Modelled by dev? |
|----------------------|------------------------------------------------------------------------------------------------------|------------------|
| **Filter pane**      | Full-page lists. Slides in from the left, multiple criteria, applies to the page's first primary DS  | System provides (developer can pre-populate fields) |
| **QuickFilter**      | Single-column, search-as-you-type, attached to a Grid                                                | **Yes** — modelled inside the pattern's filter group |
| **Grid column filter** | Click a column header to filter / sort that column                                                  | System provides on every Grid                       |
| **Advanced filter**  | Advanced query syntax, multiple data sources, multi-column sorts                                     | System provides (`Ctrl+Shift+F3`)                    |

In addition, the [Custom Filter Group](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/custom-filter-group-subpattern) subpattern lets a developer model a small set of input controls above a grid that apply custom-coded filters (e.g. a `ComboBox` of statuses + a date range). It complements but doesn't replace the four above.

---

## 2. Filter pane

The Filter pane slides in from the left when the user clicks **Show filters**. It applies to the page's **first primary data source** and any directly-joined data sources (inner or outer).

### When is it available?

Available on full-page lists. **Not** available on: Drop dialogs, Dialogs, Enhanced previews, Lookups, Page parts, Parts, Table of Contents, pages with no data sources.

### What fields appear by default?

In order of precedence:
1. All non-hidden ranges and filters currently on the query.
2. Fields from the primary index of the first primary data source.
3. `TitleFields` defined on the first primary data source. If none, no defaults.

### Making a field show in the Filter pane

Developers add an empty filter for the desired field to the query in `form init()` post-`super()`:

```xpp
public void init()
{
    super();
    SysQuery::findOrCreateRange(this.query().dataSourceTable(tableNum(CustTable)), fieldNum(CustTable, CustGroup));
}
```

Once any range exists, the auto-default behaviour stops — every field that should appear must be explicitly added.

### Filter status (lockability)

`RangeStatus` controls user interaction with a range:

- `Open` (default) — visible and modifiable.
- `Locked` — visible value, can't be changed; user can't add another filter on this column.
- `Hidden` — invisible; user can't add another filter on this column.

---

## 3. QuickFilter

A modelled `QuickFilter` control attached to a Grid. Search-as-you-type, with a column-selector drop-down that shows filterable columns.

### Required setup

| Property                | Value / notes                                                                       |
|-------------------------|-------------------------------------------------------------------------------------|
| `TargetControl`         | The Grid (or other collection) it filters. **Required** — leaving blank disables the column selector. |
| `DefaultColumn`         | The column the QuickFilter targets by default — pick the column users scan first   |
| Subpattern membership   | Lives inside a `Custom Filter Group` (variant *Custom and Quick Filters*) or directly above a grid in patterns that have a `SidePanel` |

### Non-grid collections (trees, etc.)

Leave `TargetControl` blank, override `applyFilter()` on the QuickFilter, and apply the filter to the target collection in code.

### Why no column selector?

If the column selector is missing, the most likely causes are:
- `TargetControl` is not set.
- The grid has no filterable columns (controls bound to data methods or non-text controls are not filterable).

---

## 4. Grid column filtering

Click the column header in any Grid → an Excel-like drop-down appears with filter and sort options for that column. Behaviour mirrors the Filter pane's per-field operators. Only **SQL-backed columns** support this — computed (display method) columns can't be filtered or sorted via grid headers, Filter pane, or QuickFilter.

---

## 5. Advanced filter / sort

Open with `Ctrl+Shift+F3` (or from the standard right-click menu). Opens a dialog showing the **Microsoft Dynamics AX 2012-style** query editor with full power:

- Filter on columns that aren't visible on the form.
- Add ranges across multiple joined data sources.
- Sort by multiple columns.
- Use full **advanced query syntax** (see §6).

---

## 6. Advanced query syntax (cheat sheet)

| Operator              | Syntax                            | Example                                              |
|-----------------------|-----------------------------------|------------------------------------------------------|
| Equal                 | `value`                           | `Smith`                                              |
| Not equal             | `!value`                          | `!Smith`                                             |
| In set                | `v1,v2,v3`                        | `circle,square`                                      |
| Range                 | `from..to`                        | `1..10` / `A..C` (strings range A* and B*, exactly C) |
| Less / less-equal     | `<value` / `..value`              | `<100` / `..1000`                                    |
| Greater / greater-eq  | `>value` / `value..`              | `>100` / `1000..`                                    |
| Contains              | `*value*`                         | `*shop*`                                             |
| Begins with           | `value*`                          | `shop*`                                              |
| Today                 | `T`                               | `T`                                                  |
| Blank                 | `""` (two double quotes)          | `""`                                                 |
| Embedded query        | `((expr))`                        | `((AccountNum LIKE "US*") && (DirPartyTable.Name LIKE "Cont*"))` |
| SysQueryRangeUtil     | `(method(args))`                  | `(yearRange(-2,0))` — invoices due in the last 2 years |

In the **Filter pane** and **grid column filter**, choose operators from a list — the symbols above are inferred. The **matches** operator preserves the literal symbols for advanced cases.

---

## 7. Custom Filter Group subpattern (recap)

Used when a developer needs to model a small set of input controls (≤5) above a grid that apply custom-coded filters. Two flavours:

1. **Custom Filters** — QuickFilter is optional.
2. **Custom and Quick Filters** — QuickFilter is mandatory.

Allowed input types: `StringEdit with Lookups`, `DateEdit`, `ReferenceGroup`, `ComboBox`, `CheckBox`, `QuickFilter`. Inputs in a Custom Filter Group must **not** have a `DataSource`/`DataField` assigned — they drive `QueryFilter` (not `QueryBuildRange`).

---

## 8. UX guidelines

1. **Custom filters** are limited to **5 max** above any list. Pre-populate the Filter pane with the fields users actually filter by instead.
2. The QuickFilter defaults to **name or description** column on most patterns; to the **most-likely-filtered column** on the Details Master / Details Transaction main grid.
3. Default focus on a List Page or Details main grid is **in the QuickFilter** when the form opens.
4. Filter operators auto-translate query symbols — keep this in mind when explaining behaviour to users.

---

## 9. Do / Don't

**Do**
- Set `TargetControl` on every modelled QuickFilter.
- Pre-populate the Filter pane in `form init()` for fields that should always be visible.
- Use the right tool: QuickFilter for one column, Custom Filter Group for ≤5 specific fields, Filter pane for general, Advanced filter for the rare case.

**Don't**
- Don't model more than 5 custom filter fields above a list — use the Filter pane.
- Don't bind Custom Filter Group inputs to a data source — they drive `QueryFilter` programmatically.
- Don't try to filter on display-method / computed columns — they're not SQL-backed.

---

## 10. AOT mapping (dev handoff)

| Spec field             | AOT location                                                                        | Notes                                          |
|------------------------|-------------------------------------------------------------------------------------|------------------------------------------------|
| QuickFilter             | `Form > Design > <FilterGroup> > QuickFilter`                                       | `TargetControl = <GridName>`, `DefaultColumn = <FieldName>` |
| Custom Filter Group     | Right-click `<Group>` → Apply pattern → **Custom Filters** / **Custom and Quick Filters** | Inputs not bound to a data source              |
| Filter pane defaults    | `form init() { super(); SysQuery::findOrCreateRange(…); }`                          | Adds empty ranges to make fields visible       |
| Range status            | `<DataSource>.<Range>.RangeStatus = Locked | Hidden`                                | Controls user interaction with the filter      |
| Advanced filter         | System-provided — no model work                                                     | Available everywhere via `Ctrl+Shift+F3`       |

---

## 11. Figma component definitions

- `Pattern/FilterPane` — the slide-in panel; variants for `State`: Hidden / Visible
- `Control/QuickFilter` — the single-line input with column-selector chevron; variants for `State`: Rest / Hover / Focus / Disabled / WithValue
- `Composite/GridColumnFilter` — the drop-down dialog from a column header; variants for operator and validation
- `Pattern/AdvancedFilterDialog` — the AX-2012-style query editor dialog

---

## 12. References

- Microsoft Learn — [Filtering options](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/filtering)
- Microsoft Learn — [Advanced filtering and query syntax](https://learn.microsoft.com/dynamics365/fin-ops-core/fin-ops/get-started/advanced-filtering-query-options)
- Microsoft Learn — [Custom Filter Group subpattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/custom-filter-group-subpattern)
- Microsoft Learn — [General form guidelines](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/general-form-guidelines)
