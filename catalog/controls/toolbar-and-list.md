# Toolbar and List — subpattern

> **Microsoft references:**
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/toolbar-list-subpattern
>
> **AOT subpattern:** `ToolbarAndList` / `ToolbarAndListDouble`
> **Figma component:** `Subpattern/ToolbarAndList` (+ `…/Double`)
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-07-01

---

## 1. Purpose

The required subpattern for any FastTab `TabPage` (or Group) that hosts a **local grid with its own add/remove/action buttons**. The toolbar (an ActionPane Strip) sits above the grid and owns the actions that operate on that grid's rows.

Two variants:

| Variant | When to use |
|---------|-------------|
| **Toolbar and List** | One toolbar + one grid |
| **Toolbar and List — Double** | One toolbar + two grids side-by-side (e.g. Available / Selected pairing without the List Panel subpattern) |

---

## 2. Anatomy

```
TabPage (or Group)                    ← subpattern applied here
├── Toolbar (ActionPane, Style=Strip)
│   └── ButtonGroup
│       └── MenuItemButton | MenuButton | DropDialogButton (1..N)
└── Grid (1..2)                       ← 2nd Grid only in the Double variant
```

---

## 3. Key properties

| Property / Location          | Notes                                                                                           |
|------------------------------|-------------------------------------------------------------------------------------------------|
| `ActionPane.Style`           | Must be `Strip` (local toolbar). `Standard` is only for page-level ActionPanes.                |
| Grid `DataSource`            | Required; must be a secondary data source (child of the form's primary DS)                     |
| Grid `DataSource.InsertIfEmpty` | Set to `No` — the toolbar's Add button handles new row creation, not implicit insert       |
| Grid `EditTrigger`           | `OnRowChange` for in-line editing; `Never` for read-only selection grids                       |
| Button `NeedsRecord`         | `Yes` for row-level actions (default); `No` for Add (doesn't require an existing row)          |
| Button `ButtonDisplay`       | `Auto` for first 2–3 buttons if icons exist; `TextOnly` for subsequent uncommon actions        |

---

## 4. UX guidelines

### Button ordering

1. **Add and Remove go first**, if present. Use symbol + text (`ButtonDisplay = Auto`) for these two.
2. **Common actions follow** — up to ~5, `Auto` if a recognised icon exists, otherwise `TextOnly`.
3. **Uncommon / infrequent actions go last** as `TextOnly`.
4. **Maximum 10 buttons** in the Strip toolbar. Group more actions under a `MenuButton` dropdown.

### Grid behaviour

5. **New rows are added at the bottom** of the grid. Focus moves to the first editable cell.
6. **Delete confirms only once** (standard framework dialog) — do not add a second programmatic confirm.
7. **Read-only grids** (selection lists in Lookups, FactBoxes): set `EditTrigger = Never` and remove Add/Remove buttons.
8. **Multi-select** (`Grid.MultiSelect = Yes`) is appropriate when bulk-action buttons (e.g. "Approve all selected") are present.

### Double variant

9. Use the Double variant when two related collections need side-by-side display — not as a substitute for [List Panel](./list-panel.md), which has a distinct "move items between" affordance with dedicated Add/Remove actions between the two lists.

---

## 5. Do / Don't

**Do**
- Apply this subpattern to every FastTab that contains a grid.
- Put Add before Remove; use `ButtonDisplay=Auto` for both.
- Set `InsertIfEmpty=No` on the grid's data source.
- Use `NeedsRecord=No` on the Add button.

**Don't**
- Don't exceed 10 buttons in the Strip toolbar.
- Don't use `Style=Standard` for local toolbars inside containers.
- Don't add an explicit Save button — the form-level Save covers child data sources.
- Don't add a grid to a container that uses [Fields and Field Groups](./fields-and-field-groups.md) — switch the whole subpattern.

---

## 6. AOT mapping (dev handoff)

| Spec field                   | AOT location                                                                          | Notes                                                             |
|------------------------------|---------------------------------------------------------------------------------------|-------------------------------------------------------------------|
| Subpattern                   | `TabPage.SubPattern = ToolbarAndList` (or `ToolbarAndListDouble`)                     | Applied in the designer                                           |
| Toolbar                      | `ActionPane.Style = Strip`, placed as first child of the container                   | Not a top-level ActionPane node — it's inside the TabPage         |
| Grid data source             | `Grid.DataSource = <child DS name>`                                                   | Must be a secondary (child) data source                          |
| InsertIfEmpty                | `<ChildDS>.InsertIfEmpty = No`                                                        | Prevents empty-row insert on form open                           |
| Add button                   | `MenuItemButton.NeedsRecord = No`, `MenuItemButton.MenuItemName = <CreateAction>`     | Tied to a menu item for security                                 |
| Remove button                | `MenuItemButton.NeedsRecord = Yes` (default)                                          | Standard delete, calls `delete()` on the DS                      |

---

## 7. Figma component definition

- **Name:** `Subpattern/ToolbarAndList`
- **Slots:**
  - `Toolbar` — ActionPane Strip with button group
  - `Grid` — tabular or list grid
- **Variants:**
  - `Toolbar buttons`: Add+Remove / Add+Remove+Actions / Read-only (no Add/Remove)
  - `Grid style`: Tabular / List
  - `Editable`: Yes / No
  - `Double`: No / Yes (second grid shown)
- **Usage note:** In mocks, show realistic button labels and 3–5 sample grid rows. For read-only grids, omit Add/Remove from the toolbar.

---

## 8. References

- Microsoft Learn — [Toolbar and List subpattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/toolbar-list-subpattern)
- Microsoft Learn — [General form guidelines](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/general-form-guidelines)
- [ActionPane control](./action-pane.md) — Strip style for local toolbars
- [Grid control](./grid.md) — the grid hosted inside this subpattern
- [List Panel subpattern](./list-panel.md) — use instead for Available → Selected move-between-lists UIs
