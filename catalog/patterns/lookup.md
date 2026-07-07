# Lookup form pattern

> **Microsoft pattern reference:** https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/lookup-form-pattern
> **D365FO `Form.Design.Style` value:** `Lookup`
> **D365FO `Form.Design.Pattern` value:** `LookupBasic` / `LookupWithTabs` / `LookupWithPreview`
> **Figma page:** `Patterns / Lookup`
> **Status:** Seeded
> **Last reviewed against MS docs:** 2026-05-21

---

## 1. Usage — when to choose this pattern

Use a **custom Lookup form** when the framework's default auto-lookup (built from the table's `AutoLookup` field group) does not provide the right data, or when advanced visualisation (preview, multiple views, tree) is required.

Three variants:

1. **Lookup basic** (default) — a single grid (or tree / list view) with optional custom filters and lookup actions at the bottom.
2. **Lookup w/ tabs** — multiple views in one lookup. **Tab captions are hidden** and switched via an **unbound combo box** in the custom filter group.
3. **Lookup w/ preview** — a basic lookup plus a vertical splitter that reveals a preview pane of the selected record.

### Related patterns

- [Drop Dialog](./drop-dialog.md) — for quick input dialogs (not lookups)
- The framework's default auto-lookup — try this first; only fall back to a custom Lookup form when necessary

---

## 2. Anatomy — the required structure

### Lookup basic

```
Design
├── CustomFilter (Group) [Optional]
├── Grid | Tree | ListView
└── LookupActions (Group) [Optional]
```

### Lookup w/ tabs

```
Design
├── CustomFilter (Group) [Optional]      — contains the unbound view-switching ComboBox
├── LookupTab (Tab, ShowTabs=No)
│   └── LookupTabPage (TabPage, 1..N)
│       └── Grid | Tree | ListView
└── LookupActions (Group) [Optional]
```

### Lookup w/ preview

```
Design
├── CustomFilter (Group) [Optional]
├── LookupContent (Group)
│   └── Grid | Tree | ListView
├── VerticalSplitter (Group)
│   └── Preview (Group)
└── LookupActions (ActionPane)
```

| Node             | Required? | Purpose                                                  | Figma component                  |
|------------------|-----------|----------------------------------------------------------|----------------------------------|
| `CustomFilter`   | Optional  | Custom filter fields. For `w/tabs`, includes the view-switching combo | `Subpattern/CustomFilterGroup` |
| `Grid/Tree/ListView` | Yes   | The lookup body — ≤5 columns                              | `Control/Grid` / `Tree` / `ListView` |
| `Preview`        | Yes (w/preview) | Read-only preview of selected record                | `Pattern/LookupPreview`          |
| `LookupActions`  | Optional  | Bottom-of-lookup actions (e.g. "New", "Show all")        | `Pattern/LookupActions`          |

### Allowed subpatterns

- [Custom Filter Group](../controls/custom-filter-group.md)

---

## 3. Core components — BP warnings to resolve

- [ ] `EDT.FormHelp` must reference a form where `Style = Lookup` (the field's EDT points to this lookup form)

---

## 4. UX guidelines

1. **Tabs hidden, combo-box switched** (w/tabs variant):
   - `Tab.ShowTabs = No`.
   - In the form's `run()`, post-`super()`, populate the combo box with the visible tabs:
     ```xpp
     tab2ComboBoxItemMap = SysLookup::tab2ComboBox(Tab, switchView);
     ```
   - On the combo box's `modified()`, change the active tab:
     ```xpp
     Tab.tabChanged(Tab.tabValue(), tab2ComboBoxItemMap.lookup(this.selection()));
     ```
2. ≤**5 columns** in the lookup grid (the lookup auto-sizes to all columns; more becomes very wide).
3. Tree views are allowed; consider providing a flat grid alternative as well.
4. Preview pane (when present):
   - Should help users disambiguate similar records.
   - **Read-only** — never editable.
5. Custom Filter follows the [Custom Filter Group](../controls/custom-filter-group.md) subpattern.

### Smartbox overrides

_None yet._

---

## 5. Do / Don't

**Do**
- Try the auto-lookup first; only build a custom Lookup form when you really need filters, tabs, or preview.
- For tab variants, drive view switching from a combo box, not visible tab headers.
- Keep the lookup narrow (≤5 cols) — width is the user's enemy in lookups.

**Don't**
- Don't show tab headers in `w/tabs` lookups — they confuse the combo-box switching.
- Don't make the preview pane editable.
- Don't model more than a handful of custom filter fields above the lookup.

---

## 6. AOT metadata mapping (dev handoff)

| Spec field          | AOT location                                                                                  | Notes                                                  |
|---------------------|-----------------------------------------------------------------------------------------------|--------------------------------------------------------|
| Pattern             | `Form > Design > Pattern = LookupBasic` / `LookupWithTabs` / `LookupWithPreview`              | Apply via designer                                     |
| Lookup grid         | `Form > Design > Grid (or Tree / ListView)`                                                   | ≤5 columns                                              |
| Tab view switching  | `Form > Design > CustomFilter > ComboBox` + `Form.run()` and `ComboBox.modified()` overrides  | Use `SysLookup::tab2ComboBox`                          |
| Preview pane        | `Form > Design > VerticalSplitter > Preview`                                                  | Read-only                                              |
| EDT binding         | `EDT.FormHelp = <ThisLookupForm>`                                                              | Required for the lookup to be discovered by the EDT    |

---

## 7. Worked example

_To come — likely a Smartbox **Experience SKU lookup** (filtered by partner provider)._

---

## 8. References

- Microsoft Learn — [Lookup form pattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/lookup-form-pattern)
- Microsoft Learn — [Custom Filter Group subpattern](https://learn.microsoft.com/dynamics365/fin-ops-core/dev-itpro/user-interface/custom-filter-group-subpattern)
