# ComboBox — control

> **Microsoft references:**
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/general-form-guidelines (Combo box section)
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/extended-data-types
>
> **AOT control class:** `ComboBox`
> **Figma component:** `Atom/Combo`
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-06-25

---

## 1. Purpose

A **ComboBox** is the standard control for selecting a value from a **discrete, closed list** — backed by an AOT `BaseEnum` (enumeration). The list is fixed at design time; it cannot be user-extended at run-time.

Use a ComboBox when:
- The field is bound to an `Enum`-typed EDT (e.g. `NoYes`, `SalesStatus`, `SmbOrderCategory`).
- The list has ≤20 options and is not expected to grow via customisation.

Do **not** use a ComboBox when:
- The list is user-maintained (use a [Lookup / ReferenceGroup](./lookup-control.md) instead).
- The list is binary yes/no (use a [CheckBox](./boolean-controls.md) instead).
- The field needs a search-as-you-type experience (use a Lookup).

---

## 2. Anatomy

```
ComboBox
├── Label (from EDT.Label)
├── Selected value (text)
└── Dropdown chevron ▾
    └── OptionList (rendered by framework)
        └── EnumValue (1..N, from BaseEnum)
```

The framework renders the dropdown options from the AOT `BaseEnum` — labels come from the enum value's `Label` property. The consultant never manually lists the options.

---

## 3. Key properties

| Property            | Notes                                                                                          |
|---------------------|------------------------------------------------------------------------------------------------|
| `DataSource`        | Required — bound data source                                                                    |
| `DataField`         | Required — the table field (must be `Enum`-typed)                                               |
| `EnumType`          | Auto-filled from the EDT; specifies which `BaseEnum` populates the list                        |
| `Label`             | From EDT; override only if context requires a different label                                  |
| `Mandatory`         | `Yes` draws a required-field indicator                                                          |
| `AllowEdit`         | `No` renders read-only (no dropdown, value text only)                                          |
| `Width`             | `Auto` (default); explicit width for IDs or codes with known length                           |

---

## 4. States / variants

| State      | Visual                                                       |
|------------|--------------------------------------------------------------|
| Rest       | Border, label above, current value + chevron                 |
| Hover      | Border darkens                                               |
| Open       | Dropdown list visible, currently selected value highlighted  |
| Read-only  | No border, no chevron — value text only                      |
| Error      | Error-token border + validation message below               |
| Disabled   | Label and value both greyed                                  |
| Mandatory  | Asterisk next to label                                       |

---

## 5. UX guidelines

1. **Always bind to an EDT-backed Enum** — do not create anonymous ComboBoxes with manually typed values.
2. **≤20 enum values** — beyond that, the list becomes hard to scan and a Lookup is more appropriate.
3. **Don't use for NoYes** — `NoYes` enum → use `CheckBox` which communicates binary choice more clearly.
4. **No blank / "All" option** in a record-bound ComboBox — blanks are for filters (QuickFilter, Custom Filter Group). If the field is optional, `Mandatory = No` and leave the EDT `NoYes` default blank.
5. **Label from EDT** — the same label is used everywhere this EDT appears. Fix the EDT if the label is wrong across the board.

---

## 6. Do / Don't

**Do**
- Bind to a proper `BaseEnum` so the framework manages labels and localization.
- Set `Mandatory = Yes` for required selections.
- Use read-only state (`AllowEdit = No`) for computed or status-driven enum fields.

**Don't**
- Don't create a ComboBox for a yes/no choice — use CheckBox.
- Don't exceed 20 enum values without considering a Lookup.
- Don't add a "blank / None" value to an AOT enum just for the form — handle optionality through `Mandatory` and default values.

---

## 7. AOT mapping (dev handoff)

| Spec field          | AOT location                                                   | Notes                                            |
|---------------------|----------------------------------------------------------------|--------------------------------------------------|
| Control type        | `Form > Design > … > ComboBox`                                  | Add the ComboBox control                         |
| Enum binding        | `ComboBox.DataSource` + `ComboBox.DataField`                   | Field must be Enum-typed                         |
| Enum type           | Auto-derived from `<Table>.<Field>.ExtendedDataType.EnumType`  | Do not override `EnumType` on the control        |
| Read-only           | `ComboBox.AllowEdit = No`                                       | Or toggled via `comboBox.allowEdit(false)` in code|
| Label override      | `ComboBox.Label` (label ID)                                    | Only if EDT label is wrong for this context       |

---

## 8. Figma component definition

- **Name:** `Atom/Combo`
- **Slots:** Label (text), Selected value (text), Option list (text list — for the open state)
- **Variants:**
  - `State`: Rest / Hover / Open / ReadOnly / Error / Disabled
  - `Mandatory`: Yes / No
  - `Option count`: 3 / 5 / 10 / 20 (for mock fidelity)
- **Usage note:** In mocks, set the "Selected value" slot to the expected default or a representative sample value. List the enum values in the "Option list" slot comment for dev reference.

---

## 9. References

- Microsoft Learn — [General form guidelines — Combo box](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/general-form-guidelines)
- Microsoft Learn — [Extended Data Types — Enums](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/extended-data-types)
- [CheckBox / Boolean controls](./boolean-controls.md) — for yes/no fields
- [Lookup / ReferenceGroup](./lookup-control.md) — for user-maintained lists
