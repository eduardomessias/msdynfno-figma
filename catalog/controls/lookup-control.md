# Lookup / ReferenceGroup — control

> **Microsoft references:**
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/lookup-controls
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/reference-group-control
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/build-lookup-form
>
> **AOT control classes:** `ReferenceGroup`, `StringEdit` with a lookup override
> **Figma component:** `Atom/Lookup`
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-06-25

---

## 1. Purpose

A **Lookup** is an input control backed by a **user-maintained reference table**. The user types a code or searches via a dropdown picker, and the framework resolves it to a record on a related table.

Two implementation approaches exist in D365FO:

| Approach         | When to use                                                                                  | AOT artefact        |
|------------------|----------------------------------------------------------------------------------------------|---------------------|
| **ReferenceGroup** | The preferred modern approach — directly references a surrogate-key FK relationship        | `ReferenceGroup` control + `FormDataSourceLink` |
| **StringEdit lookup** | Legacy — the field stores the natural key; a `lookup()` override on `StringEdit` opens a picker | `StringEdit` + `lookup()` override |
| **Custom Lookup form** | When the auto-generated lookup is insufficient (complex filter, multi-select, preview pane) | Separate form with `Lookup` pattern (see [Lookup pattern](../patterns/lookup.md)) |

> For **new Smartbox forms**, always prefer `ReferenceGroup`. Only use StringEdit lookup when extending a legacy form that already uses it.

---

## 2. Anatomy — ReferenceGroup

```
ReferenceGroup (bound to FK)
├── Label (from EDT or field label)
├── Replacement key field (resolved display value, e.g. customer name)
│   └── [displayed as the editable text in the control]
└── Lookup picker ▾ (opens inline dropdown or full Lookup form)
    └── Grid showing reference table rows
```

The `ReferenceGroup` wraps multiple fields from the related table but surfaces **only the replacement key** (typically the natural identifier or name) as the editable control. The surrogate `RecId` FK is stored behind the scenes.

---

## 3. Key properties

### ReferenceGroup

| Property                  | Notes                                                                                          |
|---------------------------|------------------------------------------------------------------------------------------------|
| `ReferenceField`          | The FK field on the current table (e.g. `CustAccount`)                                         |
| `DataSource`              | The **related** data source (the table being looked up, e.g. `CustTable`)                     |
| `ReplacementFieldGroup`   | Field group on the related table that provides the visible display fields                      |
| `AllowEdit`               | `No` for read-only references                                                                  |
| `Mandatory`               | `Yes` draws a required-field indicator                                                         |
| `JumpRef`                 | `Yes` enables "View details" navigation to the related record (default `Yes`)                 |

### StringEdit with lookup override

| Property                  | Notes                                                                                          |
|---------------------------|------------------------------------------------------------------------------------------------|
| `DataSource` / `DataField`| The field storing the natural key (e.g. `CustAccount`)                                         |
| `lookup()` override       | X++ method; calls `SysTableLookup` or custom form                                              |
| `lookupReference()` override | Preferred over `lookup()` for `ReferenceGroup`-backed lookups                             |

---

## 4. States / variants

| State       | Visual                                                                 |
|-------------|------------------------------------------------------------------------|
| Rest        | Border, label above, current display value + lookup icon ▾             |
| Hover       | Border darkens                                                          |
| Active      | Cursor in text area — user can type to filter                          |
| Open        | Inline dropdown or full Lookup form visible                            |
| Read-only   | No border, no lookup icon — display value text only                    |
| Error       | Error-token border + validation message (e.g. "Account not found")    |
| Disabled    | Label and value greyed; not interactive                                |
| Mandatory   | Asterisk next to label                                                 |

---

## 5. UX guidelines

1. **`ReferenceGroup` over StringEdit** — for every new FK relationship on a Smartbox form, use `ReferenceGroup`. It gives the framework correct navigation (JumpRef), filtering, and data integrity.
2. **Show the natural key** — the replacement field should show the human-readable identifier (customer name, vendor name, item number) rather than the surrogate RecId.
3. **JumpRef enabled** — leave `JumpRef = Yes` on every `ReferenceGroup` so the user can navigate to the related record with Ctrl+click or the "View details" link.
4. **Filter the lookup** — when the reference table is large, add a `performFormLookup()` override or use the [custom Lookup form pattern](../patterns/lookup.md) to scope the picker.
5. **Don't show the FK surrogate key** — the user should never see a RecId. Show the natural key or description.

---

## 6. Do / Don't

**Do**
- Use `ReferenceGroup` for all new FK fields on Smartbox forms.
- Enable `JumpRef = Yes` so users can navigate to the related record.
- Filter the lookup list when the reference table has >100 rows.

**Don't**
- Don't store surrogate RecId values in visible fields.
- Don't use `StringEdit` + `lookup()` override for new forms — use `ReferenceGroup`.
- Don't bypass the lookup with a plain text field that the dev validates manually.

---

## 7. AOT mapping (dev handoff)

| Spec field                | AOT location                                                                        | Notes                                                  |
|---------------------------|-------------------------------------------------------------------------------------|--------------------------------------------------------|
| FK relationship           | `Table > Relations > ForeignKey` (table-level relation)                             | Must exist before `ReferenceGroup` can bind            |
| ReferenceGroup control    | `Form > Design > … > ReferenceGroup`                                                | Add after FK relation exists on the table              |
| `ReferenceField`          | `ReferenceGroup.ReferenceField` = the FK field name (e.g. `CustAccount`)            | Links the control to the FK                            |
| `DataSource`              | `ReferenceGroup.DataSource` = the joined (related) data source in the form          | The related DS must be added to the form's DataSources |
| `ReplacementFieldGroup`   | `ReferenceGroup.ReplacementFieldGroup` = field group name on the related table      | Typically `AutoIdentification` or a custom field group |
| JumpRef                   | `ReferenceGroup.JumpRef = Yes`                                                      | Allows "View details" navigation                       |

---

## 8. Figma component definition

- **Name:** `Atom/Lookup`
- **Slots:** Label (text), Display value (text — the natural key or name), Lookup icon (▾)
- **Variants:**
  - `State`: Rest / Hover / Open / ReadOnly / Error / Disabled
  - `Mandatory`: Yes / No
  - `JumpRef`: Yes / No (controls whether "View details" link appears in mock annotations)
- **Usage note:** In mocks, the "Display value" slot shows the resolved reference (e.g. "Aurora Travel", not "AURTRA001"). Annotate the Figma frame with the FK field and related table for dev handoff.

---

## 9. References

- Microsoft Learn — [Lookup controls](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/lookup-controls)
- Microsoft Learn — [ReferenceGroup control](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/reference-group-control)
- Microsoft Learn — [Build a custom lookup form](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/build-lookup-form)
- [Lookup form pattern](../patterns/lookup.md) — the full-page lookup form opened for complex lookups
