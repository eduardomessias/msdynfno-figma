# Custom Filter Group — subpattern

> **Microsoft references:**
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/custom-filter-group-subpattern
>
> **AOT subpattern:** `CustomFilters` / `CustomAndQuickFilters`
> **Figma component:** `Subpattern/CustomFilterGroup` (+ `…/CustomAndQuickFilters`)
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-07-01

> **See also:** [FilterPane and friends](./filter-pane.md) — the broader filtering surface taxonomy and when to choose each option.

---

## 1. Purpose

A row of **1–5 pre-configured filter controls** (dropdowns, date pickers, lookup fields) positioned above a Grid, between the QuickFilter and the grid column headers. Provides focused, fast filtering without opening the full FilterPane.

Two variants:

| Variant | QuickFilter | Use when |
|---------|-------------|----------|
| **Custom Filters** | Optional | The form has its own QuickFilter placed separately, or no QuickFilter is needed |
| **Custom and Quick Filters** | Required (mandatory) | The QuickFilter must appear as part of the filter row — no separate QuickFilter placement |

---

## 2. Anatomy

```
CustomFilter (Group)                  ← the container with subpattern applied
├── QuickFilter                       ← required in CustomAndQuickFilters; optional in CustomFilters
├── FilterGroup (Group, 0..N)
│   └── FilterField (1..N)
└── FilterField (0..N)                ← ungrouped filter controls
```

**Allowed control types in the filter row:**

- `StringEdit` (with lookup override for code fields)
- `DateEdit` / `UtcDateTimeEdit`
- `ReferenceGroup`
- `ComboBox`
- `CheckBox`
- `QuickFilter` (for the CustomAndQuickFilters variant)

---

## 3. Key properties

| Property / Location           | Notes                                                                                                |
|-------------------------------|------------------------------------------------------------------------------------------------------|
| Filter control `DataSource`   | **Must be empty** — filter controls drive `QueryFilter` programmatically, not via data binding      |
| Filter control `DataField`    | **Must be empty** — same reason as above (BP warning if set)                                        |
| `QueryFilter` usage           | Use `QueryFilter`, not `QueryBuildRange`, in the X++ handler — works correctly with outer-joined fields |
| Max controls                  | 1–5 filter controls (≤5 is the BP guideline)                                                        |
| QuickFilter placement         | In the `CustomAndQuickFilters` variant, the QuickFilter must be the first child                     |

---

## 4. UX guidelines

### When to use Custom Filter Group

1. **Use when the user frequently filters by the same 1–5 criteria** — Status, Date range, Customer group. These criteria are domain-specific; the QuickFilter covers text-search and the FilterPane covers ad-hoc multi-criteria filtering.
2. **Do not add more than 5 controls.** Beyond 5, consider whether the FilterPane is the right surface instead.
3. **Prefer ComboBox for status/category filters** — closed list, instant apply, no free-text ambiguity.
4. **Prefer date range pickers** (two `DateEdit` controls, "From" and "To") for date-driven filtering.

### Filter behaviour

5. **Filters apply instantly or on Apply button click** — pick one model and be consistent. Instant (onChange) is common for status dropdowns; Apply button is appropriate when multiple criteria are likely to be set together.
6. **No DataSource/DataField on filter controls** — they operate on the query, not on a record. This is a common dev error that causes BP warnings.
7. **Use `QueryFilter`, not `QueryBuildRange`** — `QueryBuildRange` breaks on outer-joined data sources (a common source of bugs on forms with optional related tables).

### Integration with other filter surfaces

8. **If a FilterPane is present**, the Custom Filter Group should complement it with the 1–2 most frequently used criteria. Don't duplicate the same filters in both places.
9. **If no FilterPane**, the Custom Filter Group + QuickFilter together form the complete filter surface. Choose the `CustomAndQuickFilters` variant so the QuickFilter is adjacent.

---

## 5. Do / Don't

**Do**
- Leave `DataSource` and `DataField` blank on all filter controls.
- Use `QueryFilter` in the X++ `modified()` override on each filter control.
- Use ComboBox for closed-list criteria (Status, Type).
- Limit to 5 controls.

**Don't**
- Don't bind filter controls to a data source — they are query-driven, not record-bound.
- Don't use `QueryBuildRange` — use `QueryFilter` instead.
- Don't add free-text StringEdit fields for key columns — use a ReferenceGroup or lookup.
- Don't exceed 5 filter controls in the row.

---

## 6. AOT mapping (dev handoff)

| Spec field                | AOT location                                                                            | Notes                                                       |
|---------------------------|-----------------------------------------------------------------------------------------|-------------------------------------------------------------|
| Subpattern                | `Group.SubPattern = CustomFilters` or `CustomAndQuickFilters`                           | Applied in the designer on the outer Group                  |
| Filter controls           | `StringEdit / DateEdit / ReferenceGroup / ComboBox / CheckBox` inside the Group        | No DataSource or DataField set on these controls            |
| X++ filter handler        | `<FilterControl>.modified()` or `clicked()` → `element.query()` → `QueryFilter` add   | Use `QueryFilter`; never `QueryBuildRange`                  |
| QuickFilter               | First child in `CustomAndQuickFilters`; optional elsewhere                              | Searches `QuickFilter.searchField`-configured columns       |

---

## 7. Figma component definition

- **Name:** `Subpattern/CustomFilterGroup`
- **Slots:** 1–5 filter controls in a horizontal row; optional QuickFilter at the left
- **Variants:**
  - `Variant`: CustomFilters / CustomAndQuickFilters
  - `Filter count`: 1 / 2 / 3 / 4–5
  - `With Apply button`: Yes / No
- **Usage note:** Show realistic filter labels and representative values (Status = "Confirmed ▾", Date from/to = date range). Place this row between the QuickFilter area and the grid column headers.

---

## 8. References

- Microsoft Learn — [Custom Filter Group subpattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/custom-filter-group-subpattern)
- [FilterPane and friends](./filter-pane.md) — when to choose QuickFilter vs Custom Filter Group vs FilterPane vs Advanced Filter
- [Grid control](./grid.md) — the grid this filter row sits above
