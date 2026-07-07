# Mock — Sales orders — Experience

> First end-to-end worked example of the authoring workflow. Exercises [Details Transaction](../../catalog/patterns/details-transaction.md), ActionPane, FastTabs, Grid (List and Tabular variants), FactBox, and Drop Dialog.

| Field                  | Value                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------|
| Form name              | `SmbSalesTableExperience` (extension of `SalesTable`)                                                          |
| Caption                | "Sales orders — Experience"                                                                                    |
| Pattern                | [Details Transaction](../../catalog/patterns/details-transaction.md)                                            |
| Spec                   | [templates/form-spec-example.md](../../templates/form-spec-example.md) — this mock's spec is the template's filled example |
| Figma frame            | _<paste Figma URL when frame is published>_                                                                    |
| Process (L1)           | Order to cash                                                                                                  |
| Process (L2/L3)        | Manage sales orders / Process experience orders                                                                 |
| Consultant             | Eduardo Messias                                                                                                |
| Developer              | _TBA_                                                                                                          |
| Status                 | **Draft**                                                                                                      |
| Last updated           | 2026-05-21                                                                                                     |

---

## 1. Why this mock exists

Smartbox sells experience boxes — gift-able, redeemable experiences delivered by partner providers. The standard D365FO Sales order Form (`SalesTable`) is built around physical goods and doesn't surface:

- The **partner provider** owning each experience line.
- The **voucher** lifecycle (generated post-invoice for gift orders).
- The **activation window** for each experience.
- The **channel** (B2C / B2B / Marketplace) the order originates from.

This mock extends `SalesTable` and adds two extension tables (`SmbSalesTable_Exp`, `SmbSalesLine_Exp`) to carry the experience-specific data, plus a filtered List view and a Drop Dialog for voucher generation.

The mock is the **canonical first example** for Smartbox functional consultants — every other mock in this area should mirror its structure.

---

## 2. Spec

The full Form Spec lives at [`templates/form-spec-example.md`](../../templates/form-spec-example.md). It is double-purpose: it's the worked example for the template, **and** it's the contract for this mock. When this mock graduates beyond Draft, it gets its own dedicated spec file in this folder and the template gets a smaller fresh example.

Quick links into the spec:

- [§3 — Data sources](../../templates/form-spec-example.md#3-data-sources) (SalesTable + SmbSalesTable_Exp + SalesLine + SmbSalesLine_Exp + CustTable)
- [§4 — Fields](../../templates/form-spec-example.md#4-fields) (every visible control, with EDTs)
- [§5 — ActionPane](../../templates/form-spec-example.md#5-actionpane) (Sell / Experience / Invoice / Related / Options)
- [§7 — Navigation](../../templates/form-spec-example.md#7-navigation--how-do-users-get-here) (Main menu, workspaces, parent-form context)
- [§9 — Business rules](../../templates/form-spec-example.md#9-business-rules) (VAL-* / DEF-* / COMP-* / EFF-*)

---

## 3. Frame description — Grid view (transactions list)

Default landing when the user opens "Sales orders — Experience" from the main menu. The user sees a bulk-editable Tabular grid of orders, filtered by `SmbOrderCategory == Experience`.

```
┌──────────────────────────────────────────────────────────────────────────────────────────────────────┐
│ ≡  D365FO            Sales orders — Experience                              [search] (👤 Eduardo)    │   ← Chrome/TopBar
├──────────────────────────────────────────────────────────────────────────────────────────────────────┤
│ Sales orders — Experience                                                                            │
├──────────────────────────────────────────────────────────────────────────────────────────────────────┤
│ ┌─ Sell ──────────────────────── Experience ─── Invoice ─── Related ─── Options ──────────────────┐  │   ← Pattern/ActionPane
│ │  ┃ NEW ┃ ┃ MAINTAIN ┃ ┃ GENERATE ┃                                                              │  │     · Tabs (sentence-cased
│ │  [ New ] [ Edit ] [ Delete ]   [ Confirm ]    [ Pick ]   [ Invoice ]                            │  │       and CAPS by framework)
│ │                                                                                                 │  │     · "New / Edit / Delete"
│ └─────────────────────────────────────────────────────────────────────────────────────────────────┘  │       are foundation buttons
│                                                                                                      │
│ ┌─ Filter pane ─┐  ┌─[ 🔍 quick filter on Sales order ▾  ]  Channel:[ ▾ ]  Status:[ ▾ ]─────────┐    │   ← Subpattern/
│ │  Status        │  ┌───────────────────────────────────────────────────────────────────────┐  │    │     CustomFilterGroup
│ │   ☐ Created     │  │ □ │ Sales order  │ Customer       │ Channel │ Status    │ Ship date │   │  │    │
│ │   ☑ Confirmed   │  ├───┼──────────────┼────────────────┼─────────┼───────────┼───────────┤   │  │    │   ← Control/Grid (Tabular)
│ │   ☑ Invoiced    │  │ □ │ EXP00000123  │ Aurora Travel  │ B2B     │ Confirmed │ 12/06/26  │   │  │    │     · 6–8 columns max
│ │  Channel       │  │ □ │ EXP00000124  │ Sarah Black     │ B2C     │ Invoiced  │ 10/06/26  │   │  │    │     · First column is the
│ │   ☑ B2C         │  │ ▣ │ EXP00000125  │ Aurora Travel  │ B2B     │ Confirmed │ 14/06/26  │   │  │    │       hyperlinked default
│ │   ☑ B2B         │  │ □ │ EXP00000126  │ Carlos Ruiz    │ B2C     │ Created   │ 18/06/26  │   │  │    │       action → Header view
│ │   ☐ Mktplace    │  │ □ │ EXP00000127  │ M. Chen        │ B2C     │ Invoiced  │  8/06/26  │   │  │    │     · ID column is sticky
│ │                │  │ … │ …            │ …              │ …       │ …         │ …          │   │  │    │
│ │  [ Apply ]     │  └───────────────────────────────────────────────────────────────────────┘  │  │    │
│ └────────────────┘  Showing 142 of 142 · Selected: 1                                          │  │    │
│                                                                                                      │
└──────────────────────────────────────────────────────────────────────────────────────────────────────┘
```

**FactBoxes** (right pane, not shown above to save width):
- **Customer info** card — opens `CustTable` on More…
- **Customer order summary** card — open orders + 12-month total
- **Experience capacity** grid — capacity per partner provider for the selected line
- **Attachments** (foundation)

---

## 4. Frame description — Header view (DetailsPanel)

Reached by clicking the hyperlink on the `Sales order` column in the grid above. The framework re-flows the page: the navigation list (`SidePanel`) appears on the left and the `DetailsPanel` fills the rest.

```
┌──────────────────────────────────────────────────────────────────────────────────────────────────────┐
│ ≡  D365FO    Sales orders — Experience > EXP00000125 : Aurora Travel       [search] (👤 Eduardo)     │
├──────────────────────────────────────────────────────────────────────────────────────────────────────┤
│ EXP00000125 : Aurora Travel — Confirmed                                                              │   ← Composite/RecordTitle
│                                                                                                      │     · Title format <ID> : <Description>
│ ┌─ Sell ─── Experience ─── Invoice ─── Related ─── Options ─────────────────────────────────────┐    │   ← Pattern/ActionPane
│ │ ┃ NEW ┃ ┃ MAINTAIN ┃                                                                          │    │     (same as Grid view)
│ │ [ Confirm ]  [ Pick ]  [ Invoice ]   ❘ HEADER · LINES ❘                                       │    │     · Header / Lines toggle
│ └────────────────────────────────────────────────────────────────────────────────────────────────┘   │       under the title (native
│                                                                                                      │       tabs — proxy buttons
│ ┌── 🔍 ID                            ┐ ┌──────────────────────────────────────────────────────────┐  │       removed for a11y)
│ │  [ search… ▾ ]                       │ ▼ General                                                │  │
│ ├─────────────────────────────────────┤ │ Sales order         [EXP00000125]                       │  │   ← Subpattern/SidePanel
│ │ EXP00000123  Aurora Travel  €1,180  │ │ Customer account     [AURTRA001] (Aurora Travel)        │  │     · Style=List grid
│ │ EXP00000124  Sarah Black    €240    │ │ Customer name        Aurora Travel                      │  │     · ≤3 lines per row
│ │ EXP00000125  Aurora Travel  €2,420  │ │ Order category       Experience                         │  │     · last column = total
│ │ ▣ ─ selected                        │ │ Currency             EUR                                │  │
│ │ EXP00000126  Carlos Ruiz    €189    │ │ Requested ship       2026-06-14                         │  │
│ │ EXP00000127  M. Chen        €580    │ │ Status               Confirmed                          │  │
│ │ …                                   │ ├──────────────────────────────────────────────────────────┤  │   ← Control/FastTab
│ │                                     │ │ ▼ Experience details                                    │  │     · first FastTab expanded
│ │                                     │ │ Channel              [B2B]                              │  │       by default
│ │                                     │ │ Gifting?             ☑                                  │  │     · Summary fields are
│ │                                     │ │ Recipient name       Lina Petrescu                      │  │       NOT shown (this is
│ │                                     │ │ Recipient email      lina.p@aurora.travel               │  │       a contained FastTab)
│ │                                     │ │ Voucher delivery     2026-06-10                         │  │
│ │                                     │ ├──────────────────────────────────────────────────────────┤  │
│ │                                     │ │ ▶ Delivery address                                      │  │
│ │                                     │ ├──────────────────────────────────────────────────────────┤  │
│ │                                     │ │ ▶ Price and discount                                    │  │
│ │                                     │ ├──────────────────────────────────────────────────────────┤  │
│ │                                     │ │ ▶ Setup                                                 │  │
│ │                                     │ └──────────────────────────────────────────────────────────┘  │
│ └─────────────────────────────────────┘                                                              │
└──────────────────────────────────────────────────────────────────────────────────────────────────────┘
```

---

## 5. Frame description — Line view (LinePanel)

Reached by toggling **LINES** under the record title (or by `Ctrl+Shift+L`). The same nav list is on the left; the right shows the line summary band, the line grid, and the per-line standard tab details.

```
┌──────────────────────────────────────────────────────────────────────────────────────────────────────┐
│ EXP00000125 : Aurora Travel — Confirmed                              ❘ HEADER · LINES ❘              │
├──────────────────────────────────────────────────────────────────────────────────────────────────────┤
│ ┌── nav list  ──┐  ┌── LineViewHeader (summary band, mirror of Header view) ──────────────────────┐  │
│ │ EXP00000123   │  │  Order EXP00000125    Customer Aurora Travel    Status Confirmed    Total €2,420 │ │
│ │ EXP00000124   │  └──────────────────────────────────────────────────────────────────────────────┘  │
│ │ ▣ EXP00000125 │  ┌─[ 🔍 quick filter on Description ▾ ]──────────────────────────────────────────┐ │
│ │ EXP00000126   │  │ Line │ Experience SKU  │ Description                  │ Provider    │ Qty │ €  │ │
│ │ EXP00000127   │  ├──────┼─────────────────┼──────────────────────────────┼─────────────┼─────┼────┤ │
│ │ …             │  │  10  │ HOT-SPA-PAR-1   │ 1-night spa stay, Paris      │ Spa Group   │ 1   │ 320│ │
│ │               │  │  20  │ DIN-MICH-LDN-2  │ 2pax tasting menu, London    │ Le Gavroche │ 1   │ 380│ │
│ │               │  │ ▣ 30 │ ADV-HIKE-CHX-1  │ 1-day guided alpine hike     │ AlpinerCo   │ 4   │1280│ │
│ │               │  │  40  │ DRV-CLASSIC-IT  │ Half-day classic-car drive   │ Vintage Co  │ 1   │ 440│ │
│ │               │  │      │ + Add line                                                  │     │    │ │
│ │               │  └──────────────────────────────────────────────────────────────────────────────┘  │
│ │               │  ┌── Line details (Standard tabs, not FastTabs) ─────────────────────────────────┐ │
│ │               │  │  General · Setup · Address · ▣ Experience · Financials                       │ │
│ │               │  ├───────────────────────────────────────────────────────────────────────────────┤ │
│ │               │  │  Redemption code policy   [Per-recipient ▾]                                  │ │
│ │               │  │  Blackout date set        [EU-summer-23 ▾]                                   │ │
│ │               │  │  Multi-use allowed        ☐                                                  │ │
│ │               │  │  Issued voucher count     0  (computed)                                      │ │
│ └───────────────┘  └───────────────────────────────────────────────────────────────────────────────┘ │
└──────────────────────────────────────────────────────────────────────────────────────────────────────┘
```

---

## 6. Frame description — Drop Dialog: Generate vouchers

Reached from ActionPane → **Experience** tab → **Generate vouchers** when `Status == Invoiced AND IsGift == Yes`.

```
                         ┌────────────────────────────────────────────────────┐
                         │ Generate vouchers for EXP00000125                  │     ← Pattern/DropDialog
                         │                                                    │       · ≤7 fields
                         │ A voucher is created for each gift line. Recipients│       · No Cancel button
                         │ receive an email on the delivery date.             │       · Right-aligned OK
                         │                                                    │       · Verb-labelled default
                         │ Delivery date            [ 2026-06-10 ]            │
                         │ Send notification        ☑  (default)              │
                         │ Override template        [ Smartbox standard ▾ ]   │
                         │ Language                 [ from recipient profile ]│
                         │                                                    │
                         │                                       [ Generate ] │
                         └────────────────────────────────────────────────────┘
```

---

## 7. Reading-by-pattern (cross-reference)

Every section of this mock maps to a doc in the catalog. Use this table to navigate from a frame element back to its pattern / control doc:

| Frame element                         | Catalog reference                                                                                                                       |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------|
| Page chrome (top bar + breadcrumb)    | _(Figma chrome — `figma/library-structure.md → 🧭 7 — Page chrome`)_                                                                     |
| Page title `<ID> : <Description>`     | [Details Transaction §4 UX](../../catalog/patterns/details-transaction.md#4-ux-guidelines), `Composite/RecordTitle`                      |
| ActionPane (Sell / Experience / …)    | [ActionPane control](../../catalog/controls/action-pane.md), [Details Transaction §5](../../catalog/patterns/details-transaction.md)     |
| Header / Lines toggle                  | [Details Transaction §4 — Modern accessibility note](../../catalog/patterns/details-transaction.md#modern-accessibility-note)             |
| SidePanel + NavigationList            | [Details Transaction §2 anatomy](../../catalog/patterns/details-transaction.md#2-anatomy--the-required-structure), [Grid control](../../catalog/controls/grid.md) (`Style = List`) |
| QuickFilter above main grid           | [FilterPane control §3 QuickFilter](../../catalog/controls/filter-pane.md#3-quickfilter)                                                  |
| Custom Filter Group (Channel, Status) | [FilterPane control §7 Custom Filter Group recap](../../catalog/controls/filter-pane.md#7-custom-filter-group-subpattern-recap)            |
| MainGrid (Tabular)                    | [Grid control](../../catalog/controls/grid.md)                                                                                          |
| FastTabs (Header view)                | [FastTab control](../../catalog/controls/fast-tab.md)                                                                                   |
| Line Details Standard Tabs            | [Details Transaction §2](../../catalog/patterns/details-transaction.md#2-anatomy--the-required-structure) — `LineDetailsTab Style=Standard` |
| FactBoxes (right pane)                | [FactBox pattern](../../catalog/patterns/factbox.md)                                                                                    |
| Drop Dialog (Generate vouchers)       | [Drop Dialog pattern](../../catalog/patterns/drop-dialog.md)                                                                            |

---

## 8. Handoff status

| Stage                              | Status     | Owner    | Notes                                                                |
|------------------------------------|------------|----------|----------------------------------------------------------------------|
| Pattern selected                   | ✅ Done    | Eduardo  | Details Transaction                                                  |
| Figma frames built                 | ⬜ Todo    | Eduardo  | Frame to be created on Figma `Patterns / Details Transaction` page   |
| Spec filled                        | ✅ Done    | Eduardo  | [templates/form-spec-example.md](../../templates/form-spec-example.md) |
| Stakeholder sign-off               | ⬜ Todo    | Sales lead | Subject to Q-01 and Q-02 in spec §12                                |
| Developer assigned                 | ⬜ Todo    | _TBA_    | —                                                                    |
| AOT skeleton + pattern applied     | ⬜ Todo    | Dev      | See [dev-handoff-checklist](../../templates/dev-handoff-checklist.md) §B  |
| Data sources / fields / ActionPane | ⬜ Todo    | Dev      | §C–§E                                                                |
| Business rules (VAL/DEF/COMP/EFF)  | ⬜ Todo    | Dev      | §H                                                                   |
| BP Warnings = 0                    | ⬜ Todo    | Dev      | §K                                                                   |
| Functional verification            | ⬜ Todo    | Eduardo + Dev | §L                                                              |
| Mock marked **Verified**           | ⬜ Todo    | Eduardo  | §M                                                                   |

---

## 9. Open questions specific to this mock

- **Q-01 (spec §12)** — Should `Generate vouchers` be re-runnable after partial cancellation? **Owner:** Eduardo. **Target:** 2026-05-27.
- **Q-02 (spec §12)** — Power BI FactBox for experience-level inventory burn-down? **Owner:** BI lead. **Target:** 2026-06-03.
- **Q-03 (this mock)** — Should the navigation list also surface the **Channel** column for B2B-heavy teams? Channel is shown in the grid view but not the nav list. Pending feedback from sales clerks.
