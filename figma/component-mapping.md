# Catalog ↔ Figma — component mapping

The cross-reference between **catalog docs** (the markdown source of truth) and **Figma components** (the visual library). Every catalog page lists its planned Figma component name in its header; this file is the consolidated index in the reverse direction.

> **Conventions:** see [`figma/library-structure.md → §3 Component naming`](./library-structure.md#3-component-naming) for the prefix system (`Token/`, `Icon/`, `Atom/`, `Control/`, `Subpattern/`, `Pattern/`, `Chrome/`).
>
> **Status column:**
> - `Catalog` — catalog doc exists (Seeded or Stub)
> - `Figma` — Figma component published in the library file
> - `—` — not yet started
>
> Update both columns in the **same PR** as the catalog or Figma change.

---

## Patterns

| Catalog page                                                              | Planned Figma component                                            | Catalog status | Figma status |
|---------------------------------------------------------------------------|--------------------------------------------------------------------|----------------|--------------|
| [List Page](../catalog/patterns/list-page.md)                              | `Pattern/ListPage`                                                  | Seeded         | —            |
| [Details Master](../catalog/patterns/details-master.md)                    | `Pattern/DetailsMaster` (+ `Pattern/DetailsMaster/StandardTabs`)    | Seeded         | —            |
| [Details Transaction](../catalog/patterns/details-transaction.md)          | `Pattern/DetailsTransaction/HeaderView`, `Pattern/DetailsTransaction/LineView`, `Pattern/DetailsTransaction/GridView` | Seeded | — |
| [Simple Details](../catalog/patterns/simple-details.md)                    | `Pattern/SimpleDetails` (variants: ToolbarAndFields / FastTabs / StandardTabs / Panorama) | Seeded | — |
| [Simple List](../catalog/patterns/simple-list.md)                          | `Pattern/SimpleList`                                                | Seeded         | —            |
| [Simple List and Details](../catalog/patterns/simple-list-details.md)      | `Pattern/SimpleListAndDetails` (variants: ListGrid / TabularGrid / Tree) | Seeded     | —            |
| [Table of Contents](../catalog/patterns/table-of-contents.md)              | `Pattern/TableOfContents`                                           | Seeded         | —            |
| [Operational Workspace](../catalog/patterns/operational-workspace.md)      | `Pattern/OperationalWorkspace` (+ `…/WithTabs`)                    | Seeded         | —            |
| [Dialog](../catalog/patterns/dialog.md)                                    | `Pattern/Dialog` (variants: Basic / ReadOnly / WithTabs / WithFastTabs / DoubleTabs; sizes S/M/L/Full) | Seeded | — |
| [Drop Dialog](../catalog/patterns/drop-dialog.md)                          | `Pattern/DropDialog` (variants: Basic / ReadOnly)                   | Seeded         | —            |
| [Lookup](../catalog/patterns/lookup.md)                                    | `Pattern/Lookup` (variants: Basic / WithTabs / WithPreview)         | Seeded         | —            |
| [FactBox](../catalog/patterns/factbox.md)                                  | `Pattern/FactBox` (variants: Card / Grid)                           | Seeded         | —            |
| Form Part Section List                                                    | `Pattern/FormPartSectionList` (+ `…/Double`)                        | Todo           | —            |
| Wizard                                                                    | `Pattern/Wizard`                                                    | Todo           | —            |

---

## Composite & primitive controls

| Catalog page                                                              | Planned Figma component                                            | Catalog status | Figma status |
|---------------------------------------------------------------------------|--------------------------------------------------------------------|----------------|--------------|
| [ActionPane](../catalog/controls/action-pane.md)                           | `Pattern/ActionPane` (+ child `Pattern/ActionPane/Tab`)             | Seeded         | —            |
| [FastTab](../catalog/controls/fast-tab.md)                                 | `Control/FastTab`                                                   | Seeded         | —            |
| [Grid](../catalog/controls/grid.md)                                        | `Control/Grid` (variants: Tabular / List) + `Control/Grid/Row`     | Seeded         | —            |
| [FilterPane and friends](../catalog/controls/filter-pane.md)               | `Pattern/FilterPane`, `Control/QuickFilter`, `Composite/GridColumnFilter`, `Pattern/AdvancedFilterDialog` | Seeded | — |
| RecordTitle                                                               | `Composite/RecordTitle`                                             | Seeded         | —            |
| EntityStatus                                                              | `Composite/EntityStatus`                                            | Seeded         | —            |
| Tab (Standard)                                                             | `Control/Tab`                                                       | Seeded         | —            |
| Group                                                                     | `Control/Group`                                                     | Seeded         | —            |
| Button (typed: MenuItem / Command / MenuButton / DropDialog)              | `Atom/Button` (+ `Atom/Button/Menu`, `Atom/Button/DropDialog`)     | Seeded         | —            |
| Input fields (String / Real / Int / Date / Time / DateTime)               | `Atom/Input/String`, `…/Real`, `…/Int`, `…/Date`, `…/Time`, `…/DateTime` | Seeded    | —            |
| ComboBox                                                                   | `Atom/Combo`                                                        | Seeded         | —            |
| Lookup control (ReferenceGroup)                                            | `Atom/Lookup`                                                       | Seeded         | —            |
| Checkbox / Radio / Toggle                                                  | `Atom/Checkbox`, `Atom/Radio`, `Atom/Toggle`                       | Seeded         | —            |
| Segmented Entry                                                            | `Subpattern/DimensionEntryControl`                                  | Seeded         | —            |
| Workflow controls (Submit / Approve / History)                             | `Composite/WorkflowControls`                                        | Seeded         | —            |
| [FactBox panel (host-form)](../catalog/controls/factbox.md)               | Composed from `Pattern/FactBox` (Card / Grid) instances in the right panel | Seeded  | —            |

---

## Subpatterns

| Catalog page                                                              | Planned Figma component                                            | Catalog status | Figma status |
|---------------------------------------------------------------------------|--------------------------------------------------------------------|----------------|--------------|
| [Fields and Field Groups](../catalog/controls/fields-and-field-groups.md) | `Subpattern/FieldsAndFieldGroups`                                  | Seeded         | —            |
| [Toolbar and List](../catalog/controls/toolbar-and-list.md)               | `Subpattern/ToolbarAndList` (+ `…/Double`)                          | Seeded         | —            |
| [Toolbar and Fields](../catalog/controls/toolbar-and-fields.md)           | `Subpattern/ToolbarAndFields`                                       | Seeded         | —            |
| [Nested Simple List and Details](../catalog/controls/nested-simple-list-details.md) | `Subpattern/NestedSimpleListDetails`                     | Seeded         | —            |
| [Custom Filter Group](../catalog/controls/custom-filter-group.md)         | `Subpattern/CustomFilterGroup` (+ `…/CustomAndQuickFilters`)        | Seeded         | —            |
| [Tabular Fields](../catalog/controls/tabular-fields.md)                   | `Subpattern/TabularFields`                                          | Seeded         | —            |
| [Fill Text](../catalog/controls/fill-text.md)                             | `Subpattern/FillText`                                               | Seeded         | —            |
| [List Panel](../catalog/controls/list-panel.md)                           | `Subpattern/ListPanel`                                              | Seeded         | —            |
| [Horizontal Fields and Buttons Group](../catalog/controls/horizontal-fields-buttons.md) | `Subpattern/HorizontalFieldsAndButtonsGroup`          | Seeded         | —            |
| [Filters and Toolbar](../catalog/controls/filters-and-toolbar.md)         | `Subpattern/FiltersAndToolbar/Inline`, `…/Stacked`                  | Seeded         | —            |
| [Workspace Page Filter Group](../catalog/controls/workspace-page-filter-group.md) | `Subpattern/WorkspacePageFilterGroup`                       | Seeded         | —            |
| [Workspace Section subpatterns](../catalog/controls/section-workspace.md) | `Subpattern/Section/Tiles`, `/Chart`, `/TabbedList`, `/RelatedLinks`, `/Stacked`, `/PowerBI` | Seeded | — |
| [Dimension Entry Control](../catalog/controls/dimension-entry.md)         | `Subpattern/DimensionEntryControl`                                  | Seeded         | —            |
| [Dimension Expression Builder](../catalog/controls/dimension-expression-builder.md) | `Subpattern/DimensionExpressionBuilder`                  | Seeded         | —            |

---

## Page chrome

| Element                                                                   | Planned Figma component                                            | Status |
|---------------------------------------------------------------------------|--------------------------------------------------------------------|--------|
| Navigation pane (left)                                                    | `Chrome/NavigationPane` (collapsed / expanded)                      | —      |
| Top bar                                                                   | `Chrome/TopBar`                                                     | —      |
| Breadcrumb                                                                | `Chrome/Breadcrumb`                                                 | —      |
| Status bar                                                                | `Chrome/StatusBar`                                                  | —      |

---

## How to update this file

When a Figma component is published:

1. Locate the row in the relevant section above.
2. Change the **Figma status** column from `—` to `Published` (or to a Figma version tag like `v0.1`).
3. Make sure the catalog doc's header line `> **Figma component:** …` matches the name in the **Planned Figma component** column.
4. Add a row to the change log on the library's `📘 0 — Read me` page in Figma.
