# RecordTitle — composite

> **Microsoft references:**
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/details-master-form-pattern (§ Title area)
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/details-transaction-form-pattern (§ Title area)
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/general-form-guidelines (Page title section)
>
> **AOT artefact:** Not a standalone AOT control — composed from `Design.Caption`, `HeaderTitle` group, and data-bound fields as specified by the pattern
> **Figma component:** `Composite/RecordTitle`
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-06-25

---

## 1. Purpose

The **RecordTitle** is the visual identity strip at the top of a details form (Details Master, Details Transaction). It shows:

1. **Identifier** — the primary key or natural key of the current record (e.g. `EXP00000125`).
2. **Description** — a human-readable name or description (e.g. `Aurora Travel`).
3. **Status badge** (optional) — the workflow or business status of the record (e.g. `Confirmed`).

The combined format is: **`<ID> : <Description> — <Status>`**

Example: `EXP00000125 : Aurora Travel — Confirmed`

This title is **read-only at all times** — it is not an editable field. It is rendered by the browser frame, not inside a FastTab.

---

## 2. Anatomy

```
RecordTitle area (top of form body, above ActionPane)
├── Identifier text        — primary/natural key, bold
├── Separator " : "
├── Description text       — name or description field
└── Status text (optional) — em-dash separator + status value
```

In the AOT, this is assembled from:
- `Form.Design.Caption` (form title for the browser tab / breadcrumb)
- The pattern-specified **HeaderTitle** group (which controls render the ID and description)
- The **EntityStatus** composite (if a status badge is displayed — see [entity-status.md](./entity-status.md))

---

## 3. Key properties

| Property / Location           | Notes                                                                                          |
|-------------------------------|------------------------------------------------------------------------------------------------|
| `Form.Design.Caption`         | Label ID for the form's page title (e.g. "Sales orders — Experience"). This is the title in the breadcrumb and browser tab, not the record identity line. |
| `HeaderTitle group`           | Group at the top of the Design tree (pattern-specific). Contains the ID and description fields rendered as the record title. |
| ID field                      | Bound to the primary/natural key field (e.g. `SalesId`). Always `AllowEdit = No` after creation. |
| Description field             | Bound to the entity name/description field (e.g. `CustAccount`'s `Name` via ReferenceGroup or display method). |
| Status field                  | Optional; typically bound to a `Status` Enum field or computed via display method              |

---

## 4. Format rules (MS UX guidelines)

| Scenario                          | Format                                    | Example                              |
|-----------------------------------|-------------------------------------------|--------------------------------------|
| ID + Description (standard)       | `<ID> : <Description>`                    | `EXP00000125 : Aurora Travel`        |
| ID only (no name field)            | `<ID>`                                   | `GL-00124`                           |
| ID + Description + Status          | `<ID> : <Description> — <Status>`        | `EXP00000125 : Aurora Travel — Confirmed` |
| Inline separator                  | `·` bullet for secondary attributes      | `EXP00000125 · B2B · €2,420`         |

> The separator between ID and Description is `" : "` (space-colon-space). The separator before status is `" — "` (space-em-dash-space). Do not use other separators.

---

## 5. UX guidelines

1. **Always show the natural key** — the ID segment should be the field users refer to in external comms (order number, customer account, etc.), not an internal RecId.
2. **Description is the entity name** — for customers it's the customer name; for orders it's the ordering party; for items it's the product name.
3. **Status badge is optional** — only include when the record has a meaningful workflow or business status that affects what the user can do. Don't add status for simple setup records.
4. **Non-editable** — the title area is never editable inline. The user edits the ID and description in the FastTab body below.
5. **New record** — on a new, unsaved record, the title shows `<New>` for the ID and the description is empty until saved.

---

## 6. Do / Don't

**Do**
- Follow the `<ID> : <Description>` format for all Details Master and Details Transaction forms.
- Show status in the title when the record has a lifecycle (workflow states, processing status).
- Keep the description to the **primary name** — not a concatenation of multiple fields.

**Don't**
- Don't use the record title for editable fields.
- Don't concatenate many attributes into the description — use the FastTab body and FactBox for additional context.
- Don't show an internal surrogate key (RecId) as the ID — always the natural/business key.

---

## 7. AOT mapping (dev handoff)

| Spec field               | AOT location                                                                      | Notes                                                 |
|--------------------------|-----------------------------------------------------------------------------------|-------------------------------------------------------|
| Form page title          | `Form.Design.Caption` (label ID)                                                  | Browser tab + breadcrumb title                        |
| ID field in title        | `HeaderTitle group > <ID field>` (pattern-specified position)                     | `AllowEdit = No` after record creation                |
| Description field        | `HeaderTitle group > <Description field>` (display method or ReferenceGroup)     | Usually a `display` method resolving the name         |
| Status in title          | `HeaderTitle group > <Status field>` or `EntityStatus` composite                 | Enum field or display method                          |
| New record placeholder   | Framework default — renders `<New>` when `RecId == 0`                             | No dev action required                                |

---

## 8. Figma component definition

- **Name:** `Composite/RecordTitle`
- **Slots:**
  - `ID text` (text — e.g. "EXP00000125")
  - `Description text` (text — e.g. "Aurora Travel")
  - `Status text` (text, optional — e.g. "Confirmed")
- **Variants:**
  - `Format`: IDOnly / IDAndDescription / IDDescriptionStatus
  - `State`: Existing / New (renders `<New>` in the ID slot)
- **Usage note:** Always fill both the ID and Description slots in mocks. Use realistic business-meaningful values (order numbers, customer names), not "Record 1" placeholders.

---

## 9. References

- Microsoft Learn — [Details Master form pattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/details-master-form-pattern)
- Microsoft Learn — [Details Transaction form pattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/details-transaction-form-pattern)
- Microsoft Learn — [General form guidelines — Page title](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/general-form-guidelines)
- [EntityStatus composite](./entity-status.md) — the status badge shown in the title area
