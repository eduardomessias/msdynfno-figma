# FactBox form patterns

> **Microsoft pattern reference:** https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/factbox-form-patterns
> **D365FO `Form.Design.Style` value:** `FormPart`
> **D365FO `Form.Design.Pattern` value:** `FactBoxGrid` / `FactBoxCard`
> **Figma page:** `Patterns / FactBox`
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-05-21

---

## 1. Usage — when to choose this pattern

A **FactBox** is a small, focused panel that renders **related information for the current record** on the right side of a host form. It saves users from opening additional forms just to glance at totals, balances, related contacts, attachments, etc.

Two variants:

1. **Form Part FactBox Grid** — when the related info is a **child collection** (multiple rows). Example: contacts for a customer.
2. **Form Part FactBox Card** — when the related info is a **set of fields about one related record**. Example: customer statistics card on the Sales order details.

The host form references the FactBox via `Form > Parts > FormPart > FormName`.

### Related patterns

- Any host pattern that allows FactBoxes ([List Page](./list-page.md), [Details Master](./details-master.md), [Details Transaction](./details-transaction.md), [Simple List and Details](./simple-list-details.md))

> **Cannot** be used inside: [Dialog](./dialog.md), [Drop Dialog](./drop-dialog.md), [Table of Contents](./table-of-contents.md), [Operational Workspace](./operational-workspace.md), [Simple Details](./simple-details.md).

---

## 2. Anatomy — the required structure

### Form Part FactBox Grid

```
Design
├── Grid
├── GridDefaultAction (Button) [Optional]
└── ButtonGroup (ButtonGroup) [Optional]
    └── Button
```

### Form Part FactBox Card

```
Design
├── FieldGroups (Group) [0..N]
│   └── Fields ($Fields, 1..N)
├── Fields ($Field) [0..N]
└── ButtonGroup (ButtonGroup) [Optional]
    └── Button
```

| Node                | Required? | Purpose                                                  | Figma component                  |
|---------------------|-----------|----------------------------------------------------------|----------------------------------|
| `Grid` (Grid variant) | Yes     | The child-collection list                                | `Control/Grid`                   |
| `FieldGroups` / `Fields` (Card variant) | Yes | The related fields                                | `Subpattern/FieldsAndFieldGroups` |
| `ButtonGroup`       | Optional  | A `(More…)` link to the backing form, etc.               | `Atom/ButtonGroup`               |

---

## 3. Core components — BP warnings to resolve

- [ ] `Design.Caption` is not empty
- [ ] `Grid.DataSource` is not empty (Grid variant)

---

## 4. UX guidelines

**General**
1. If a backing form exists, the FactBox must have a `(More…)` link to it. The names should be similar (`Customer summary` → `Customer details`).
2. The title is **not a verb** or verb phrase.
3. The title does **not contain a label to a specific record** (the record is the host form's context).
4. FactBoxes must **not** contain fields that users type into.
5. The title should fit at the FactBox's default size without truncating.

**Grid variant**
1. **1 to 4 columns** in the grid.

**Card variant**
1. Every field has a label.
2. Do not show the ID and Name of the host record (host already shows them).
3. **2 to 10 fields**.
4. Currency indicator fields are the **last** field in the FactBox.

### Smartbox overrides

_None yet._

---

## 5. Do / Don't

**Do**
- Use FactBoxes to surface counts, statistics, and "is X up to date?" answers without forcing navigation.
- Always link to the backing form via a `(More…)` link when one exists.
- Keep the title nominal — name the thing being shown, not the action.

**Don't**
- Don't put editable fields in a FactBox.
- Don't echo the host record's ID/Name in a card.
- Don't exceed 4 grid columns or 10 card fields.

---

## 6. AOT metadata mapping (dev handoff)

| Spec field         | AOT location                                                                          | Notes                                              |
|--------------------|---------------------------------------------------------------------------------------|----------------------------------------------------|
| Pattern            | `Form > Design > Pattern = FactBoxGrid` / `FactBoxCard`                               | Apply via designer                                 |
| Host wire-up       | `<HostForm> > Parts > FormPart > FormName = <ThisForm>`                               | The host form decides where the FactBox appears    |
| Grid (Grid variant)| `Form > Design > Grid`                                                                | ≤4 columns                                          |
| Fields (Card)      | `Form > Design > FieldGroups | Fields`                                                | 2–10 fields                                         |
| (More…) link       | `Form > Design > ButtonGroup > Button` → opens backing form via `MenuItem`            | Required if a backing form exists                  |

---

## 7. Worked example

_To come — Smartbox **Experience capacity** FactBox under the Sales order Experience mock._

---

## 8. References

- Microsoft Learn — [FactBox form patterns](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/factbox-form-patterns)
- Microsoft Learn — [General Form Guidelines](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/general-form-guidelines)
