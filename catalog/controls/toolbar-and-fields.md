# Toolbar and Fields — subpattern

> **Microsoft references:**
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/toolbar-fields-subpattern
>
> **AOT subpattern:** `ToolbarAndFields`
> **Figma component:** `Subpattern/ToolbarAndFields`
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-07-01

---

## 1. Purpose

Container-level subpattern for sections that need **action buttons directly above a field layout**. The toolbar (ActionPane Strip) sits above a content group — making this the fields-equivalent of [Toolbar and List](./toolbar-and-list.md).

Canonical uses:
- **Line summary band** on a Details Transaction: read-only header fields with Generate/Print actions
- **FastTab with record-level actions** that operate on the fields shown (e.g. "Calculate" re-runs a computation and updates displayed values)
- **Simple Details with a local toolbar** (no page-level ActionPane)

---

## 2. Anatomy

```
TabPage (or Group)                    ← subpattern applied here
├── Toolbar (ActionPane, Style=Strip)
│   └── ButtonGroup
│       └── MenuItemButton | MenuButton | DropDialogButton (1..N)
└── ContentGroup (Group)              ← applies one of: FieldsAndFieldGroups,
    └── fields / groups                  TabularFields, or DimensionExpressionBuilder
```

The `ContentGroup` itself is typically set to `ColumnsMode=Auto` so fields inside flow responsively. Apply the appropriate inner subpattern (`FieldsAndFieldGroups` for standard fields, `TabularFields` for totals rows).

---

## 3. Key properties

| Property / Location    | Notes                                                                                        |
|------------------------|----------------------------------------------------------------------------------------------|
| `ActionPane.Style`     | `Strip` — not `Standard`                                                                    |
| `ContentGroup.ColumnsMode` | `Auto` for responsive field layout within the group                                    |
| `ContentGroup.DataSource`  | Required when the group contains bound fields (BP rule)                               |
| Button `NeedsRecord`   | `Yes` for record-level actions (default); explicitly set `No` only for record-independent actions |

---

## 4. UX guidelines

1. **Use when actions are directly tied to the fields shown** — the user changes values and then triggers an action that operates on those values. If the actions are page-level (not specific to a FastTab), they belong in the main ActionPane, not a Strip.
2. **Maximum 10 buttons** in the Strip. Consolidate infrequent actions into a MenuButton dropdown.
3. **Button ordering:** Primary action first (left), secondary actions follow, infrequent actions last — same rules as [ActionPane](./action-pane.md) Strip buttons.
4. **Read-only field groups:** Use Toolbar and Fields even when the ContentGroup is read-only, if the toolbar has Generate/Print/Inquire actions that relate to the displayed summary. Don't add an edit button — link to the relevant form instead.
5. **The ContentGroup is not a FactBox.** Fields here are bound to the current record's data source, not a related record. For related-record info, use the FactBox panel.

---

## 5. Do / Don't

**Do**
- Use this subpattern when a FastTab body needs both action buttons and a field layout.
- Set `ContentGroup.DataSource` on the inner group.
- Use Strip style on the ActionPane — never Standard inside a container.

**Don't**
- Don't replicate the page-level ActionPane here — only FastTab-scoped actions belong in a Strip.
- Don't combine a grid and a field group in the same `ToolbarAndFields` container — use a separate FastTab with [Toolbar and List](./toolbar-and-list.md) for the grid.
- Don't exceed 10 Strip buttons.

---

## 6. AOT mapping (dev handoff)

| Spec field          | AOT location                                                                     | Notes                                     |
|---------------------|----------------------------------------------------------------------------------|-------------------------------------------|
| Subpattern          | `TabPage.SubPattern = ToolbarAndFields`                                          | Applied in the designer                   |
| Toolbar             | `ActionPane.Style = Strip`, first child of the container                         | Placed before the ContentGroup            |
| ContentGroup        | `Group.ColumnsMode = Auto`, `Group.DataSource = <DS>`                            | Hosts the fields; apply inner subpattern  |
| Inner subpattern    | `ContentGroup.SubPattern = FieldsAndFieldGroups` (standard) or `TabularFields`  | Depends on field layout needs             |

---

## 7. Figma component definition

- **Name:** `Subpattern/ToolbarAndFields`
- **Slots:**
  - `Toolbar` — ActionPane Strip
  - `ContentGroup` — field layout (mirrors `Subpattern/FieldsAndFieldGroups` or `Subpattern/TabularFields`)
- **Variants:**
  - `Toolbar buttons`: Generate / Print / Inquire / Actions (mix to fit the use case)
  - `ContentGroup style`: FieldsAndFieldGroups / TabularFields
  - `Fields editable`: Yes / No
- **Usage note:** Model the toolbar with realistic action labels. For the summary band on Details Transaction, show read-only stat fields with Generate/Print buttons.

---

## 8. References

- Microsoft Learn — [Toolbar and Fields subpattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/toolbar-fields-subpattern)
- [ActionPane control](./action-pane.md) — Strip style
- [Toolbar and List subpattern](./toolbar-and-list.md) — same toolbar pattern but with a grid instead of fields
- [Fields and Field Groups subpattern](./fields-and-field-groups.md) — the inner field layout subpattern
- [Tabular Fields subpattern](./tabular-fields.md) — alternative inner layout for totals rows
