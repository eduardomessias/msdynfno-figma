# Authoring guide — from idea to D365FO Form

This guide walks a functional consultant through the four steps of producing a Form that's ready for a developer to build. It's the spine of the library; every other document either explains a step or supplies an asset used in one.

```
┌────────────────────┐    ┌──────────────────┐    ┌────────────────────┐    ┌─────────────────────┐
│ 1. Pick a pattern  │ →  │ 2. Mock in Figma │ →  │ 3. Fill the spec   │ →  │ 4. Hand off to dev  │
│  catalog/patterns/ │    │  Smartbox D365FO │    │ templates/form-spec│    │  dev-handoff        │
│                    │    │  Figma library   │    │ + Figma frame URL  │    │  checklist          │
└────────────────────┘    └──────────────────┘    └────────────────────┘    └─────────────────────┘
```

Each step has a checklist. Don't skip a step — the dev handoff only works if all four are done.

---

## Step 1 — Pick a pattern

D365FO has a closed set of Form patterns defined by Microsoft. You pick **one** for your Form, and that choice determines its structure, default behaviors, and the controls available to you. **The pattern is the most important decision you make.**

**Decision questions** (answer in order — the first "yes" wins):

| # | Question                                                                                          | Pattern                            |
|---|---------------------------------------------------------------------------------------------------|------------------------------------|
| 1 | Is this a modal that gathers a small set of inputs to perform an action?                          | **Drop Dialog** or **Dialog**      |
| 2 | Is this a master-record list with 1:1 details? (e.g. Customers, Vendors)                          | **Details Master** (merged list)   |
| 3 | Is this a transactional record with a Header and Lines? (e.g. Sales order, Purchase order)        | **Details Transaction**            |
| 4 | Is this a setup/parameters page with loosely-related sections?                                    | **Table of Contents**              |
| 5 | Is this a list with 6 or fewer fields and no parent/child? (e.g. Customer groups)                 | **Simple List**                    |
| 6 | Is this an entity of medium complexity (6+ fields, 0–5 child collections)?                        | **Simple List and Details**        |
| 7 | Is this a single-record edit form (no list)?                                                      | **Simple Details**                 |
| 8 | Is this an activity overview / primary navigation hub for a role?                                 | **Operational Workspace**          |
| 9 | Is this a custom lookup the framework auto-lookup can't satisfy?                                  | **Lookup**                         |

> **List Page on its own is now discouraged** when there is a 1:1 list↔details mapping. Microsoft has merged List Page into Details Master / Details Transaction. Only use a standalone List Page when there is no details form, or multiple different details forms back the same list (e.g. project quotations + sales quotations).

Open the chosen pattern's catalog page (e.g. [`catalog/patterns/details-transaction.md`](../catalog/patterns/details-transaction.md)) and read its **Usage**, **Anatomy**, and **Do / Don't** sections before mocking.

**Checklist — Step 1**
- [ ] Pattern selected from the catalog
- [ ] Pattern's `do` list reviewed
- [ ] Pattern's `don't` list reviewed
- [ ] Pattern URL noted (you'll paste it into the spec)

---

## Step 2 — Mock in Figma

Open the **Smartbox D365FO** Figma library. Its structure is documented in [`figma/library-structure.md`](../figma/library-structure.md). The library page corresponding to your chosen pattern contains a ready-to-use **page template** plus all of the controls allowed inside that pattern.

**How to mock:**

1. Duplicate the pattern's page template into your working Figma file.
2. Fill the **ActionPane** with the buttons the user actually needs (New, Delete, Workflow, Print, Send, etc.). Stick to the four ActionPane tabs at most.
3. Fill the **FastTabs** / **Grid** / **FilterPane** with the fields the business asked for. Use Figma component variants — don't draw rectangles.
4. Add **FactBoxes** on the right for context (related counts, related records, attachments).
5. Keep every control aligned to the library's 4px / 8px grid. Don't free-form sizes.

**Anti-patterns to avoid** (these will break the dev handoff):
- ❌ Detaching components — devs can't trace a detached frame to a control.
- ❌ Renaming layers to your own labels — keep the library's component names; put your label in the Text override.
- ❌ Mixing Fluent 1 / Office UI Fabric icons. Use Fluent 2.
- ❌ Inventing a new pattern shape (e.g. "details + 2 grids side by side"). If the catalog doesn't have it, talk to the architect first.

**Checklist — Step 2**
- [ ] Pattern page template duplicated
- [ ] All controls placed from the library (no detached frames)
- [ ] Layer names match library component names
- [ ] No invented patterns
- [ ] Frame published / shared link available

---

## Step 3 — Fill the spec

Copy [`templates/form-spec.md`](../templates/form-spec.md) into your mock directory and fill it out. The spec is the contract between you and the developer.

The spec captures what the mock cannot:

- **Pattern** (with link to catalog page)
- **Data sources** — root table, joined tables, query
- **Fields** — for every visible field: label, source table.field, EDT (Extended Data Type), required?, allowEdit?
- **ActionPane buttons** — for each: caption, target menu item, security privilege, enabled-when rule
- **Navigation entry points** — which menu items open this Form, from which workspaces / nav lists
- **Security** — privileges and duties touched
- **Business rules** — field validations, default values, computed fields
- **Workflow** — if applicable, which workflow template

See [`templates/form-spec-example.md`](../templates/form-spec-example.md) for a fully filled example.

**Checklist — Step 3**
- [ ] Every visible field has a table.field + EDT
- [ ] Every ActionPane button has a menu item name + security privilege
- [ ] Data source query is unambiguous (joins, ranges)
- [ ] Navigation entry points listed
- [ ] Figma frame URL pasted at the top

---

## Step 4 — Hand off to dev

Share the spec + Figma link with the dev. They will open [`templates/dev-handoff-checklist.md`](../templates/dev-handoff-checklist.md) and translate the spec into D365FO metadata:

1. Create or extend the **Form** AOT element; apply the pattern selected in Step 1.
2. Add **data sources** per the spec; set `AllowEdit` / `AllowCreate` / `AllowDelete` per the pattern's BP Warnings.
3. Lay out controls under the pattern's required containers (ActionPane → Custom Filter → Grid; or ActionPane → SidePanel → PanelTab → … depending on pattern).
4. Bind each control to the field listed in the spec; verify EDTs match.
5. Add menu items and security privileges.
6. Verify against the pattern's **Core components** Best Practice warnings — zero warnings before merge.

If the dev finds a mismatch (e.g. spec says EDT `CustAccount` but the field is actually `CustGroupId`), they return the spec with comments — they do not silently change the Form. The spec is the contract.

**Checklist — Step 4 (dev)**
- [ ] Pattern applied in Form designer
- [ ] All BP Warnings resolved
- [ ] Menu items + security in place
- [ ] Mock and built Form compared side-by-side
- [ ] Functional consultant signed off
