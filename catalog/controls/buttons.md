# Buttons — control

> **Microsoft references:**
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/action-controls
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/system-defined-buttons
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/general-form-guidelines (Button section)
>
> **AOT control classes:** `MenuItemButton`, `CommandButton`, `MenuButton`, `DropDialogButton`, `Button`
> **Figma components:** `Atom/Button`, `Atom/Button/Menu`, `Atom/Button/DropDialog`
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-06-25

---

## 1. Purpose

D365FO has **five button types**, each with a distinct contract between the UI and the application layer:

| Type                | Fires                                 | Primary use                                                     |
|---------------------|---------------------------------------|-----------------------------------------------------------------|
| `MenuItemButton`    | Navigates to a menu item (Display, Output, Action) | ActionPane commands, open related forms                |
| `CommandButton`     | Calls a `SysCommand` or system action | System-defined buttons (Save, New, Delete, Refresh, …)         |
| `MenuButton`        | Opens a **drop-down menu** of child buttons | Action groups too numerous for a flat button strip    |
| `DropDialogButton`  | Opens a **Drop Dialog** form inline   | Contextual input before an action (e.g. "Print settings")      |
| `Button`            | Calls an `clicked()` method override  | Legacy / non-framework actions; avoid for new forms            |

> **System-defined buttons** (New, Delete, Save, Edit, Restore, Attachments, Export to Excel, etc.) are added automatically by the framework and typed `CommandButton`. Do **not** re-create them manually.

---

## 2. Anatomy — where buttons live

```
ActionPane (Style=Standard)
└── ActionPaneTab
    └── ButtonGroup
        └── MenuItemButton | MenuButton | DropDialogButton   ← page-level actions

Tab (Style=Strip) / Group (toolbar)
└── ButtonGroup
    └── MenuItemButton | Button                               ← local toolbar actions

Group (inline)
└── Button | MenuItemButton                                   ← field-adjacent actions (e.g. "Lookup")
```

---

## 3. Key properties

### Common to all buttons

| Property          | Notes                                                                                     |
|-------------------|-------------------------------------------------------------------------------------------|
| `Text`            | Button label — verb-phrase, sentence case ("Confirm order", not "CONFIRM ORDER")          |
| `Image`           | Icon resource; set from the standard Fluent 2 / D365FO icon library                      |
| `ShowShortLabel`  | `Yes` shows the `ShortText` label under the icon on promoted buttons                     |
| `ShortText`       | Short label (≤12 chars) shown under icon when `ShowShortLabel = Yes`                     |
| `Enabled`         | Toggled at run-time (`button.enabled(false)`) to grey out unavailable actions             |
| `Visible`         | Toggled at run-time to hide buttons the user lacks privilege for                          |
| `SecurityKey`     | If set, the button auto-hides when the user lacks the referenced security privilege       |
| `NeedsRecord`     | `Yes` — button is disabled when no record is selected (BP default for most commands)     |

### MenuItemButton-specific

| Property            | Notes                                                                               |
|---------------------|-------------------------------------------------------------------------------------|
| `MenuItemType`      | `Display` / `Output` / `Action` — must match the menu item type exactly             |
| `MenuItemName`      | Name of the `MenuItemDisplay`, `MenuItemOutput`, or `MenuItemAction` AOT object      |
| `DataSource`        | The DS whose current record is passed to the called form                            |

### DropDialogButton-specific

| Property            | Notes                                                                               |
|---------------------|-------------------------------------------------------------------------------------|
| `MenuItemType`      | `Display` (points to the Drop Dialog form)                                          |
| `MenuItemName`      | The Drop Dialog form's `Display` menu item                                          |

---

## 4. States / variants

| State      | Visual                                                          |
|------------|-----------------------------------------------------------------|
| Rest       | Flat label + icon; no background                                |
| Hover      | Light fill background                                           |
| Pressed    | Darker fill                                                     |
| Focus      | Focus ring (keyboard navigation)                               |
| Disabled   | Label and icon greyed; `Enabled = false`                       |

**Promoted buttons** (in the ButtonGroup directly under ActionPane, not under a Tab) render with a larger icon and label beneath — the "primary action" treatment.

---

## 5. UX guidelines

1. **Verb labels** — every button label is a verb or verb phrase: "Confirm", "Post invoice", "Generate vouchers". Never a noun only ("Invoice" as a label is ambiguous — "Post invoice" is clear).
2. **Icons** — use the standard D365FO icon set. Only promoted buttons (in the first ActionPane ButtonGroup or a toolbar) render both icon + label. Tab-level buttons show label only.
3. **NeedsRecord = Yes** is the default — disable buttons that operate on the current record when no record is selected. Explicitly set `NeedsRecord = No` only for "New" and form-level actions.
4. **≤8 buttons per ButtonGroup** — beyond 8, create a new ButtonGroup or promote to a MenuButton.
5. **MenuButton** for more than 5 related sub-actions — groups that would otherwise create button sprawl.
6. **DropDialogButton** when the action needs 1–7 inputs before executing — this avoids a full dialog form.
7. **No custom `Button` type for new forms** — always prefer `MenuItemButton` so the action is tied to a menu item and inherits security.

---

## 6. Do / Don't

**Do**
- Tie commands to `MenuItemButton` → `MenuItemAction` for proper security and privilege assignment.
- Use sentence-case verb labels.
- Set `NeedsRecord = Yes` for commands that operate on the selected record.

**Don't**
- Don't re-create system-defined buttons (New, Delete, Save, etc.) — the framework adds them automatically.
- Don't exceed 8 buttons per ButtonGroup without introducing a `MenuButton` group.
- Don't use raw `Button` type for new Smartbox forms — it bypasses the security framework.

---

## 7. AOT mapping (dev handoff)

| Spec field             | AOT location                                                                     | Notes                                              |
|------------------------|----------------------------------------------------------------------------------|----------------------------------------------------|
| Button type            | Right-click ButtonGroup → Add → `MenuItemButton` / `MenuButton` / `DropDialogButton` | Choose correctly — can't change type after creation |
| Menu item binding      | `MenuItemButton.MenuItemName` + `MenuItemButton.MenuItemType`                    | The menu item must exist and be privileged          |
| Label                  | `Button.Text` (via label ID)                                                     | Verb phrase; sentence-case                         |
| Icon                   | `Button.ImageLocation` + `Button.Image`                                          | Use standard D365FO image resource name            |
| Enabled condition      | `button.enabled(condition)` in `FormRun.enableButtons()` or `DS.active()`       | Called whenever selection or record state changes  |
| Security               | `Button.SecurityKey` or `Button.MenuItemName`→ privilege                         | Prefer menu item privilege over explicit SecurityKey |

---

## 8. Figma component definitions

### `Atom/Button`
- Standard button — used in toolbars and inline contexts
- **Slots:** Label (text), Icon (icon component, optional)
- **Variants:**
  - `Type`: Default / Primary / Danger
  - `State`: Rest / Hover / Pressed / Focus / Disabled
  - `Size`: Small (toolbar) / Medium (dialog) / Large (promoted)
  - `Icon position`: Left / None

### `Atom/Button/Menu`
- `MenuButton` — opens a dropdown list of child actions
- **Slots:** Label (text), Chevron (auto)
- **Variants:** State (Rest / Hover / Open / Disabled)

### `Atom/Button/DropDialog`
- Button that opens a Drop Dialog inline
- Same visual as `Atom/Button`; annotate in the mock with `→ Pattern/DropDialog`

---

## 9. References

- Microsoft Learn — [Action controls](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/action-controls)
- Microsoft Learn — [System-defined buttons](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/system-defined-buttons)
- Microsoft Learn — [General form guidelines — Buttons](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/general-form-guidelines)
- [ActionPane control](./action-pane.md) — where buttons live on page-level forms
- [Drop Dialog pattern](../patterns/drop-dialog.md) — the form opened by `DropDialogButton`
