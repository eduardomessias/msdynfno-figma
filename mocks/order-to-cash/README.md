# Order to cash — mocks

The **Order to cash** business process covers the full lifecycle from receiving a customer order through invoicing and collecting payment. For Smartbox, the dominant variant of this process is the **Experience order** — gift-experience boxes sold to B2C and B2B customers, redeemed by recipients with partner experience providers.

See the Microsoft Learn [Order to cash business process](https://learn.microsoft.com/dynamics365/finance/accounts-receivable/order-to-cash) for the standard scope, and the [local patterns repo](C:/Users/Eduardo.Messias/source/repos/dynamics365patternspractices/graphics) for the full process-flow diagrams.

---

## Mocks

| # | Form (caption)                       | Pattern                                                                   | Status   | Mock entry                                                       |
|---|--------------------------------------|---------------------------------------------------------------------------|----------|------------------------------------------------------------------|
| 1 | Sales orders — Experience            | [Details Transaction](../../catalog/patterns/details-transaction.md)      | Draft    | [sales-order-experience.md](./sales-order-experience.md)         |

> Planned (not yet started): Customer onboarding (Details Master), Voucher inquiry (List Page), Experience capacity workspace (Operational Workspace), Experience catalogue (Simple List and Details), Generate vouchers (Drop Dialog).

---

## Conventions specific to this process area

- Smartbox extension tables and EDTs are prefixed `Smb…`. The mocks reflect this — devs verify EDTs against the actual extension model before build.
- Experience-specific functionality is gated by `SalesTable.SmbOrderCategory == Experience`. Forms targeting the experience flow filter the primary data source on this range.
- The **Order category** field is **always** `Experience` on Forms in this folder and is **not editable** post-creation (see VAL-* rules in each spec).
