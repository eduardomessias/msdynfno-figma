# Filters and Toolbar — subpattern

> **Microsoft references:**
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/filters-and-toolbar-subpattern
>
> **AOT subpattern:** `FiltersAndToolbar/Inline` / `FiltersAndToolbar/Stacked`
> **Figma component:** `Subpattern/FiltersAndToolbar/Inline` (+ `…/Stacked`)
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-07-01

---

## 1. Purpose

A workspace-specific subpattern that combines **filter controls and action buttons in a single band** at the top of a Panorama section or workspace content area. The filters let the user scope the data shown in the section; the buttons trigger actions on that data set.

Two layout variants:

| Variant | Layout | Use when |
|---------|--------|----------|
| **Inline** | Filters and buttons on a single row | Fewer than 4 filter controls + fewer than 4 buttons fit comfortably on one line |
| **Stacked** | Filters on the top row, buttons on the row below | More filters or buttons than fit on one line |

> Distinct from the [Custom Filter Group](./custom-filter-group.md) subpattern, which is for non-workspace forms (List Page, Details Master). Filters and Toolbar is specifically designed for the workspace context.

---

## 2. Anatomy

### Inline variant

```
FilterAndToolbarGroup (Group)
├── FilterGroup (Group)               ← filter controls (left)
│   └── filter controls (1..N)
└── ButtonGroup (ButtonGroup)         ← action buttons (right)
    └── MenuItemButton | MenuButton (1..N)
```

### Stacked variant

```
FilterAndToolbarGroup (Group)
├── FilterGroup (Group)               ← filter controls row (top)
│   └── filter controls (1..N)
└── ToolbarGroup (Group)              ← toolbar row (below filters)
    └── ButtonGroup → buttons
```

---

## 3. Key properties

| Property / Location             | Notes                                                                                       |
|---------------------------------|---------------------------------------------------------------------------------------------|
| Filter controls `DataSource`    | **Empty** — filters drive the workspace data source via code, not via field binding        |
| Filter controls `DataField`     | **Empty** — same as Custom Filter Group                                                     |
| Button `NeedsRecord`            | `No` for workspace-scoped actions (act on the filtered set, not a single selected record)  |
| Max filter controls             | 1–5; more than 5 → split into a separate `WorkspacePageFilterGroup`                        |
| Max buttons                     | 1–5; more → use a MenuButton dropdown                                                       |

---

## 4. UX guidelines

1. **Workspace-scoped, not record-scoped.** Filters in this band narrow down the workspace data set (e.g. "show data for this legal entity / date range"). Actions in this band trigger workspace-level operations (e.g. "Run calculation", "Export", "Refresh"). Neither operates on a single selected record.
2. **Prefer Inline for simple cases** (1–2 filters + 1–2 buttons). Stacked is appropriate when the filter row alone uses more than half the available width.
3. **Filters apply immediately or via an explicit Apply button.** If filters interact with each other (e.g. selecting "By department" reveals a department picker), use an Apply button to avoid partial-state updates triggering data queries.
4. **Don't duplicate the workspace-level filter.** The [Workspace Page Filter Group](./workspace-page-filter-group.md) at the top of the workspace drives all sections. Filters here are section-specific overrides, not a second global filter. If all sections need the same filter, it belongs in the workspace filter group.

---

## 5. Do / Don't

**Do**
- Leave `DataSource` and `DataField` empty on all filter controls.
- Use the Inline variant for ≤3 filters + ≤3 buttons.
- Use the Stacked variant when the single row is too crowded.
- Make actions workspace-scoped (act on the data set, not a selected row).

**Don't**
- Don't bind filter controls to a data source.
- Don't use this subpattern on non-workspace forms — use [Custom Filter Group](./custom-filter-group.md).
- Don't duplicate the workspace-level filter set from `WorkspacePageFilterGroup`.
- Don't exceed 5 filter controls or 5 buttons.

---

## 6. AOT mapping (dev handoff)

| Spec field              | AOT location                                                                                  | Notes                                         |
|-------------------------|-----------------------------------------------------------------------------------------------|-----------------------------------------------|
| Subpattern              | `Group.SubPattern = FiltersAndToolbar/Inline` or `FiltersAndToolbar/Stacked`                  | Applied in designer on the outer Group        |
| Filter controls         | `StringEdit / DateEdit / ComboBox / ReferenceGroup` — no DS / DataField binding              | Programmatic filter application in X++        |
| Button binding          | `MenuItemButton.MenuItemName = <action menu item>`                                            | Standard menu item security flow              |
| X++ integration         | `modified()` on filter controls → update workspace data source query → `element.updateDesign()` | Pattern for workspace filter refresh         |

---

## 7. Figma component definition

- **Name:** `Subpattern/FiltersAndToolbar/Inline` (+ `/Stacked`)
- **Slots:**
  - `FilterGroup` — 1–5 filter controls (ComboBox, DateEdit, ReferenceGroup)
  - `ButtonGroup` — 1–5 action buttons
- **Variants:**
  - `Layout`: Inline / Stacked
  - `Filter count`: 1 / 2 / 3 / 4–5
  - `Button count`: 1 / 2 / 3
  - `Apply button`: Yes / No
- **Usage note:** Show inside a Panorama section header. Filter labels and values should reflect the workspace domain (e.g. "Period: This month ▾", "Legal entity: USSI ▾"). Buttons should be domain actions ("Refresh", "Export to Excel").

---

## 8. References

- Microsoft Learn — [Filters and Toolbar subpattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/filters-and-toolbar-subpattern)
- [Custom Filter Group subpattern](./custom-filter-group.md) — equivalent for non-workspace forms
- [Workspace Page Filter Group subpattern](./workspace-page-filter-group.md) — workspace-level (global) filter
- [Operational Workspace pattern](../patterns/operational-workspace.md) — the host pattern
