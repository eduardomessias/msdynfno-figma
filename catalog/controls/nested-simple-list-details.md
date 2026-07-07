# Nested Simple List and Details — subpattern

> **Microsoft references:**
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/nested-simple-list-details-subpattern
>
> **AOT subpattern:** `NestedSimpleListDetails`
> **Figma component:** `Subpattern/NestedSimpleListDetails`
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-07-01

---

## 1. Purpose

Embeds a **self-contained list-and-detail panel inside a FastTab or TOC section** of a host form. Lets the user navigate and edit a child collection without leaving the parent record's form.

The subpattern is a scaled-down [Simple List and Details](../patterns/simple-list-details.md) pattern: a grid on the left, detail fields on the right, with a local toolbar above for adding and removing rows. It's the right choice when:

- A child collection has its own secondary fields that don't fit comfortably in a flat grid
- The user needs to work on one child record at a time while staying in context of the parent

---

## 2. Anatomy

```
TabPage (or Group)                    ← subpattern applied here
├── ActionPane (Style=Strip)          ← local toolbar: Add / Remove / actions
└── ContainerBody (Group, Columns=2)
    ├── ListContainer (Group)
    │   └── Grid (Tabular or List)    ← child record navigation
    └── DetailsContainer (Group)
        ├── DetailsHeader (Group)     ← key fields (ID, name)
        └── DetailsGroup (Group)      ← further detail fields (optional)
            └── SubPattern: FieldsAndFieldGroups or TabularFields
```

---

## 3. Key properties

| Property / Location               | Notes                                                                                              |
|-----------------------------------|----------------------------------------------------------------------------------------------------|
| `Grid.DataSource`                 | A secondary (child) data source on the host form                                                   |
| `DataSource.InsertIfEmpty`        | `No` — the toolbar Add button handles new row creation                                             |
| `ActionPane.DataSource`           | Must match the grid's data source so toolbar buttons act on the correct table                      |
| Grid `EditTrigger`                | `OnRowChange` if inline grid editing is needed; `Never` if detail fields are the only edit surface |
| `DetailsContainer.DataSource`     | Same child data source as the grid — detail panel responds to the selected row                     |
| Add button `NeedsRecord`          | `No`                                                                                               |
| Remove button `NeedsRecord`       | `Yes` (default)                                                                                    |

---

## 4. UX guidelines

### When to use

1. **Use when the child collection has secondary fields** that would make the grid too wide if shown as columns. Example: a `SalesLine` has 30+ fields — showing 5 key columns in the grid and the rest in the detail panel is more usable than a 30-column grid.
2. **Use when the child collection has 1–3 levels of nesting** (parent → child shown in the FastTab, child's own child navigated by opening a separate form). Don't nest deeper than 2 levels in the same surface.
3. **Don't use if the child collection only has 3–4 fields** — a plain grid (Toolbar and List) is simpler and more direct.

### Grid (left panel)

4. **Show 2–4 columns** in the navigation grid — ID, name, and 1–2 status/key values.
5. **First column is the active indicator** (bold or highlighted row) — the selected row drives the detail panel.
6. **Don't make the grid editable** (`EditTrigger=Never`) when the detail panel handles all editing. Avoid dual-edit modes.

### Detail panel (right)

7. **DetailsHeader shows the key identity fields** — the same ID and name shown in the grid, plus the primary status. These are always visible and give the user a grounding label.
8. **DetailsGroup contains the secondary fields** using [Fields and Field Groups](./fields-and-field-groups.md) or [Tabular Fields](./tabular-fields.md).
9. **Mandatory fields in the detail panel** should prompt the user when they try to move to another row — the standard framework validation handles this.

### Read-only grids

10. **No Add/Remove buttons** if the child data source is read-only (e.g. posted lines). Set `Grid.EditTrigger=Never`, omit Add/Remove from the toolbar, and keep only Inquire/Print actions.

---

## 5. Do / Don't

**Do**
- Set `DataSource.InsertIfEmpty=No` on the child data source.
- Match `ActionPane.DataSource` to the grid's data source.
- Show only 2–4 navigation columns in the left grid.
- Use FieldsAndFieldGroups in DetailsGroup for secondary field layout.

**Don't**
- Don't use if the child collection has ≤4 fields — use a plain grid instead.
- Don't add Add/Remove buttons for read-only child data sources.
- Don't make both the grid and the detail panel editable simultaneously.
- Don't nest more than 2 levels of list-and-details in the same form surface.

---

## 6. AOT mapping (dev handoff)

| Spec field                    | AOT location                                                                          | Notes                                                |
|-------------------------------|---------------------------------------------------------------------------------------|------------------------------------------------------|
| Subpattern                    | `TabPage.SubPattern = NestedSimpleListDetails`                                        | Applied in designer                                  |
| Toolbar                       | `ActionPane.Style = Strip`, `ActionPane.DataSource = <child DS>`                     | Must reference the same DS as the grid               |
| ListContainer                 | `Group` containing the child grid                                                     | Width ~40% of the container                          |
| Grid                          | `Grid.DataSource = <child DS>`, `InsertIfEmpty=No` on DS                              | Navigation only; keep editable limited               |
| DetailsContainer              | `Group.DataSource = <child DS>`, contains DetailsHeader + optional DetailsGroup       | Responds to grid row selection automatically         |
| DetailsGroup inner subpattern | `DetailsGroup.SubPattern = FieldsAndFieldGroups` (typical) or `TabularFields`        | Drives field layout in the right panel               |

---

## 7. Figma component definition

- **Name:** `Subpattern/NestedSimpleListDetails`
- **Layout:** Split panel — narrow list left (~40%), detail fields right (~60%)
- **Slots:**
  - `Toolbar` — Strip with Add / Remove + domain actions
  - `ListGrid` — 2–4 column grid with one row highlighted (selected)
  - `DetailsHeader` — key identity fields (ID, name, status)
  - `DetailsGroup` — secondary fields using FieldsAndFieldGroups layout
- **Variants:**
  - `Editable`: Yes (Add/Remove in toolbar) / ReadOnly (no Add/Remove)
  - `Detail depth`: HeaderOnly / HeaderAndGroup
- **Usage note:** Mock 3–5 rows in the left grid with one row active (highlighted). Fill the right panel with realistic detail fields for the selected row.

---

## 8. References

- Microsoft Learn — [Nested Simple List and Details subpattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/nested-simple-list-details-subpattern)
- [Simple List and Details pattern](../patterns/simple-list-details.md) — the top-level pattern this subpattern mirrors
- [Fields and Field Groups subpattern](./fields-and-field-groups.md) — inner subpattern for DetailsGroup
- [Toolbar and List subpattern](./toolbar-and-list.md) — use instead when only a grid (no detail panel) is needed
