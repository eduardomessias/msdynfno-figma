# ActionPane — control

> **Microsoft references:**
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/action-controls
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/general-form-guidelines (ActionPane section)
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/system-defined-buttons
>
> **AOT control class:** `ActionPane` (Style = `Standard`) and `ActionPane` (Style = `Strip`) for toolbars
> **Figma component:** `Pattern/ActionPane`
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-05-21

---

## 1. Purpose

The **ActionPane** is the **primary location for page-level actions** in a D365FO Form. It sits at the top of the form and combines:

- **System-defined buttons** — added automatically by the framework (New, Delete, Save, Refresh, Attachments, Export to Excel, View/Edit, Restore, etc.).
- **Developer-defined buttons** — grouped into either:
  - **ActionPane tabs** (flyout-style, for grouping many actions by activity), or
  - **Button Groups** directly under the ActionPane (promoted, always-visible).

A **toolbar** is a local `ActionPane` with `Style = Strip` placed inside a container (e.g. above a FastTab grid). Same metadata, different visual treatment and scope.

> Dialog, Drop Dialog, and Lookup patterns are **exceptions** — they don't use a Standard Action Pane.

---

## 2. Anatomy

```
ActionPane (Style=Standard)
├── ActionPaneTab (0..N)
│   └── ButtonGroup (1..N)
│       └── MenuItemButton | CommandButton | MenuButton | DropDialogButton | Button (1..8 per group)
└── ButtonGroup (0..N)                     — promoted, always visible
    └── …buttons…
```

```
ActionPane (Style=Strip)                   — local toolbar (inside a TabPage / Group)
├── ButtonGroup (1..N)
│   └── …buttons…
```

| Node              | Type                                                                          | Notes                                                                |
|-------------------|-------------------------------------------------------------------------------|----------------------------------------------------------------------|
| `ActionPaneTab`   | Group of activity-related actions                                             | Up to **10 tabs**. First tab is the **home tab** named after the entity (singular). Last tab named **General** for infrequent, unrelated actions. |
| `ButtonGroup`     | Logical group inside a tab (or promoted)                                      | 1–8 actions per group. Common groups: **New**, **Maintain**, **Generate**, **Inquire**, **Related**. |
| `MenuItemButton`  | Bound to a Display / Action / Output menu item                                | The default and most common button type. Carries the privilege.       |
| `CommandButton`   | Runs a built-in framework command (Save, Refresh, etc.)                       | Rarely needed in app code — system provides these.                   |
| `MenuButton`      | Container for a flyout of sub-buttons                                         | Use sparingly; nested menus add depth.                                |
| `DropDialogButton`| Opens a [Drop Dialog](../patterns/drop-dialog.md)                              | Use for ≤7-field contextual inputs.                                  |
| `Button`          | Bare button; behaviour in code                                                | Avoid — prefer typed buttons.                                         |

---

## 3. Key properties (per button)

| Property         | Notes                                                                                                       |
|------------------|-------------------------------------------------------------------------------------------------------------|
| `Text`           | Button label (via label file, **sentence case** — framework upper-cases group/tab captions automatically)   |
| `Image`          | Symbol image. Required for replacement of system New/Delete; auto-show common-action symbols where available |
| `ButtonDisplay`  | `Auto` (= Text only inside ActionPane tabs / MenuButtons); for canvas buttons supports `Image + Text`, `Image only`, `Text only` |
| `MenuItem*`      | `MenuItemName`, `MenuItemType = Action | Display | Output`                                                  |
| `Privilege`      | Set on the **menu item**, not on the button                                                                  |
| `Enabled`        | Wire via `setControlState()` based on record state                                                          |
| `ShortcutKey`    | Optional accelerator (avoid colliding with browser/system shortcuts)                                         |

---

## 4. States / variants

The button visuals follow Fluent 2: `Rest`, `Hover`, `Pressed`, `Focus`, `Disabled`. Background and foreground change per [`component.tokens.json → button`](../../figma/tokens/component.tokens.json).

For the ActionPane container itself, [`component.tokens.json → actionPane`](../../figma/tokens/component.tokens.json) defines its background, border-bottom, group caption color, tab indicator, and geometry.

---

## 5. UX guidelines (consolidated from MS Learn)

1. **Page-level only** — actions on the standard ActionPane must apply to the whole entity. Use a **local toolbar** (`Style = Strip`) for actions that target a subset of the page.
2. **No more than 10 tabs**.
3. **1–8 actions per group**.
4. **Home tab first**: same name as the entity, **singular**.
5. **Activity tabs** are verb-named (e.g. **Sell**, **Pick**, **Invoice**).
   - Remove system-defined actions from Activity tabs (don't duplicate New / Delete / Save).
   - The **New** and **Maintain** groups host the New/Delete/Edit/Save/Restore actions when needed.
6. **General tab** is the last tab — for infrequent, non-activity actions. Don't repeat commands here that exist elsewhere.
7. **Labels** are sentence case in source; framework displays group / tab / button-group labels in **ALL CAPS**.
8. **Tooltips** explain any button whose label isn't fully self-evident.
9. **Button images**:
   - Replacement system buttons (custom New/Delete) use the New/Delete symbols.
   - Common actions: `ButtonDisplay = Auto` (with symbol). Otherwise `TextOnly`.
   - **Images aren't supported on ActionPane tabs.**
10. **Toolbars (`Style = Strip`)**: first buttons are **Add / Remove** (with symbols, image+text); common actions next with `ButtonDisplay = Auto`; uncommon actions are `TextOnly`.
11. **No duplicate** Refresh / Attachments / Export to Excel / Restore / OK / Done / New / Delete buttons — the framework provides them.

---

## 6. Behavior

- The **home tab** is selected by default when the Form opens.
- Tabs can be configured to **stay open** vs auto-collapse (user preference / future framework feature).
- Buttons honour the **enabled-when** rule from the spec — wire via `dataSource.active()` or a dedicated `setControlState()` method.
- Privileges live on the menu items, not on the buttons — controlling a button's **visibility** for a role means controlling the privilege on its menu item.

---

## 7. Do / Don't

**Do**
- Group activity actions into verb-named tabs (Sell, Pick, Invoice, Related).
- Promote your top 1–3 most-used actions as a Button Group directly under the ActionPane (always visible).
- Use the **Strip** style ActionPane as a local toolbar inside FastTab containers.

**Don't**
- Don't model duplicates of foundation buttons (New / Delete / Save / Refresh / Attachments / Export / OK / Done / Restore).
- Don't put record-scoped actions on the page ActionPane — push them to a local toolbar.
- Don't add images to ActionPane **tabs** (framework doesn't render them).
- Don't exceed 10 tabs or 8 buttons per group.

---

## 8. AOT mapping (dev handoff)

| Spec field            | AOT location                                                                       | Notes                                              |
|-----------------------|------------------------------------------------------------------------------------|----------------------------------------------------|
| ActionPane             | `Form > Design > ActionPane`                                                       | `Style = Standard` (top-of-form) or `Strip` (local)|
| Tab → Group → Button   | `ActionPane > ActionPaneTab > ButtonGroup > MenuItemButton`                        | Spec §5 row maps 1:1                               |
| Menu item              | `MenuItems > <Action / Display / Output>`                                          | Carries the privilege                              |
| Enabled-when           | `setControlState()` (form-level) wired from data source events                     | Avoid ad-hoc init code                             |
| Removing a system button | `Form > Design > ActionPane > SystemActionPane > <Button>.Visible = No`           | Only remove a system button to replace it with an app-owned equivalent |

---

## 9. Figma component definition

- **Name:** `Pattern/ActionPane`
- **Slots:**
  - `Slot — promoted ButtonGroup` (always visible)
  - `Tab` (repeating instance) → `Pattern/ActionPane/Tab`
- **Variants:**
  - `State`: Rest / Focus
  - `Mode`: Light / Dark / HighContrast
  - `Density`: Compact (default) / Comfortable
- **Sub-components:**
  - `Pattern/ActionPane/Tab` — with `ButtonGroup` slots
  - `Atom/Button` variants — Primary / Secondary / Subtle / Transparent

---

## 10. References

- Microsoft Learn — [Action controls](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/action-controls)
- Microsoft Learn — [General form guidelines — Standard Action Pane](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/general-form-guidelines)
- Microsoft Learn — [System-defined buttons](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/system-defined-buttons)
