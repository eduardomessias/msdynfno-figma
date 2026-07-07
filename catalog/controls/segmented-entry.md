# Segmented Entry — control

> **Microsoft references:**
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/segmented-entry-control-migration-guidance
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/segmented-entry-control-patterning-support
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/financial/segmented-entry-control-dialog-support
>
> **AOT control class:** `SegmentedEntry` (controller pattern via `DimensionDefaultingController`, `DimensionDynamicAccountController`, etc.)
> **Figma component:** `Subpattern/DimensionEntryControl` (catalog alias: `Subpattern/SegmentedEntry`)
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-06-25

---

## 1. Purpose

The **Segmented Entry control** is the D365FO UI for entering **financial dimension values** and **ledger account combinations**. It renders as a segmented text field where each segment corresponds to a dimension (Main account, Department, Cost center, etc.) and allows independent lookup per segment.

It is used exclusively for:
- **Ledger account / dimension combination** fields (e.g. `LedgerDimension`, `DefaultDimension`).
- **Dynamic account** fields where the account type (customer, vendor, ledger) drives which picker opens.

It is **not** used for any other data entry. For a regular lookup, use [Lookup / ReferenceGroup](./lookup-control.md).

---

## 2. Anatomy

```
SegmentedEntry (controller-driven)
├── Segment 1 — Main account   [  110100  ]
├── Separator                   -
├── Segment 2 — Department     [  0250    ]
├── Separator                   -
├── Segment N — …              [  …       ]
└── Lookup icon ▾ (per segment; opens dimension-specific picker)
```

The number of visible segments and their labels are determined by the **account structure** active for the legal entity, not by the form design. The control is inherently dynamic.

---

## 3. Controller types

Each Segmented Entry instance is wired to a **controller class** that determines what can be entered:

| Controller class                         | Stores                         | Typical fields                             |
|------------------------------------------|--------------------------------|--------------------------------------------|
| `DimensionDefaultingController`          | `DefaultDimension` (no account) | Financial dimension defaults on a record  |
| `DimensionDynamicAccountController`      | `LedgerDimension` + account type | Journal line account fields              |
| `LedgerDimensionAccountController`       | `LedgerDimension` (ledger only) | General ledger postings                  |
| `BudgetLedgerDimensionController`        | Budget-specific dimension entry | Budget register entries                  |

---

## 4. Key properties

| Property / Method      | Notes                                                                                    |
|------------------------|------------------------------------------------------------------------------------------|
| `controller` class     | Instantiated in `Form.init()` or DS `init()` — determines behaviour and segments shown  |
| `parmAttributeSetDataSource()` | Links the control to the `DefaultDimension` or `LedgerDimension` field          |
| `parmDimensionAccountStorageUsage()` | Controls which account types are valid in dynamic account mode               |
| `AccountType` field    | Sibling field (e.g. `LedgerJournalTrans.AccountType`) that drives dynamic account picker |
| Read-only              | `control.allowEdit(false)` — called when the record is posted/confirmed                 |

---

## 5. States / variants

| State       | Visual                                                                    |
|-------------|---------------------------------------------------------------------------|
| Rest        | Segmented boxes, separator dashes, labels above each segment              |
| Hover       | Active segment border darkens                                             |
| Focus       | Active segment highlighted + lookup icon shown                            |
| Open        | Segment picker dropdown visible (one segment at a time)                   |
| Read-only   | No borders, no lookup icon — segment values as plain text                 |
| Error       | Error-token border on the invalid segment + message below                 |
| Partial     | Some segments filled, others empty — valid interim state during entry      |

---

## 6. UX guidelines

1. **Don't redesign or simplify** — the Segmented Entry is a framework control. Consultants and devs cannot change its visual appearance; they can only configure which dimensions appear via the account structure setup.
2. **Account structure drives segments** — the mock should show a representative account structure (e.g. Main account - Department - Cost center). Add a note that actual segments depend on the live legal entity configuration.
3. **Read-only for posted records** — always call `allowEdit(false)` after a journal line or ledger entry is posted. The BP pattern enforces this.
4. **Dynamic account type** — when the `AccountType` ComboBox changes (Customer / Vendor / Ledger / …), the segments and the picker change accordingly. Mock the most common type for the form context.
5. **Label** — label the control after the field concept ("Offset account", "Ledger account", "Default dimensions"), not after the control type.

---

## 7. Do / Don't

**Do**
- Wire the correct controller class for the field type (DefaultDimension vs LedgerDimension vs Dynamic).
- Show a representative account structure in the mock, with a note that it's configuration-dependent.
- Call `allowEdit(false)` on posted/confirmed records.

**Don't**
- Don't try to replicate the control with individual StringEdit fields — always use the framework control.
- Don't hard-code segment labels or count in the Figma mock without noting configuration dependency.
- Don't use Segmented Entry for anything other than financial dimensions.

---

## 8. AOT mapping (dev handoff)

| Spec field             | AOT location                                                                        | Notes                                                    |
|------------------------|-------------------------------------------------------------------------------------|----------------------------------------------------------|
| Control type           | `Form > Design > … > SegmentedEntry`                                                | Add the control                                          |
| Controller binding     | `SegmentedEntry.controller` set in `Form.init()` via `new DimensionDefaultingController(…)` | Must match the field being bound               |
| Field binding          | `controller.parmAttributeSetDataSource(ds, fieldnum(Table, DefaultDimension))`      | Links control to the dimension field                     |
| Account type source    | `controller.parmDimensionAccountStorageUsage(…)` + linked `AccountType` field       | For dynamic account controllers only                     |
| Read-only              | `segmentedEntry.allowEdit(false)` in `DS.active()` or status-driven event           | Called whenever the record enters a protected state      |

---

## 9. Figma component definition

- **Name:** `Subpattern/DimensionEntryControl` (also referenced as `Subpattern/SegmentedEntry` in the component mapping)
- **Slots:** Label (text), Segment 1 value / Segment 2 value / Segment 3 value (text, configurable count)
- **Variants:**
  - `State`: Rest / Focus / ReadOnly / Error
  - `Segment count`: 2 / 3 / 4 (representative; note actual count is config-driven)
  - `Mode`: DefaultDimension / LedgerDimension / DynamicAccount
- **Usage note:** Always add a Figma annotation: "Segments shown are representative of the [Legal entity] account structure. Final segment count and labels depend on Chart of Accounts setup."

---

## 10. References

- Microsoft Learn — [Segmented Entry control migration guidance](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/segmented-entry-control-migration-guidance)
- Microsoft Learn — [Segmented Entry control patterning support](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/segmented-entry-control-patterning-support)
- Microsoft Learn — [Segmented Entry control dialog support](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/financial/segmented-entry-control-dialog-support)
