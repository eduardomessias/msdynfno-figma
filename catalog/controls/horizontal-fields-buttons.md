# Horizontal Fields and Buttons Group — subpattern

> **Microsoft references:**
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/horizontal-fields-buttons-group-subpattern
>
> **AOT subpattern:** `HorizontalFieldsAndButtonsGroup`
> **Figma component:** `Subpattern/HorizontalFieldsAndButtonsGroup`
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-07-01

---

## 1. Purpose

A horizontal band that places **read-only summary fields on the left and action buttons on the right**, all on a single line. The canonical use is the **line-view header summary band** in a Details Transaction: while the user works on lines below, this strip keeps key header values (order number, customer, total) in view alongside Generate/Print buttons.

Different from [Toolbar and Fields](./toolbar-and-fields.md) in that:
- The fields and buttons coexist on the **same horizontal row** (not toolbar above / fields below)
- The fields are **always read-only** — this is a summary display, not an edit surface

---

## 2. Anatomy

```
Group (HorizontalFieldsAndButtonsGroup)
├── SummaryFields (Group)             ← read-only fields, left-aligned
│   └── Field (0..N, AllowEdit=No)
└── ButtonGroup (ButtonGroup)         ← action buttons, right-aligned
    └── MenuItemButton | MenuButton | DropDialogButton (0..N)
```

The outer Group arranges its two children (`SummaryFields` and `ButtonGroup`) horizontally with `SummaryFields` growing to fill available space.

---

## 3. Key properties

| Property / Location            | Notes                                                                                        |
|--------------------------------|----------------------------------------------------------------------------------------------|
| `SummaryFields` fields         | All `AllowEdit=No` — the strip is strictly read-only                                         |
| Field binding                  | Bound to the header data source; display methods for computed values (total, status)         |
| `ButtonGroup` alignment        | Right-aligned by default (framework handles layout)                                          |
| Button `NeedsRecord`           | `Yes` for document-level actions; `No` for navigation buttons                               |
| Summary field count            | 3–6 fields. More than 6 makes the strip too crowded; promote the most important subset.      |
| Button count                   | 1–5 buttons. More than 5 → use a MenuButton dropdown for secondary actions.                 |

---

## 4. UX guidelines

1. **Show only the fields the user needs while working on lines.** In a Details Transaction line view, the user doesn't need to see all 30 header fields — 3–5 key values (Order, Customer, Status, Currency, Total) are sufficient.
2. **Right-align numeric summary values** (Total, Remaining amount) even in this horizontal layout.
3. **Actions in the button group are document-level** — they act on the header record, not on individual lines. Line-level actions belong in the grid's local toolbar.
4. **Don't make any field editable.** If the user needs to edit a header field while viewing lines, they navigate to the Header view — the horizontal summary band is display-only by design.
5. **Currency amounts include the currency code** or ISO symbol as a suffix field or inline label, so the value is unambiguous.

---

## 5. Do / Don't

**Do**
- Keep to 3–6 summary fields and 1–5 buttons.
- Show the most contextually important header values (identifier, party, total, status).
- Right-align numeric values.
- Use MenuButton for secondary/infrequent actions.

**Don't**
- Don't add editable fields — this is a display band only.
- Don't exceed 6 summary fields or 5 buttons.
- Don't use this for standalone toolbar scenarios — use [Toolbar and Fields](./toolbar-and-fields.md).
- Don't repeat information already visible in the ActionPane tabs or RecordTitle.

---

## 6. AOT mapping (dev handoff)

| Spec field             | AOT location                                                                       | Notes                                          |
|------------------------|------------------------------------------------------------------------------------|------------------------------------------------|
| Subpattern             | `Group.SubPattern = HorizontalFieldsAndButtonsGroup`                               | Applied in designer on the outer Group         |
| SummaryFields          | `Group` of bound fields; `AllowEdit=No` on each; `DataSource=<header DS>`         | Display methods acceptable for computed totals |
| ButtonGroup            | `ButtonGroup` placed as second child of the outer Group                            | Framework right-aligns automatically           |
| Header DS binding      | All fields bound to the same header data source; no mixing of DS in this band      | Consistent DS = consistent row context         |

---

## 7. Figma component definition

- **Name:** `Subpattern/HorizontalFieldsAndButtonsGroup`
- **Layout:** Single horizontal row — summary fields left, buttons right
- **Slots:**
  - `SummaryFields` — 3–6 read-only labeled field pairs (label above value), horizontal flow
  - `ButtonGroup` — 1–5 action buttons, right-aligned
- **Variants:**
  - `Field count`: 3 / 4 / 5 / 6
  - `Button count`: 1 / 2 / 3 / 4–5
- **Usage note:** Use inside the Lines view of a Details Transaction Figma frame, above the line grid. Fill with realistic header values (e.g. "SO-00001", "Fabrikam Inc.", "Confirmed", "USD", "$12,500"). Label buttons with verbs ("Confirm", "Invoice", "Print ▾").

---

## 8. References

- Microsoft Learn — [Horizontal Fields and Buttons Group subpattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/horizontal-fields-buttons-group-subpattern)
- [Toolbar and Fields subpattern](./toolbar-and-fields.md) — toolbar-above / fields-below variant
- [Details Transaction pattern](../patterns/details-transaction.md) — primary host of this subpattern (LineViewHeader)
