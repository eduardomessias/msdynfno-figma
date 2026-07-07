# Simple List and Details form pattern

> **Microsoft pattern reference:** https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/simple-list-details-form-pattern
> **D365FO `Form.Design.Style` value:** `SimpleListDetails`
> **D365FO `Form.Design.Pattern` value:** `SimpleListDetailsListGrid` (default) / `SimpleListDetailsTabularGrid` / `SimpleListDetailsTree`
> **Figma page:** `Patterns / Simple List and Details`
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-05-21

---

## 1. Usage — when to choose this pattern

For entities of **medium complexity** — typically 6+ fields with 0–5 child collections. Sits between [Simple List](./simple-list.md) (≤6 fields, no children) and [Details Master](./details-master.md) (many fields, FastTabs galore). The form is split: a navigation list on the left, a Details section on the right (Header group + Details Tab).

Three variants, picked by the shape of the "list":

1. **Simple List and Details — List Grid** (default) — 2–3 fields per row in the navigation list. Pick this whenever possible.
2. **Simple List and Details — Tabular Grid** — when you need 4–5 fields, or you need to compare a sequence of values across rows (e.g. effective-date rows, route step numbers). Tabular grid here is **not editable**.
3. **Simple List and Details — Tree** — only when the "list" part is genuinely hierarchical (e.g. fiscal calendars, BOM lines as a tree).

### Related patterns

- [Simple List](./simple-list.md) — simpler entities with no children
- [Details Master](./details-master.md) — when the entity is heavy enough to need FastTabs
- [Table of Contents](./table-of-contents.md) — setup-style forms with loosely related sections

---

## 2. Anatomy — the required structure

```
Design
├── ActionPane                              (ActionPane)
├── NavigationList                          (Group)
│   ├── QuickFilter
│   ├── CustomFilterGroup (Group) [Optional]
│   └── ListStyleGrid (Grid) | Tree | TabularGrid (Grid)
├── VerticalSplitter (Group)                [only for Tree / TabularGrid variants]
├── DetailsHeader                           (Group)
└── DetailsTab                              (Tab)
```

| Node                | Required? | Purpose                                                | Figma component                  |
|---------------------|-----------|--------------------------------------------------------|----------------------------------|
| `ActionPane`        | Yes       | Foundation provides New/Delete/Edit                    | `Pattern/ActionPane`             |
| `NavigationList`    | Yes       | Left list. Variant picks the grid style (list/tabular/tree) | `Subpattern/NavigationList`  |
| `QuickFilter`       | Yes       | Default-targeted to name/description                   | `Control/QuickFilter`            |
| `DetailsHeader`     | Yes       | Horizontal field group repeating the list fields       | `Subpattern/HorizontalFieldsGroup` |
| `DetailsTab`        | Yes       | Tabs holding the remaining fields                      | `Control/Tab`                    |
| FactBoxes (`Parts`) | Optional  | Right-pane related-info cards                          | `Pattern/FactBox`                |

### Allowed subpatterns

- [Fields and Field Groups](../controls/fields-and-field-groups.md)
- [Toolbar and List](../controls/toolbar-and-list.md)
- [Toolbar and Fields](../controls/toolbar-and-fields.md)
- [Nested Simple List and Details](../controls/nested-simple-list-details.md) — for embedded child collections

---

## 3. Core components — BP warnings to resolve

- [ ] `Design.Caption` matches the `Name` label on the table
- [ ] `Design.DataSource == Grid.DataSource`
- [ ] Primary data source has `InsertIfEmpty = No`
- [ ] `ActionPane.DataSource == Grid.DataSource`
- [ ] `Grid.DataSource` is set to the primary data source

---

## 4. UX guidelines

1. Page title is **plural**, accurately describes the entity.
2. No duplicate **New** / **Delete** buttons.
3. QuickFilter defaults to the name/description column.
4. **At most 2** custom filter fields above the list.
5. The left edge must be a list-style grid, tabular grid, or tree:
   - List-style grids: rows ≤3 lines, 2–5 fields visible.
   - Tabular grids (variant): **not** editable.
   - Empty list: do **not** auto-add a new record.
6. **Details** section: list fields appear as the **first fields in the DetailsHeader**, in the same order as the list — so the user can edit them and see their labels.
7. A SL+D form must **not** contain standard tabs to group fields.

### Smartbox overrides

_None yet._

---

## 5. Do / Don't

**Do**
- Prefer the **List Grid** variant. Only fall back to Tabular when comparing rows is a real user task.
- Use a Tree only when the data really is hierarchical.
- Mirror the list fields at the top of the Details header.

**Don't**
- Don't grow standard Tabs in the details — promote to Details Master if you need them.
- Don't put more than two custom filter fields above the list.
- Don't make the Tabular Grid variant editable.

---

## 6. AOT metadata mapping (dev handoff)

| Spec field            | AOT location                                                                                          | Notes                                       |
|-----------------------|-------------------------------------------------------------------------------------------------------|---------------------------------------------|
| Pattern               | `Form > Design > Pattern = SimpleListDetailsListGrid` (or `…TabularGrid` / `…Tree`)                   | Apply via designer                          |
| Primary data source   | `Form > Data Sources > <DS>` with `InsertIfEmpty = No`                                                | Must match Design / ActionPane / Grid datasources |
| Navigation list       | `Form > Design > NavigationList > ListStyleGrid | TabularGrid | Tree`                                 | 2–5 fields                                   |
| Details header        | `Form > Design > DetailsHeader > <fields>`                                                            | Mirror list fields                          |
| Details tab           | `Form > Design > DetailsTab > <TabPage>`                                                              | Each TabPage uses an appropriate subpattern |
| ActionPane buttons    | `Form > Design > ActionPane > … > MenuItemButton`                                                     | —                                            |

---

## 7. Worked example

_To come._

---

## 8. References

- Microsoft Learn — [Simple List and Details form pattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/simple-list-details-form-pattern)
- Microsoft Learn — [Nested Simple List and Details subpattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/nested-simple-list-details-subpattern)
- Microsoft Learn — [General Form Guidelines](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/general-form-guidelines)
