# Figma library structure

Spec for the **Smartbox D365FO** Figma library. This document defines the file, page, component, and variant structure so the library is predictable for consultants and traceable to the [catalog](../catalog/README.md) for developers.

> One catalog doc ↔ one Figma component (or page template). If you can't trace a Figma node back to a catalog page, the node should not be in the library.

---

## 1. Files

The library lives in **one** team library file, with **two** consumer files for different purposes.

| File                                  | Role                                                          | Who edits                  |
|---------------------------------------|---------------------------------------------------------------|----------------------------|
| **Smartbox D365FO — Library**         | Source of truth. Tokens, primitives, controls, patterns.      | Architects + designers only |
| **Smartbox D365FO — Sandbox**         | Drafting playground. Designers test new components here before promoting them into the Library file. | Designers |
| **Smartbox D365FO — <Project> Mocks** | Consultant working files. One file per project / programme.   | Functional consultants     |

**Versioning:** the Library uses Figma's branching for non-trivial changes. The `main` branch is the published library. Branches are named `lib/<short-description>` (e.g. `lib/add-factbox-grid-variant`).

---

## 2. Pages — inside `Smartbox D365FO — Library`

In order, top-to-bottom in the Figma side panel:

| Page                            | What it contains                                                                                                  |
|---------------------------------|-------------------------------------------------------------------------------------------------------------------|
| `📘 0 — Read me`                 | Library overview, version, change log, links back to `/catalog/README.md` and `/workflow/00-authoring-guide.md`. |
| `🎨 1 — Foundations`             | Color styles, type styles, spacing, radii, elevations, motion. Mirrors the three token files.                    |
| `🔣 2 — Icons`                   | Fluent 2 icon set used by D365FO. Each icon as a component, named to match Microsoft's Fluent icon names.        |
| `⚛️ 3 — Primitives`              | Atomic controls: `Button`, `Input/*`, `Combo`, `Lookup`, `Checkbox`, `Radio`, `Toggle`, `Tab`, `Badge`, …          |
| `🧱 4 — Composite controls`      | One page per composite: ActionPane, FastTab, FilterPane, Grid, FactBox, RecordTitle, QuickFilter, EntityStatus.   |
| `🧩 5 — Subpatterns`             | Custom Filter Group, Toolbar and Fields, Toolbar and List, Fields and Field Groups, List Panel, Section *, …      |
| `📐 6 — Patterns / List Page`    | Page template + slot guides + ✅/❌ examples for the **List Page** pattern.                                       |
| `📐 6 — Patterns / Details Master` | Same shape, for **Details Master**.                                                                              |
| `📐 6 — Patterns / Details Transaction` | Same shape, for **Details Transaction**. With both Header view and Line view as separate template frames.    |
| `📐 6 — Patterns / Simple Details` | …                                                                                                                |
| `📐 6 — Patterns / Simple List`  | …                                                                                                                |
| `📐 6 — Patterns / Simple List and Details` | …                                                                                                       |
| `📐 6 — Patterns / Table of Contents` | …                                                                                                          |
| `📐 6 — Patterns / Operational Workspace` | …                                                                                                       |
| `📐 6 — Patterns / Dialog`       | Six Dialog variants on one page.                                                                                 |
| `📐 6 — Patterns / Drop Dialog`  | Two Drop Dialog variants.                                                                                        |
| `📐 6 — Patterns / Lookup`       | Three Lookup variants (basic / w/tabs / w/preview).                                                              |
| `📐 6 — Patterns / FactBox`      | FactBox card + FactBox grid variants.                                                                            |
| `🧭 7 — Page chrome`             | Navigation pane, top bar, breadcrumb, command bar, status bar — the frame that wraps any pattern.                |
| `🧪 8 — Examples`                | Linked thumbnails of the worked mocks under `/mocks/`. Read-only references — do not edit components here.        |
| `📝 9 — Patterns playground`     | Scratch frames inside the library for in-progress component work that hasn't graduated to its own page yet.       |

> One page per pattern keeps each pattern's published library entry self-contained — consultants can copy a single page template without dragging unrelated assets.

---

## 3. Component naming

The rule: **`<Layer> / <Family> / <Name>`**, with optional sub-name segments separated by `/`. Layers move from atomic to composite, matching the page on which the component lives.

| Layer        | Prefix      | Examples                                                                              |
|--------------|-------------|---------------------------------------------------------------------------------------|
| Token        | `Token/`    | `Token/Color/Neutral/Grey/10`, `Token/Type/Body1`, `Token/Space/M`                     |
| Icon         | `Icon/`     | `Icon/Add`, `Icon/Edit`, `Icon/Delete`, `Icon/More`                                    |
| Primitive    | `Atom/`     | `Atom/Button`, `Atom/Input/String`, `Atom/Combo`, `Atom/Lookup`, `Atom/Checkbox`       |
| Composite    | `Control/`  | `Control/Grid`, `Control/ActionPane`, `Control/FastTab`, `Control/FilterPane`          |
| Subpattern   | `Subpattern/` | `Subpattern/CustomFilterGroup`, `Subpattern/ToolbarAndList`, `Subpattern/FieldsAndFieldGroups` |
| Pattern      | `Pattern/`  | `Pattern/ListPage`, `Pattern/DetailsTransaction/HeaderView`, `Pattern/DetailsTransaction/LineView` |
| Page chrome  | `Chrome/`   | `Chrome/NavigationPane`, `Chrome/TopBar`, `Chrome/Breadcrumb`                          |

**Component names must match the AOT control names from D365FO** (or the closest readable equivalent). This is how `templates/dev-handoff-checklist.md` lets the developer pattern-match Figma layers back to AOT element names.

> ❌ Don't rename layers to your own preferred labels. Use the Text override on the inner label, not a rename of the component itself. A renamed component breaks the dev's mapping table.

---

## 4. Variants

Every component declares variants over a stable set of axes. Use only the axes the component actually needs.

| Variant axis  | Values                                                          | Use it when                                                                                |
|---------------|-----------------------------------------------------------------|--------------------------------------------------------------------------------------------|
| `State`       | `Rest`, `Hover`, `Pressed`, `Focus`, `Disabled`, `ReadOnly`     | Any interactive control. Foundation auto-renders Rest unless overridden.                  |
| `Mode`        | `Light`, `Dark`, `HighContrast`                                 | Anything that paints background or text. The library is Light-default.                    |
| `Density`     | `Compact`, `Comfortable`                                        | Data-dense controls (Grid rows, FastTab fields). D365FO defaults to Compact.              |
| `Size`        | `S`, `M`, `L`                                                   | Buttons, inputs, dialogs.                                                                  |
| `Variant`     | `Primary`, `Secondary`, `Subtle`, `Transparent`, `Outline`      | Button-family components.                                                                  |
| `Validation`  | `None`, `Warning`, `Error`, `Success`                           | Input controls.                                                                            |
| `Required`    | `True`, `False`                                                 | Input controls (drives the asterisk).                                                       |
| `Direction`   | `LTR`, `RTL`                                                    | Only on chrome / pattern templates that flip whole layouts.                                 |
| `Content`     | `Filled`, `Empty`, `Loading`, `Error`                           | Grid, FactBox, FilterPane.                                                                  |

**Order matters.** Declare axes in the order above — Figma's variant picker shows them in declaration order; a stable order keeps muscle memory.

---

## 5. Slots and overrides

Use **Figma component slots** (instance swap / boolean / text / number) generously. Consultants should never need to ungroup a primitive to "make it fit". If a pattern template needs to be filled, expose slots for it.

Slot conventions:

- A slot that accepts a child component is an **instance swap** property named `<slot purpose>` (e.g. `Slot — primary action`).
- A slot that toggles visibility of a child is a **boolean** property named `Show <thing>` (e.g. `Show currency`).
- Text inside a primitive is a **text** property named `Label`, `Helper`, `Placeholder`, or `Value`.
- Numerical fills are **number** properties with units stripped from the name (e.g. `Max width`).

> **Locked layers:** lock all visual decoration layers inside components (borders, inner backgrounds). Consultants edit content, not chrome.

---

## 6. Connection to the catalog

Every component / page template must carry, in its **Description** (right panel in Figma), this footer:

```
Catalog: catalog/<path>.md
AOT: <Form.Design.Pattern or Control name in D365FO>
Last verified: YYYY-MM-DD
```

Example, for the Details Transaction Header view template:

```
Catalog: catalog/patterns/details-transaction.md
AOT: Form.Design.Pattern = DetailsTransaction (HeaderPanel branch)
Last verified: 2026-05-20
```

The mapping in both directions is also enumerated in [`component-mapping.md`](./component-mapping.md), which is generated as patterns are filled in.

---

## 7. Tokens → Figma variables

The library's tokens live in `figma/tokens/*.tokens.json` (W3C DTCG format). They are imported into Figma as **variables and styles** via [Tokens Studio for Figma](https://tokens.studio/) or any DTCG-compatible plugin.

Mapping into Figma:

| Token file             | Imported as                          | Where it's used                                       |
|------------------------|--------------------------------------|-------------------------------------------------------|
| `core.tokens.json`     | Figma variables, mode = "Light"      | Foundation page — global palette, type, spacing, radii |
| `semantic.tokens.json` | Figma variables, refs to core        | Color styles + numeric variables                       |
| `component.tokens.json`| Figma variables, refs to semantic    | Bound to component fills, strokes, paddings            |

Modes:

- `Light` (default), `Dark`, and `HighContrast` modes are declared on the **semantic** token collection. Components reference semantic, not core, so swapping modes flips the whole library at once.
- Density modes (`Compact`, `Comfortable`) live on **numeric** semantic tokens (spacing, control height).

Publish discipline: never bind a component fill to a `core/*` token directly. Always bind to a `semantic/*` token. Architects police this in PR review.

---

## 8. Publish rules

Before publishing a change to the library:

- [ ] Every changed component carries the updated **Description** footer (Catalog / AOT / Last verified).
- [ ] Variant axes are intact (no removed values used downstream — check usage list in Figma).
- [ ] Catalog markdown updated in the same PR. If it can't be, mark the library version as **Draft** and don't publish to consumers.
- [ ] Run the **A11y contrast** plugin on all new combinations of color tokens — text must meet WCAG 2.2 AA on its semantic surface.
- [ ] Add a row to the change log on page `📘 0 — Read me`.

Versioning: semver on the library file description (`v0.1.0`, `v0.2.0`, …). Breaking renames or variant removals bump minor pre-1.0 / major post-1.0.

---

## 9. What goes outside the library

These are deliberately **not** in the library and live in consumer files only:

- Smartbox customer logos, real customer/vendor names, real product images
- Workshop output sketches and IA diagrams
- Project-specific Form mocks (those live in the `Mocks` file per project, with components linked back to the Library)

Keeping the Library free of project content is what lets the library scale — every mock file can publish back without polluting the source of truth.
