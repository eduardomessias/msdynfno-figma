# Simple List form pattern

> **Microsoft pattern reference:** https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/simple-list-form-pattern
> **D365FO `Form.Design.Style` value:** `SimpleList`
> **D365FO `Form.Design.Pattern` value:** `SimpleList`
> **Figma page:** `Patterns / Simple List`
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-05-21

---

## 1. Usage — when to choose this pattern

The simplest editable list form, for **simple entities** — 6 or fewer fields, no parent/child relationships. Exceptions allow up to ~15 fields when an entity is still conceptually simple (e.g. enumerations, code tables). View mode is the default; the user toggles to edit when needed.

Use Simple List when:
- The entity has ≤6 fields (loose upper bound: 15) and **no child collections**.
- You only need a grid plus filters — no per-record details form is justified.
- Common examples: Customer groups, Vendor groups, Payment terms (small variant), Number sequence references that don't need details.

If the entity grows beyond this, escalate to [Simple List and Details](./simple-list-details.md).

### Related patterns

- [Simple List and Details](./simple-list-details.md) — when the entity has 6+ fields or child collections
- [List Page](./list-page.md) — when records are read-only and back zero or many Details forms

---

## 2. Anatomy — the required structure

```
Design
├── ActionPane                              (ActionPane)
├── Custom Filter                           (Group)
│   ├── QuickFilter
│   └── OtherFilters ($Field) [0..N]
├── TabularGrid                             (Grid)
└── Footer (Group) [Optional]
```

| Node           | Required? | Purpose                                              | Figma component               |
|----------------|-----------|------------------------------------------------------|-------------------------------|
| `ActionPane`   | Yes       | Foundation provides New/Delete/Edit — do not duplicate | `Pattern/ActionPane`          |
| `CustomFilter` | Yes       | QuickFilter + optional discrete filter fields        | `Subpattern/CustomFilterGroup`|
| `TabularGrid`  | Yes       | Editable grid — ≤15 columns                           | `Control/Grid`                |
| `Footer`       | Optional  | Aggregates / totals strip below the grid             | `Pattern/Footer`              |

### Allowed subpatterns

- [Custom Filter Group](../controls/custom-filter-group.md) — on the `CustomFilter` group container

---

## 3. Core components — BP warnings to resolve

- [ ] `Design.Caption` is not empty
- [ ] `Design.DataSource` is not empty
- [ ] `Grid.DataSource` is set
- [ ] Form is referenced by at least one menu item
- [ ] `Design.DataSource == Grid.DataSource`
- [ ] Primary key field of the primary data source has `IgnoreEDTRelation = Yes`
- [ ] The grid does not contain more than 15 fields

---

## 4. UX guidelines

1. **QuickFilter** defaults to the name or description column.
2. ≤15 columns in the grid.
3. No duplicate **New** / **Delete** buttons (foundation provides them).
4. Page title in **plural** form.
5. When the grid has no data, **do not** automatically add a new record.
6. Supports multi-select in the grid.
7. When used as a dependent form, the parent record context is automatically shown above the form caption — do **not** model your own page title group.

### Smartbox overrides

_None yet._

---

## 5. Do / Don't

**Do**
- Use for short, flat reference data (groups, types, statuses, codes).
- Default the QuickFilter to the column users actually scan first.
- Add a Footer group for sums/counts when the data is numeric.

**Don't**
- Don't grow the form by adding child collections — promote to Simple List and Details.
- Don't add an explicit Edit toggle button — the foundation owns that affordance.
- Don't auto-insert a blank row when the table is empty.

---

## 6. AOT metadata mapping (dev handoff)

| Spec field            | AOT location                                                | Notes                                            |
|-----------------------|-------------------------------------------------------------|--------------------------------------------------|
| Pattern               | `Form > Design > Pattern = SimpleList`                      | Apply via designer right-click                   |
| Primary data source   | `Form > Data Sources > <DS>`                                | Must match `Design.DataSource` and `Grid.DataSource` |
| Grid columns          | `Form > Design > TabularGrid > <FormControl>`               | ≤15; first column typically Name or Code         |
| QuickFilter target    | `Form > Design > CustomFilter > QuickFilter.targetControl`  | Defaults to name/description column              |
| ActionPane buttons    | `Form > Design > ActionPane > … > MenuItemButton`           | App-specific actions only                        |

---

## 7. Worked example

_To come._

---

## 8. References

- Microsoft Learn — [Simple List form pattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/simple-list-form-pattern)
- Microsoft Learn — [General Form Guidelines](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/general-form-guidelines)
- Microsoft Learn — [Custom Filter Group subpattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/custom-filter-group-subpattern)
