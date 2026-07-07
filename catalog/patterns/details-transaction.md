# Details Transaction form pattern

> **Microsoft pattern reference:** https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/details-transaction-form-pattern
> **D365FO `Form.Design.Style` value:** `DetailsFormTransaction`
> **D365FO `Form.Design.Pattern` value:** `DetailsTransaction`
> **Figma page:** `Patterns / Details Transaction`
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-05-20

---

## 1. Usage — when to choose this pattern

A Details Transaction form represents a **header + lines** transactional entity (Sales order, Purchase order, Project quotation, Production order, …). It bundles three things into one form:

- A **navigation grid** on the left for browsing transactions (replaces the old standalone List Page).
- A **Header view** with all header fields organised in FastTabs.
- A **Line view** showing the lines grid, an editable line-details panel, and a summary band of the most important header fields.

The Header / Line views are switched via the native tab controls under the record title (modern accessible version of the old Header/Lines proxy buttons).

**Use Details Transaction when:**

- The entity is a transaction with a clear Header and one or more Line collections (1..N children).
- Users need to drill from a list of transactions into a single one and edit it.
- Bulk-editing across transactions is desirable from the grid view.

### Related patterns

- [Details Master](./details-master.md) — same shape, but the entity has no Lines (master record only)
- [Simple List and Details](./simple-list-details.md) — for medium-complexity entities with 0–5 small child collections
- [List Page](./list-page.md) — only when the transactions are read-only and have no Details form (rare)

---

## 2. Anatomy — the required structure

From Microsoft's pattern article — this is exactly the high-level model the BP-checker validates:

```
Design
├── ActionPane                                       (ActionPane)
├── SidePanel                                        (Group)
│   ├── QuickFilter
│   ├── CustomFilters (Group)                         [Optional]
│   └── NavigationList                                (Grid, Style=List)
└── PanelTab                                         (Tab, ShowTabs=No)
    ├── DetailsPanel                                 (TabPage)
    │   ├── TitleGroup                                (Group)
    │   │   ├── HeaderTitle                           (String)
    │   │   └── EntityStatus (Group) [Optional]       (1..N status fields)
    │   └── HeaderLinePanels                          (Tab, ShowTabs=No)
    │       ├── LinePanel              (TabPage PanelStyle=Line)
    │       │   └── LineViewTab        (Tab Style=FastTabs)
    │       │       ├── LineViewHeader     (TabPage)
    │       │       ├── LineViewLines      (TabPage)
    │       │       └── LineViewLineDetails(TabPage)
    │       │           └── LineDetailsTab (Tab Style=Standard)
    │       │               └── LineDetailsTabPages (TabPages 1..N)
    │       └── HeaderPanel            (TabPage PanelStyle=Header)
    │           └── HeaderViewTab      (Tab Style=FastTabs)
    │               └── HeaderViewTabPages (TabPages 1..N)
    └── GridPanel                       (TabPage PanelStyle=Grid)
        ├── CustomFilterGroup           (Group)
        │   ├── QuickFilter
        │   └── OtherFilters ($Field) [0..N]
        ├── MainGrid                    (Grid)
        └── MainGridDefaultAction       (CommandButton)
```

| Node                       | Required? | Purpose                                                                | Figma component                  |
|----------------------------|-----------|------------------------------------------------------------------------|----------------------------------|
| `ActionPane`               | Yes       | Top toolbar. Foundation provides New/Delete/Save/Refresh/Attachments/Export. Add app-specific buttons only. | `Pattern/ActionPane`             |
| `SidePanel`                | Yes       | Left navigation list with its own QuickFilter + optional filters       | `Subpattern/SidePanel`           |
| `NavigationList`           | Yes       | List-style grid. ≤3 lines per row (typically ID + Description, plus transaction total on the last column) | `Control/Grid (Style=List)`      |
| `DetailsPanel`             | Yes       | Wraps the Header/Line views and the record title                       | `Pattern/DetailsPanel`           |
| `TitleGroup` / `HeaderTitle` | Yes     | Page title in **`<ID>` : `<Description>`** format                       | `Composite/RecordTitle`          |
| `EntityStatus`             | Optional  | Status pills next to the title (Workflow status, Approval, etc.)       | `Composite/EntityStatus`         |
| `HeaderPanel`              | Yes       | Header view — FastTabs of header fields                                | `Pattern/HeaderPanel`            |
| `LinePanel`                | Yes       | Line view — FastTabs containing a header summary, the lines grid, and a line-details tab page | `Pattern/LinePanel`     |
| `LineViewHeader`           | Yes       | Header summary band shown above the lines                              | `Subpattern/HorizontalFieldsButtonsGroup` |
| `LineViewLines`            | Yes       | The lines `Grid` itself                                                | `Control/Grid`                   |
| `LineViewLineDetails`      | Yes       | Standard-tab area for fields that don't fit in the line grid           | `Pattern/LineDetailsTab`         |
| `GridPanel`                | Yes       | The "list" of transactions (navigation grid takes you here on close)   | `Pattern/GridPanel`              |
| `MainGrid`                 | Yes       | Bulk-editable transactions grid                                        | `Control/Grid`                   |
| FactBoxes (in `Parts`)     | Optional  | Right-pane related-info cards                                          | `Pattern/FactBox`                |

### Allowed subpatterns

- [Fields and Field Groups](../controls/fields-and-field-groups.md) — inside each FastTab
- [Toolbar and List](../controls/toolbar-and-list.md) — for line-grid toolbars
- [Toolbar and Fields](../controls/toolbar-and-fields.md) — for field-only sections with actions
- [Nested Simple List and Details](../controls/nested-simple-list-details.md) — for embedded sub-collections
- [Custom Filter Group](../controls/custom-filter-group.md) — on the SidePanel and on the GridPanel

---

## 3. Core components — Best-Practice (BP) warnings to resolve

Zero BP warnings before merge. From Microsoft's pattern article:

- [ ] `Design.Caption` is not empty
- [ ] Form is referenced by at least one menu item
- [ ] `TabPage.Caption` is not empty (every TabPage)
- [ ] `TabPage.DataSource` is not empty (every TabPage)

Plus the foundation-button rule:

- [ ] No app-defined **New**, **Delete**, **Save**, **Refresh**, **Attachments**, or **Export to Excel** buttons (the foundation provides them, unless the foundation-provided button has been explicitly removed)

---

## 4. UX guidelines

From Microsoft's pattern article — apply on mocks **and** in code:

1. No duplicate **New** / **Delete** buttons in the ActionPane (the foundation provides them).
2. ActionPane: see [General Form Guidelines](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/general-form-guidelines).
3. In its **default** state, the content of the first FastTab is fully visible without scrolling.
4. FastTabs: see [General Form Guidelines](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/general-form-guidelines).
5. **Page title area:**
   - Format: **`<ID> : <Description>`**.
   - Title is **plural** when referring to the entity collection (consistent with the related list link in Main Menu).
   - A link to the Details page is in the Main Menu after the merge from a former List Page.
6. **Navigation list grid (left side, Style=List):**
   - Rows span **at most 3 lines** of text.
   - Typically just `ID` and `Description` — at least 2 fields.
   - The **last field** is the transaction **total** (gives users a quick scan dimension).
7. **Grid view (GridPanel / MainGrid):**
   - 2 to 15 fields. Include all mandatory fields so users can create records directly in the grid.
   - First textual column is a hyperlink to the Details (Header) view.
   - QuickFilter default targets the most-likely filtered column.
   - Focus is in the QuickFilter on open.
   - `ID` first, then master entity `ID` + `Name`.
8. FactBoxes: see [FactBox Form Patterns](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/factbox-form-patterns).

### Modern accessibility note

Microsoft removed the legacy Header/Lines proxy buttons next to the record title (they were radio buttons styled as tabs, which screen readers misread). Enable the **Removal of header/lines proxy buttons** feature so the native tab controls under the record title are used instead. The feature is shipped in platform updates for finance and operations apps **10.0.23+**.

### Smartbox overrides

_None yet. Any divergence here must be approved by the architect and recorded as an ADR._

---

## 5. Do / Don't

**Do**

- Always include the Header view, even if it initially holds only the summary fields shown in the Line view band. Header view is **compulsory** — application teams, ISVs, and localization teams will extend it.
- Use FastTabs for both Header and Line views, **Standard tabs** inside the Line Details tab page.
- Keep the lines `Grid` editable inline for the common edit path. Push rare/large fields into the LineDetails standard-tab area.
- Use the navigation grid (left) and the `MainGrid` (GridPanel) for bulk editing scenarios.

**Don't**

- Don't drop the Header view because "today there's nothing in it" — it will be extended over time and removing it breaks upgrade compatibility.
- Don't introduce custom buttons for Save / Refresh / Attachments / Export — the foundation owns those.
- Don't use a wide Line Details FastTabs layout — use **Standard** tabs at that level.
- Don't put the page title at the top of the form area; the framework renders the record title above your `DetailsPanel`.

---

## 6. AOT metadata mapping (dev handoff)

| Spec field                       | AOT location                                                                              | Notes                                                                |
|----------------------------------|-------------------------------------------------------------------------------------------|----------------------------------------------------------------------|
| Pattern                          | `Form > Design > Pattern = DetailsTransaction`                                            | Apply via designer right-click → Apply pattern                       |
| Header data source               | `Form > Data Sources > <HeaderDS>`                                                        | Typically the master transaction table (`SalesTable`, `PurchTable`)  |
| Line data source                 | `Form > Data Sources > <LineDS>`, with parent join on `<HeaderDS>`                        | `JoinSource = <HeaderDS>`, `LinkType = Delayed`                      |
| Navigation list columns          | `Form > Design > SidePanel > NavigationList > <FormStringControl bound to DS.field>`      | Row height ≤ 3 lines; last column is the transaction total           |
| Header FastTabs                  | `Form > Design > … > HeaderPanel > HeaderViewTab > <TabPage> > Group(s)`                  | Apply [Fields and Field Groups](../controls/fields-and-field-groups.md) subpattern on each TabPage |
| Line summary band                | `LineViewHeader` TabPage with `Toolbar and Fields` subpattern                             | Mirror the 4–6 most-important header fields                          |
| Lines grid                       | `LineViewLines > Grid bound to <LineDS>`                                                  | `Grid.DefaultAction` → button that opens the LineDetails tab page    |
| Line details                     | `LineViewLineDetails > LineDetailsTab (Tab Style=Standard)`                               | Use Standard tabs at this level (not FastTabs)                       |
| MainGrid (transactions list)     | `Form > Design > … > GridPanel > MainGrid bound to <HeaderDS>`                            | `Grid.DefaultAction = MainGridDefaultAction` → opens DetailsPanel    |
| ActionPane buttons               | `Form > Design > ActionPane > ActionPaneTab > ButtonGroup > MenuItemButton`               | Bind to Action/Display/Output menu items; security on the menu item  |
| FactBoxes                        | `Form > Parts > FormPart > FormName`                                                      | Apply [FactBox](./factbox.md) pattern on the target form             |
| Removal of header/lines proxy    | Feature management: **Removal of header/lines proxy buttons**                              | Required for accessibility on 10.0.23+                               |

---

## 7. Worked example

_To come: the first end-to-end mock for Details Transaction will be a Smartbox **Sales order** under [`/mocks/order-to-cash/`](../../mocks/)._

---

## 8. References

- Microsoft Learn — [Details Transaction form pattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/details-transaction-form-pattern)
- Microsoft Learn — [Selecting a form pattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/select-form-pattern)
- Microsoft Learn — [General Form Guidelines](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/general-form-guidelines)
- Microsoft Learn — [FactBox Form Patterns](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/factbox-form-patterns)
- Microsoft Learn — [Fields and Field Groups subpattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/fields-field-groups-subpattern)
- Microsoft Learn — [Toolbar and List subpattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/toolbar-list-subpattern)
- Microsoft Learn — [Toolbar and Fields subpattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/toolbar-fields-subpattern)
- AX 2012 legacy — [Details Form with Lines UX Guidelines](https://learn.microsoft.com/en-us/dynamicsax-2012/developer/details-form-with-lines-user-experience-guidelines)
