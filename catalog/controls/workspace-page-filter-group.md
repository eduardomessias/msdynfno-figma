# Workspace Page Filter Group — subpattern

> **Microsoft references:**
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/workspace-page-filter-group-subpattern
>
> **AOT subpattern:** `WorkspacePageFilterGroup`
> **Figma component:** `Subpattern/WorkspacePageFilterGroup`
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-07-01

---

## 1. Purpose

The **top-level filter bar on an Operational Workspace** that drives all sections simultaneously. When the user changes a filter here (date range, legal entity, worker group), every tile, chart, list, and related-links section in the workspace refreshes to reflect the new scope.

There is at most **one** Workspace Page Filter Group per workspace. It sits between the ActionPane (if present) and the first Panorama section.

---

## 2. Anatomy

```
WorkspacePageFilterGroup (Group)
├── Filter 1 (ComboBox / ReferenceGroup / DateEdit)
├── Filter 2
├── Filter N  (up to ~5)
└── Apply button (optional)
```

The outer group is full-width and horizontally lays out the filter controls left-to-right, with an optional Apply button at the right end.

---

## 3. Key properties

| Property / Location             | Notes                                                                                       |
|---------------------------------|---------------------------------------------------------------------------------------------|
| Filter controls `DataSource`    | **Empty** — controls are unbound; filter application happens in X++ via `modified()`       |
| Filter controls `DataField`     | **Empty**                                                                                    |
| Button `NeedsRecord`            | `No` (Apply button acts on the workspace scope, not a selected record)                      |
| Max filter controls             | 1–5; typically 2–3 (date period + legal entity is the most common combination)              |
| `Group.SubPattern`              | `WorkspacePageFilterGroup`                                                                  |

---

## 4. UX guidelines

### What to filter on

1. **Date period is the most common filter** — "This month", "This quarter", "Custom range". Use a `ComboBox` for a fixed period list, or paired `DateEdit` controls for a custom range.
2. **Legal entity** is the second most common — particularly relevant in multi-company setups. Use a `ReferenceGroup` or `ComboBox` populated with accessible legal entities.
3. **Department, cost centre, or worker group** — for workspaces scoped to a specific organisational unit. Use a `ReferenceGroup`.
4. **Keep the filter set small.** 2–3 filters cover the vast majority of workspace use cases. If more filtering is needed, provide a section-level [Filters and Toolbar](./filters-and-toolbar.md) per section.

### Filter behaviour

5. **Instant apply vs Apply button:**
   - Use instant apply (onChange) when filters are independent — changing one doesn't constrain another.
   - Use an explicit Apply button when filters interact (e.g. selecting "By department" reveals a department picker, and only after both are set should the data refresh).
6. **Preserve filter values across sessions.** Use `SysLastValue` to persist the user's last-used filter combination. Don't reset to defaults on every form open.
7. **Default to the most common scope.** For most workspaces: current period + current legal entity.

### Driving sections

8. **All sections must respond to the workspace filter.** If a section can't meaningfully filter by the workspace criteria, exclude it from the workspace or add a note in the section header explaining the scope.
9. **Filter change triggers workspace refresh** — typically via `element.updateDesign('filter')` in the `modified()` event of each filter control, which re-runs the workspace's initialization logic.

---

## 5. Do / Don't

**Do**
- Keep to 2–3 filters — date period and legal entity cover most cases.
- Persist filter values using `SysLastValue`.
- Default to current period + current legal entity.
- Make all sections respond to the workspace filter.

**Don't**
- Don't bind filter controls to a DataSource / DataField.
- Don't add more than 5 filter controls to the workspace-level filter.
- Don't add section-level filters that duplicate the workspace filter — use them for additional narrowing only.
- Don't reset filters on every form open.

---

## 6. AOT mapping (dev handoff)

| Spec field               | AOT location                                                                        | Notes                                                         |
|--------------------------|-------------------------------------------------------------------------------------|---------------------------------------------------------------|
| Subpattern               | `Group.SubPattern = WorkspacePageFilterGroup`                                        | Applied on the Group placed immediately after the ActionPane  |
| Filter controls          | Unbound `ComboBox` / `ReferenceGroup` / `DateEdit` controls                         | No DataSource / DataField — driven by code                    |
| Apply button             | Optional `MenuItemButton` or `Button` at end of filter row                          | Use `NeedsRecord=No`                                          |
| X++ refresh              | `modified()` on each filter → `element.updateDesign('filter')` → workspace re-init | SysLastValue stores per-user defaults                         |
| `SysLastValue`           | `SysLastValue::set()` and `SysLastValue::get()` calls in `init()` and `modified()`  | Preserves filter state across sessions                        |

---

## 7. Figma component definition

- **Name:** `Subpattern/WorkspacePageFilterGroup`
- **Layout:** Full-width horizontal strip of filter controls, optional Apply button at right
- **Slots:**
  - Filter controls (2–3 preferred): labelled ComboBox or ReferenceGroup per filter criterion
  - Apply button (optional): right-aligned
- **Variants:**
  - `Filter count`: 2 / 3 / 4
  - `Apply button`: Yes / No
  - `Date filter style`: Period picker / Date range (From–To)
- **Usage note:** Place at the top of the Operational Workspace frame, between the ActionPane and the first Panorama section. Fill with realistic filter values: "Period: This month ▾", "Legal entity: USSI ▾". The frame should show the workspace in a filtered state (not a blank/empty state).

---

## 8. References

- Microsoft Learn — [Workspace Page Filter Group subpattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/workspace-page-filter-group-subpattern)
- [Filters and Toolbar subpattern](./filters-and-toolbar.md) — section-level filters within a workspace section
- [Operational Workspace pattern](../patterns/operational-workspace.md) — the host pattern
- [Custom Filter Group subpattern](./custom-filter-group.md) — equivalent for non-workspace forms
