# Drop Dialog form pattern

> **Microsoft pattern reference:** https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/drop-dialog-form-pattern
> **D365FO `Form.Design.Style` value:** `DropDialog`
> **D365FO `Form.Design.Pattern` value:** `DropDialog` / `DropDialogReadOnly`
> **Figma page:** `Patterns / Drop Dialog`
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-05-21

---

## 1. Usage — when to choose this pattern

A **Drop Dialog** is a lightweight modal that drops from a button. Use it when you need to gather a small amount of input (≤7 fields), quickly, with minimal validation. Drop Dialogs should feel as light as a menu, not as heavyweight as a full Dialog.

Two variants:

1. **Drop Dialog** (basic) — editable, with an `OK` button.
2. **Drop Dialog — Read only** — informational; **no `OK` button**.

Pick Drop Dialog over a full [Dialog](./dialog.md) when all of:
- ≤7 fields.
- User can fill it in quickly.
- Minimal field validation.
- No buttons that open further child forms (lookups, enhanced preview, View Details are OK).
- No editable grid (select-only grids are OK).

### Related patterns

- [Dialog](./dialog.md) — when any of the above is false
- [Lookup](./lookup.md) — for select-from-list affordances

---

## 2. Anatomy — the required structure

### Drop Dialog (basic)

```
Design
├── SecondaryInstruction (StaticText) [Optional]
├── DialogContent (Group)
└── DialogCommitContainer (ButtonGroup)
    └── OKButton
```

### Drop Dialog (read only)

```
Design
├── SecondaryInstruction (StaticText) [Optional]
└── DialogContent (Group)
```

| Node                    | Required? | Purpose                                                  | Figma component                  |
|-------------------------|-----------|----------------------------------------------------------|----------------------------------|
| `SecondaryInstruction`  | Optional  | Single-sentence supporting line                          | `Atom/StaticText`                |
| `DialogContent`         | Yes       | The fields                                               | `Pattern/DropDialogBody`         |
| `DialogCommitContainer` | Basic     | Right-aligned single `OK` (no Cancel)                    | `Pattern/CommitBar`              |

### Allowed subpatterns

- [Fields and Field Groups](../controls/fields-and-field-groups.md)
- [Toolbar and List](../controls/toolbar-and-list.md) — for read-only select-grids

---

## 3. Core components — BP warnings to resolve

- [ ] `Design.Caption` is not empty
- [ ] Form is referenced by at least one menu item
- [ ] `StaticText.Text` is not empty (when present)

---

## 4. UX guidelines

1. Focus lands on the **first editable field** when the drop dialog opens.
2. **Main instruction** (caption) tells the user what to do — specific statement, imperative, or question. No final period if it's a statement; question mark if it's a question.
3. **Secondary instruction** — sentence case, full stop, only if it adds meaning beyond the caption.
4. Use **constrained input controls** (radio, checkbox, combo, command links) whenever possible to avoid validation errors.
5. Provide reasonable defaults.
6. **Commit area**:
   - **No `Cancel` button** — closing the drop dialog cancels.
   - The default button is verb-labelled to match the main instruction (e.g. caption "Create new product" → button "Create"). Use `OK` only when no verb fits.
   - Buttons are **right-aligned**.

A Drop Dialog must **not** contain:
1. A toolbar or ActionPane.
2. Buttons that navigate to another page or open further dialogs (Lookups, Enhanced preview, View Details are allowed).
3. Field groups (radio button / checkbox groups are an exception).
4. A tab control.
5. FactBoxes.
6. FastTabs.

### Smartbox overrides

_None yet._

---

## 5. Do / Don't

**Do**
- Use for one-step quick actions: "Confirm order", "Add comment", "Apply discount".
- Always provide a verb-labelled default button matching the caption.
- Use constrained controls (combo, radio, check) instead of free text where possible.

**Don't**
- Don't add a `Cancel` button — it's redundant. Closing cancels.
- Don't open further dialogs or pages from a Drop Dialog button.
- Don't try to fit ≥8 fields — promote to a Dialog.

---

## 6. AOT metadata mapping (dev handoff)

| Spec field        | AOT location                                                                          | Notes                                          |
|-------------------|---------------------------------------------------------------------------------------|------------------------------------------------|
| Pattern           | `Form > Design > Pattern = DropDialog` (or `DropDialogReadOnly`)                      | Apply via designer                             |
| Content           | `Form > Design > DialogContent > <fields>`                                            | Up to 7 fields, mostly constrained controls    |
| OK button         | `Form > Design > DialogCommitContainer > OKButton`                                    | Verb label matching the caption                |
| Menu items        | Drop Dialog button on the parent form                                                 | Use `DropDialogButton` control type             |

---

## 7. Worked example

_To come._

---

## 8. References

- Microsoft Learn — [Drop Dialog form pattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/drop-dialog-form-pattern)
- Microsoft Learn — [Dialog form pattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/dialog-form-pattern)
