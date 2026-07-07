# Smartbox D365FO Figma Visual Library

A visual library and design system for Smartbox's **Dynamics 365 Finance & Supply Chain Management (D365FO)** implementation, part of the company-level **ERP Transformation** programme.

## Why this exists

Two audiences, one source of truth:

- **Functional consultants** need a no-code way to mock new Forms quickly, in a way that looks like the real ERP — so business stakeholders can validate UX before a single line of X++ is written.
- **Developers** need an unambiguous spec that maps directly to D365FO metadata (Form pattern, data sources, controls, EDTs, menu items) — so they can implement what was mocked without guesswork.

The visual baseline is **Microsoft Fluent 2 (Web)** and every Form pattern is grounded in Microsoft's official D365FO pattern documentation. We do not invent custom Form shapes; we curate Microsoft's catalog for Smartbox.

## Repo layout

```
msdynfno-figma/
├── README.md                    # You are here
├── workflow/                    # The consultant authoring journey
│   └── 00-authoring-guide.md    # Start here
├── catalog/                     # Markdown pattern catalog — source of truth
│   ├── README.md                # Index of patterns and controls
│   ├── _template-pattern.md     # Template for new pattern docs
│   ├── patterns/                # Form-level patterns (ListPage, DetailsTransaction, …)
│   └── controls/                # Primitive & composite controls (ActionPane, FastTab, …)
├── figma/                       # Figma file structure spec + design tokens
│   ├── library-structure.md
│   ├── component-mapping.md
│   └── tokens/                  # W3C DTCG / Figma Variables JSON
├── templates/                   # Fillable spec + dev handoff templates
│   ├── form-spec.md
│   ├── form-spec-example.md
│   └── dev-handoff-checklist.md
└── mocks/                       # Worked examples per business process area
```

## How to use this repo

- **Functional consultant?** → Read [`workflow/00-authoring-guide.md`](workflow/00-authoring-guide.md), then pick a pattern from [`catalog/README.md`](catalog/README.md).
- **Developer?** → Open the Form Spec next to the Figma mock and follow [`templates/dev-handoff-checklist.md`](templates/dev-handoff-checklist.md).
- **Maintainer (architect / lead)?** → New patterns are added by copying [`catalog/_template-pattern.md`](catalog/_template-pattern.md) and registering them in [`catalog/README.md`](catalog/README.md) and [`figma/component-mapping.md`](figma/component-mapping.md).

## Authoritative sources

- [Microsoft Learn — Form styles and patterns](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/form-styles-patterns)
- [Microsoft Learn — Selecting a form pattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/select-form-pattern)
- [Microsoft Learn — General Form Guidelines](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/general-form-guidelines)
- [Fluent 2 Design System (Web)](https://fluent2.microsoft.design/)
- Local: `C:\Users\Eduardo.Messias\source\repos\dynamics365patternspractices` (Microsoft patterns & practices repo, business process catalog)

## Status

Bootstrapping. First batch covers the authoring workflow, the catalog template, and two seed patterns (List Page, Details Transaction). The remaining patterns, controls, tokens, and mocks will be filled in iteratively.
