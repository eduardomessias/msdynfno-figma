# Fields and Field Groups — subpattern

> **Microsoft references:**
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/fields-field-groups-subpattern
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/page-layout
>
> **AOT subpattern:** `FieldsAndFieldGroups` (applied to a `TabPage` or `Group` container)
> **Figma component:** `Subpattern/FieldsAndFieldGroups`
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-07-01

---

## 1. Purpose

The most commonly applied subpattern in D365FO. It governs how fields inside a container (FastTab `TabPage`, Dialog body, TOC section) **flow into a responsive multi-column layout**. The framework distributes fields and field groups across one, two, or three columns based on the available width.

Apply it to every FastTab `TabPage` that contains only fields (no grid). If the `TabPage` contains a grid, use [Toolbar and List](./toolbar-and-list.md) instead.

---

## 2. Anatomy

```
TabPage (or Group)                    ← container with subpattern applied
├── FieldGroup (Group, 0..N)          ← logical grouping of related fields
│   ├── Field 1                       ← bound input control
│   ├── Field 2
│   └── Field N
└── Field (0..N)                      ← ungrouped fields flow after groups
```

Fields and groups are distributed left-to-right, top-to-bottom across the column grid. The framework decides the column count based on the container width — typically 2 columns on a standard widescreen layout.

---

## 3. Key properties

| Property / Location          | Notes                                                                                          |
|------------------------------|------------------------------------------------------------------------------------------------|
| `Group.Caption`              | Visible section header within the FastTab (optional but recommended for groups ≥3 fields)      |
| `Group.Breakable`            | `No` — keeps the group together when columns reflow. `Yes` (default) allows split across cols  |
| `Group.ColumnsMode`          | `Auto` for responsive column count within the group; `Fixed` + `Columns=N` for explicit lock   |
| `Group.DataSource`           | Required on every Group that contains bound fields (BP rule)                                   |
| `Group.FrameType`            | `None` (standard — no visible border); `Box` only inside Dialog groupings                      |
| Field `Mandatory`            | Prefer setting on the EDT so all uses of the field inherit the same mandatory state            |
| Field `AllowEdit`            | `No` for computed/display fields; `Auto` inherits from the data source                         |

---

## 4. UX guidelines

### Column layout

1. **Target 2 columns** for most FastTabs. The framework auto-flows to 1 column on narrow containers and up to 3 on very wide ones.
2. **Logical grouping before column count** — group related fields together; the columns are a presentation concern, not a semantic one.
3. **Set `Group.Breakable = No`** on groups that lose meaning when split (e.g. an address block that should always show as a unit).
4. **Never hard-code column count on the outer container** — leave it `Auto` so the layout responds to the user's window size.

### Field ordering

5. **Lead with the most important fields.** The natural key and description should be at the top-left of the FastTab.
6. **Mandatory fields first** within their logical group.
7. **Read-only computed fields last** within their group — they are supporting context, not primary input.

### Labels

8. **Never override an EDT label at field level** without architect sign-off — labels set on the EDT propagate everywhere the field is used.
9. **Provide a Help text** on the EDT for every non-obvious field — it appears in the tooltip on hover.

### Field counts

10. **5–15 fields per FastTab** is the comfortable range. Below 5: consider merging with a sibling FastTab. Above 15: consider splitting into two FastTabs.
11. **Don't add a field to a FastTab just because it exists on the table.** Show only fields the user acts on in the context of that FastTab.

---

## 5. Do / Don't

**Do**
- Apply `FieldsAndFieldGroups` to every FastTab `TabPage` that contains only fields.
- Set `Group.DataSource` on every Group that contains bound fields.
- Use `Group.Breakable = No` for address blocks and other cohesive groups.
- Prefer 2-column layouts; let the framework handle responsiveness.

**Don't**
- Don't put a Grid inside a `FieldsAndFieldGroups` container — use [Toolbar and List](./toolbar-and-list.md) instead.
- Don't override EDT labels at the field level without a documented reason.
- Don't exceed 15 fields per FastTab without restructuring.
- Don't use `FrameType = Box` on Groups in main-form patterns (only dialogs).

---

## 6. AOT mapping (dev handoff)

| Spec field              | AOT location                                                                        | Notes                                          |
|-------------------------|-------------------------------------------------------------------------------------|------------------------------------------------|
| Subpattern              | `TabPage.SubPattern = FieldsAndFieldGroups`                                         | Applied in the designer, not code              |
| Group DataSource        | `Group.DataSource = <table DS name>`                                                | Required BP check; missing = BP warning        |
| Column control          | `Group.ColumnsMode = Auto` (default) or `Fixed` + `Group.Columns = N`              | Leave Auto on the outer layer                  |
| Group integrity         | `Group.Breakable = No` for cohesive groups                                          | Default is `Yes` (breakable)                   |
| Field binding           | `<ControlType>.DataSource + DataField` OR `.DataMethod` for display methods         | Every visible field must be bound              |

---

## 7. Figma component definition

- **Name:** `Subpattern/FieldsAndFieldGroups`
- **Use in:** The body of a `Control/FastTab` (expanded state), Dialog body group, TOC section
- **Layout to mock:** 2-column responsive grid of labeled input pairs
- **Variants:**
  - `Columns`: 1 / 2 / 3
  - `GroupCaption`: Show / Hide
- **Usage note:** Place field pairs (label above, input below) in a 2-column grid. Group related fields with a small group label. Don't replicate the exact pixel alignment — the framework handles it; the mock just needs to convey grouping logic.

---

## 8. References

- Microsoft Learn — [Fields and Field Groups subpattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/fields-field-groups-subpattern)
- Microsoft Learn — [Page layout in the web client](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/page-layout)
- Microsoft Learn — [General form guidelines](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/general-form-guidelines)
- [Toolbar and List subpattern](./toolbar-and-list.md) — use instead when the TabPage contains a grid
- [Tabular Fields subpattern](./tabular-fields.md) — use instead for totals/aggregate rows
- [FastTab control](./fast-tab.md) — the container that hosts this subpattern
