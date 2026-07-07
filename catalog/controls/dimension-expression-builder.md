# Dimension Expression Builder — subpattern

> **Microsoft references:**
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/financial/dimension-expression-builder-subpattern
>
> **AOT subpattern:** `DimensionExpressionBuilder`
> **Figma component:** `Subpattern/DimensionExpressionBuilder`
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-07-01

---

## 1. Purpose

A specialised composite control for **building financial dimension filter expressions** — range queries that include or exclude combinations of dimension values. Used in:

- **Budget control rules** — defining which account/dimension combinations a rule applies to
- **Financial reporting row and column definitions** — specifying account ranges and dimension filters
- **Ledger settlement rules** and similar finance configuration forms

The output is a structured filter string (e.g. `110100..110999+200000, -CC-EAST`) that the financial framework evaluates at runtime against transactions.

---

## 2. Anatomy

```
Group (DimensionExpressionBuilder)
├── DimensionSelector               ← dimension to filter on (Combo: Main account / Dept / …)
├── OperatorSelector                ← Include / Exclude (Combo)
└── ValueEntry                      ← the expression value
    └── SegmentedEntry              ← resolves to the dimension's lookup/validation
        ├── Value input             ← single value, range (110100..110999), or wildcard (*)
        └── Expression chips        ← accumulated filter tokens (read-only display)
```

The full expression is built token by token: the user picks a dimension, an operator (include/exclude), and a value or range; each token is added to the accumulated expression.

---

## 3. Key properties

| Property / Location              | Notes                                                                                            |
|----------------------------------|--------------------------------------------------------------------------------------------------|
| Dimension selector               | Determines which dimension's lookup and validation applies to the value entry                     |
| Expression syntax                | `A..B` for range; `*` for wildcard; `,` to separate tokens; `-` prefix to exclude               |
| Value validation                 | Real-time against the dimension's allowed values (account structure, dimension set)              |
| `parmDimensionAttributeName()`   | Binds the selected dimension context to the value entry control                                  |
| Output field                     | Typically a `StringEdit` with `AllowEdit=No` showing the accumulated expression string           |

---

## 4. UX guidelines

1. **This control is for finance configuration, not transactional entry.** It appears on setup forms (budget control configuration, financial reporting designer) — not on daily transaction forms. Users are typically finance managers or system administrators.
2. **The expression syntax is not self-evident** — include a Help text or tooltip on the value entry field explaining the range (`..`), wildcard (`*`), and exclude (`-`) operators.
3. **Validate incrementally.** Each token is validated when added. Show an inline error on the ValueEntry if the entered range is structurally invalid (e.g. `110999..110100` — reversed range).
4. **Provide a "Clear" button** to reset the expression from scratch. Don't require the user to delete tokens individually if they want to start over.
5. **Show the full expression in a read-only StringEdit** below the builder — this lets the user review the accumulated expression and copy it if needed.

---

## 5. Do / Don't

**Do**
- Use only on finance configuration and setup forms.
- Include Help text explaining the expression syntax.
- Show the accumulated expression in a read-only field below the builder.
- Provide a Clear button.

**Don't**
- Don't use for transactional dimension entry — use [Dimension Entry Control](./dimension-entry.md) instead.
- Don't suppress the validation on range inputs — real-time validation prevents malformed expressions.
- Don't allow saving if the expression contains invalid tokens.

---

## 6. AOT mapping (dev handoff)

| Spec field                    | AOT location                                                                              | Notes                                                         |
|-------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------------------------------|
| Subpattern                    | `Group.SubPattern = DimensionExpressionBuilder`                                           | Applied on the Group hosting the builder components           |
| Dimension selector            | `ComboBox` populated from `DimensionAttribute` table                                      | Drives which dimension's values the ValueEntry resolves against|
| Value entry                   | `DimensionExpressionBuilderControl` (custom control class)                                | Provides the SegmentedEntry + expression token UI             |
| Expression output             | Bound `StringEdit.AllowEdit=No` on the same data source                                   | Displays the full accumulated expression string               |
| X++ integration               | `parmDimensionAttributeName()` on the control; `getDimensionExpression()` to read result | Standard API — see Microsoft Learn uptake guide               |

---

## 7. Figma component definition

- **Name:** `Subpattern/DimensionExpressionBuilder`
- **Slots:**
  - `DimensionSelector` — ComboBox (e.g. "Main account ▾")
  - `OperatorSelector` — ComboBox (e.g. "Include ▾")
  - `ValueEntry` — SegmentedEntry-style input (e.g. "110100..110999")
  - `AddTokenButton` — "Add" action button
  - `ExpressionDisplay` — read-only StringEdit showing accumulated expression (e.g. "110100..110999, -CC-EAST")
  - `ClearButton` — "Clear" action button
- **Variants:**
  - `State`: Empty / With expression / Error (invalid token)
- **Usage note:** This is a configuration control appearing on setup and parameter forms only. Show with a realistic expression already built (2–3 tokens). Include a note in the mock: *"Used in budget control configuration — not on transactional forms."*

---

## 8. References

- Microsoft Learn — [Dimension Expression Builder subpattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/financial/dimension-expression-builder-subpattern)
- [Dimension Entry Control subpattern](./dimension-entry.md) — for transactional dimension entry (different use case)
- [Segmented Entry control](./segmented-entry.md) — the underlying segment input control
- [Toolbar and Fields subpattern](./toolbar-and-fields.md) — often wraps this builder when additional actions are needed
