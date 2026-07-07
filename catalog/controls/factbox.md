# FactBox panel — host-form control

> **Microsoft references:**
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/factbox-form-patterns
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/general-form-guidelines (FactBox panel section)
>
> **AOT artefact:** `SectionFactBoxes` group containing `Part` (FormPart) controls in the host form's Design tree
> **Figma component:** Right panel of the host-form frame, composed with `Pattern/FactBox` instances (Card or Grid variant)
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-07-01

> **Related doc:** [`catalog/patterns/factbox.md`](../patterns/factbox.md) covers how to *build* the standalone FactBox form. This doc covers how to *host* FactBoxes in another form.

---

## 1. Purpose

The **FactBox panel** is the collapsible right-side strip on a host form (Details Master, Details Transaction, List Page, Simple List and Details). It surfaces related information about the currently selected record — totals, balances, related contacts, recent activity — without requiring the user to navigate away.

Each individual FactBox in the panel is a reference (`Part`) to a standalone FactBox form. The host form owns the panel; the referenced form provides the content.

---

## 2. Anatomy (host-form side)

```
Design
└── SectionFactBoxes                    ← the FactBox panel container
    ├── Part (FormPart, 1..N)           ← reference to a FactBox form
    │   └── FormName = <FactBoxForm>
    └── Part (FormPart, system-added)   ← e.g. Attachments, ActivityWall
```

The visible panel at runtime:

```
FactBox panel (right side, collapsible as a whole)
├── FactBox 1 ▾ (expanded by default)
│   └── Card content: read-only fields
├── FactBox 2 ▸ (collapsed)
│   └── Grid content: child rows
└── FactBox 3 ▸ (collapsed)
    └── Card content: system FactBox
```

---

## 3. Key properties

| Property / Location                     | Notes                                                                                         |
|-----------------------------------------|-----------------------------------------------------------------------------------------------|
| `SectionFactBoxes` container            | Added by the pattern — do not manually add it to forms that don't call for a FactBox panel   |
| `Part.FormName`                         | The name of the standalone FactBox form (`Form.Design.Style = FormPart`)                      |
| `Part.DataSource`                       | Links the FactBox to a data source on the host form so it refreshes when the selected record changes |
| `Part.Caption`                          | Override caption (optional) — the FactBox form's `Design.Caption` is used by default         |
| Display order                           | Drag `Part` nodes in the designer to control panel order. Critical FactBoxes go first.        |
| `Part.Visible`                          | Can be toggled by X++ at runtime based on configuration or security; defaults to `Yes`        |

---

## 4. System-provided FactBoxes

The D365FO framework automatically injects these into the `SectionFactBoxes` container on appropriate forms. **Do not recreate them manually:**

| System FactBox              | Trigger                                | Notes                                                           |
|-----------------------------|----------------------------------------|-----------------------------------------------------------------|
| **Attachments**             | `Form.Design.Style ≠ FormPart`        | Always present; shows document count badge in the header        |
| **Activity wall** (CRM)     | CRM module enabled                     | Recent activity for the current record                          |
| **Workflow history**        | Workflow enabled on the form           | Shows workflow step history                                      |

---

## 5. UX guidelines

### Panel presence

1. **Include a FactBox panel when users regularly need related info while working on a record.** The canonical hosts are Details Master and Details Transaction. Don't add a panel to Simple Details, Dialog, Drop Dialog, or Operational Workspace.
2. **Maximum 4–5 FactBoxes in the panel.** More than five overwhelms the panel; consolidate or remove rather than hiding everything collapsed.
3. **Expand the most-used FactBox by default.** All others default to collapsed.

### FactBox selection and order

4. **Lead with the most actionable FactBox** — typically totals, balances, or a count that affects what the user does next.
5. **The Attachments FactBox** is always last (it's framework-injected anyway and rarely the primary concern).
6. **Don't duplicate information already visible in the form body.** The FactBox exists to surface *related-record* info, not to re-show fields from the current record.

### Content rules (Card variant)

7. **2–10 fields per Card FactBox** (from the FactBox pattern guidelines).
8. **Don't echo the host record's ID or Name** — the user already sees that in the title area.
9. **Label every field** — no unlabelled values in a FactBox.
10. **Currency indicator fields go last** within the FactBox.

### Content rules (Grid variant)

11. **1–4 columns** in the FactBox grid — prioritise the ID/name column and one value column.
12. **Row count is naturally limited** — the panel shows ~5–8 rows by default; a `(More…)` link opens the full form.

### Linking to the backing form

13. **Always add a `(More…)` link** when a full form for the related entity exists. The user should be able to navigate there without searching.
14. Name the link after the action or destination: `"Customer transactions"`, `"All attachments"` — not a generic "More".

---

## 6. Do / Don't

**Do**
- Include a FactBox panel on all Details Master and Details Transaction forms.
- Lead with the most actionable FactBox (totals, open counts, status of a key related record).
- Add a `(More…)` link to the backing form whenever one exists.
- Keep FactBoxes focused on a single related entity or concept.

**Don't**
- Don't add editable fields to any FactBox content (the FactBox form defines content as read-only).
- Don't exceed 4–5 FactBoxes in the panel — prefer depth over breadth.
- Don't recreate the Attachments FactBox — the framework provides it.
- Don't add a FactBox panel to Simple Details, Dialog, Drop Dialog, Lookup, or Operational Workspace forms.
- Don't show internal RecIds or surrogate keys in FactBox content.

---

## 7. AOT mapping (dev handoff)

| Spec field                  | AOT location                                                                              | Notes                                                              |
|-----------------------------|-------------------------------------------------------------------------------------------|--------------------------------------------------------------------|
| FactBox panel container     | `<HostForm> > Design > SectionFactBoxes`                                                  | Added automatically by the pattern — do not duplicate             |
| Individual FactBox          | `<HostForm> > Design > SectionFactBoxes > Part > FormName = <FactBoxForm>`               | One `Part` per FactBox; order in the designer = display order      |
| Data binding                | `Part.DataSource = <DS on host form>`                                                    | Required so the FactBox refreshes when the record changes          |
| Expand first FactBox        | First `Part` in the container — framework expands it by default                          | No explicit property needed; order determines default-expand       |
| System FactBoxes            | Framework-injected; visible in the designer as `DocuRef`, `ActivityWall`, etc.           | Do not manually add; do not remove unless intentionally suppressed |
| Conditional visibility      | `Part.Visible = No` + X++ `<Part>.visible(condition)` in `init()` or datasource methods | Use for FactBoxes only relevant in specific configurations         |

---

## 8. Figma component definition

- **Panel name in mock:** Right panel of the host-form Figma frame, labelled `FactBox panel`
- **Compose with:** `Pattern/FactBox` instances from the Figma library (Card or Grid variant per entry)
- **Panel width:** Fixed at 200–240 px in the Figma frame; collapsible in the live product
- **Slots per FactBox instance:**
  - `Caption` (text — the FactBox title, e.g. "Customer statistics")
  - `Content` (Card: read-only field pairs; Grid: compact row table)
  - `More link` (optional hyperlink — "Customer transactions →")
- **State to show in mocks:** Default expanded for the first FactBox; collapsed (header-only) for the rest
- **Usage note:** Mock realistic field values. Use `(More…)` links to indicate navigation destinations even if those forms are not yet mocked.

---

## 9. References

- Microsoft Learn — [FactBox form patterns](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/factbox-form-patterns) — how to build the standalone FactBox form
- Microsoft Learn — [General form guidelines — FactBox panel](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/general-form-guidelines)
- [`catalog/patterns/factbox.md`](../patterns/factbox.md) — the FactBox form pattern (how to build the backing form)
- [`catalog/controls/fast-tab.md`](./fast-tab.md) — FastTabs are the primary field-grouping mechanism on the same forms that host FactBox panels
- [`catalog/controls/record-title.md`](./record-title.md) — the title area above which the FactBox panel sits
