# Workflow controls — control

> **Microsoft references:**
> - https://learn.microsoft.com/dynamics365/fin-ops-core/fin-ops/organization-administration/overview-workflow-system
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/workflow-toolbar
> - https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/workflow/create-workflow
>
> **AOT artefact:** Workflow system integration via `WorkflowWorkItemActionManager`, `WorkflowControllerSubmitDocument`, and the `WorkflowStatusBar` web control
> **Figma component:** `Composite/WorkflowControls`
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-06-25

---

## 1. Purpose

**Workflow controls** are the D365FO UI surface for document workflow — they allow users to **Submit**, **Approve**, **Reject**, **Recall**, **Delegate**, and view **Workflow history** directly from the form.

When a workflow is enabled for a document (e.g. Sales order, Purchase order, Journal), the framework:
1. Injects a **workflow toolbar** (a special bar beneath the ActionPane with Submit / Recall / etc. buttons).
2. Updates a **workflow status indicator** (shown in the form title area or status bar).
3. Enables per-record locking (the document becomes read-only when submitted to workflow).

> Workflow controls are **framework-managed** — the dev does not design them; they configure the workflow process in the `Workflow` AOT node and the form integrates automatically once the workflow document is registered.

---

## 2. Anatomy

```
Form (with workflow enabled)
├── ActionPane (Standard)
│   └── … normal form actions …
├── WorkflowBar (injected by framework, beneath ActionPane)
│   ├── Status chip  — "Draft" / "In review" / "Approved" / "Rejected"
│   ├── Submit button       (visible when status = Draft / Recalled)
│   ├── Approve button      (visible to current approver)
│   ├── Reject button       (visible to current approver)
│   ├── Request change      (visible to current approver)
│   ├── Delegate button     (visible to current approver)
│   ├── Recall button       (visible to originator, when status = In review)
│   └── History button      (always visible when workflow exists)
└── Form content (read-only when submitted)
```

---

## 3. Workflow states and button visibility

| Workflow status | Submit | Approve | Reject | Recall | History |
|-----------------|--------|---------|--------|--------|---------|
| Draft           | ✅ (originator) | — | — | — | ✅ |
| Submitted / In review | — | ✅ (approver) | ✅ (approver) | ✅ (originator) | ✅ |
| Approved        | — | — | — | — | ✅ |
| Rejected        | ✅ (originator, resubmit) | — | — | — | ✅ |
| Recalled        | ✅ (originator) | — | — | — | ✅ |

---

## 4. Workflow history dialog

Clicking **History** opens the `WorkflowHistory` form — a read-only dialog showing the full audit trail of workflow events (who submitted, who approved/rejected, timestamps, and comments). This form is framework-provided and cannot be customised.

---

## 5. Key configuration (not form design)

Workflow controls appear automatically when:
1. A **Workflow type** (`WorkflowType` AOT node) is created for the document table.
2. A **Workflow document** class is created (maps the table fields to workflow participants).
3. The workflow type is **registered** on the form via `form.workflowEnabled = Yes` and the `WorkflowType` property set.
4. A workflow is **activated** in the D365FO admin interface under **Organization administration → Workflows**.

> The consultant's job is to **design the approval steps and participants** in the admin UI, not to code the form. The dev's job is to register the workflow type on the form and create the document class.

---

## 6. UX guidelines

1. **Document locks when submitted** — the form becomes read-only automatically. The mock should show the read-only state when status = "In review" or "Approved".
2. **Status chip placement** — the framework places the status chip inside the WorkflowBar. Don't add a separate status field on the form body for workflow status (duplication).
3. **Workflow-driven ActionPane** — some ActionPane buttons should be disabled or hidden based on workflow status (e.g. "Post invoice" should not be available while the order is "In review"). Implement via `WorkflowWorkItemActionManager` events.
4. **Submit dialog** — clicking Submit opens a system-provided dialog for entering a submission comment. No custom form needed.
5. **Recall is not cancel** — Recall moves the document back to Draft for correction. It is not the same as cancelling the business transaction.

---

## 7. Do / Don't

**Do**
- Show the WorkflowBar in all form mocks where workflow will be enabled — even in Draft state (with only Submit + History visible).
- Show the form in read-only state in mocks where status = In review or Approved.
- Note which ActionPane buttons should be disabled per workflow status in the form spec.

**Don't**
- Don't design a custom workflow status field on the form body — the WorkflowBar chip is the authoritative status indicator.
- Don't try to show workflow buttons outside the WorkflowBar — the framework controls placement.
- Don't confuse workflow approval with D365FO confirmation (e.g. `Confirm` on a Sales order is a business process step, not workflow approval).

---

## 8. AOT mapping (dev handoff)

| Spec field                     | AOT location                                                                   | Notes                                                    |
|--------------------------------|--------------------------------------------------------------------------------|----------------------------------------------------------|
| Enable workflow on form        | `Form.Design.WorkflowEnabled = Yes` + `Form.Design.WorkflowDatasource` set     | The DS whose primary key identifies the workflow instance |
| Workflow type                  | `Form.Design.WorkflowType` = `<WorkflowType name>`                             | Created in `AOT > Workflow > WorkflowType`               |
| Document class                 | `WorkflowType.DocumentClassName` = `<class implementing WorkflowDocument>`     | Maps table fields to workflow participants               |
| Button visibility              | Override `WorkflowWorkItemActionManager.updateWorkflowControls()` if needed    | To disable ActionPane buttons per workflow status        |
| Status-driven read-only        | Call `ds.allowEdit(false)` in `WorkflowWorkItemActionManager.statusChanged()`  | Framework event fired when status changes               |

---

## 9. Figma component definition

- **Name:** `Composite/WorkflowControls`
- **Slots:** Status chip text (text), Visible buttons (boolean per button: Submit / Approve / Reject / Recall / Delegate / History)
- **Variants:**
  - `Status`: Draft / InReview / Approved / Rejected / Recalled
  - (Button visibility derives automatically from the Status variant)
- **Usage note:** In form mocks, place `Composite/WorkflowControls` immediately below the `Pattern/ActionPane` component. Set the `Status` variant to match the scenario being mocked. Mark the form body as read-only when Status = InReview or Approved.

---

## 10. References

- Microsoft Learn — [Overview of the workflow system](https://learn.microsoft.com/dynamics365/fin-ops-core/fin-ops/organization-administration/overview-workflow-system)
- Microsoft Learn — [Workflow toolbar](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/workflow-toolbar)
- Microsoft Learn — [Create a workflow](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/workflow/create-workflow)
