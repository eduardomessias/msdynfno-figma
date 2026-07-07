# <Pattern Name> form pattern

> **Microsoft pattern reference:** [<paste learn.microsoft.com URL>]
> **D365FO `Form.Design.Style` value:** `<e.g. DetailsFormTransaction>`
> **Figma page:** `Patterns / <pattern-name>` (see [figma/library-structure.md](../../figma/library-structure.md))
> **Status:** Draft | Reviewed | Approved
> **Last reviewed against MS docs:** YYYY-MM-DD

---

## 1. Usage — when to choose this pattern

One paragraph from Microsoft's official description, plus a Smartbox note if our usage diverges.

Use this pattern when…

Do **not** use this pattern when…

### Related patterns

- [<Related pattern A>](./<file>.md) — when to pick that one instead
- [<Related pattern B>](./<file>.md) — …

---

## 2. Anatomy — the required structure

Copy Microsoft's **High-level structure** verbatim, then annotate each node with what a consultant places there.

```
Design
├── ActionPane (ActionPane)
│   └── … buttons …
├── <NextRequiredContainer>
│   └── …
└── …
```

| Node                | Required? | Purpose                                                      | Figma component         |
|---------------------|-----------|--------------------------------------------------------------|-------------------------|
| `ActionPane`        | Yes       | Top-of-form actions, grouped into tabs                       | `Pattern/ActionPane`    |
| …                   | …         | …                                                            | …                       |

### Allowed subpatterns

- [<Subpattern>](../controls/<file>.md) — used inside `<container>`

---

## 3. Core components — Best-Practice (BP) warnings to resolve

Reproduce Microsoft's BP warnings checklist so the dev knows what zero-warning means for this pattern.

- [ ] `Design.Caption` is not empty
- [ ] Form is referenced by at least one menu item
- [ ] …

---

## 4. UX guidelines

Bullet list of Microsoft's UX guidelines for this pattern (page title format, field ordering, default focus, max field count, etc.).

### Smartbox overrides

Anything we deliberately do differently — must be approved by the architect.

---

## 5. Do / Don't

**Do**

- …

**Don't**

- …

---

## 6. AOT metadata mapping (dev handoff)

What a dev should set in Visual Studio after applying this pattern.

| Spec field                  | AOT location                                                   | Notes                              |
|-----------------------------|----------------------------------------------------------------|------------------------------------|
| Pattern                     | `Form > Design > Pattern`                                      | Select `<PatternName>` in designer |
| Data source (primary)       | `Form > Data Sources > <DS>`                                   | Set `AllowEdit/Create/Delete` per BP |
| ActionPane button → action  | `MenuItem` of type Action/Display/Output bound to `Form.Button`| Privilege on the menu item         |
| …                           | …                                                              | …                                  |

---

## 7. Worked example

Link to a `mocks/<process>/<form-name>.md` that uses this pattern end-to-end.

- [Example: <link>](../../mocks/...)

---

## 8. References

- Microsoft Learn: [<exact section URL>]
- AX 2012 legacy: [<URL if applicable>]
- Internal Smartbox decisions: [link to ADR if any]
