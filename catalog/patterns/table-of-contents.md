# Table of Contents form pattern

> **Microsoft pattern reference:** https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/table-of-contents-form-pattern
> **D365FO `Form.Design.Style` value:** `TableOfContents`
> **D365FO `Form.Design.Pattern` value:** `TableOfContents`
> **Figma page:** `Patterns / Table of Contents`
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-05-21

---

## 1. Usage — when to choose this pattern

For **setup / parameters** surfaces with multiple loosely-related sections. The pattern renders a **vertical tab strip** down the left, each tab carrying its own root entity or section of configuration. Use this when you have two or more logically related forms that all participate in the same setup. The vertical ordering of tabs implies the recommended order of completion.

Common examples: AR/AP/GL Parameters, Inventory parameters, Project management parameters, Warehouse management parameters.

### Related patterns

- [Simple List and Details](./simple-list-details.md) — for a single entity with related details
- [Details Master](./details-master.md) — for a single complex entity using FastTabs

---

## 2. Anatomy — the required structure

```
Design
└── Tab (Style=VerticalTabs)
    └── TabPage [repeats 1..N]
        ├── Title (Group)
        │   ├── MainInstruction (StaticText)
        │   └── SecondaryInstruction (StaticText) [Optional]
        └── Body (Group) | FastTabContent (Tab)
```

| Node                | Required? | Purpose                                                  | Figma component                  |
|---------------------|-----------|----------------------------------------------------------|----------------------------------|
| `Tab (VerticalTabs)`| Yes       | Left vertical tab strip                                  | `Control/Tab (Vertical)`         |
| `Title.MainInstruction` | Yes   | One-sentence section heading                              | `Atom/StaticText`                |
| `Title.SecondaryInstruction` | Optional | Supporting sentence — full stop, sentence case      | `Atom/StaticText`                |
| `Body` / `FastTabContent` | Yes | Section content — uses a subpattern                       | `Subpattern/*`                   |

### Allowed subpatterns (per `Body`)

- [Fields and Field Groups](../controls/fields-and-field-groups.md)
- [Toolbar and List](../controls/toolbar-and-list.md)
- [Toolbar and Fields](../controls/toolbar-and-fields.md)
- [Nested Simple List and Details](../controls/nested-simple-list-details.md)
- [Tabular Fields](../controls/tabular-fields.md)
- [List Panel](../controls/list-panel.md)

The pattern guidance says the Body should primarily be one of **Simple List**, **Simple List and Details**, or **Simple Details** content (via the matching subpattern).

---

## 3. Core components — BP warnings to resolve

- [ ] `Design.Caption` is not empty
- [ ] Form is referenced by at least one menu item
- [ ] `TabPage.Caption` is not empty
- [ ] `TabPage.DataSource` is not empty
- [ ] `StaticText.Text` is not empty

---

## 4. UX guidelines

1. Tabs appear in the **order of completion** users would typically follow.
2. On open, the **first tab** is highlighted, unless the form was opened from a task context that should select a specific tab.
3. The main instruction is a concise sentence; the secondary instruction (when present) is a complete sentence in sentence case with end punctuation.
4. **Actions** appear on a toolbar **within a tab page** (not on a top-level standard ActionPane).
5. A TOC form must **not** contain:
   - Application actions on a standard ActionPane (framework actions only)
   - FactBoxes
   - Standard tabs inside a TOC tab page

### Smartbox overrides

_None yet._

---

## 5. Do / Don't

**Do**
- Group your setup pages into one TOC instead of scattering them across many menu items.
- Put global init / sync buttons on the toolbar of the most related tab page (or the first tab if nothing fits).
- Use FastTabs inside a tab page when the section itself benefits from collapsible sub-sections.

**Don't**
- Don't add a top-level ActionPane with app-specific buttons.
- Don't add FactBoxes — there is no record context that owns them.
- Don't nest standard tabs inside a TOC tab page.

---

## 6. AOT metadata mapping (dev handoff)

| Spec field          | AOT location                                                                      | Notes                                           |
|---------------------|-----------------------------------------------------------------------------------|-------------------------------------------------|
| Pattern             | `Form > Design > Pattern = TableOfContents`                                       | Apply via designer                              |
| Vertical tabs       | `Form > Design > Tab(Style=VerticalTabs) > TabPage`                               | One TabPage per setup section                   |
| Section data source | `TabPage.DataSource = <DS>`                                                       | Each section can have its own root entity       |
| Instructions        | `TabPage > Title > MainInstruction.Text` / `SecondaryInstruction.Text`            | Both via label files                            |
| Section actions     | Toolbar inside the tab page, via `Toolbar and Fields` / `Toolbar and List`        | Not on a top-level ActionPane                   |

---

## 7. Worked example

_To come — likely a Smartbox **Experience parameters** Form._

---

## 8. References

- Microsoft Learn — [Table of Contents form pattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/table-of-contents-form-pattern)
- Microsoft Learn — [Nested Simple List and Details subpattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/nested-simple-list-details-subpattern)
- Microsoft Learn — [Toolbar and Fields subpattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/toolbar-fields-subpattern)
