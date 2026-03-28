# Portal tab icons (navigation) — UABEA changes

- **Patch folder:** `nav-menu_icons`
- **Bundle:** `ui-widgets_assets_all`
- **Dump format:** `.txt`
- **Dumps in repo:** `ui-widgets_assets_all/win/`, `ui-widgets_assets_all/mac/`

Structural changes in **navigation-tab-portal-default** plus a new style rule in the shared **inlineStyle** so **SIImage** can be tinted. Both assets are in **ui-widgets_assets_all.bundle**.

**Summary:** Move the **SIImage** into the same parent as the tab text (**SIText**), set that parent to flex row, and apply an inline style to the icon for colour (background-image tint).

**Do not rely on `m_Id` or array indices** — they can differ per platform. Locate elements by **type** and **class names** (or hierarchy in your dump).

---

## Path IDs

| Bundle                | Filename                        | Path ID              | Change                                                                                               |
| --------------------- | ------------------------------- | -------------------- | ---------------------------------------------------------------------------------------------------- |
| ui-widgets_assets_all | `inlineStyle`                   | 8251223908227938625  | Portal tab inline style — add new background-image tint rule + colour entry.                       |
| ui-widgets_assets_all | `navigation-tab-portal-default` | -3526698279923353279 | Portal tab layout — move icon next to text, `row-direction-normal`, assign icon tint rule index.   |

---

## 1. `navigation-tab-portal-default` (Path ID: -3526698279923353279)

In **m_VisualElementAssets**:

| Find by type + class                                                               | Change                                                                                                                                                                                                                                                                                                                                                                        |
| ---------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **SIImage** with class `portal-image`                                              | **m_ParentId:** same as **SIText**’s parent (the **SIVisible** that contains the tab text). **m_RuleIndex:** index of the new tint rule in `inlineStyle` (below). **m_Classes:** add `margin-right-global-gap-small` (keep `portal-image`). **m_OrderInDocument:** icon before **SIText** in that parent.                                                                      |
| **SIVisible** that is the direct parent of **SIText** with class `portal-text`     | **m_Classes:** add `row-direction-normal` (flex row). Container then has both **SIImage** and **SIText** as children.                                                                                                                                                                                                                                                         |

Result: the text container **SIVisible** has two children — **SIImage** first, then **SIText** — with `row-direction-normal`; **SIImage** **m_RuleIndex** points at the new tint rule in `inlineStyle`.

---

## 2. `inlineStyle` (Path ID: 8251223908227938625)

Referenced by **navigation-tab-portal-default** (`inlineSheet`). Add one new rule and one new colour for portal icon tint.

| What to change    | Detail                                                                                                                                                                                                 |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **m_Rules**       | **Append a rule** at the next index (e.g. if you have 9 rules, add index 9). One property: `m_Name` = `-unity-background-image-tint-color`, `m_ValueType` = 4 (colour), `valueIndex` = 0.               |
| **vector colours** | **Add one ColorRGBA** (e.g. r=0.851, g=0.91, b=0.929, a=1). The new rule’s `valueIndex` 0 refers to the first entry in vector colours.                                                                  |

- **Original (typical):** 9 rules, 0 vector colour entries.
- **Patched:** 10 rules (one new at end), 1 vector colour entry.

Use the **index of that new rule** as **SIImage** **m_RuleIndex** in **navigation-tab-portal-default**. Tune **ColorRGBA** for your skin.
