# Boolean controls — CheckBox, RadioButton, ToggleButton

> **Microsoft references:**
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/general-form-guidelines (Check box section)
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/general-form-guidelines (Toggle section)
>
> **AOT control classes:** `CheckBox`, `RadioButton`
> **Figma components:** `Atom/Checkbox`, `Atom/Radio`, `Atom/Toggle`
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-06-25

---

## 1. Purpose

D365FO provides three controls for **binary or mutex selection**:

| Control       | Use it for                                                               | AOT class     |
|---------------|--------------------------------------------------------------------------|---------------|
| `CheckBox`    | A single boolean field (Yes/No) the user toggles independently           | `CheckBox`    |
| `RadioButton` | Mutually exclusive choice from a **small, fixed set** (2–5 options)     | `RadioButton` |
| `ToggleButton`| Binary mode switch — visually prominent, often used in toolbars/filters | Not a distinct AOT class; rendered via `Button.Style = Toggle` or a styled `CheckBox` |

> **D365FO does not have a native `Toggle` AOT class.** In practice, toggle-style switches appear via `CheckBox` styled as a toggle in the web client, or via custom controls. In Figma, `Atom/Toggle` represents the visual treatment.

---

## 2. Anatomy

### CheckBox
```
CheckBox
├── Box (☐ / ☑)
└── Label (from EDT.Label or CheckBox.Text)
```

The label is positioned **to the right** of the box (unlike most field labels which appear above). The label text comes from the control's `Text` property or the EDT label.

### RadioButton group
```
Group
└── RadioButton (2..5)
    ├── Indicator (○ / ●)
    └── Label (from RadioButton.Text)
```

RadioButton controls in the same `Group` are automatically treated as a mutex set by the framework. One RadioButton per enum value.

---

## 3. Key properties

### CheckBox

| Property       | Notes                                                                                    |
|----------------|------------------------------------------------------------------------------------------|
| `DataSource`   | Required — bound data source                                                              |
| `DataField`    | Required — must be a `NoYes` or boolean-typed EDT field                                  |
| `Text`         | Label text (to the right of the box); prefer EDT label                                   |
| `AllowEdit`    | `No` renders read-only (box visible but not clickable)                                   |
| `Checked`      | Design-time default (`Yes` / `No`); run-time state driven by data                       |

### RadioButton

| Property       | Notes                                                                                    |
|----------------|------------------------------------------------------------------------------------------|
| `DataSource`   | Required on every RadioButton in the group                                               |
| `DataField`    | Required — the same Enum field on all buttons in the group                              |
| `EnumValue`    | The specific enum value this button represents                                           |
| `Text`         | Label for this option                                                                    |
| `AllowEdit`    | `No` makes the entire group read-only                                                   |

---

## 4. States / variants

### CheckBox states

| State      | Visual                                              |
|------------|-----------------------------------------------------|
| Unchecked  | ☐ empty box                                         |
| Checked    | ☑ box with checkmark                                |
| Hover      | Box border darkens                                  |
| Focus      | Focus ring                                          |
| Disabled   | ☐ or ☑ greyed, label greyed, not interactive       |
| Read-only  | `AllowEdit = No` — visual state but no interaction |
| Error      | Error token border (rare for boolean fields)        |

### RadioButton states

| State      | Visual                                              |
|------------|-----------------------------------------------------|
| Unselected | ○ empty circle                                      |
| Selected   | ● filled circle                                     |
| Hover      | Circle border darkens                               |
| Focus      | Focus ring                                          |
| Disabled   | Greyed; not interactive                             |

---

## 5. UX guidelines

1. **CheckBox for NoYes** — whenever the field is a `NoYes` EDT, use a CheckBox, not a ComboBox. The checkbox pattern communicates boolean choice more clearly.
2. **Label placement** — CheckBox labels appear to the right, not above. Keep labels short (≤5 words). Avoid negatives ("Don't send" is confusing; use "Send notification" and let the unchecked state mean "don't send").
3. **RadioButton for 2–5 mutually exclusive options** — if the set has ≥6 values, use a ComboBox instead; the RadioButton group becomes too tall.
4. **Group RadioButtons visually** — wrap them in a named Group with a caption so the option set is identifiable.
5. **No hidden RadioButtons** — don't hide individual RadioButton options (hide the Group if the whole mutex set is conditional).
6. **ToggleButton in toolbars** — use the toggle visual treatment for mode-switch actions in a FilterPane or toolbar (e.g. "Show all / Show active"). In the AOT this is a `CheckBox` inside a `Group` with specific styling; in Figma, use `Atom/Toggle`.
7. **Don't use RadioButton for Yes/No** — use CheckBox.

---

## 6. Do / Don't

**Do**
- Use `CheckBox` for every `NoYes`-typed field.
- Label CheckBoxes with positive affirmations ("Gifting", "Send notification").
- Wrap RadioButton sets in a named Group.

**Don't**
- Don't use `ComboBox` for a boolean field — use `CheckBox`.
- Don't use RadioButton for more than 5 options — use ComboBox.
- Don't negate the label ("No confirmation required") — invert the field logic instead.

---

## 7. AOT mapping (dev handoff)

| Spec field           | AOT location                                                  | Notes                                                  |
|----------------------|---------------------------------------------------------------|--------------------------------------------------------|
| CheckBox control     | `Form > Design > … > CheckBox`                                | Add CheckBox, bind DataSource + DataField              |
| NoYes field binding  | `CheckBox.DataSource` + `CheckBox.DataField`                  | Field must be `NoYes` or `Boolean`-typed EDT           |
| Label                | `CheckBox.Text` (label ID, ≤5 words)                          | Positive affirmation; right of box                     |
| RadioButton set      | `Group > RadioButton (×N)`                                    | Same DataSource + DataField on all; unique EnumValue   |
| Read-only            | `CheckBox.AllowEdit = No` or `checkBox.allowEdit(false)`      | Whole group read-only — no per-option override         |

---

## 8. Figma component definitions

### `Atom/Checkbox`
- **Slots:** Label (text)
- **Variants:**
  - `State`: Unchecked / Checked / Indeterminate
  - `Interaction`: Rest / Hover / Focus / Disabled / ReadOnly
  - `Mandatory`: Yes / No

### `Atom/Radio`
- Individual RadioButton — compose 2–5 in a `Control/Group` to form a mutex set
- **Slots:** Label (text)
- **Variants:**
  - `State`: Unselected / Selected
  - `Interaction`: Rest / Hover / Focus / Disabled

### `Atom/Toggle`
- Visual toggle switch — maps to a styled CheckBox or toolbar button in the AOT
- **Slots:** Label (text, optional — may be icon-only in toolbars)
- **Variants:**
  - `State`: Off / On
  - `Interaction`: Rest / Hover / Focus / Disabled

---

## 9. References

- Microsoft Learn — [General form guidelines — Check boxes](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/general-form-guidelines)
- [ComboBox control](./combo.md) — for multi-value enum fields
- [Input fields](./input-fields.md) — for other scalar field types
