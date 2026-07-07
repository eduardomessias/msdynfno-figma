# Input fields — control

> **Microsoft references:**
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/general-form-guidelines (Field types section)
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/data-validation-workspace
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/extended-data-types
>
> **AOT control classes:** `StringEdit`, `RealEdit`, `IntEdit`, `DateEdit`, `TimeEdit`, `UtcDateTimeEdit`
> **Figma components:** `Atom/Input/String`, `Atom/Input/Real`, `Atom/Input/Int`, `Atom/Input/Date`, `Atom/Input/Time`, `Atom/Input/DateTime`
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-06-25

---

## 1. Purpose

Input fields are the **atomic editable controls** for scalar data in D365FO. Each is a bound control that reads from and writes to a data source field via its **Extended Data Type (EDT)** — the EDT carries the label, help text, alignment, and validation constraints.

| Control class     | Stores                           | Typical EDTs                                    |
|-------------------|----------------------------------|-------------------------------------------------|
| `StringEdit`      | String / Memo                    | `Name`, `Description`, `Email`, `Phone`         |
| `RealEdit`        | Real (decimal)                   | `Qty`, `Price`, `Amount`, `CostPrice`           |
| `IntEdit`         | Integer / Int64                  | `LineNum`, `Qty` (whole), `RecId`               |
| `DateEdit`        | Date                             | `ShipDate`, `InvoiceDate`, `StartDate`          |
| `TimeEdit`        | Time (seconds since midnight)    | `StartTime`, `EndTime`                          |
| `UtcDateTimeEdit` | UTC date+time                    | `CreatedDateTime`, `ModifiedDateTime`           |

---

## 2. Anatomy (field + label pair)

```
Group (container)
└── StringEdit | RealEdit | … (1 per field)
    ├── Label (auto-rendered from EDT.Label)
    ├── Input area
    │   ├── Prefix icon (optional, EDT.Symbol)
    │   └── Value text
    └── Validation message (shown on error)
```

The framework always renders a **label above** the input (Top layout) or **label left** (Left layout, legacy). The label text comes from the EDT and should not be overridden at the field level unless absolutely necessary.

---

## 3. Key properties

### Common to all input fields

| Property              | Notes                                                                                            |
|-----------------------|--------------------------------------------------------------------------------------------------|
| `DataSource`          | Required — bound data source                                                                      |
| `DataField`           | Required — bound table field (carries EDT)                                                        |
| `Label`               | Override only if the EDT label is wrong in this form context; prefer fixing the EDT               |
| `HelpText`            | Override only if the EDT help text is wrong here                                                  |
| `Mandatory`           | `Yes` draws a required-field indicator (asterisk); driven by EDT or field property                |
| `AllowEdit`           | `No` renders read-only; `Auto` inherits from data source `AllowEdit`                              |
| `AllowEditOnCreate`   | `No` prevents edit on new records (useful for auto-generated IDs)                                |
| `Visible`             | Prefer dynamic visibility over design-time `No`                                                   |
| `Width`               | `Auto` (default) / `ColumnWidth` / explicit — prefer `Auto` for responsive layout               |
| `DisplayLength`       | Max visible characters — use EDT default; override only for memos                               |

### StringEdit-specific

| Property              | Notes                                                                               |
|-----------------------|-------------------------------------------------------------------------------------|
| `MultiLine`           | `Yes` for memo fields — renders a multi-line text area                              |
| `CharacterSet`        | `Unicode` / `ACP` — almost always `Unicode` for new Smartbox fields               |
| `PasswordStyle`       | `Yes` for credential fields — masks input                                           |

### RealEdit-specific

| Property              | Notes                                                                               |
|-----------------------|-------------------------------------------------------------------------------------|
| `NoOfDecimals`        | Override here only if the EDT default is wrong for this field instance             |
| `ShowZero`            | `Yes` renders `0`; `No` renders blank for zero values                              |

### DateEdit-specific

| Property              | Notes                                                                               |
|-----------------------|-------------------------------------------------------------------------------------|
| `DateDay` / `DateMonth` / `DateYear` | Override display order only when locale differs from system default |

---

## 4. States / variants

| State       | Visual                                                                   |
|-------------|--------------------------------------------------------------------------|
| Rest        | Default — light border, label above                                      |
| Hover       | Border darkens                                                            |
| Focus       | Accent-color border + focus ring (WCAG 2.1 AA)                           |
| Active      | Cursor visible, value editable                                           |
| Read-only   | No border, no cursor — value text only (greyed label)                    |
| Error       | Red/error-token border + validation message below                        |
| Disabled    | `AllowEdit = No` + `Enabled = No` — label and value both greyed          |
| Mandatory   | Asterisk next to label (`Mandatory = Yes`)                               |

---

## 5. UX guidelines

1. **Labels from EDT** — never duplicate the EDT label text into the `Label` property unless the form context demands a different label. Fix the EDT if the label is wrong globally.
2. **Field order** — match the business process, not the table column order. Most-used fields first in each Group.
3. **Alignment** — `Right`-align numeric fields (Real, Int) to enable column scanning. `Left`-align all string fields.
4. **Width** — use `Auto` for most fields. Use an explicit width only for IDs or codes with a known max length (e.g. Sales order number).
5. **Mandatory indicator** — set `Mandatory = Yes` on the EDT when the field is always required (not conditionally). Conditional mandatory is handled in `validateField()`.
6. **Read-only computed fields** — use `AllowEdit = No` and bind to a `display` method on the data source. Do not use `StringEdit` to display non-field strings; use `StaticText` instead.
7. **Memo fields** — `MultiLine = Yes` + `Height = ColumnHeight` to auto-expand with content.

---

## 6. Do / Don't

**Do**
- Bind every input to a real EDT — never use anonymous field types.
- Right-align numeric fields.
- Use `display` methods for computed read-only values.

**Don't**
- Don't set `Label` at the field level if the EDT label is correct — creates a maintenance burden when the EDT label changes.
- Don't use `StringEdit` for enumeration values — use `ComboBox` or `CheckBox`.
- Don't override `NoOfDecimals` at the field level without an architect decision — it creates a divergence from the EDT.

---

## 7. AOT mapping (dev handoff)

| Spec field              | AOT location                                                   | Notes                                                    |
|-------------------------|----------------------------------------------------------------|----------------------------------------------------------|
| Control type            | `Form > Design > … > <ControlType>`                            | Add the correct class (StringEdit, RealEdit, etc.)       |
| Data source binding     | `<Control>.DataSource` + `<Control>.DataField`                 | Both required for bound controls                         |
| EDT                     | `<Table>.<Field>.ExtendedDataType`                             | Carries label, help, size, validation                    |
| Read-only computed      | `<Control>.DataMethod` = `display` method name                 | Use instead of `DataField` for display-only              |
| Mandatory               | `<Control>.Mandatory = Yes` or EDT-level `Mandatory`           | Prefer EDT-level so all uses are consistent              |
| AllowEdit               | `<Control>.AllowEdit = Auto` (inherits DS) or explicit         | Override only when DS `AllowEdit` is not granular enough |

---

## 8. Figma component definitions

### `Atom/Input/String`
- **Slots:** Label (text), Value (text), Prefix icon (optional)
- **Variants:** State (Rest / Focus / Error / ReadOnly / Disabled), Mandatory (Yes/No), Width (Auto / ColumnWidth / Fixed)

### `Atom/Input/Real`
- Same as String + **Variant:** Alignment (Right — default for numbers)

### `Atom/Input/Int`
- Same as Real (integer, no decimals)

### `Atom/Input/Date`
- **Slots:** Label (text), Value (date text), Calendar icon
- **Variants:** State (Rest / Focus / Error / ReadOnly / Disabled), Mandatory (Yes/No)

### `Atom/Input/Time`
- **Slots:** Label (text), Value (HH:MM text), Clock icon
- **Variants:** State (Rest / Focus / Error / ReadOnly / Disabled), Mandatory (Yes/No)

### `Atom/Input/DateTime`
- Combination of Date + Time slots
- **Variants:** State, Mandatory

---

## 9. References

- Microsoft Learn — [Extended Data Types](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/extended-data-types)
- Microsoft Learn — [General form guidelines — Fields](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/general-form-guidelines)
- Microsoft Learn — [Data validation workspace](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/data-validation-workspace)
- Microsoft Learn — [Page layout](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/page-layout)
