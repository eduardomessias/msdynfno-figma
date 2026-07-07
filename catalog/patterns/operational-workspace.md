# Operational Workspace form pattern

> **Microsoft pattern reference:** https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/workspace-form-pattern
> **D365FO `Form.Design.Style` value:** `Workspace`
> **D365FO `Form.Design.Pattern` value:** `OperationalWorkspace` (default) / `OperationalWorkspaceWithTabs`
> **Figma page:** `Patterns / Operational Workspace`
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-05-21

---

## 1. Usage — when to choose this pattern

An **Operational Workspace** is a role-tailored landing surface for a business activity. It is part of the **primary navigation**: a workspace gives an overview of an activity, lets the user complete light tasks directly, and routes deeper navigation by selecting from lists, tiles, or links. Use it for the daily landing surface of a specific persona (Sales clerk, AP coordinator, Warehouse operator).

Two top-level variants:

1. **Operational Workspace** (default) — vertical FastTab orientation (panorama replaced in 10.0.25+). Contains tiles, tabbed lists, optional charts/Power BI, related links.
2. **Operational Workspace w/ Tabs** — when the workspace needs standard tabs of content (e.g. multiple Power BI reports). Wraps the standard workspace content in a top-level Tab.

> A workspace is **not** a Form pattern you reach for to hold a single entity. Pick [Details Master](./details-master.md) or [Details Transaction](./details-transaction.md) for that.

### Related patterns

- [Form Part Section List](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/section-list-form-pattern) — the lists that appear inside a workspace tab
- [Details Master](./details-master.md) / [Details Transaction](./details-transaction.md) — typical drill-downs from workspace lists

---

## 2. Anatomy — the required structure

### Operational Workspace (default)

```
Design
├── ActionPane (ActionPane) [Optional]
├── WorkspacePageFilterGroup (Group) [Optional]    — applies Workspace Page Filter Group subpattern
└── FastTabs (Tab)
    ├── SectionSummaryTiles (TabPage)               — applies Section Tiles subpattern
    ├── SectionTabbedList (TabPage)                 — applies Section Tabbed List subpattern
    ├── SectionCharts (TabPage) [Optional]          — applies Section Stacked Chart subpattern
    ├── SectionPowerBI (TabPage) [Optional]         — applies Section PowerBI subpattern
    └── SectionRelatedLinks (TabPage)               — applies Section Related Links subpattern
```

### Operational Workspace w/ Tabs

```
Design
├── ActionPane [Optional]
├── WorkspacePageFilterGroup [Optional]
└── StandardTab (Tab)
    ├── OperationalWorkspaceContent
    └── OtherContent (0..N)
```

| Node                       | Required? | Purpose                                                   | Figma component                  |
|----------------------------|-----------|-----------------------------------------------------------|----------------------------------|
| `ActionPane`               | Optional  | Workspace-wide actions                                    | `Pattern/ActionPane`             |
| `WorkspacePageFilterGroup` | Optional  | Single filter that scopes the whole workspace             | `Subpattern/WorkspacePageFilterGroup` |
| `SectionSummaryTiles`      | Yes       | Tiles + cards/charts as Form Parts                        | `Subpattern/SectionTiles`        |
| `SectionTabbedList`        | Yes       | Vertical tab list of `FormPartControl`s pointing to Form Part Section List forms | `Subpattern/SectionTabbedList` |
| `SectionCharts`            | Optional  | Up to two stacked charts                                  | `Subpattern/SectionStackedChart` |
| `SectionPowerBI`           | Optional  | Power BI tile                                             | `Subpattern/SectionPowerBI`      |
| `SectionRelatedLinks`      | Yes       | Grouped menu-item hyperlinks                              | `Subpattern/SectionRelatedLinks` |

---

## 3. Core components — BP warnings to resolve

- [ ] Form is referenced by at least one menu item
- [ ] `TabPage.Caption` is not empty (every content section)

Future BP guidance (not enforced today, but recommended):
- Workspace-wide filter fields are covered by indexes
- Chart parts only contain OLAP charts
- Count tiles have a query defined (on the tile or its menu item) — required for the tile-caching system to work

---

## 4. UX guidelines

1. Tiles, tabbed list, related links — these three **must** be present. Charts and Power BI are optional.
2. **Tile sizing** — give all tiles the **same height** (ideally the same size) so the horizontal-wrap layout looks clean. From 10.0.26, users can personalise tile size if the feature is on.
3. **Simple lists** that take only part of the page width don't look great on a vertical workspace — prefer a tabular grid or card list that flows horizontally.
4. **Card lists** can be made horizontal-flowing in vertical workspaces to use page width better.
5. Each tabbed list item is its own **Form Part** form, applying the `Form Part Section List` pattern.

### Smartbox overrides

_None yet._

---

## 5. Do / Don't

**Do**
- Use one workspace per persona × activity. A user gets a small set of workspaces, not dozens.
- Put light tasks (filter, complete, mark) directly in the workspace so the user doesn't have to navigate away.
- Define queries on all count tiles to enable the caching system.

**Don't**
- Don't use the deprecated `Workspace` pattern or `Panorama` style — migrate to **Operational Workspace** (vertical / FastTabs orientation).
- Don't put deep editing tasks in a workspace — push them to a Details Master/Transaction reached via a tile or list.
- Don't expect FactBoxes here.

---

## 6. AOT metadata mapping (dev handoff)

| Spec field                  | AOT location                                                                                  | Notes                                                          |
|-----------------------------|-----------------------------------------------------------------------------------------------|----------------------------------------------------------------|
| Pattern                     | `Form > Design > Pattern = OperationalWorkspace` (or `…WithTabs`)                             | Apply via designer; do **not** use deprecated `Workspace`       |
| Tiles                       | `SectionSummaryTiles > TileButton` and/or `FormPartControl` (for chart/card)                  | Same height across tiles                                       |
| Tabbed lists                | `SectionTabbedList > Tab > TabPage > FormPartControl` → `Form Part Section List` form         | Each list is its own form                                       |
| Section charts (optional)   | `SectionCharts > TabPage > FormPartControl(s)`                                                | Chart Form Parts using `Hub Part Chart` pattern                |
| Power BI (optional)         | `SectionPowerBI > TabPage`                                                                    | Power BI tile component                                         |
| Related links               | `SectionRelatedLinks > MenuFunctionButton` (and grouped `LinksGroup`)                         | One level of nesting allowed                                    |
| Workspace-wide filter       | `WorkspacePageFilterGroup`                                                                    | Single input control, drives the workspace as a whole          |

---

## 7. Worked example

_To come — Smartbox **Experience operations** workspace under [`/mocks/order-to-cash/`](../../mocks/)._

---

## 8. References

- Microsoft Learn — [Workspace form pattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/workspace-form-pattern)
- Microsoft Learn — [Build operational workspaces](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/build-workspaces)
- Microsoft Learn — [Section Tiles subpattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/section-tiles-subpattern)
- Microsoft Learn — [Section Tabbed List subpattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/section-tabbed-list-subpattern)
- Microsoft Learn — [Section Related Links subpattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/section-related-links-subpattern)
- Microsoft Learn — [Form Part Section List form patterns](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/section-list-form-pattern)
