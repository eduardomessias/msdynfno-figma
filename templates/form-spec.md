# Form Spec — `<Form name>`

> **The contract between consultant and developer.** Fill every section. Empty sections mean ambiguity, and ambiguity means a Form that gets built differently than what the business approved.
>
> Copy this file next to your Figma mock and rename it `<form-name>.spec.md`.

---

## 0. Metadata

| Field                    | Value                                                                |
|--------------------------|----------------------------------------------------------------------|
| Form name                | `<e.g. SmbExperienceProviderTable>`                                  |
| Caption (page title)     | `<e.g. "Experience providers">`                                      |
| Business process (L1)    | `<e.g. Order to cash / Source to pay / Hire to retire>`              |
| Business process (L2/L3) | `<e.g. Manage customers>`                                            |
| Module                   | `<e.g. Accounts receivable>`                                         |
| Pattern                  | `<one of: ListPage / DetailsMaster / DetailsTransaction / SimpleDetails / SimpleList / SimpleListAndDetails / TableOfContents / OperationalWorkspace / Dialog / DropDialog / Lookup>` |
| Pattern doc              | `<link to catalog/patterns/<pattern>.md>`                            |
| Figma frame              | `<URL to the Figma frame>`                                           |
| Consultant               | `<name>`                                                             |
| Developer                | `<name — once assigned>`                                             |
| Stakeholder sign-off     | `<name + date>`                                                      |
| Last updated             | `YYYY-MM-DD`                                                         |
| Status                   | Draft / Ready for dev / In build / Built / Verified                  |

---

## 1. Purpose

Two or three sentences. What is this Form for? Whose problem does it solve? Why this pattern?

---

## 2. Pattern justification

Why **this** pattern? Reference the decision tree in [`workflow/00-authoring-guide.md`](../workflow/00-authoring-guide.md#step-1--pick-a-pattern). If the same outcome could be achieved with a simpler pattern, explain why we chose this one anyway.

---

## 3. Data sources

| Order | Data source name | Table       | Join with          | Link type | Range / Query filters       | AllowEdit | AllowCreate | AllowDelete |
|-------|------------------|-------------|--------------------|-----------|-----------------------------|-----------|-------------|-------------|
| 1     | `<DS1>`          | `<Table1>`  | —                  | —         | `<e.g. DataAreaId == curExt()>` | Yes/No    | Yes/No      | Yes/No      |
| 2     | `<DS2>`          | `<Table2>`  | `<DS1>`            | Delayed   | —                           | Yes/No    | Yes/No      | Yes/No      |

> AllowEdit/Create/Delete must satisfy the BP warnings for the chosen pattern. See the pattern doc's **Core components** section.

---

## 4. Fields

Per-field detail. Group rows under the Form section / FastTab they live in. **One row per visible control.**

### Section: `<e.g. ActionPane / SidePanel / Header view → General FastTab>`

| Label (UI)        | Bound to                  | EDT                    | Control type       | Mandatory | AllowEdit | Default       | Visible-when           | Notes                                  |
|-------------------|---------------------------|------------------------|--------------------|-----------|-----------|---------------|------------------------|----------------------------------------|
| `<e.g. Customer>` | `<DS1>.<Field>`           | `<e.g. CustAccount>`   | ReferenceGroup     | Yes       | Yes       | —             | Always                 | Lookup on `CustTable`                  |
| `<e.g. Currency>` | `<DS1>.<Field>`           | `<e.g. CurrencyCode>`  | ComboBox           | Yes       | No (after first save) | Company default | Always           | —                                      |
| `<e.g. Status>`   | _Computed_                | `<e.g. SalesStatus>`   | StringEdit (read-only) | No   | No        | —             | Always                 | Mirrors `SalesTable.SalesStatus`       |

### Section: `<…next section…>`

…

> **EDT** is the **Extended Data Type** that the field uses. Devs verify the EDT on the bound table matches what's listed here. If a new EDT is needed, raise it as a separate request.

---

## 5. ActionPane

ActionPane buttons by tab → group → button.

| Tab               | Group              | Caption       | Type                | Menu item                  | Privilege               | Enabled-when                 | Notes                              |
|-------------------|--------------------|---------------|---------------------|----------------------------|-------------------------|------------------------------|------------------------------------|
| `<e.g. Order>`    | `<e.g. New>`       | _provided_    | (foundation)        | —                          | —                       | —                            | New/Delete/Save/Refresh/Attachments/Export are foundation — **do not list** unless you're explicitly removing one |
| `<e.g. Order>`    | `<e.g. Maintain>`  | Confirm       | MenuItemButton      | `SalesFormLetter_Confirm`  | `SalesOrderConfirm`     | Status == `Created`          | Opens dialog                        |
| `<e.g. Invoice>`  | `<e.g. Generate>`  | Invoice       | MenuItemButton      | `SalesFormLetter_Invoice`  | `SalesOrderInvoice`     | Status in `{Delivered}`      | Posts via SalesFormLetter framework |
| `<e.g. Related>`  | `<e.g. Customer>`  | Customer      | MenuItemButton      | `CustTable`                | `CustTableMaintain`     | Always                       | Opens Customer Details              |

> Tabs are the top-level grouping. Conventional names: **<Entity>**, **Manage**, **Related information**, **Options**. Adapt to scenario.

---

## 6. FactBoxes (right pane)

| Order | FormPart        | Form name                | Bound to                   | Notes                                |
|-------|-----------------|--------------------------|----------------------------|--------------------------------------|
| 1     | `<e.g. CustInfo>` | `CustTableFactBoxPart` | `<DS1>.CustAccount`        | Standard FactBox card                |
| 2     | …               | …                        | …                          | …                                    |

---

## 7. Navigation — how do users get here?

- **Main menu:** `<module > group > item>` — display menu item `<name>`, privilege `<name>`
- **Workspaces:** list any tile / link that opens this Form
- **From other Forms:** which Form buttons or grid hyperlinks open this one (record context if applicable)

---

## 8. Security

| Privilege name             | Duty (if part of one)      | Role(s) that get it     | Maintain / View only   |
|----------------------------|----------------------------|-------------------------|------------------------|
| `<Privilege>`              | `<Duty>`                   | `<Role(s)>`             | Maintain               |

> Privileges attach to menu items, not directly to the Form. Every button in §5 and every entry point in §7 needs an explicit privilege here.

---

## 9. Business rules

Field-level validations, default values, computed fields, side effects. Write each as `WHEN <condition> THEN <action>` so devs can translate them 1:1 into form-event / table-event methods.

- **VAL-01** — `WHEN <field> changes THEN <validation>` — error message: `"<text>"`, label id: `<@SmbXyz:Label_xyz>`
- **DEF-01** — `WHEN form opens AND <condition> THEN default <field> = <expression>`
- **COMP-01** — `<field> = <expression>` (computed; non-editable)
- **EFF-01** — `WHEN <field> changes THEN <side effect>` (e.g. recompute totals, set status)

---

## 10. Workflow (if applicable)

| Aspect              | Value                                                       |
|---------------------|-------------------------------------------------------------|
| Workflow template   | `<e.g. SalesQuotationApproval>`                             |
| Submit when         | `<state condition>`                                         |
| Approval steps      | `<role / participant resolution>`                           |
| Recall allowed      | Yes / No                                                    |
| Workflow controls   | Submit / Recall / Approval history visible — Yes / No       |

Remove this section if not applicable.

---

## 11. Number sequences

| Reference                 | Scope                | Format       | Notes                              |
|---------------------------|----------------------|--------------|------------------------------------|
| `<e.g. SalesId>`          | `DataArea`           | `SO########` | Continuous? Manual override?       |

---

## 12. Out of scope / open questions

- **OOS-01** — `<what we deliberately did not include>` — reason
- **Q-01** — `<open question for the architect>` — assignee, target date

---

## 13. Change log

| Date       | Author | Change                                               |
|------------|--------|------------------------------------------------------|
| YYYY-MM-DD | `<n>`  | First draft                                          |
