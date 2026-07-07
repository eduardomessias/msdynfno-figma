# List Panel — subpattern

> **Microsoft references:**
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/list-panel-subpattern
>
> **AOT subpattern:** `ListPanel`
> **Figma component:** `Subpattern/ListPanel`
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-07-01

---

## 1. Purpose

The **two-list "move items between" UI**: Available items on the left, Selected items on the right, with Add / Remove transfer buttons in the centre. The user's primary task is composing a **selection set** from a source collection.

Use List Panel when:
- The user needs to choose multiple items from a maintained list (e.g. assign security roles to a user, pick report columns, configure a batch job's entity set)
- The selected items have an inherent order that the user can adjust (via optional Move Up / Move Down buttons)

For very large available lists (hundreds of rows), the full-form [Advanced Selection](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/advanced-selection-form-pattern) pattern is the equivalent at the dialog level.

---

## 2. Anatomy

```
[Container]                           ← TabPage / Group with subpattern applied
├── CustomFilterGroup (optional)      ← filter the Available list
└── ListPanelGroup (Group)
    ├── AvailablePanel (Group)
    │   ├── Label "Available"
    │   └── Grid | Tree | ListView | ListBox
    ├── ActionPanel (Group)
    │   ├── AddButton      →          ← moves selected row(s) from Available to Selected
    │   ├── RemoveButton   ←          ← moves selected row(s) from Selected to Available
    │   ├── AddAllButton   →→         ← optional
    │   └── RemoveAllButton ←←        ← optional
    ├── SelectedPanel (Group)
    │   ├── Label "Selected"
    │   └── Grid | Tree | ListView | ListBox
    └── MoveUpDownPanel (Group)       ← optional, only when order matters
        ├── MoveUpButton
        └── MoveDownButton
```

---

## 3. Key properties

| Property / Location            | Notes                                                                                          |
|--------------------------------|------------------------------------------------------------------------------------------------|
| Available list `DataSource`    | Bound to the source collection (all available items)                                           |
| Selected list `DataSource`     | Bound to the target/junction table that stores the user's selection                            |
| Add / Remove buttons           | `NeedsRecord=Yes` (default) — a row must be selected in the source list before transfer        |
| AddAll / RemoveAll             | Optional; include when the available list is short enough that transferring all is useful       |
| MoveUp / MoveDown              | Include only when the selected items have a meaningful sequence (e.g. report column order)     |
| CustomFilterGroup              | Optional; apply the `CustomFilters` subpattern to filter the Available list by category/type   |

---

## 4. UX guidelines

### Transfer mechanics

1. **Double-click on an Available item transfers it immediately** to Selected — the user shouldn't need to select then click Add for single items. This is the platform default; preserve it.
2. **Double-click on a Selected item removes it** back to Available — same logic.
3. **The Add / Remove buttons operate on the current selection** — if the user has multi-selected several items (Shift-click or Ctrl-click), transferring all selected items at once is expected behaviour.
4. **AddAll / RemoveAll are secondary actions** — use `TextOnly` button display and position them below Add/Remove in the ActionPanel.

### List layout

5. **Both lists should show the same columns** — ID and Name are the minimum. Don't show different columns in Available vs Selected.
6. **Sort Available alphabetically by default.** Sort Selected by the user-defined sequence order (if MoveUpDown is present) or by the order items were added.
7. **Show the item count in each list header** — "Available (42)" and "Selected (3)" give the user orientation.

### When order matters

8. **Include MoveUpDown only when sequence is meaningful** (column order in a report, priority order in a rule set). Omit it for unordered selections (role assignments, batch entity lists).
9. **MoveUp is disabled on the first item; MoveDown is disabled on the last.** The framework handles this via `NeedsRecord=Yes` on the buttons — set the correct `enabled()` logic in X++.

### Filter on Available

10. **Include a CustomFilterGroup above the Available list** when the source collection has a useful categorisation (e.g. role type, entity group). Limit to 1–2 filter controls.

---

## 5. Do / Don't

**Do**
- Support double-click transfer in both directions.
- Show item counts in list headers.
- Include AddAll/RemoveAll only when the available list is short (≤30 rows).
- Include MoveUpDown only when the selected order is semantically meaningful.

**Don't**
- Don't show different columns in Available vs Selected — consistency is essential.
- Don't include MoveUpDown for unordered selections.
- Don't use List Panel when a simple multi-select grid is sufficient — List Panel implies the selected set is distinct and separately managed.

---

## 6. AOT mapping (dev handoff)

| Spec field                  | AOT location                                                                           | Notes                                                          |
|-----------------------------|----------------------------------------------------------------------------------------|----------------------------------------------------------------|
| Subpattern                  | `Group.SubPattern = ListPanel`                                                          | Applied on the outer container Group                           |
| Available list DS           | Grid bound to the source table (all available records)                                 | Read-only from the business perspective; Add transfers to selected |
| Selected list DS            | Grid bound to the junction / target table                                              | Records here represent the user's current selection             |
| Transfer X++                | `AddButton.clicked()` → insert into selected DS; `RemoveButton.clicked()` → delete from selected DS | Standard pattern; use `moveItem()` helper if available |
| MoveUp/Down X++             | `MoveUpButton.clicked()` → swap `LineNum` (or equivalent sequence field) with previous record | Only when order field exists on selected DS            |

---

## 7. Figma component definition

- **Name:** `Subpattern/ListPanel`
- **Layout:** Three-column — Available list / Action buttons / Selected list
- **Slots:**
  - `AvailableLabel` — "Available (N)" header
  - `AvailableList` — grid with 2 columns (ID, Name), several unselected rows
  - `ActionPanel` — → / ← / →→ / ←← buttons, vertically centred between lists
  - `SelectedLabel` — "Selected (N)" header
  - `SelectedList` — same column structure, showing the chosen items
  - `MoveUpDown` (optional) — ↑ / ↓ buttons to the right of Selected list
- **Variants:**
  - `AddAll`: Yes / No
  - `MoveUpDown`: Yes / No
  - `Filter`: None / WithCustomFilter
- **Usage note:** Mock with 5–8 rows in Available and 2–3 rows in Selected. Use realistic item labels (role names, column names, entity names depending on context).

---

## 8. References

- Microsoft Learn — [List Panel subpattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/list-panel-subpattern)
- Microsoft Learn — [Advanced Selection form pattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/advanced-selection-form-pattern) — dialog-level equivalent for large lists
- [Grid control](./grid.md) — the control used inside Available and Selected panels
- [Custom Filter Group subpattern](./custom-filter-group.md) — filter above the Available list
