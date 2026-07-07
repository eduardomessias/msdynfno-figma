# EntityStatus — composite

> **Microsoft references:**
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/details-master-form-pattern (§ Status area)
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/general-form-guidelines (Status section)
>
> **AOT artefact:** Not a standalone control — a styled `StringEdit` or `ComboBox` with `AllowEdit = No` placed inside the `HeaderTitle` group, or a workflow status indicator injected by the workflow framework
> **Figma component:** `Composite/EntityStatus`
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-06-25

---

## 1. Purpose

The **EntityStatus** composite is the **status badge** shown in the title area of a details form. It communicates the current **business lifecycle state** of the record at a glance — before the user reads any field.

Two distinct status mechanisms exist in D365FO:

| Type                 | Driver                          | Appearance                                             |
|----------------------|---------------------------------|--------------------------------------------------------|
| **Business status**  | A `Status` Enum field on the record | Coloured text or badge (e.g. "Confirmed", "Invoiced") |
| **Workflow status**  | The D365FO workflow engine      | WorkflowBar status chip (see [Workflow controls](./workflow-controls.md)) |

> These are separate and **both can appear simultaneously** on a form. Business status lives in the title area; workflow status lives in the WorkflowBar beneath the ActionPane.

---

## 2. Anatomy

```
HeaderTitle group
└── EntityStatus (positioned right-of-description in the title area)
    ├── Status label (text — the enum label, e.g. "Confirmed")
    └── Status colour indicator (token-driven background or text colour)
```

The control is `AllowEdit = No` — always read-only. The colour treatment is driven by design tokens mapped to the status enum values.

---

## 3. Status-to-colour mapping (Fluent 2 / D365FO convention)

| Status category        | Token role           | Visual treatment                         | Examples                                  |
|------------------------|----------------------|------------------------------------------|-------------------------------------------|
| Neutral / Draft        | `status.neutral`     | Grey text / no badge                     | "Created", "Draft", "Open"                |
| Active / Positive      | `status.success`     | Green badge or text                      | "Confirmed", "Active", "Approved"         |
| In-progress / Info     | `status.info`        | Blue badge or text                       | "In review", "Picking", "Scheduled"       |
| Warning / Attention    | `status.warning`     | Amber badge or text                      | "On hold", "Partial", "Pending review"    |
| Completed / Closed     | `status.neutral`     | Grey (same as neutral — indicates done)  | "Invoiced", "Posted", "Closed"            |
| Error / Rejected       | `status.danger`      | Red badge or text                        | "Rejected", "Cancelled", "Error"          |

> These mappings are **decisions made by the UX designer and architect** per project — D365FO framework renders the enum label in plain text by default; the colour treatment requires Figma tokens and CSS overrides in Smartbox-specific themes.

---

## 4. Key properties

| Property / Location            | Notes                                                                                       |
|--------------------------------|---------------------------------------------------------------------------------------------|
| Status field binding           | `StringEdit` or display method bound to the `Status` Enum field; `AllowEdit = No`          |
| Position in AOT                | Inside `HeaderTitle` group (pattern-specified); after the ID + Description fields           |
| EnumType                       | The `BaseEnum` that drives the status values (e.g. `SalesStatus`, `PurchStatus`)           |
| Token mapping                  | Each enum value maps to a `status.*` semantic token in `semantic.tokens.json`              |

---

## 5. Defining status enum values for a new Smartbox entity

When creating a new entity with a lifecycle, define the status enum in this order:

1. Create a `BaseEnum` in the AOT (e.g. `SmbVoucherStatus`).
2. Add enum values in lifecycle order (e.g. `Created → Generated → Redeemed → Expired`).
3. Map each value to a status token in the form spec (see token mapping table above).
4. Add the status field to the `HeaderTitle` group in the form design.
5. Include the mapping in the form spec §4 (Fields) and in the Figma component annotation.

---

## 6. UX guidelines

1. **One status indicator per form** — don't add a second status field elsewhere on the form body for the same status. The title area badge is the authoritative indicator.
2. **Meaningful states only** — don't add a status if the entity has only one state (e.g. a setup table with no lifecycle). Status is for entities with a meaningful workflow or business process.
3. **Positive states last** — order enum values so the lifecycle flows logically (Draft → Confirmed → Invoiced), not alphabetically.
4. **Don't encode compound state** — if "On hold" overlaps with "Confirmed", represent it as a separate field/flag, not a combined status value.
5. **Consistent labels** — use the same status label everywhere the entity appears (list page grid column, details form badge, workspace tile).

---

## 7. Do / Don't

**Do**
- Show the entity status badge in all form mocks for entities with a lifecycle.
- Map each status to a meaningful `status.*` token from `semantic.tokens.json`.
- Align the status label with what business users call the state in everyday speech.

**Don't**
- Don't show a status badge for setup/configuration entities with no lifecycle.
- Don't duplicate the status in a FastTab field (the title badge is sufficient).
- Don't use the workflow status chip as the business status badge — they are different things.

---

## 8. AOT mapping (dev handoff)

| Spec field               | AOT location                                                                       | Notes                                                   |
|--------------------------|------------------------------------------------------------------------------------|---------------------------------------------------------|
| Status enum              | `AOT > Data Types > Base Enums > <SmbEntityStatus>`                                | Create with values in lifecycle order                   |
| Status field             | `<Table>.<StatusField>` typed to the status enum EDT                               | Set `AllowEdit = No` (status driven by business logic)  |
| Form control             | `HeaderTitle group > StringEdit` bound to status field                             | `AllowEdit = No`; positioned after Description          |
| Token mapping            | `figma/tokens/semantic.tokens.json > status.*`                                     | Map each enum value label to a status token in the spec |
| Status transitions       | `<Table>.changeStatus()` method or business-logic service class                    | Status should only change via explicit business operations, not direct field edit |

---

## 9. Figma component definition

- **Name:** `Composite/EntityStatus`
- **Slots:** Status label (text — enum value label, e.g. "Confirmed")
- **Variants:**
  - `Category`: Neutral / Success / Info / Warning / Danger
  - `Style`: Text / Badge (pill background)
- **Usage note:** In mocks, pick the `Category` variant matching the status token mapping table above. Use the exact label from the AOT enum (not a paraphrase). Place `Composite/EntityStatus` inside `Composite/RecordTitle` in the title area of the Figma frame.

---

## 10. References

- Microsoft Learn — [Details Master form pattern — Status area](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/details-master-form-pattern)
- Microsoft Learn — [General form guidelines — Status indicators](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/general-form-guidelines)
- [RecordTitle composite](./record-title.md) — the title area that hosts EntityStatus
- [Workflow controls](./workflow-controls.md) — the separate workflow status chip
- [`figma/tokens/semantic.tokens.json`](../../figma/tokens/semantic.tokens.json) — status token definitions
