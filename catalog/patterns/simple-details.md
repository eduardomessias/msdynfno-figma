# Simple Details form pattern

> **Microsoft pattern reference:** https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/simple-details-form-pattern
> **D365FO `Form.Design.Pattern` value:** `SimpleDetails` (Toolbar and Fields) / `SimpleDetailsFastTabs` / `SimpleDetailsStandardTabs` / `SimpleDetailsPanorama`
> **Figma page:** `Patterns / Simple Details`
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-05-21

---

## 1. Usage — when to choose this pattern

For surfaces that show **fields of a single record** with **no list/navigation** — typically read-only summaries (customer balance, account totals, statistics) but occasionally editable when synced with a parent form.

Four variants:

1. **Simple Details w/ Toolbar and Fields** (default) — fields in a single Body group, optionally inside sub-groups.
2. **Simple Details w/ FastTabs** — when fields organise naturally into expandable sections.
3. **Simple Details w/ Standard Tabs** — when fields organise into mutually-exclusive tabs.
4. **Simple Details w/ Panorama** — horizontally-scrolling panorama. Rare; use only when the data has a strong horizontal narrative.

**Important:** Simple Details forms **must not** be referenced by Main Menu items — they are dependent forms opened in context.

### Related patterns

- [Simple List and Details](./simple-list-details.md) — when there is also a navigation list
- [Dialog](./dialog.md) — when the goal is to gather input modally

---

## 2. Anatomy — the required structure

### Toolbar and Fields (default)

```
Design
├── ActionPane                              (ActionPane)
└── Body                                    (Group)            — uses a Fields subpattern
```

### FastTabs

```
Design
├── ActionPane                              (ActionPane)
├── HeaderGroup (Group)                     [Optional]
├── Body                                    (Tab, Style=FastTabs)
│   └── BodyTabPages                        (TabPage 1..N)
└── FooterGroup (Group)                     [Optional]
```

### Standard Tabs

```
Design
├── ActionPane                              (ActionPane)
├── HeaderGroup (Group)                     [Optional]
├── Body                                    (Tab, Style=Tabs)
│   └── BodyTabPages                        (TabPage 1..N)
└── FooterGroup (Group)                     [Optional]
```

### Panorama

```
Design
├── ActionPane                              (ActionPane)
├── Body                                    (Tab, Style=Panorama)
│   └── BodyTabPages                        (TabPage 1..N)
└── FooterGroup (Group)                     [Optional]
```

| Node             | Required? | Purpose                                                  | Figma component                  |
|------------------|-----------|----------------------------------------------------------|----------------------------------|
| `ActionPane`     | Yes       | Foundation actions                                       | `Pattern/ActionPane`             |
| `HeaderGroup`    | Optional  | Identifying fields at the top                            | `Subpattern/HorizontalFieldsGroup` |
| `Body`           | Yes       | Group, FastTabs Tab, Standard Tab, or Panorama Tab       | `Pattern/SimpleDetails*Body`     |
| `FooterGroup`    | Optional  | Summary totals / aggregates                              | `Pattern/Footer`                 |

### Allowed subpatterns

- [Fields and Field Groups](../controls/fields-and-field-groups.md)
- [Toolbar and Fields](../controls/toolbar-and-fields.md)
- [Tabular Fields](../controls/tabular-fields.md)
- [Toolbar and List](../controls/toolbar-and-list.md)

---

## 3. Core components — BP warnings to resolve

- [ ] `Design.Caption` is not empty
- [ ] Form is referenced by at least one menu item (e.g. as `Args.record()` source — not a Main Menu item)
- [ ] `TabPage.Caption` is not empty (when tabs are used)
- [ ] **MainMenu must not contain menu items that reference a SimpleDetails form**

---

## 4. UX guidelines

1. Default to **view mode**. If editable, sync edit mode to the parent form.
2. Use **Toolbar and Fields** unless tabs / FastTabs / panorama are clearly warranted.
3. Avoid Panorama unless the content has a genuine horizontal-narrative shape.
4. Page title format: same `<ID> : <Description>` convention as Details Master where applicable.

### Smartbox overrides

_None yet._

---

## 5. Do / Don't

**Do**
- Use Simple Details to show totals, statistics, and per-record summaries opened in context from a parent.
- Sync editability with the parent form to avoid divergent state.

**Don't**
- Don't expose this Form on the Main Menu — open it via record context only.
- Don't model standard tabs and FastTabs in the same Simple Details Form — pick one Body style.

---

## 6. AOT metadata mapping (dev handoff)

| Spec field         | AOT location                                                                                | Notes                                          |
|--------------------|---------------------------------------------------------------------------------------------|------------------------------------------------|
| Pattern            | `Form > Design > Pattern = SimpleDetails` / `…FastTabs` / `…StandardTabs` / `…Panorama`     | Apply via designer                             |
| Data source        | `Form > Data Sources > <DS>`                                                                | Often joined to the parent form's record       |
| Body               | `Form > Design > Body`                                                                      | Group / Tab style as per variant               |
| Footer (optional)  | `Form > Design > FooterGroup`                                                               | Summary fields                                 |
| Menu items         | _Display menu item attached to a button on the parent form_                                 | **Not** placed on Main Menu                    |

---

## 7. Worked example

_To come._

---

## 8. References

- Microsoft Learn — [Simple Details form pattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/simple-details-form-pattern)
- Microsoft Learn — [Toolbar and Fields subpattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/toolbar-fields-subpattern)
- Microsoft Learn — [Tabular Fields subpattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/tabular-fields-subpattern)
