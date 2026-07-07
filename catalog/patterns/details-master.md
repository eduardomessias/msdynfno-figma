# Details Master form pattern

> **Microsoft pattern reference:** https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/details-master-form-pattern
> **D365FO `Form.Design.Style` value:** `DetailsFormMaster`
> **D365FO `Form.Design.Pattern` value:** `DetailsMaster` (default) or `DetailsMasterStandardTabs`
> **Figma page:** `Patterns / Details Master`
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-05-21

---

## 1. Usage — when to choose this pattern

The primary way to enter and edit data for a **complex master entity**. Header information is organised into **FastTabs** that the user expands/collapses independently. Like Details Transaction, the form bundles a List style navigation grid on the left, the FastTab Details view, and a Grid view (the merged-in former List Page) for bulk editing.

Use Details Master when:
- The entity is a master record with many fields (customers, vendors, items, workers, projects).
- There are **no Lines** — if there are, use [Details Transaction](./details-transaction.md).
- The fields can be grouped into expandable FastTabs.

Two variants:
1. **Details Master** (default) — FastTabs directly on the body.
2. **Details Master w/ Standard Tabs** — when there are >15 FastTabs that need a top-level category Tab. Also the right choice when migrating a former TOC-extended Master Details form.

### Related patterns

- [Details Transaction](./details-transaction.md) — same shape but with Lines
- [Simple List and Details](./simple-list-details.md) — for medium-complexity entities

---

## 2. Anatomy — the required structure

```
Design
├── ActionPane                              (ActionPane)
├── SidePanel                               (Group)
│   ├── QuickFilter
│   ├── CustomFilters (Group) [Optional]
│   └── NavigationList                      (Grid, Style=List)
└── GridDetailsTab                          (Tab, ShowTabs=No)
    ├── TabPageDetails                      (TabPage)
    │   ├── TitleGroup
    │   │   ├── HeaderTitle                 (String)
    │   │   └── EntityStatus (Group) [Optional]
    │   └── DetailsTab                      (Tab, Style=FastTabs)
    │       └── DetailsTabPage              (TabPage 1..N)
    └── GridTabPage                         (TabPage)
        ├── CustomFilterGroup               (Group)
        │   ├── QuickFilter
        │   └── OtherFilters ($Field) [0..N]
        ├── MainGrid                        (Grid)
        └── MainGridDefaultAction           (CommandButton)
```

| Node                | Required? | Purpose                                                   | Figma component                  |
|---------------------|-----------|-----------------------------------------------------------|----------------------------------|
| `ActionPane`        | Yes       | Top toolbar. Foundation provides Save/Refresh/Attachments/Export — do not duplicate. | `Pattern/ActionPane` |
| `SidePanel`         | Yes       | Left navigation list                                      | `Subpattern/SidePanel`           |
| `NavigationList`    | Yes       | List-style grid, ≤3 lines per row                         | `Control/Grid (Style=List)`      |
| `TitleGroup`        | Yes       | `<ID> : <Description>` band                               | `Composite/RecordTitle`          |
| `DetailsTab`        | Yes       | FastTabs containing the entity's fields                   | `Control/FastTab`                |
| `MainGrid`          | Yes       | Bulk-edit grid of all master records                      | `Control/Grid`                   |
| FactBoxes (`Parts`) | Optional  | Right-pane related-info cards                             | `Pattern/FactBox`                |

### Allowed subpatterns

- [Fields and Field Groups](../controls/fields-and-field-groups.md) — on each FastTab page
- [Toolbar and List](../controls/toolbar-and-list.md) — on tab pages with embedded grids
- [Toolbar and Fields](../controls/toolbar-and-fields.md) — on tab pages with embedded fields + actions
- [Nested Simple List and Details](../controls/nested-simple-list-details.md) — embedded child collections
- [Custom Filter Group](../controls/custom-filter-group.md) — on the SidePanel and on GridPanel

---

## 3. Core components — BP warnings to resolve

- [ ] `Design.Caption` is not empty
- [ ] Form is referenced by at least one menu item
- [ ] `TabPage.Caption` is not empty

Plus the foundation-button rule:

- [ ] No app-defined **New / Delete / Save / Refresh / Attachments / Export** buttons (foundation provides them)

---

## 4. UX guidelines

1. No duplicate **New** / **Delete** buttons.
2. Use FastTabs (not standard tabs) to group fields. Use the **Standard Tabs** variant only when FastTabs > 15.
3. Default state: content of the first FastTab fully visible without scrolling.
4. **Page title**: `<ID> : <Description>` (plural form when in main menu).
5. **Navigation list grid**: ≤3 lines per row, ≥2 fields (typically ID + Description).
6. **Grid view (MainGrid)**: 2–15 fields, all mandatory fields present, linked field opens Details, QuickFilter defaulted to most-likely-filtered column.
7. **Master entities**: `Name` first column when ID isn't needed; otherwise `ID` first.

### Smartbox overrides

_None yet. Architect approval required for any divergence._

---

## 5. Do / Don't

**Do**
- Use FastTabs to expose all header fields; let users expand only what they need.
- Bulk-edit common fields directly in the MainGrid.
- Push related-record context into FactBoxes.

**Don't**
- Don't add a Lines collection — switch to Details Transaction if you find yourself needing one.
- Don't model standard tabs inside this pattern unless on the `StandardTabs` variant.
- Don't duplicate foundation buttons in the ActionPane.

---

## 6. AOT metadata mapping (dev handoff)

| Spec field             | AOT location                                                                       | Notes                                                            |
|------------------------|------------------------------------------------------------------------------------|------------------------------------------------------------------|
| Pattern                | `Form > Design > Pattern = DetailsMaster` (or `DetailsMasterStandardTabs`)         | Apply via designer right-click → Apply pattern                   |
| Primary data source    | `Form > Data Sources > <DS>`                                                       | `AllowEdit/Create/Delete` follow BP for the variant              |
| Navigation list cols   | `Form > Design > SidePanel > NavigationList > <FormStringControl>`                 | ≤3 lines per row                                                 |
| FastTabs               | `Form > Design > … > DetailsTab > <TabPage> > Group(s)`                            | Apply [Fields and Field Groups](../controls/fields-and-field-groups.md) on each TabPage |
| MainGrid (bulk edit)   | `Form > Design > … > GridTabPage > MainGrid bound to <DS>`                         | `Grid.DefaultAction = MainGridDefaultAction` → opens DetailsPanel |
| ActionPane buttons     | `Form > Design > ActionPane > … > MenuItemButton`                                  | Bind to menu items; security on the menu item                     |
| FactBoxes              | `Form > Parts > FormPart > FormName`                                               | Apply [FactBox](./factbox.md) pattern on the target form          |

---

## 7. Worked example

_To come — Smartbox **Customer** Form will land under [`/mocks/order-to-cash/`](../../mocks/)._

---

## 8. References

- Microsoft Learn — [Details Master form pattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/details-master-form-pattern)
- Microsoft Learn — [Build the Customer form (tutorial)](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/build-customer-form)
- Microsoft Learn — [General Form Guidelines](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/general-form-guidelines)
- Microsoft Learn — [Fields and Field Groups subpattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/fields-field-groups-subpattern)
- Microsoft Learn — [FactBox Form Patterns](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/factbox-form-patterns)
