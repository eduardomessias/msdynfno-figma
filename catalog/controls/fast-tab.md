# FastTab — control

> **Microsoft references:**
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/general-form-guidelines (FastTabs section)
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/toolbar-list-subpattern
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/fields-field-groups-subpattern
>
> **AOT control class:** `Tab` with `Style = FastTabs` containing `TabPage` children
> **Figma component:** `Control/FastTab`
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-05-21

---

## 1. Purpose

A **FastTab** is an **expandable / collapsible section** of a Form. It's the dominant grouping mechanism for fields on D365FO details forms — Details Master, Details Transaction (Header view + Line Details), Simple Details, Operational Workspace content sections, and Dialog (when content is grouped into FastTabs).

Each FastTab:
- Has a header with a caption and **summary fields** (key fields that stay visible when collapsed).
- Can be **expanded** to reveal its full content area, or **collapsed** to its header.
- Hosts fields, grids, or sub-controls via the matching subpattern.

> A `Tab` control with `Style = FastTabs` renders FastTabs; with `Style = Tabs` it renders standard tabs; with `Style = VerticalTabs` it renders the [Table of Contents](../patterns/table-of-contents.md) layout.

---

## 2. Anatomy

```
Tab (Style=FastTabs)
└── TabPage (1..N)   — one TabPage per FastTab
    ├── Header
    │   ├── Caption
    │   └── SummaryFields (0..N)
    └── Body                                — uses a subpattern:
        ├── Fields and Field Groups, OR
        ├── Toolbar and Fields, OR
        ├── Toolbar and List, OR
        ├── Nested Simple List and Details, …
```

| Node            | Purpose                                                       | Notes                                                                  |
|-----------------|---------------------------------------------------------------|------------------------------------------------------------------------|
| `TabPage`       | One FastTab                                                   | `Caption` not empty, `DataSource` not empty (BP enforced)              |
| Caption         | Header text                                                   | Sentence case in source; framework renders                              |
| Summary fields  | Key fields shown in the header when the FastTab is collapsed  | Set on each field with `IsSummaryField = Yes`. Not supported: ReferenceGroups, display methods, unbound fields. |
| Body            | The fields / grid / nested form rendered when expanded         | Always apply a subpattern to the TabPage's first container             |

---

## 3. Key properties

| Property             | Notes                                                                                                       |
|----------------------|-------------------------------------------------------------------------------------------------------------|
| `Tab.Style`          | `FastTabs` for this control type                                                                            |
| `TabPage.Caption`    | Required, via label file                                                                                    |
| `TabPage.DataSource` | Required (BP). Set even when fields come from a sub-data source                                              |
| `FastTabExpanded`    | Initial state. Default-expand only the first FastTab                                                         |
| `FastTabSummary`     | Field-level property — promotes a field as a summary value to the collapsed header                          |
| `ArrangeMethod` / `Columns` | Internal layout of fields on the FastTab (see [Page layout](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/page-layout)) |

---

## 4. States / variants

The header has the usual Fluent 2 states (`Rest`, `Hover`, `Pressed`, `Focus`, `Disabled`). The content area is either `Expanded` (visible) or `Collapsed` (hidden).

Bound tokens (`component.tokens.json → tab.fastTab`):
- `headerBackground.rest` / `hover`
- `headerForeground.rest`
- `summaryForeground.rest`
- `divider.color`

---

## 5. UX guidelines (consolidated from MS Learn)

1. **First FastTab** contains the most important fields (the ones edited most often) and is **fully visible without scrolling** in the default state.
2. Field layout **flows across** the FastTab (left-to-right, then wraps), not strictly two columns.
3. FastTabs that contain **only a grid** are not expected to have summary fields.
4. **Dimensions FastTab pages** can't display summary fields (framework limitation).
5. **No horizontal scrolling** within a FastTab — the form layout must adapt.
6. If a FastTab contains a grid, apply the [Toolbar and List](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/toolbar-list-subpattern) subpattern to the TabPage.
7. **Keyboard**:
   - `Alt+Shift+Down/Up` — next/previous FastTab.
   - `Alt+<n>` — go to the n-th FastTab.
   - `Space` / `Enter` on header — expand.
   - `Alt+0` — collapse current FastTab.

---

## 6. Behavior

- Multiple FastTabs can be open at the same time.
- Summary fields are read-only-rendered in the collapsed header — clicking a value drills via `View details` if the EDT supports it.
- FastTabs participate in responsive layout: when the viewport is wide, fields flow into more columns; on narrow viewports they collapse into one column.

---

## 7. Do / Don't

**Do**
- Default-expand only the **first** FastTab — collapse the rest.
- Promote 1–3 high-value fields per FastTab as summary fields.
- Keep the first FastTab's content visible without scrolling.

**Don't**
- Don't put reference groups, display methods, or unbound fields as summary fields — framework won't render them.
- Don't nest FastTabs (use a Standard Tab inside a TabPage's Body if you need grouping below the FastTab).
- Don't allow horizontal scrolling — adjust field count or use a Standard Tab variant of the parent pattern.

---

## 8. AOT mapping (dev handoff)

| Spec field             | AOT location                                                            | Notes                                            |
|------------------------|-------------------------------------------------------------------------|--------------------------------------------------|
| FastTabs container      | `Form > Design > … > Tab (Style=FastTabs)`                              | One TabPage per FastTab                          |
| TabPage caption / DS    | `TabPage.Caption`, `TabPage.DataSource`                                 | Required by BP                                   |
| Summary field           | `<FormControl>.IsSummaryField = Yes` (or `FastTabSummary = Yes`)         | Each field individually                          |
| Initial expansion       | `TabPage.FastTabExpanded = Yes` (first FastTab only)                    | All other TabPages default-collapsed             |
| Body subpattern         | Right-click `TabPage > <first container> > Apply pattern → <subpattern>` | Fields and Field Groups / Toolbar and Fields / Toolbar and List / Nested Simple List and Details |

---

## 9. Figma component definition

- **Name:** `Control/FastTab`
- **Slots:**
  - `Slot — body` (instance swap; default is `Subpattern/FieldsAndFieldGroups`)
  - `Summary field 1` / `Summary field 2` / `Summary field 3` (text or instance swap)
- **Variants:**
  - `State`: Collapsed / Expanded
  - `Mode`: Light / Dark / HighContrast
  - `Density`: Compact / Comfortable
  - `Header style`: Default / Emphasised

---

## 10. References

- Microsoft Learn — [General form guidelines — FastTabs](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/general-form-guidelines)
- Microsoft Learn — [Toolbar and List subpattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/toolbar-list-subpattern)
- Microsoft Learn — [Fields and Field Groups subpattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/fields-field-groups-subpattern)
- Microsoft Learn — [Page layout in the web client](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/page-layout)
- Microsoft Learn — [Keyboard shortcuts](https://learn.microsoft.com/dynamics365/fin-ops-core/fin-ops/get-started/shortcut-keys)
