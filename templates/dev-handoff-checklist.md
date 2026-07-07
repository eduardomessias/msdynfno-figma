# Dev handoff checklist

The Form Spec (`<form-name>.spec.md`) is your contract. **Do not improvise**. If the spec is wrong or ambiguous, return it to the consultant with comments — don't silently fix it in code. The Figma mock is the visual reference; the spec is the metadata reference.

Work through this checklist top-to-bottom. Stop at any item that cannot be satisfied and resolve it before continuing.

---

## A. Before you start

- [ ] Spec status is **Ready for dev** (not Draft)
- [ ] Figma frame URL in the spec opens and matches the spec
- [ ] You have access to the source AOT element (Form / Table / EDT) the spec references
- [ ] Spec **§12 — Out of scope / open questions** has no blockers assigned to you
- [ ] Read the pattern's catalog page (the link in spec **§0 — Metadata → Pattern doc**) — specifically its **Anatomy**, **Core components** (BP warnings), **UX guidelines**, and **AOT metadata mapping**

---

## B. AOT — Form skeleton

- [ ] Create the Form (or extension), name matches **§0**.
- [ ] On `Form > Design`, apply the pattern from **§0** (right-click → **Apply pattern** → `<PatternName>`).
- [ ] Verify the pattern is applied — the Design node shows `Pattern: <PatternName>` and the **Pattern** tab lists the expected structure.
- [ ] Set `Design.Caption` from **§0**.
- [ ] Confirm the Form is referenced by **at least one** display menu item (from **§7 — Navigation**).

---

## C. AOT — Data sources

For each row in spec **§3 — Data sources**:

- [ ] Data source added under `Form > Data Sources` with the exact `Name`.
- [ ] `Table` matches the spec.
- [ ] `JoinSource` and `LinkType` match the spec.
- [ ] Range / query filters from the spec are applied as ranges (or in `executeQuery`).
- [ ] `AllowEdit`, `AllowCreate`, `AllowDelete` are set as in the spec **and** are consistent with the chosen pattern's BP warnings.

> List Page pattern requires `AllowEdit=No, AllowCreate=No, AllowDelete=Yes` on the primary data source. Read the pattern doc if you're not sure.

---

## D. AOT — Controls and field bindings

For each row in spec **§4 — Fields**:

- [ ] Control exists in the correct container per the pattern's **Anatomy**.
- [ ] `DataSource` + `DataField` match the spec's `Bound to`.
- [ ] **EDT** on the table field matches the spec's EDT. If it differs, **stop** — escalate to architect; do not silently extend or change EDTs.
- [ ] Control type matches the spec (e.g. `ReferenceGroup` vs `StringEdit` vs `ComboBox`).
- [ ] `Mandatory`, `AllowEdit`, and default value match the spec.
- [ ] Visible-when rules in the spec are implemented in `Control.visible(…)` or in a control method on the form's data source events — not as ad-hoc init code.
- [ ] Labels use label files (`@<LabelFile>:<LabelId>`), never hardcoded strings.

---

## E. AOT — ActionPane

For each row in spec **§5 — ActionPane**:

- [ ] Tab → Group → Button hierarchy matches the spec.
- [ ] **No app-defined New / Delete / Save / Refresh / Attachments / Export buttons** (foundation provides them).
- [ ] Each `MenuItemButton` binds to the menu item named in the spec.
- [ ] Each menu item has the privilege named in the spec attached.
- [ ] Each button's `enabled` is wired from the **Enabled-when** rule (typically via `super()` on dataSource `active()` or a dedicated `setControlState()` method).

---

## F. AOT — Navigation and entry points

For each row in spec **§7 — Navigation**:

- [ ] Display menu item created/extended; label and privilege as per spec.
- [ ] Menu item placed in the Main Menu location stated in the spec.
- [ ] If launched with record context from another Form, the args (`Args.record()` / `Args.lookupRecord()`) are unpacked in `init()` and applied as a range on the data source.
- [ ] Workspace tiles updated (cue, info-part) if the spec lists workspace entry points.

---

## G. AOT — Security

For each row in spec **§8 — Security**:

- [ ] Privilege exists; attached to the menu items it should secure (per **§5** and **§7**).
- [ ] Privilege belongs to the Duty the spec assigns it to.
- [ ] Duty(ies) assigned to the listed Role(s).
- [ ] Maintain vs View-only matches — View-only privileges grant Read on the entry points, not Update/Create/Delete.

---

## H. AOT — Business rules

For each rule in spec **§9 — Business rules**:

- [ ] Validations (VAL-*) implemented in `validateField` / `validateWrite` on the data source (preferred over form-level events).
- [ ] Defaults (DEF-*) implemented in `initValue` on the data source, or on field-level `modified()` for cascades.
- [ ] Computed fields (COMP-*) implemented via display methods or computed columns on a view — not stored fields, unless the spec explicitly says so.
- [ ] Side effects (EFF-*) implemented in the table/datasource event matching the rule's `WHEN`. Side effects that warn the user use `warning()`; those that block use `error()`.
- [ ] Every rule's user-facing string is a label, with the id quoted in the spec.

---

## I. AOT — Number sequences

If spec **§11** is present:

- [ ] Number sequence reference added to the relevant `NumberSequenceModule` extension.
- [ ] Scope, format, continuous flag, and manual-override flag match the spec.
- [ ] Number sequence consumed via `NumberSeqRecordFieldHandler` (Tables/Forms) at the right lifecycle event.

---

## J. Workflow (if §10 present)

- [ ] Workflow template registered.
- [ ] `Submit when` condition implemented; submission triggers from the spec's surface (button or auto).
- [ ] Approval step provider configured per the spec.
- [ ] Recall behavior matches the spec.

---

## K. BP Warnings

- [ ] **Run Best Practices check on the project.** Zero warnings before merge.
- [ ] Specifically verify every item under the pattern's **Core components** section in its catalog page is satisfied.

---

## L. Functional verification

- [ ] Open the Form on a dev environment and run through the **happy path** end-to-end.
- [ ] Open it with each Role listed in **§8** and confirm visible buttons and grids match the privilege level.
- [ ] Exercise every validation (VAL-*) at least once — expected error label shown.
- [ ] Exercise every side effect (EFF-*) — expected behavior observed.
- [ ] Side-by-side compare the built Form with the Figma mock — layout, fields, ordering, action grouping.

---

## M. Hand back to consultant

- [ ] Recorded a 30–60s Loom / screen capture walking the consultant through the built Form.
- [ ] Spec status updated to **Built**.
- [ ] Consultant has signed off in **§0 — Stakeholder sign-off**.
- [ ] Spec status moved to **Verified**.
- [ ] PR merged; Figma frame marked **Built** (use the Figma plugin / status tag, not a renamed component).
