# List Page form pattern

> **Microsoft pattern reference:** https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/list-page-form-pattern
> **D365FO `Form.Design.Style` value:** `ListPage`
> **D365FO `Form.Design.Pattern` value:** `ListPage`
> **Figma page:** `Patterns / List Page`
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-05-20

---

## 1. Usage — when to choose this pattern

A List Page presents a set of records optimized for browsing: the user searches, filters, sorts, and then takes an action on a record. The grid sits in the centre, FactBoxes with related info on the right, and ActionPane buttons on top.

**Microsoft's current guidance is to use List Page only when:**

- There is **no** backing Details page for the records, **OR**
- The list is backed by **multiple different** Details pages (for example, project quotations and sales quotations rendered together).

When there is a 1:1 list↔details relationship, Microsoft now merges the List Page directly into [**Details Master**](./details-master.md) or [**Details Transaction**](./details-transaction.md). Do **not** model a separate List Page in that case — performance and bulk-edit suffer.

### Related patterns

- [Details Master](./details-master.md) — preferred when there is exactly one Details form for the records
- [Details Transaction](./details-transaction.md) — preferred when the records are header+lines transactions
- [Simple List](./simple-list.md) — for simple entities (≤6 fields, no children)

---

## 2. Anatomy — the required structure

From the Microsoft pattern article:

```
Design
├── ActionPane                       (ActionPane)
├── Custom Filter                    (Group)
│   ├── Quick Filter                 (QuickFilter)
│   └── OtherFilters                 ($Field) [0..N]
└── Grid                             (Grid)
```

FactBoxes live in `Form.Parts` and render on the right side automatically.

| Node                | Required? | Purpose                                                                  | Figma component              |
|---------------------|-----------|--------------------------------------------------------------------------|------------------------------|
| `ActionPane`        | Yes       | Top toolbar with tabs grouping command buttons (New is provided by foundation; do not duplicate) | `Pattern/ActionPane`         |
| `Custom Filter`     | Yes       | Strip above the grid for the QuickFilter + optional discrete filter fields | `Subpattern/CustomFilterGroup` |
| `Quick Filter`      | Yes       | Single search-as-you-type filter bound to a default column               | `Control/QuickFilter`        |
| `OtherFilters`      | Optional  | Discrete custom filter controls (combo, date-range, segmented entry)     | `Control/*Edit`              |
| `Grid`              | Yes       | The list itself. ≤15 columns. First textual column hyperlinks to Details (or is read-only when no details exist) | `Control/Grid`               |
| FactBoxes (in `Parts`) | Optional | Related-record cards/grids on the right                                | `Pattern/FactBox`            |

### Allowed subpatterns

- [Custom Filter Group](../controls/custom-filter-group.md) — applied on the `Custom Filter` group container

---

## 3. Core components — Best-Practice (BP) warnings to resolve

Zero BP warnings before merge. From Microsoft's pattern article:

- [ ] `Design.Caption` is not empty
- [ ] Form is referenced by at least one menu item
- [ ] `TabPage.Caption` is not empty (where TabPages exist)
- [ ] `TabPage.DataSource` is not empty (where TabPages exist)
- [ ] Primary data source has `AllowEdit = No`, `AllowCreate = No`, `AllowDelete = Yes`
- [ ] `Grid.DefaultAction` references the button that opens the child form (when a child form exists)
- [ ] `Grid.DefaultLabelAction` references a label shown in the grid context menu

---

## 4. UX guidelines

From Microsoft's pattern article (carry forward to mocks **and** to the implementation):

1. ≤ 15 fields in the grid.
2. The first textual / data column is a hyperlink that opens the Details form (set the grid's default action).
3. A **QuickFilter** appears above the list. By default it filters the most-likely field for the scenario.
4. There must be no duplicate **New** and **Delete** buttons (the foundation provides them).
5. The List page must have a link in the Main Menu.
6. Default focus is in the QuickFilter on open.
7. **Page title area:**
   - Title is **plural**.
   - Primary list pages: title is the entity name (e.g. "Customers").
   - Secondary list pages: title reflects the activity / status (e.g. "Open customer invoices").
8. **Grid column order:**
   - Transactional entities: `ID` first, then master entity `ID` + `Name`.
   - Master entities: `Name` first, then `ID`.
9. ActionPane: follow the [General Form Guidelines](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/general-form-guidelines).
10. FactBoxes: follow [FactBox Form Patterns](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/factbox-form-patterns).

### Smartbox overrides

_None yet. Any divergence here must be approved by the architect and recorded as an ADR._

---

## 5. Do / Don't

**Do**

- Pick List Page only when no 1:1 Details exists, or several Details forms back the same list.
- Hyperlink the first column to the Details form.
- Let the foundation render New / Delete / Edit / Save / Refresh / Attachments / Export to Excel — do not duplicate them.
- Keep the grid focused on the user's "find and identify" task — push everything else into FactBoxes.

**Don't**

- Don't add a separate List Page when a Details Master/Transaction already exists for the entity.
- Don't add a Preview pane — it was eliminated. Split into FactBoxes instead.
- Don't allow create / edit on the primary data source — list pages are browse surfaces.
- Don't exceed ~15 visible columns. Push extra fields to FactBoxes or the Details form.

---

## 6. AOT metadata mapping (dev handoff)

| Spec field                | AOT location                                                                   | Notes                                                                 |
|---------------------------|--------------------------------------------------------------------------------|-----------------------------------------------------------------------|
| Pattern                   | `Form > Design > Pattern = ListPage`                                           | Apply via designer right-click → Apply pattern                        |
| Primary data source       | `Form > Data Sources > <DS>` with `AllowEdit=No, AllowCreate=No, AllowDelete=Yes` | BP enforces this                                                  |
| Grid columns              | `Form > Design > Grid > <FormControl bound to DS.field>`                       | First textual column gets `Grid.DefaultAction = <button to details>`  |
| QuickFilter target        | `Form > Design > CustomFilterGroup > QuickFilter.targetControl`                | Bind to the column that the user most often filters on                |
| Page title (Main Menu)    | Display `MenuItem > Label`                                                     | Plural form per UX guidelines                                         |
| FactBoxes                 | `Form > Parts > FormPart > FormName`                                           | Apply [FactBox](./factbox.md) pattern on the target form              |
| ActionPane buttons        | `Form > Design > ActionPane > ActionPaneTab > ButtonGroup > MenuItemButton`    | Bind to an Action / Display / Output menu item; security on menu item |

---

## 7. Worked example

_To come: a Smartbox-specific worked example will land under [`/mocks/`](../../mocks/). The first one will be **Order to cash**._

---

## 8. References

- Microsoft Learn — [List Page form pattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/list-page-form-pattern)
- Microsoft Learn — [General Form Guidelines](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/general-form-guidelines)
- Microsoft Learn — [FactBox Form Patterns](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/factbox-form-patterns)
- Microsoft Learn — [Custom Filter Group subpattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/custom-filter-group-subpattern)
- AX 2012 legacy — [List Page User Experience Guidelines](https://learn.microsoft.com/en-us/dynamicsax-2012/developer/list-page-user-experience-guidelines)
