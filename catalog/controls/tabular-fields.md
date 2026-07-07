# Tabular Fields — subpattern

> **Microsoft references:**
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/tabular-fields-subpattern
>
> **AOT subpattern:** `TabularFields`
> **Figma component:** `Subpattern/TabularFields`
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-07-01

---

## 1. Purpose

Container-level subpattern for a **structured row-column alignment of fields**, where the visual alignment across rows is more important than responsive reflow. Intended primarily for **totals, balances, and aggregate summaries** — financial subtotals on a journal, voucher balances, order statistics — where the user reads down a column of values and expects them to line up.

**Not a grid.** Tabular Fields renders static fields in a tabular layout; it does not host an editable record collection. For editable row collections, use [Toolbar and List](./toolbar-and-list.md).

---

## 2. Anatomy

```
TabPage (or Group)                    ← subpattern applied here
├── Row 1 Group
│   ├── LabelField (StaticText or display method)
│   └── ValueField (display method or bound field, AllowEdit=No)
├── Row 2 Group
│   └── …
└── Row N Group
    └── …
```

Each "row" is typically a Group of two controls: a label on the left and a value on the right. The framework aligns these rows into a clean table-like structure.

---

## 3. Key properties

| Property / Location          | Notes                                                                                          |
|------------------------------|------------------------------------------------------------------------------------------------|
| Field `AllowEdit`            | `No` on all value fields — Tabular Fields is for display, not input                           |
| Field binding                | Display methods preferred for computed totals; bound fields with `AllowEdit=No` for stored values |
| `Group.ColumnsMode`          | Fixed (typically 2 — label column + value column) within each row Group                        |
| `font-variant-numeric`       | Numeric values should use tabular-nums alignment (handled in Fluent 2 CSS)                     |
| Value alignment              | Right-align numeric amounts; left-align text labels                                            |

---

## 4. UX guidelines

1. **Use for totals, not for input.** Every field should be `AllowEdit=No` or a display method. If the user needs to edit any value, this is the wrong subpattern — use [Fields and Field Groups](./fields-and-field-groups.md).
2. **Right-align numeric columns.** The value column in a totals summary should be right-aligned so decimal points line up vertically.
3. **Currency symbol and amount in the same row.** Don't split the currency indicator to a separate row — pair it with the amount it qualifies.
4. **Subtotal before grand total.** Order rows from most granular (line subtotal) to most aggregate (grand total). Add a visual separator (border or space) before the grand total row.
5. **Keep it compact.** 3–8 rows is typical. Beyond 8, consider whether a Grid with aggregation is more appropriate.
6. **No Add / Remove.** This is a static summary; do not add toolbar buttons above it unless they trigger a recalculation (then use [Toolbar and Fields](./toolbar-and-fields.md) instead).

---

## 5. Do / Don't

**Do**
- Use for journal balances, order totals, voucher subtotals, and similar static aggregate rows.
- Right-align all numeric values.
- Set `AllowEdit=No` on every value field.

**Don't**
- Don't mix editable and read-only fields in a Tabular Fields container — if any field is editable, use Fields and Field Groups.
- Don't use more than 8 rows without considering a grid.
- Don't put action buttons inside the container — wrap the whole thing in [Toolbar and Fields](./toolbar-and-fields.md) if actions are needed.

---

## 6. AOT mapping (dev handoff)

| Spec field          | AOT location                                                                  | Notes                                              |
|---------------------|-------------------------------------------------------------------------------|----------------------------------------------------|
| Subpattern          | `TabPage.SubPattern = TabularFields` (or `Group.SubPattern`)                  | Applied in the designer                            |
| Row Groups          | Each row is a `Group.ColumnsMode = Fixed, Columns = 2`                        | Label control left; value control right            |
| Value fields        | `<Control>.AllowEdit = No` or use display method bound via `DataMethod`       | Required — no editable fields in this subpattern   |
| Numeric alignment   | EDT carries the right-alignment for numeric types automatically                | Don't override alignment at field level            |

---

## 7. Figma component definition

- **Name:** `Subpattern/TabularFields`
- **Slot:** Stack of label–value row pairs in a 2-column layout
- **Variants:**
  - `Rows`: 3 / 5 / 8
  - `Style`: Plain / WithSubtotalDivider (adds separator before last row)
  - `Values`: Currency / Quantity / Mixed
- **Usage note:** Right-align all numeric values. Use realistic amounts (e.g. "€12,500.00", "3 lines"). Show a visual separator before the grand total row.

---

## 8. References

- Microsoft Learn — [Tabular Fields subpattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/tabular-fields-subpattern)
- [Fields and Field Groups subpattern](./fields-and-field-groups.md) — responsive flow layout for editable + mixed fields
- [Toolbar and Fields subpattern](./toolbar-and-fields.md) — wrapper when actions + tabular summary are needed together
