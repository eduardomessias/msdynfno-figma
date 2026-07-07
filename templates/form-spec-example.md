# Form Spec — `SmbSalesTableExperience` (Sales order — Smartbox Experience)

> **This is an illustrative example.** It demonstrates how the template is filled in for a realistic Smartbox scenario: a Sales order Form extended for the **Experience** product line (gift experiences sold to B2C and B2B). The Form re-uses standard `SalesTable` / `SalesLine` and adds Smartbox-specific fields for experience activation, partner provider, and voucher generation.

---

## 0. Metadata

| Field                    | Value                                                                |
|--------------------------|----------------------------------------------------------------------|
| Form name                | `SmbSalesTableExperience` (extension of `SalesTable`)                |
| Caption (page title)     | "Sales orders — Experience"                                          |
| Business process (L1)    | Order to cash                                                        |
| Business process (L2/L3) | Manage sales orders / Process experience orders                      |
| Module                   | Accounts receivable / Sales and marketing                            |
| Pattern                  | `DetailsTransaction`                                                 |
| Pattern doc              | [catalog/patterns/details-transaction.md](../catalog/patterns/details-transaction.md) |
| Figma frame              | _<paste Figma URL when frame is published>_                          |
| Consultant               | Eduardo Messias                                                      |
| Developer                | _<TBA>_                                                              |
| Stakeholder sign-off     | _<pending>_                                                          |
| Last updated             | 2026-05-20                                                           |
| Status                   | Draft                                                                |

---

## 1. Purpose

Sales reps capture orders for gift-experience products on behalf of B2C and B2B customers. The Form supports the full order lifecycle (capture → confirm → reserve experience capacity with the partner provider → invoice → generate voucher → activate). It extends the standard D365FO Sales order to surface the partner-experience metadata that makes Smartbox's business model possible.

## 2. Pattern justification

Step 3 in the decision tree: the entity is a transaction with a clear Header (one sales order) and Lines (one or more experience SKUs). Sales reps need to drill in from a list, edit a single order, and occasionally bulk-edit basic fields across many. → **Details Transaction**.

A Simple List and Details was considered and rejected — child collections (lines, voucher records, delivery sets) are more than 5, and the experience activation logic warrants a dedicated Header view that can grow.

---

## 3. Data sources

| Order | Data source name      | Table              | Join with         | Link type | Range / Query filters                                  | AllowEdit | AllowCreate | AllowDelete |
|-------|-----------------------|--------------------|-------------------|-----------|--------------------------------------------------------|-----------|-------------|-------------|
| 1     | `SalesTable`          | `SalesTable`       | —                 | —         | `SalesType == Sales` AND `SmbOrderCategory == Experience` | Yes       | Yes         | Yes         |
| 2     | `SmbSalesTable_Exp`   | `SmbSalesTable_Exp`| `SalesTable`      | Inner     | —                                                      | Yes       | Yes         | Yes         |
| 3     | `SalesLine`           | `SalesLine`        | `SalesTable`      | Delayed   | —                                                      | Yes       | Yes         | Yes         |
| 4     | `SmbSalesLine_Exp`    | `SmbSalesLine_Exp` | `SalesLine`       | Inner     | —                                                      | Yes       | Yes         | Yes         |
| 5     | `CustTable`           | `CustTable`        | `SalesTable`      | Outer     | —                                                      | No        | No          | No          |

> `SmbSalesTable_Exp` / `SmbSalesLine_Exp` are Smartbox extension tables holding experience-only fields. Inner join keeps them in lockstep with the parent rows.

---

## 4. Fields

### Section: ActionPane

Covered in §5.

### Section: SidePanel — NavigationList (transactions list)

| Label (UI)      | Bound to                              | EDT                  | Control type           | Mandatory | AllowEdit | Default     | Visible-when | Notes                                          |
|-----------------|---------------------------------------|----------------------|------------------------|-----------|-----------|-------------|--------------|------------------------------------------------|
| Sales order     | `SalesTable.SalesId`                  | `SalesIdBase`        | StringEdit (link)      | —         | No        | —           | Always       | Opens DetailsPanel on click                    |
| Customer        | `SalesTable.CustAccount`              | `CustAccount`        | StringEdit (read-only) | —         | No        | —           | Always       | Two-line row: SalesId on line 1, Customer on 2 |
| Order total     | `SalesTotals.totalAmount` (computed)  | `AmountMST`          | StringEdit (read-only) | —         | No        | —           | Always       | Last column per Details Transaction UX rule    |

### Section: Header view — `Tab: General` (FastTab)

| Label (UI)         | Bound to                              | EDT                  | Control type        | Mandatory | AllowEdit              | Default                          | Visible-when    | Notes                                  |
|--------------------|---------------------------------------|----------------------|---------------------|-----------|------------------------|----------------------------------|-----------------|----------------------------------------|
| Sales order       | `SalesTable.SalesId`                  | `SalesIdBase`        | StringEdit          | Yes       | No (after first save)  | Number seq `SmbSalesId`          | Always          | See §11                                |
| Customer account  | `SalesTable.CustAccount`              | `CustAccount`        | ReferenceGroup      | Yes       | Yes (until confirmed)  | —                                | Always          | Lookup on `CustTable`                  |
| Customer name     | `CustTable.Name`                      | `CustName`           | StringEdit (read-only) | —     | No                     | —                                | Always          | Driven by CustAccount                  |
| Order category    | `SalesTable.SmbOrderCategory`         | `SmbOrderCategory`   | ComboBox            | Yes       | No (always Experience) | `Experience`                     | Always          | Form is filtered to Experience         |
| Currency          | `SalesTable.CurrencyCode`             | `CurrencyCode`       | ComboBox            | Yes       | Yes (until first line) | Customer's default               | Always          | —                                      |
| Requested ship    | `SalesTable.RequestedShippingDate`    | `ShipDate`           | DateEdit            | Yes       | Yes                    | Today                            | Always          | —                                      |
| Status            | `SalesTable.SalesStatus`              | `SalesStatus`        | StringEdit (read-only) | —     | No                     | `Created`                        | Always          | —                                      |

### Section: Header view — `Tab: Experience details` (FastTab — Smartbox extension)

| Label (UI)               | Bound to                                       | EDT                       | Control type     | Mandatory | AllowEdit              | Default            | Visible-when                       | Notes                                  |
|--------------------------|------------------------------------------------|---------------------------|------------------|-----------|------------------------|--------------------|------------------------------------|----------------------------------------|
| Channel                  | `SmbSalesTable_Exp.Channel`                    | `SmbSalesChannel`         | ComboBox         | Yes       | Yes (until confirmed)  | `B2C`              | Always                             | `B2C` / `B2B` / `Marketplace`          |
| Gifting?                 | `SmbSalesTable_Exp.IsGift`                     | `NoYesId`                 | CheckBox         | No        | Yes (until invoiced)   | No                 | Always                             | Toggles voucher generation             |
| Recipient name           | `SmbSalesTable_Exp.RecipientName`              | `SmbRecipientName`        | StringEdit       | Conditional | Yes (until invoiced) | —                  | `IsGift == Yes`                    | VAL-01                                 |
| Recipient email          | `SmbSalesTable_Exp.RecipientEmail`             | `Email`                   | StringEdit       | Conditional | Yes (until invoiced) | —                  | `IsGift == Yes`                    | VAL-02                                 |
| Voucher delivery date    | `SmbSalesTable_Exp.VoucherDeliveryDate`        | `TransDate`               | DateEdit         | Conditional | Yes (until invoiced) | Requested ship date | `IsGift == Yes`                   | —                                      |

### Section: Header view — `Tab: Delivery address` (FastTab)

…(standard Sales order address fields — omitted in this example for brevity)

### Section: Line view — `LineViewHeader` (Toolbar and Fields summary band)

| Label (UI)        | Bound to                              | EDT             | Control type        | Mandatory | AllowEdit | Default  | Visible-when | Notes                                      |
|-------------------|---------------------------------------|-----------------|---------------------|-----------|-----------|----------|--------------|--------------------------------------------|
| Sales order       | `SalesTable.SalesId`                  | `SalesIdBase`   | StringEdit (read-only) | —     | No        | —        | Always       | Mirror from Header view                    |
| Customer          | `SalesTable.CustAccount`              | `CustAccount`   | StringEdit (read-only) | —     | No        | —        | Always       | Mirror from Header view                    |
| Status            | `SalesTable.SalesStatus`              | `SalesStatus`   | StringEdit (read-only) | —     | No        | —        | Always       | Mirror from Header view                    |
| Order total       | `SalesTotals.totalAmount` (computed)  | `AmountMST`     | StringEdit (read-only) | —     | No        | —        | Always       | Updates on line edit                       |

### Section: Line view — `LineViewLines` (Grid — `SalesLine` + `SmbSalesLine_Exp`)

| Label (UI)        | Bound to                                       | EDT                     | Control type     | Mandatory | AllowEdit                | Default    | Visible-when | Notes                                                |
|-------------------|------------------------------------------------|-------------------------|------------------|-----------|--------------------------|------------|--------------|------------------------------------------------------|
| Line              | `SalesLine.LineNum`                            | `LineNumber`            | RealEdit (read-only) | —     | No                       | Auto       | Always       | First column                                         |
| Experience SKU    | `SalesLine.ItemId`                             | `ItemId`                | ReferenceGroup   | Yes       | Yes                      | —          | Always       | Lookup constrained to `SmbExperienceCatalogue`       |
| Description       | `SalesLine.Name`                               | `ItemName`              | StringEdit (read-only) | —   | No                       | —          | Always       | Driven by ItemId                                     |
| Partner provider  | `SmbSalesLine_Exp.ProviderId`                  | `SmbProviderId`         | ReferenceGroup   | Yes       | Yes (until confirmed)    | From item  | Always       | Default from `SmbExperienceCatalogue.DefaultProvider`|
| Quantity          | `SalesLine.SalesQty`                           | `SalesQty`              | RealEdit         | Yes       | Yes                      | 1          | Always       | —                                                    |
| Unit price        | `SalesLine.SalesPrice`                         | `SalesPrice`            | RealEdit         | Yes       | Yes                      | From item  | Always       | —                                                    |
| Activation window | `SmbSalesLine_Exp.ActivationDays`              | `SmbActivationDays`     | IntEdit          | Yes       | Yes (until confirmed)    | 365        | Always       | Days from voucher delivery                           |
| Line amount       | `SalesLine.LineAmount` (computed)              | `AmountMST`             | RealEdit (read-only) | —     | No                       | —          | Always       | Last column                                          |

### Section: Line view — `LineViewLineDetails` (Standard tabs)

Tabs: **General**, **Setup**, **Address**, **Experience** (Smartbox), **Financials**.

(Field list omitted for brevity — the **Experience** tab carries `RedemptionCodePolicy`, `BlackoutDateSet`, `MultiUseAllowed`, and `IssuedVoucherCount` (computed).)

---

## 5. ActionPane

| Tab               | Group              | Caption          | Type            | Menu item                  | Privilege                  | Enabled-when                 | Notes                              |
|-------------------|--------------------|------------------|-----------------|----------------------------|----------------------------|------------------------------|------------------------------------|
| Sell              | Maintain           | Confirm          | MenuItemButton  | `SalesFormLetter_Confirm`  | `SalesOrderConfirm`        | `Status == Created`          | Standard sales confirmation        |
| Sell              | Generate           | Pick             | MenuItemButton  | `WHSWorkExecuteFromSales`  | `WHSOutboundPick`          | `Status == Confirmed`        | Picking starts experience capacity reserve |
| Sell              | Generate           | Invoice          | MenuItemButton  | `SalesFormLetter_Invoice`  | `SalesOrderInvoice`        | `Status in {Delivered}`      | —                                  |
| Experience        | Vouchers           | Generate vouchers| MenuItemButton  | `SmbVoucherGenerate`       | `SmbVoucherManage`         | `Status == Invoiced AND IsGift == Yes` | Custom Smartbox action       |
| Experience        | Vouchers           | Voucher history  | MenuItemButton  | `SmbVoucherHistory`        | `SmbVoucherView`           | At least one voucher exists  | Opens history Form (Simple List)   |
| Experience        | Provider           | Provider details | MenuItemButton  | `SmbProviderTable`         | `SmbProviderView`          | Always                       | Opens Provider Details             |
| Invoice           | Journals           | Invoice journals | MenuItemButton  | `CustInvoiceJour`          | `CustInvoiceView`          | Has posted invoices          | —                                  |
| Related           | Customer           | Customer         | MenuItemButton  | `CustTable`                | `CustTableMaintain`        | Always                       | —                                  |
| Options           | (foundation)       | _provided_       | (foundation)    | —                          | —                          | —                            | Show/hide, Personalize, Page options |

> Foundation buttons (New / Delete / Save / Refresh / Attachments / Export) are not listed — they come from the Details Transaction pattern.

---

## 6. FactBoxes (right pane)

| Order | FormPart                  | Form name                    | Bound to                  | Notes                                                |
|-------|---------------------------|------------------------------|---------------------------|------------------------------------------------------|
| 1     | CustInfo                  | `CustTableFactBoxPart`       | `SalesTable.CustAccount`  | Customer card                                        |
| 2     | CustOrderSummary          | `CustOrderSummaryFactBox`    | `SalesTable.CustAccount`  | Open orders count + value                            |
| 3     | SmbExperienceCapacity     | `SmbCapacityFactBox`         | `SmbSalesLine_Exp.ProviderId` (selected line) | Provider capacity for selected line          |
| 4     | DocumentAttachments       | (foundation)                 | `SalesTable` record        | Foundation attachments                                |

---

## 7. Navigation — how do users get here?

- **Main menu:** _Accounts receivable > Orders > Experience sales orders_ — display menu item `SmbSalesTableExperienceListPage`, privilege `SmbSalesOrderExperienceView`
- **Workspaces:**
  - _Sales order processing and inquiry_ tile **Experience orders in progress** (filtered by `SmbOrderCategory == Experience AND SalesStatus in {Created, Confirmed}`)
  - _Experience operations_ workspace (Smartbox custom) — tile **Open experience orders**
- **From other Forms:**
  - From `CustTable` (Customer) ActionPane → Sell → Experience sales orders (record-context: filtered by `CustAccount`)
  - From `SmbExperienceCatalogue` row hyperlink **Recent orders for this experience**

---

## 8. Security

| Privilege name                          | Duty                              | Role(s)                                         | Maintain / View only |
|-----------------------------------------|-----------------------------------|-------------------------------------------------|----------------------|
| `SmbSalesOrderExperienceView`           | `SmbSalesOrderInquire`            | Sales clerk, Sales manager, Customer service    | View                 |
| `SmbSalesOrderExperienceMaintain`       | `SmbSalesOrderMaintain`           | Sales clerk, Sales manager                      | Maintain             |
| `SmbVoucherManage`                      | `SmbVoucherProcess`               | Sales manager, Voucher operations               | Maintain             |
| `SmbVoucherView`                        | `SmbVoucherInquire`               | Sales clerk, Sales manager, Customer service    | View                 |
| `SmbProviderView`                       | `SmbProviderInquire`              | Sales clerk, Sales manager, Sourcing            | View                 |

---

## 9. Business rules

- **VAL-01** — `WHEN SmbSalesTable_Exp.IsGift == Yes AND SmbSalesTable_Exp.RecipientName is empty THEN reject save` — error message: `"Recipient name is required for gift orders."` — label id: `@SmbSales:Label_RecipientNameRequired`
- **VAL-02** — `WHEN SmbSalesTable_Exp.IsGift == Yes AND SmbSalesTable_Exp.RecipientEmail does not match e-mail regex THEN reject save` — error: `"Provide a valid recipient e-mail."` — label id: `@SmbSales:Label_RecipientEmailInvalid`
- **VAL-03** — `WHEN SalesLine.ItemId is not present in SmbExperienceCatalogue THEN reject save` — error: `"Only catalogue experiences can be added to an experience sales order."` — label id: `@SmbSales:Label_ItemNotInCatalogue`
- **DEF-01** — `WHEN form opens for new record THEN SalesTable.SmbOrderCategory = Experience`
- **DEF-02** — `WHEN SalesLine.ItemId changes THEN SmbSalesLine_Exp.ProviderId = SmbExperienceCatalogue.find(ItemId).DefaultProvider`
- **COMP-01** — `LineAmount = SalesQty * SalesPrice * (1 - DiscPercent/100) - LineDisc + LineTax` (standard SalesLine compute, unchanged)
- **EFF-01** — `WHEN SalesTable.SalesStatus changes to Invoiced AND SmbSalesTable_Exp.IsGift == Yes THEN auto-trigger Generate vouchers (no UI prompt; logs to voucher history)`
- **EFF-02** — `WHEN SmbSalesLine_Exp.ProviderId changes AND SalesTable.SalesStatus == Confirmed THEN warn user that re-confirmation is required` — message: `"Provider changed after confirmation. Please re-confirm the order."`

---

## 10. Workflow

Not applicable for v1 — orders flow without approval. Future increment may add an approval workflow for orders above a threshold (tracked separately).

---

## 11. Number sequences

| Reference          | Scope        | Format        | Notes                                             |
|--------------------|--------------|---------------|---------------------------------------------------|
| `SmbSalesId`       | DataArea     | `EXP########` | Continuous, no manual override (audit requirement)|

---

## 12. Out of scope / open questions

- **OOS-01** — Multi-line discount approvals — out of scope for v1; we ship with single approver per customer agreement.
- **OOS-02** — Marketplace channel mapping to specific partner agreements — handled by a separate Form (`SmbMarketplaceMapping`).
- **Q-01** — Should `Generate vouchers` be re-runnable after partial cancellation? — assigned to architect (Eduardo) — target 2026-05-27.
- **Q-02** — Do we need a Power BI FactBox showing experience-level inventory burn-down? — assigned to BI lead — target 2026-06-03.

---

## 13. Change log

| Date       | Author          | Change                                                                  |
|------------|-----------------|-------------------------------------------------------------------------|
| 2026-05-20 | Eduardo Messias | First draft — illustrative example for the spec template demonstration. |
