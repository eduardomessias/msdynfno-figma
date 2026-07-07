# Tab / TabPage (Standard) — control

> **Microsoft references:**
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/general-form-guidelines (Tabs section)
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/tab-subpattern
>
> **AOT control class:** `Tab` (Style = `Tabs`) containing `TabPage` children
> **Figma component:** `Control/Tab`
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-06-25

---

## 1. Purpose

A **Standard Tab** (`Tab.Style = Tabs`) renders a **horizontal tab strip** with persistent tab headers. Unlike a FastTab (which collapses), a Standard Tab keeps all headers visible at once and only shows the content of the **active** tab.

Standard Tabs are used in two contexts inside D365FO:

| Context | Description |
|---------|-------------|
| **Line Details area** in Details Transaction | The lower panel showing per-line detail in multiple tabs (General, Setup, Address, …) |
| **Dialog** with `WithTabs` variant | A Dialog that organises a large input set across multiple tabs |
| **Simple List and Details** with `StandardTabs` | The details panel variant |

> Compare with `Tab.Style = FastTabs` → [FastTab control](./fast-tab.md) and `Tab.Style = VerticalTabs` → [Table of Contents pattern](../patterns/table-of-contents.md).

---

## 2. Anatomy

```
Tab (Style=Tabs)
└── TabPage (1..N)
    └── Group | Tab (nested, for complex dialogs)
        └── FormControls…
```

| Node        | Required? | Purpose                                                  | Figma component      |
|-------------|-----------|----------------------------------------------------------|----------------------|
| `Tab`       | Yes       | Wrapper; `Style = Tabs`                                  | `Control/Tab`        |
| `TabPage`   | Yes (≥1)  | One tab; `Caption` is the tab header label               | (variant of Tab)     |
| Body        | Yes       | Fields / subpattern content inside the active tab        | `Subpattern/*`       |

---

## 3. Key properties

| Property            | Notes                                                                                     |
|---------------------|-------------------------------------------------------------------------------------------|
| `Tab.Style`         | `Tabs` for standard horizontal tabs                                                        |
| `TabPage.Caption`   | Required; sentence-case label ID. This is the text on the tab header.                    |
| `TabPage.DataSource`| Recommended; set on every TabPage that contains bound controls                            |
| `Tab.Width`         | `ColumnWidth` to stretch across its container                                              |
| `TabPage.Visible`   | Can be toggled at run-time for security/context-sensitive tabs                             |
| `TabPage.AllowEdit` | Can be toggled at run-time to make a tab read-only                                        |

---

## 4. States / variants

| Variant | Description |
|---------|-------------|
| `Active` | The currently selected tab — content visible |
| `Inactive` | Header visible, content hidden |
| `Hover` | Mouse-over state on an inactive tab header |
| `Disabled` | Tab header visible but not interactive |
| `Hidden` | Tab not rendered (visibility driven by code) |

---

## 5. UX guidelines

1. **Maximum 7 tabs** per tab strip — beyond that, use a Table of Contents pattern or rethink the information architecture.
2. **Label the first tab** after the primary data context of the form area (e.g. "General", not "Tab 1").
3. **No sub-tabs** inside a TabPage within a Standard Tab — nest a FastTab instead if sectioning is needed.
4. **Active tab persistence** — the framework remembers the last active tab per form/user. Don't force `ActiveTab` programmatically unless onboarding UX requires it.
5. **Keyboard:** `Ctrl+Tab` / `Ctrl+Shift+Tab` to move between tabs; `Tab` to move focus within a tab's content.

---

## 6. Do / Don't

**Do**
- Use Standard Tabs for Line Details in Details Transaction forms — this is the prescribed pattern.
- Set `Caption` via a label ID so the UI can be localized.
- Set `DataSource` on each `TabPage` to enable BP compliance.

**Don't**
- Don't use Standard Tabs as the primary field-grouping mechanism on a Details Master or Details Transaction header — use FastTabs there.
- Don't exceed 7 tabs; beyond 7, the tab strip overflows and becomes unusable.
- Don't nest Standard Tabs inside Standard Tabs.

---

## 7. AOT mapping (dev handoff)

| Spec field            | AOT location                                          | Notes                                              |
|-----------------------|-------------------------------------------------------|----------------------------------------------------|
| Tab container         | `Form > Design > … > Tab (Style=Tabs)`                | One TabPage child per logical tab                  |
| TabPage caption       | `TabPage.Caption` (label ID)                          | Required; renders as tab header text               |
| TabPage data source   | `TabPage.DataSource`                                  | Required by BP                                     |
| Active tab on open    | `Tab.ActiveTab` property or `element.page(<n>)` call  | Leave at default unless UX specifically requires   |
| Conditional tab       | `tabPageName.visible(false)` in `FormRun.init()`      | Prefer dynamic visibility over design-time `No`    |

---

## 8. Figma component definition

- **Name:** `Control/Tab`
- **Slots:**
  - `Tab strip` — array of tab header labels (text)
  - `Slot — active content` (instance swap; default is `Subpattern/FieldsAndFieldGroups`)
- **Variants:**
  - `Active tab`: 1 / 2 / 3 / 4 / 5 (which tab is selected in the mock)
  - `Tab count`: 2 / 3 / 4 / 5 / 6 / 7
  - `Mode`: Light / Dark / HighContrast

---

## 9. References

- Microsoft Learn — [General form guidelines — Tabs](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/general-form-guidelines)
- Microsoft Learn — [Tab subpattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/tab-subpattern)
- [FastTab control](./fast-tab.md) — `Tab.Style = FastTabs`
- [Table of Contents pattern](../patterns/table-of-contents.md) — `Tab.Style = VerticalTabs`
