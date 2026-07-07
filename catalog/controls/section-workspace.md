# Workspace Section subpatterns

> **Microsoft references:**
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/workspace-form-pattern
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/section-tiles-list-subpattern
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/section-tabbed-list-subpattern
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/section-related-links-subpattern
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/section-stacked-chart-subpattern
>
> **AOT subpatterns:** `SectionTiles`, `SectionChart`, `SectionTabbedList`, `SectionRelatedLinks`, `SectionStacked`, `PowerBISection`
> **Figma components:** `Subpattern/Section/Tiles`, `/Chart`, `/TabbedList`, `/RelatedLinks`, `/Stacked`, `/PowerBI`
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-07-01

---

## 1. Purpose

Workspace section subpatterns define the content inside each **Panorama section** of an [Operational Workspace](../patterns/operational-workspace.md). Each section has a title and uses exactly one of these subpatterns for its body.

---

## 2. Section type reference

### 2.1 Section Tiles (KPI tiles)

**AOT subpattern:** `SectionTiles`  
**Use for:** A grid of KPI / metric tiles showing aggregated counts or amounts. Always the **first section** in a workspace — orients the user to the current state before any lists or details.

```
PanoramaSection
├── SectionTitle "Key metrics"
└── TileContainer (Group, SubPattern=SectionTiles)
    └── Tile (TileControl, 1..N)
        ├── Label (e.g. "Open orders")
        └── Count / Amount (display method)
```

**Guidelines:**
- 2–8 tiles per section. Arrange in order of business priority (most actionable → least).
- Each tile is a navigation link — clicking it opens the relevant list page or workspace view filtered to the tile's criteria.
- Tiles show counts (integers) or currency amounts, not text labels or statuses.
- Distinguish attention-requiring tiles visually (amber/red accent) from neutral informational tiles (grey/blue). The framework provides Tile.Style variants (Standard, Alert, Primary).

---

### 2.2 Section Chart

**AOT subpattern:** `SectionChart`  
**Use for:** A single embedded chart (bar, line, pie) rendered from a query or Power BI. Secondary to tiles — provides trend context rather than a navigable count.

```
PanoramaSection
├── SectionTitle "Sales trend"
└── ChartContainer (Group, SubPattern=SectionChart)
    └── Chart (ChartControl)
        ├── DataSource = <aggregate query or cube>
        └── ChartType = Bar | Line | Pie | Area
```

**Guidelines:**
- One chart per section. Don't embed multiple charts in the same section.
- Label axes clearly. Minimise chart junk (grid lines, 3D effects, unnecessary legend entries).
- Chart data source is typically an aggregate query or an SSAS cube view, not a raw table.
- Provide a "View details" link to the full report or analysis page.

---

### 2.3 Section Tabbed List

**AOT subpattern:** `SectionTabbedList`  
**Use for:** A workspace section that shows **multiple actionable lists** under a tab strip. Each tab is a filtered view of the same (or related) entity — e.g. "My overdue" / "Assigned to me" / "All open".

```
PanoramaSection
├── SectionTitle "My work items"
└── TabbedListContainer (Group, SubPattern=SectionTabbedList)
    └── Tab (Style=Tabs)
        └── TabPage (1..4)
            ├── HeaderGroup (optional) — quick stats for this tab
            └── Grid (Tabular or List, read-only or limited edit)
```

**Guidelines:**
- 2–4 tabs per section. More than 4 tabs makes the section hard to scan.
- Each tab shows a meaningfully different filter view of the same entity — not arbitrary groupings.
- Grids in workspace sections are typically read-only; actions are handled via ActionPane buttons or links to the detail form.
- Show a count badge on each tab label (e.g. "Overdue (7)").

---

### 2.4 Section Related Links

**AOT subpattern:** `SectionRelatedLinks`  
**Use for:** A list of hyperlinks to frequently used forms, reports, and external pages. Always the **last section** in a workspace — it's orientation and navigation, not primary data.

```
PanoramaSection
├── SectionTitle "Related links"
└── LinksContainer (Group, SubPattern=SectionRelatedLinks)
    └── MenuFunctionButton | MenuItemButton (1..N)
        └── Label "All customers"  → navigates to the list page
```

**Guidelines:**
- 4–15 links. Fewer than 4: consider whether a separate section adds value. More than 15: group into sub-categories.
- Group related links under sub-headings if the list exceeds ~8 items.
- Links navigate to forms, reports, or URLs — they never trigger actions (use ActionPane or Tiles for actions).
- Labels are noun phrases naming the destination ("All customers", "Customer aging report") — not verbs.

---

### 2.5 Section Stacked Chart

**AOT subpattern:** `SectionStacked`  
**Use for:** A compact "stack" of a small grid (top) and a chart (bottom) sharing the same data scope — typically a list of entities on top with a distribution chart below. Less common than the other types; only use when the chart directly explains the list above it.

```
PanoramaSection
├── SectionTitle
└── StackedContainer (Group, SubPattern=SectionStacked)
    ├── Grid (top, read-only)
    └── Chart (bottom)
```

**Guidelines:**
- The chart and grid must be meaningfully related — the chart should show the breakdown of what the grid lists.
- Keep the grid to ≤5 rows; keep the chart simple (bar or pie).

---

### 2.6 Power BI Section

**AOT subpattern:** `PowerBISection`  
**Use for:** Embedding a Power BI report tile or page directly into a Panorama section. Requires Power BI integration to be configured in the environment.

```
PanoramaSection
├── SectionTitle "Power BI"
└── PowerBIContainer (Group, SubPattern=PowerBISection)
    └── PowerBIPart (embedded report)
```

**Guidelines:**
- One Power BI report per section.
- Ensure the report respects the workspace's page-level filter (pass filter context via URL parameters or report-level filtering).
- Provide a fallback message ("Power BI not configured") gracefully — not all environments have Power BI enabled.

---

## 3. UX guidelines (all section types)

1. **Section order in the workspace:** Tiles → Tabbed List (most actionable) → Charts → Related Links. This ordering surfaces actionable information first and reference/context last.
2. **Section title is a noun phrase** naming what the section contains ("Key metrics", "My work items") — not a verb ("See work items").
3. **Every section must respond to the Workspace Page Filter Group.** If a section shows data unaffected by the workspace filter, add a clarifying note in the section title or header.
4. **Don't mix section types.** Each section uses exactly one subpattern. A Tabbed List section doesn't also contain a chart — that would be Section Stacked.
5. **Maximum 5 Panorama sections** per workspace. More sections reduce discoverability; collapse infrequently used ones behind a "See more" pattern or move them to a related workspace.

---

## 4. AOT mapping (dev handoff)

| Spec field             | AOT location                                                                           | Notes                                                          |
|------------------------|----------------------------------------------------------------------------------------|----------------------------------------------------------------|
| Panorama section       | `Form.Design > PanoramaSection (1..5)` within the workspace form                      | One subpattern per PanoramaSection                             |
| Section subpattern     | `Group.SubPattern = SectionTiles | SectionChart | SectionTabbedList | …`               | Set in the designer on the content Group                       |
| Tile data              | Display methods or aggregate queries returning integer/decimal; bound to `TileControl` | Queries should be lightweight (cached where possible)          |
| Chart data             | Aggregate query or SSAS measure; bound to `ChartControl`                               | Not raw transactional tables — performance critical            |
| Tabbed list grids      | Read-only `Grid.EditTrigger=Never`; data source is a filtered view or query            | Don't use inline editing in workspace grids                    |
| Related links          | `MenuItemButton` / `MenuFunctionButton` bound to menu items                            | Security is inherited from the menu item                       |
| Power BI               | `PowerBIPart` configured with report URL and filter context                             | Requires `SysPowerBIUserConfiguration` setup in the environment|

---

## 5. Figma component definitions

| Type | Figma name | Key slots |
|------|-----------|-----------|
| Tiles | `Subpattern/Section/Tiles` | 2–8 `Atom/KpiTile` instances in a row |
| Chart | `Subpattern/Section/Chart` | `Component/Chart` (bar / line / pie variant) |
| Tabbed List | `Subpattern/Section/TabbedList` | Tab strip (2–4 tabs) + Grid per tab |
| Related Links | `Subpattern/Section/RelatedLinks` | Stacked anchor links, optional sub-headings |
| Stacked | `Subpattern/Section/Stacked` | Grid (top) + Chart (bottom) |
| Power BI | `Subpattern/Section/PowerBI` | Placeholder frame labelled "Power BI report" |

---

## 6. References

- Microsoft Learn — [Operational Workspace form pattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/workspace-form-pattern)
- Microsoft Learn — [Section Tiles / List subpattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/section-tiles-list-subpattern)
- Microsoft Learn — [Section Tabbed List subpattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/section-tabbed-list-subpattern)
- Microsoft Learn — [Section Related Links subpattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/section-related-links-subpattern)
- Microsoft Learn — [Section Stacked Chart subpattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/section-stacked-chart-subpattern)
- [Operational Workspace pattern](../patterns/operational-workspace.md)
- [Workspace Page Filter Group subpattern](./workspace-page-filter-group.md)
- [Filters and Toolbar subpattern](./filters-and-toolbar.md)
