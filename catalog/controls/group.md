# Group — control

> **Microsoft references:**
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/general-form-guidelines (Groups section)
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/fields-field-groups-subpattern
>
> **AOT control class:** `Group`
> **Figma component:** `Control/Group`
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-06-25

---

## 1. Purpose

A **Group** is the fundamental **logical container** for fields, labels, and nested controls in D365FO. It maps directly to `<Group>` in the AOT tree and renders as an unstyled rectangular container (no border or shadow — unlike a FastTab, it has no collapsible header).

Groups are the building blocks inside every subpattern:
- They cluster related fields horizontally or vertically.
- They carry layout metadata (`ArrangeMethod`, `Columns`, `ColumnsMode`).
- They are the entry point for every subpattern — the pattern is applied to the **root Group** of a container.

---

## 2. Anatomy

```
Group
├── Label (static text, optional)
├── FormControl (0..N)   — StringEdit, ComboBox, ReferenceGroup, …
└── Group (nested, optional)   — for sub-grouping within a subpattern
```

Groups are always **inside** a parent container (TabPage, Design, another Group). They are never the top-level element of a form.

---

## 3. Key properties

| Property              | Purpose                                                                                      | Notes                                                                       |
|-----------------------|----------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------|
| `Caption`             | Optional visible label above the group                                                        | Leave empty if the surrounding context (FastTab heading, subpattern) makes the label redundant |
| `ArrangeMethod`       | `HorizontalLeft` / `HorizontalRight` / `VerticalTop` / `VerticalBottom`                      | Controls field flow direction inside the group                              |
| `Columns`             | Integer — number of layout columns                                                            | Framework default is 2; `ColumnsMode = Auto` overrides                     |
| `ColumnsMode`         | `Auto` (framework chooses) / `Fixed` (use `Columns` value)                                   | Prefer `Auto` unless a fixed layout is required by the pattern              |
| `FrameType`           | `None` (default) / `Box` / `Auto`                                                             | `Box` draws a visible border — reserved for special groupings; do not use without UX justification |
| `Width`               | `Auto` / `ColumnWidth` / explicit px                                                          | `Auto` is almost always correct; `ColumnWidth` stretches to the layout column |
| `Visible`             | `Yes` / `No`                                                                                 | Use data methods or display logic, not hard-coded `No`, when visibility is conditional |
| `DataSource`          | Recommended; BP rule enforced on some patterns                                                | Set on every Group that contains bound fields; left empty only on layout-only Groups |

---

## 4. Layout patterns

D365FO uses a **column-based flow layout**:

1. Fields inside a Group flow **horizontally** by default — left to right, then wrap.
2. Setting `Columns = 1` creates a **single-column** block (useful for wide fields like memo strings).
3. Setting `ArrangeMethod = VerticalTop` stacks children top-to-bottom with no wrapping.

Within a FastTab or Dialog, the **standard two-column layout** is achieved by placing fields directly in a Group with `ArrangeMethod = HorizontalLeft, Columns = Auto`. The framework balances columns responsive to the viewport.

---

## 5. UX guidelines

1. **Caption only when it adds information** — if a Group is the sole content of a FastTab named "General", the Group caption "General" is redundant; leave it empty.
2. **Keep groups shallow** — avoid nesting more than two levels deep (Group > Group > Field). Deep nesting makes BP compliance harder and muddies the Figma structure.
3. **No `FrameType = Box`** in standard form patterns — bordered groups are allowed only inside Dialog and Drop Dialog for explicit visual separation.
4. **Data source** — always set so that conditional visibility and validation work correctly.

---

## 6. Do / Don't

**Do**
- Set `DataSource` on every Group that contains bound controls.
- Use `ColumnsMode = Auto` to let the framework handle responsive column count.
- Give the Group a `Caption` when it represents a logical sub-section within a FastTab body.

**Don't**
- Don't use `FrameType = Box` in main-form patterns — it's reserved for Dialog groupings.
- Don't hard-code `Visible = No`; use a display method or `allowEdit()` pattern.
- Don't nest Groups more than two levels deep.

---

## 7. AOT mapping (dev handoff)

| Spec field          | AOT location                                                     | Notes                                            |
|---------------------|------------------------------------------------------------------|--------------------------------------------------|
| Caption             | `Group.Caption` (via label ID)                                   | Empty if label is redundant                      |
| Layout columns      | `Group.ArrangeMethod` + `Group.ColumnsMode` / `Group.Columns`    | `Auto` for most cases                            |
| Data source         | `Group.DataSource`                                               | Required by BP when Group contains bound fields  |
| Visibility          | `Group.Visible` or `FormGroup.visible()` in `init()`            | Prefer run-time method over design-time property |
| Frame               | `Group.FrameType`                                                | `None` for standard patterns; `Box` for dialogs  |

---

## 8. Figma component definition

- **Name:** `Control/Group`
- **Slots:**
  - `Slot — content` (free-form; typically populated with field atoms or a subpattern component)
- **Variants:**
  - `Frame`: None / Box
  - `Columns`: 1 / 2 / Auto
  - `Caption`: Show / Hide
- **Usage note:** In Figma mocks, use `Control/Group` as the container frame inside a FastTab body before the subpattern is chosen. Once a subpattern is selected, swap for the matching `Subpattern/*` component.

---

## 9. References

- Microsoft Learn — [General form guidelines — Groups](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/general-form-guidelines)
- Microsoft Learn — [Fields and Field Groups subpattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/fields-field-groups-subpattern)
- Microsoft Learn — [Page layout in the web client](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/page-layout)
