# Dialog form pattern

> **Microsoft pattern reference:** https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/dialog-form-pattern
> **D365FO `Form.Design.Style` value:** `Dialog`
> **D365FO `Form.Design.Pattern` value:** `Dialog` / `DialogReadOnly` / `DialogWithTabs` / `DialogWithFastTabs` / `DialogDoubleTabs`
> **Figma page:** `Patterns / Dialog`
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-05-21

---

## 1. Usage — when to choose this pattern

A **Dialog** represents an action or activity that the user must explicitly commit or cancel. Dialogs are **modal** — the user must interact with the dialog before returning to the parent page. Used when the system needs input on how to proceed before completing a task.

Five variants:

1. **Dialog (basic)** — default. Optional ActionPane + DialogHeader + DialogContent + commit buttons.
2. **Dialog (read only)** — informational only; uses a `Close` button (no OK/Cancel).
3. **Dialog w/ Tabs** — content organised into a tab control.
4. **Dialog w/ FastTabs** — content organised into FastTabs.
5. **Dialog w/ double tabs** — two stacked tab controls (rare).

### Sizes — pick deliberately

| Size     | Width                | Use when                                              |
|----------|----------------------|-------------------------------------------------------|
| Small    | 1 column             | Few simple fields, no wide tables                     |
| Medium   | 2 columns            | More content than a small can comfortably hold        |
| Large    | 3 columns            | More content than medium, but full-width not needed   |
| Full     | Viewport-wide minus peek | Wide elements (grids), or genuinely lots of space  |

For Drop Dialog (≤7 fields, lightweight), use [Drop Dialog](./drop-dialog.md) instead.

### Related patterns

- [Drop Dialog](./drop-dialog.md) — lighter modal for ≤7 fields
- [Advanced Selection](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/advanced-selection-form-pattern) — wide-list select-and-add modal

---

## 2. Anatomy — the required structure

### Dialog (basic)

```
Design
├── SecondaryInstruction (StaticText)   [Optional]
├── ActionPane (ActionPane)             [Optional]
├── DialogHeader (Group, can repeat)    [Optional]
├── DialogContent (Group, repeats 1..N)
└── DialogCommitContainer (ButtonGroup)
    ├── OKButton
    ├── OtherButton ($Button, can repeat) [Optional]
    └── CancelButton
```

### Dialog w/ Tabs and Dialog w/ FastTabs

```
Design
├── SecondaryInstruction [Optional]
├── ActionPane [Optional]
├── DialogHeader [Optional]
├── TabContent (Tab)
│   └── TabPage (1..N)
├── DialogFooter (Group) [Optional]
└── DialogCommitContainer
    ├── OKButton
    ├── OtherButton [Optional]
    └── CancelButton
```

### Dialog w/ double tabs

Same as Tabs but with **two** stacked `TabContent` controls.

### Dialog (read only)

Same as basic but `DialogCommitContainer` contains a single `CloseButton`.

| Node                  | Required? | Purpose                                                | Figma component                  |
|-----------------------|-----------|--------------------------------------------------------|----------------------------------|
| `SecondaryInstruction`| Optional  | Sentence-case helper line under the caption            | `Atom/StaticText`                |
| `DialogHeader`        | Optional  | Identifying field group                                | `Subpattern/HorizontalFieldsGroup` |
| `DialogContent`       | Yes       | The form fields / tabs                                 | `Pattern/DialogBody`             |
| `DialogCommitContainer` | Yes     | OK + Cancel (or Close for read-only)                   | `Pattern/CommitBar`              |

### Allowed subpatterns

- [Fields and Field Groups](../controls/fields-and-field-groups.md)
- [Toolbar and List](../controls/toolbar-and-list.md)
- [Toolbar and Fields](../controls/toolbar-and-fields.md)
- [Fill Text](../controls/fill-text.md)

---

## 3. Core components — BP warnings to resolve

- [ ] `Design.Caption` is not empty
- [ ] Form is referenced by at least one menu item
- [ ] `StaticText.Text` is not empty (when present)

---

## 4. UX guidelines

1. Right-most button is **Cancel** (cancels without side effects), in both editable and read-only dialogs.
2. There is a **default** button — the safest, most secure response.
3. The default button should not be destructive unless undoing is obvious and easy.
4. A dialog must **not** contain FactBoxes.
5. Switching tabs / expanding FastTabs must **not** cause the dialog to resize.
6. Choose the dialog size from the table above — for grids, prefer wider sizes so horizontal scrolling can be avoided.

### Smartbox overrides

_None yet._

---

## 5. Do / Don't

**Do**
- Always provide a clear, verb-leading caption that describes the user's task.
- Set the safest button as the default (e.g. `Save` rather than `Delete`).
- Pick the smallest size that fits without horizontal/vertical scrolling.

**Don't**
- Don't model FactBoxes inside dialogs.
- Don't change dialog size as the user navigates tabs.
- Don't use Dialog when ≤7 fields will fit — use [Drop Dialog](./drop-dialog.md).

---

## 6. AOT metadata mapping (dev handoff)

| Spec field           | AOT location                                                                                  | Notes                                              |
|----------------------|-----------------------------------------------------------------------------------------------|----------------------------------------------------|
| Pattern              | `Form > Design > Pattern = Dialog` / `DialogReadOnly` / `DialogWithTabs` / `…WithFastTabs` / `…DoubleTabs` | Apply via designer                          |
| Dialog content       | `Form > Design > DialogContent` (or `TabContent > TabPage`)                                   | Apply subpattern per content shape                 |
| Commit container     | `Form > Design > DialogCommitContainer`                                                       | Right-most is Cancel; default is the safe choice    |
| Size                 | `Form > Design > Style = Dialog`, `WindowType = SizeableSize…` (Small/Medium/Large/Full)      | Per the dialog size table                          |
| Menu items           | Display menu item attached to a button on the parent form                                     | Dialogs are typically opened from a parent surface |

---

## 7. Worked example

_To come._

---

## 8. References

- Microsoft Learn — [Dialog form pattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/dialog-form-pattern)
- Microsoft Learn — [Drop Dialog form pattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/drop-dialog-form-pattern)
- Microsoft Learn — [Advanced Selection form pattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/advanced-selection-form-pattern)
