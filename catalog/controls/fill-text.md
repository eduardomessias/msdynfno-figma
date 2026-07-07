# Fill Text — subpattern

> **Microsoft references:**
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/fill-text-subpattern
>
> **AOT subpattern:** `FillText`
> **Figma component:** `Subpattern/FillText`
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-07-01

---

## 1. Purpose

Apply this subpattern to a container (TabPage or Group) that hosts **exactly one input control that should expand to fill the full available width and height** — typically a multi-line `StringEdit` for notes, long descriptions, or free-text comment fields.

Without this subpattern, the standard field layout applies a label column to the left, leaving the input narrower than the container. `FillText` removes that padding so the control spans edge-to-edge.

---

## 2. Anatomy

```
TabPage (or Group)                    ← subpattern applied here
└── StringEdit (or similar)           ← single control, full width + height
    ├── Label (suppressed or top-positioned)
    └── Multi-line text area
```

One control only. If more than one control is needed, the container should use [Fields and Field Groups](./fields-and-field-groups.md) instead.

---

## 3. Key properties

| Property / Location         | Notes                                                                                          |
|-----------------------------|------------------------------------------------------------------------------------------------|
| `StringEdit.MultiLine`      | `Yes` — the defining property for a notes/memo field                                           |
| `StringEdit.AutoSizeHeight` | `Yes` — grows vertically as the user types, up to the container height                         |
| `StringEdit.DisplayHeight`  | Optional initial height in rows (e.g. 4); framework expands from there if `AutoSizeHeight=Yes` |
| `StringEdit.Label`          | Can be suppressed (`""`), since the FastTab caption already names the field                     |
| `StringEdit.DataSource + DataField` | Required; bind to the memo/notes field on the table                                   |
| `TabPage.Caption`           | Name the FastTab after the field concept ("Notes", "Description") — serves as the implied label |

---

## 4. UX guidelines

1. **One field per container** — the subpattern's entire purpose is to make a single field fill its space. If you find yourself adding a second field, switch to `FieldsAndFieldGroups`.
2. **Name the FastTab after the field** — since the label is often suppressed, the tab caption ("Notes") is the only heading the user sees.
3. **`MultiLine = Yes` is mandatory.** A single-line `StringEdit` that fills the container is confusing — users expect a wide single-line field to stay single-line. If the field is truly single-line and wide, use a regular FastTab with `FieldsAndFieldGroups`.
4. **Notes fields are almost always optional.** Set `Mandatory = No`. Mark them mandatory only with explicit business justification.
5. **Read-only notes.** On posted/confirmed records, set `AllowEdit = No`. The field should still display with full width.

---

## 5. Do / Don't

**Do**
- Use for notes, descriptions, comments, and memo fields that benefit from maximum width.
- Set `StringEdit.MultiLine = Yes` and `AutoSizeHeight = Yes`.
- Name the FastTab after the field concept rather than labelling the input itself.

**Don't**
- Don't add a second field to the container — the subpattern supports exactly one.
- Don't use for single-line StringEdit fields — the fill layout is wasted on a one-line input.
- Don't suppress the FastTab caption — it's the implied label.

---

## 6. AOT mapping (dev handoff)

| Spec field                | AOT location                                                              | Notes                                          |
|---------------------------|---------------------------------------------------------------------------|------------------------------------------------|
| Subpattern                | `TabPage.SubPattern = FillText`                                           | Applied in the designer                        |
| Multi-line                | `StringEdit.MultiLine = Yes`                                              | Required                                        |
| Auto-grow                 | `StringEdit.AutoSizeHeight = Yes`                                         | Recommended                                    |
| Initial height            | `StringEdit.DisplayHeight = 4` (or as appropriate)                        | Sets the minimum visible rows                  |
| Binding                   | `StringEdit.DataSource + DataField` → memo/notes column (e.g. `Notes` `string` EDT) | Required               |

---

## 7. Figma component definition

- **Name:** `Subpattern/FillText`
- **Slot:** Single `Atom/Input/String` (multi-line variant) spanning the full container width
- **Variants:**
  - `State`: Empty / With content / ReadOnly
  - `Height`: Compact (4 rows) / Standard (6 rows) / Tall (10+ rows)
- **Usage note:** In mocks, fill with realistic notes text (2–4 sentences). Use the multi-line input variant, not the single-line variant. The input spans full width with no label column to its left.

---

## 8. References

- Microsoft Learn — [Fill Text subpattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/fill-text-subpattern)
- [Fields and Field Groups subpattern](./fields-and-field-groups.md) — use instead when more than one field is needed
- [Input Fields](./input-fields.md) — `StringEdit.MultiLine` property details
