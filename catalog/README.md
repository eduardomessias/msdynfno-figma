# Catalog — D365FO Form patterns & controls

This is the **source of truth** that drives the Figma library. Every Figma component, page template, and design token traces back to a doc in here.

The pattern set is Microsoft's; we do not invent. Each page mirrors the official Microsoft Learn pattern article, with a Smartbox-specific section for overrides and AOT mapping.

> **New pattern doc?** Copy [`_template-pattern.md`](./_template-pattern.md), fill it in, and register it in the tables below.

---

## Top-level form patterns

These go on `Form.Design.Pattern`. **Pick exactly one per Form.**

| Pattern                       | Use it for                                                                 | Status   | Catalog doc                                                  |
|-------------------------------|----------------------------------------------------------------------------|----------|--------------------------------------------------------------|
| **List Page**                 | Standalone list with no 1:1 details, or list backed by multiple details    | Seeded   | [list-page.md](./patterns/list-page.md)                      |
| **Details Master**            | Master entity details (e.g. Customer, Vendor). List + details merged.      | Seeded   | [details-master.md](./patterns/details-master.md)            |
| **Details Transaction**       | Transactional entity with Header + Lines (e.g. Sales order)                | Seeded   | [details-transaction.md](./patterns/details-transaction.md)  |
| **Simple Details**            | Single-record edit, no list                                                | Seeded   | [simple-details.md](./patterns/simple-details.md)            |
| **Simple List**               | Maintain simple entity (≤6 fields, no children)                            | Seeded   | [simple-list.md](./patterns/simple-list.md)                  |
| **Simple List and Details**   | Medium-complexity entity (6+ fields, 0–5 child collections)                | Seeded   | [simple-list-details.md](./patterns/simple-list-details.md)  |
| **Table of Contents**         | Setup / parameters pages with loosely-related sections                     | Seeded   | [table-of-contents.md](./patterns/table-of-contents.md)      |
| **Operational Workspace**     | Role-tailored activity hub, primary navigation                             | Seeded   | [operational-workspace.md](./patterns/operational-workspace.md) |
| **Form Part Section List**    | Workspace tab content (single or double list)                              | Todo     | _patterns/section-list.md_                                   |
| **Dialog**                    | Modal that gathers a set of inputs                                         | Seeded   | [dialog.md](./patterns/dialog.md)                            |
| **Drop Dialog**               | Small contextual dialog hanging off a button/lookup                        | Seeded   | [drop-dialog.md](./patterns/drop-dialog.md)                  |
| **Lookup**                    | Custom lookup form when auto-lookup is not enough                          | Seeded   | [lookup.md](./patterns/lookup.md)                            |
| **Wizard**                    | Stepwise data gathering (legacy — prefer business workflows where possible)| Todo     | _patterns/wizard.md_                                         |
| **FactBox**                   | Card or grid panel showing related info on the right side                  | Seeded   | [factbox.md](./patterns/factbox.md)                          |

> **Discouraged / legacy:** Task Single, Task Double — keep only for migration of existing forms. Do not pick for new Smartbox forms.

---

## Subpatterns (container-level)

These apply to a container inside a top-level pattern (e.g. a `TabPage` inside a Details Transaction's FastTab area).

| Subpattern                          | Applied to            | Status | Catalog doc                                          |
|-------------------------------------|-----------------------|--------|------------------------------------------------------|
| Custom Filter Group                 | Group above a Grid    | Seeded | [custom-filter-group.md](./controls/custom-filter-group.md) |
| Fields and Field Groups             | TabPage / Group       | Seeded | [fields-and-field-groups.md](./controls/fields-and-field-groups.md) |
| Fill Text                           | Single full-width input | Seeded | [fill-text.md](./controls/fill-text.md)            |
| Filters and Toolbar                 | Workspace section     | Seeded | [filters-and-toolbar.md](./controls/filters-and-toolbar.md) |
| Horizontal Fields and Buttons Group | Header summary band   | Seeded | [horizontal-fields-buttons.md](./controls/horizontal-fields-buttons.md) |
| List Panel                          | Two-list move UI      | Seeded | [list-panel.md](./controls/list-panel.md)            |
| Nested Simple List and Details      | Embedded SL+D         | Seeded | [nested-simple-list-details.md](./controls/nested-simple-list-details.md) |
| Tabular Fields                      | Dense field block     | Seeded | [tabular-fields.md](./controls/tabular-fields.md)    |
| Toolbar and Fields                  | Actions + fields band | Seeded | [toolbar-and-fields.md](./controls/toolbar-and-fields.md) |
| Toolbar and List                    | Actions + grid band   | Seeded | [toolbar-and-list.md](./controls/toolbar-and-list.md) |
| Workspace Section subpatterns       | Workspace sections    | Seeded | [section-workspace.md](./controls/section-workspace.md) |
| Workspace Page Filter Group         | Workspace top filter  | Seeded | [workspace-page-filter-group.md](./controls/workspace-page-filter-group.md) |
| Dimension Entry Control             | Financial dim editor  | Seeded | [dimension-entry.md](./controls/dimension-entry.md)  |
| Dimension Expression Builder        | Financial dim filter  | Seeded | [dimension-expression-builder.md](./controls/dimension-expression-builder.md) |

---

## Primitive controls

Atomic Fluent 2 controls that build everything above.

| Control                  | Notes                                              | Status | Catalog doc                          |
|--------------------------|----------------------------------------------------|--------|--------------------------------------|
| ActionPane               | Top toolbar with tabs and command buttons          | Seeded | [action-pane.md](./controls/action-pane.md) |
| FastTab                  | Collapsible section with summary fields            | Seeded | [fast-tab.md](./controls/fast-tab.md) |
| FilterPane               | Side filter pane + QuickFilter + Advanced filter   | Seeded | [filter-pane.md](./controls/filter-pane.md) |
| QuickFilter              | Single-field grid-top filter                       | Seeded | [filter-pane.md](./controls/filter-pane.md) (see §3) |
| Grid                     | Editable / read-only data grid                     | Seeded | [grid.md](./controls/grid.md)        |
| Group                    | Logical container for fields                       | Seeded | [group.md](./controls/group.md)      |
| Tab / TabPage            | Standard horizontal tab strip                      | Seeded | [tab.md](./controls/tab.md)          |
| FactBox                  | Right-pane card or grid                            | Seeded | [factbox.md](./controls/factbox.md)  |
| StringEdit / RealEdit / IntEdit / DateEdit / TimeEdit / UtcDateTimeEdit | Input fields | Seeded | [input-fields.md](./controls/input-fields.md) |
| ComboBox                 | Enum-backed discrete-value selector                | Seeded | [combo.md](./controls/combo.md)      |
| CheckBox / RadioButton / ToggleButton | Boolean / mutex selectors             | Seeded | [boolean-controls.md](./controls/boolean-controls.md) |
| Lookup (ReferenceGroup)  | Lookup-backed control                              | Seeded | [lookup-control.md](./controls/lookup-control.md) |
| SegmentedEntry           | Financial dimension entry                          | Seeded | [segmented-entry.md](./controls/segmented-entry.md) |
| MenuItemButton / CommandButton / MenuButton / DropDialogButton | ActionPane button types | Seeded | [buttons.md](./controls/buttons.md)  |
| Workflow controls        | Submit / Approve / Recall / History                | Seeded | [workflow-controls.md](./controls/workflow-controls.md) |

---

## Index by business process area

When ready, mocks in [`/mocks/`](../mocks/) will be indexed here under the L1 process taxonomy from the [Microsoft Business Process Catalog](https://github.com/microsoft/dynamics365patternspractices).

- Acquire to dispose
- Administer to operate
- Case to resolution
- Concept to market
- Design to retire
- Forecast to plan
- Hire to retire
- Inventory to deliver
- **Order to cash** ← first worked example will land here
- Plan to produce
- Project to profit
- Prospect to quote
- Record to report
- Service to deliver
- Source to pay
