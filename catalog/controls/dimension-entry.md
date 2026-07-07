# Dimension Entry Control — subpattern

> **Microsoft references:**
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/dimension-entry-control-subpattern
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/financial/dimension-entry-control-uptake
>
> **AOT subpattern:** `DimensionEntryControl` (container-level)
> **AOT control:** `DimensionEntry` control class (wraps SegmentedEntry)
> **Figma component:** `Subpattern/DimensionEntryControl`
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-07-01

> **Related doc:** [`catalog/controls/segmented-entry.md`](./segmented-entry.md) covers the inner SegmentedEntry control and its controller classes. This doc covers the container subpattern that hosts it.

---

## 1. Purpose

The **Dimension Entry Control** is the full container subpattern for entering financial dimension values in a form. It wraps the lower-level `SegmentedEntry` control (which renders the individual dimension segments) and adds the surrounding FastTab or Group infrastructure needed to apply the subpattern correctly.

Use this subpattern when the form requires a user to enter or review:
- A **default dimension** (a set of dimension values without a main account) — e.g. on a vendor, department, or cost centre record
- A **ledger account combination** (main account + dimensions) — e.g. on a journal line or a posting profile

---

## 2. Anatomy

```
TabPage or Group                      ← subpattern applied here
└── DimensionEntry (custom control)   ← the SegmentedEntry-based composite
    ├── Segment: Main account         ← if using LedgerDimensionAccountController
    ├── Separator
    ├── Segment: Dimension 1          ← e.g. Department
    ├── Separator
    └── Segment: Dimension N          ← count depends on chart of accounts configuration
```

The segment count and labels are determined by the active **chart of accounts** and **account structure** configuration in the environment — they are not controlled by the form design. The form specifies the controller and the data binding; Finance determines the segment layout.

---

## 3. Controller classes

Choose the correct controller based on the field's purpose:

| Controller | Field type | Stores | Use when |
|-----------|-----------|--------|----------|
| `DimensionDefaultingController` | Default dimension | Dimension values without main account | Vendor, customer, department default dimensions |
| `LedgerDimensionAccountController` | Ledger dimension | Main account + dimensions | Journal line account, posting profile |
| `DimensionDynamicAccountController` | Dynamic account | Account type determined at runtime (Customer, Vendor, Ledger, etc.) | Journal lines where the account type varies |
| `BudgetPlanningDimensionEntryController` | Budget dimension | Budget-specific dimension combinations | Budget planning forms only |

---

## 4. Key properties

| Property / Location                          | Notes                                                                                                        |
|----------------------------------------------|--------------------------------------------------------------------------------------------------------------|
| `parmAttributeSetDataSource()`               | Links the control to the dimension field on the table — required for all controller types                    |
| Controller instantiation                     | In `init()`: `controller = DimensionDefaultingController::construct(...)` then `controller.bind(formRun, fieldControl)` |
| `allowEdit(false)`                           | Call on the controller when the record is posted / confirmed — prevents dimension changes on closed records  |
| Segment labels                               | Derived from the chart of accounts configuration — not form-level labels; don't attempt to override them     |
| `parmAccountType()` (DynamicAccount only)    | Set based on another field (e.g. `LedgerJournalTrans.AccountType`) to determine which account type to use    |

---

## 5. UX guidelines

1. **Show representative segments in mocks** — use a typical 2–4 segment combination (Main account – Department – Cost centre) and annotate: *"Segment count depends on Chart of Accounts configuration."*
2. **Lock the control on posted records** — call `controller.allowEdit(false)` when `LedgerJournalTrans.Posted == NoYes::Yes` or equivalent. The control renders as read-only (grey background, no segment lookup).
3. **Don't replicate with individual StringEdit fields** — the Dimension Entry Control provides validation, lookup, and cross-segment dependency resolution that individual fields cannot.
4. **Dimension validation is real-time** — invalid combinations (e.g. a main account that doesn't allow the entered cost centre) display an inline error on the relevant segment. Don't add a separate validation button.
5. **Allow blank dimensions** when the entity's dimension requirements permit it. Mandatory enforcement is configured in the chart of accounts, not in the form.

---

## 6. Do / Don't

**Do**
- Select the correct controller class for the field purpose (defaulting vs ledger vs dynamic).
- Call `allowEdit(false)` on posted/confirmed records.
- Annotate mocks with a config note about segment count.
- Show realistic account combinations (e.g. "110100 – 0250 – CC-WEST").

**Don't**
- Don't replicate with individual StringEdit fields per dimension.
- Don't hardcode segment labels in the Figma mock — they vary by chart of accounts.
- Don't allow editing after the record is posted.
- Don't use `DimensionDefaultingController` for ledger account fields — use `LedgerDimensionAccountController`.

---

## 7. AOT mapping (dev handoff)

| Spec field                  | AOT location                                                                                  | Notes                                                                   |
|-----------------------------|-----------------------------------------------------------------------------------------------|-------------------------------------------------------------------------|
| Subpattern                  | `TabPage.SubPattern = DimensionEntryControl` (or applied to a Group)                         | May host just the DimensionEntry control, or alongside other fields      |
| Controller class            | X++ `init()` method on the form datasource or form                                           | Instantiate, configure, and bind the controller in `init()`             |
| `parmAttributeSetDataSource()` | Controller method — binds control to the dimension field on the table                      | Required; missing = control renders blank                               |
| `allowEdit(false)`          | Controller method — called when the record transitions to a locked state                      | Hook into datasource `active()` and record status change events         |
| `DimensionEntry` control    | Placed in the form design as a `DimensionEntry` custom control; no DataSource/DataField props | Controller handles all data binding                                     |

---

## 8. Figma component definition

- **Name:** `Subpattern/DimensionEntryControl`
- **Represents:** The SegmentedEntry composite within its container
- **Slots:** Segment pills in a horizontal row, separated by "–" markers; lookup icon at the right end
- **Variants:**
  - `Segment count`: 2 / 3 / 4 (representative; annotate as config-dependent)
  - `Controller type`: DefaultDimension / LedgerDimension / DynamicAccount
  - `State`: Rest / Active segment / ReadOnly (posted)
- **Usage note:** Show a realistic account combination (e.g. "110100 – 0250 – CC-WEST"). Include a footnote in the mock: *"Segment count and labels determined by Chart of Accounts configuration."* Use the ReadOnly variant on posted records.

---

## 9. References

- Microsoft Learn — [Dimension Entry Control subpattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/dimension-entry-control-subpattern)
- Microsoft Learn — [Dimension Entry Control uptake guide](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/financial/dimension-entry-control-uptake)
- [Segmented Entry control](./segmented-entry.md) — the inner control; includes controller class details
- [Dimension Expression Builder subpattern](./dimension-expression-builder.md) — used for dimension filter expressions (budget control, financial reporting)
