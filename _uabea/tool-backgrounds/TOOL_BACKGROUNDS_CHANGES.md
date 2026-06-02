# Tool tile background image (UABEA)

- **Patch folder:** `tool-backgrounds`
- **Bundle:** `ui-panelids-uxml_assets_all`
- **Dump format:** serialized asset dump (`.json`)
- **Working copies:** `win/` and `mac/` (copy from each platform’s `orig/` before editing)
- **Stock only:** `win/orig/`, `mac/orig/` — do not modify; keep as the unpatched baseline.

Stock FM tool/report/site panel templates do not render a per-tool **background image** in the UXML tree. This patch inserts a bound **`SI.Bindable.SIImage`** under each template’s root **`BindingVariables`** scope so the game can drive **`game.BackgroundImage.BackgroundImage`**, while the skin styles that image via the USS class **`tool-background-image`** (see **`styles/_testing.css`** in this skin). **Cannot** be done from Skin Builder alone — the bindable node must exist in the template UXML.

## Implementation notes

- Notation **`[n]`** = **0-based index** into the named array in the JSON dump — see **[Bracket notation and the `references` object](../UABEA-Notes.md#bracket-notation-and-the-references-object)** in **`UABEA-Notes.md`**.
- **`m_direct`** on **`SIImage`** must use the **Path + Nullable** wrapper (not legacy flat **`m_path`**).
- **No `inlineStyle` edits** — templates keep their stock **`inlineSheet`** Path ID; appearance is skin USS on **`.tool-background-image`**.
- **Same logical patch** on all **11** assets below; only **`m_Id`**, **`m_ParentId`**, and the new **`rid`** differ per dump (and per OS on Mac).

---

## Assets (`ui-panelids-uxml_assets_all`)

| Filename | Path ID | Patched `SIImage` `rid` (Win, this lineage) |
| -------- | ------- | ------------------------------------------- |
| `FigmaBaseToolTemplate` | `-3950974047860622828` | `1015` |
| `FigmaBaseToolTabbedTemplate` | `-3753704939979211631` | `1017` |
| `FigmaBaseToolTabbedHeaderTemplate` | `-5643027006255053682` | `1017` |
| `FigmaBaseToolTabbedTemplate-withheader` | `5824540884813275916` | `1013` |
| `FigmaBaseToolMultiGridTemplate` | `-5382185517154910959` | `1022` |
| `FigmaBaseReportTemplate` | `-7286913424223670684` | `1016` |
| `FigmaBaseReportTabbedTemplate` | `-6853685571306610514` | `1017` |
| `FigmaBaseReportMultiGridTemplate` | `-7128261493660232504` | `1022` |
| `FigmaBaseSiteTemplate` | `-8447082276870198169` | `1013` |
| `FigmaBaseSiteMultiGridTemplate` | `8566240388769420849` | `1012` |
| `ContractNegotiationToolTemplate` | `-2260545897271500938` | `1027` |

After a Football Manager rebuild, **recompute the `rid`** as the next unused integer in **`references.RefIds`** for that export (do not assume the table above if SI added new handlers). **`uxmlAssetId`** in the new RefIds block must equal the new node’s **`m_Id`**.

---

## Regression counts (`m_VisualElementAssets`, Windows)

Each patched file gains **exactly one** visual element (+1 matching **`SIImage/UxmlSerializedData`** in **`references.RefIds`**).

| Asset | Stock VE count | Patched VE count |
| ----- | -------------- | ---------------- |
| `FigmaBaseSiteMultiGridTemplate` | `13` | `14` |
| `FigmaBaseSiteTemplate` | `14` | `15` |
| `FigmaBaseToolTabbedTemplate-withheader` | `14` | `15` |
| `FigmaBaseToolTemplate` | `16` | `17` |
| `FigmaBaseReportTabbedTemplate` | `17` | `18` |
| `FigmaBaseReportTemplate` | `17` | `18` |
| `FigmaBaseToolTabbedHeaderTemplate` | `18` | `19` |
| `FigmaBaseToolTabbedTemplate` | `18` | `19` |
| `FigmaBaseToolMultiGridTemplate` | `23` | `24` |
| `FigmaBaseReportMultiGridTemplate` | `23` | `24` |
| `ContractNegotiationToolTemplate` | `28` | `29` |

---

## What changes vs stock (`win/orig` → `win/`)

### Summary

1. **`m_VisualElementAssets`:** one new **`SI.Bindable.SIImage`** row.
2. **`references.RefIds`:** one new **`SIImage/UxmlSerializedData`** handler.
3. **Unchanged:** **`m_Usings`**, **`inlineSheet`**, template imports, and the rest of the tree (sibling order / **`m_OrderInDocument`** values on existing nodes are not renumbered).

### Anchor (find on your `orig` export)

Locate the **root** **`SI.Bindable.BindingVariables`** for the card:

- **`m_OrderInDocument`:** `2`
- **`m_ParentId`:** the **`FM.UI.BaseCardTemplateImpl`** (or site equivalent) node’s **`m_Id`**
- **`m_SerializedData.rid`:** typically **`1001`**

Insert the new **`SIImage`** **immediately after** that node in **`m_VisualElementAssets.Array`**, **before** the previous first child:

| Lineage | Next sibling (stock) |
| ------- | -------------------- |
| Tool / report / contract (most templates) | **`SI.Bindable.SIStyleSetter`** with class **`flex-grow-class`** |
| Site (`FigmaBaseSite*`) | First **`UnityEngine.UIElements.VisualElement`** child of the same **`BindingVariables`** |

### New visual element (template)

Use a fresh **`m_Id`** (negative int) unique in the file. **`m_ParentId`** = root **`BindingVariables`** **`m_Id`**. **`m_OrderInDocument`:** `3` (same as the former first child under that parent). **`m_RuleIndex`:** `-1`. **`m_Classes`:** **`["tool-background-image"]`**. **`m_SerializedData.rid`:** the new RefIds slot (see table / next free **`rid`**).

Illustrative **`FigmaBaseToolTemplate`** (Windows this lineage):

| Field | Value |
| ----- | ----- |
| `m_Id` | `-2070191861` |
| `m_ParentId` | `-1516492720` (root `BindingVariables`) |
| `m_SerializedData.rid` | `1015` |

### New `references.RefIds` entry (canonical targets)

Append one object (adjust **`rid`** and **`uxmlAssetId`** per export):

| Field | Target |
| ----- | ------ |
| `type.class` | `SIImage/UxmlSerializedData` |
| `data.name` | `ToolBackgroundImage` |
| `data.uxmlAssetId` | same as **`SIImage`** **`m_Id`** |
| `data.pickingMode` | `1` (ignore hits) |
| `data.ScaleMode` | `1` |
| `data.ImageKind` | `2` |
| `data.Binding.m_kind` | `1` |
| `data.Binding.m_direct` | see below |
| `*_UxmlAttributeFlags` | match patched **`win/FigmaBaseToolTemplate`** (`name`, `pickingMode`, `ScaleMode`, `ImageKind`, `Binding` flagged) |

**Binding (all templates):**

```json
"m_direct": {
  "Path": {
    "m_path": "game.BackgroundImage.BackgroundImage"
  },
  "Nullable": 0
}
```

**`StaticImage`**, **`BindFunction`**, and **`m_visualFunction`** stay unassigned (empty GUID / **`m_isAssigned`: 0**), same as stock bindable images elsewhere.

---

## Rebuilding from `orig` (step checklist)

For **each** asset in the table:

1. Copy **`win/orig/<filename>.json`** → **`win/<filename>.json`** (or Mac equivalent).
2. In **`m_VisualElementAssets.Array`**, find root **`BindingVariables`** (`m_OrderInDocument` `2`, `rid` `1001`).
3. Insert the **`SIImage`** element block after it (see **New visual element**).
4. Pick unused **`m_Id`** and next free **`rid`**; wire **`m_SerializedData.rid`** and **`data.uxmlAssetId`**.
5. Append **`SIImage/UxmlSerializedData`** to **`references.RefIds`** (copy shape from **`win/FigmaBaseToolTemplate`** patched export; swap ids).
6. Validate JSON parses; VE count = stock + **1**; RefIds contains exactly one new **`SIImage`** handler.
7. Re-import into **`ui-panelids-uxml_assets_all`** on the matching platform.

**Faster path after minor FM updates:** diff **`orig`** vs patched **`win/`** for one template, then replay the same insert on the new **`orig`** (ids/`rid` may need re-resolution).

---

## Skin USS (not UABEA)

The patch only exposes the class name **`tool-background-image`**. Example from this skin:

```css
.tool-background-image {
  border-radius: 0px;
}
```

Related inner-frame clears (**`.figma-base-tool-template-inner-frame`**, report/site variants, **`.tool-container`**) live in the same skin stylesheet and are independent of the UXML insert.

---

## Pitfalls

| Mistake | Symptom |
| ------- | ------- |
| Reuse an existing **`rid`** or **`m_Id`** | Import errors or wrong handler wired |
| Legacy **`m_direct`** shape | Bind fails silently or UABEA rejects import |
| Wrong parent (`BindingVariables` depth > 1) | Image behind wrong layer or no image |
| Omit **`tool-background-image`** class | Unstyled / wrong layout vs skin |
| Edit **`orig/`** | Loses stock baseline after FM rebuild |

---

## Windows vs macOS

| | Windows | macOS |
| - | ------- | ----- |
| Patched dumps | **`win/`** (committed) | **`mac/`** — apply **§ Rebuilding from `orig`** on each **`mac/orig`** export |
| Contract negotiation | Patched in **`win/`** | Export **`ContractNegotiationToolTemplate`** into **`mac/orig/`** if missing, then patch manually |

Do not paste Windows **`references`** blocks into Mac dumps unless IDs already match.

### macOS rebuild

1. Refresh **`mac/orig/*.json`** from UABEA after an FM rebuild.
2. Copy each baseline to **`mac/<filename>.json`** and apply **§ Rebuilding from `orig`** using Mac-native **`m_Id`** and the next free **`rid`** in that file.
3. Binding path, class, and serialized field targets match Windows (see **§ New `references.RefIds` entry**).

**Patched `SIImage` `rid` (Mac, this lineage)** — `m_Id` is Mac-only (see dumps); **`rid`** aligns with Windows where stock RefIds depth matches:

| Asset | Mac `rid` |
| ----- | --------- |
| `FigmaBaseSiteMultiGridTemplate` | `1012` |
| `FigmaBaseSiteTemplate`, `FigmaBaseToolTabbedTemplate-withheader` | `1013` |
| `FigmaBaseToolTemplate` | `1015` |
| `FigmaBaseReportTemplate`, `FigmaBaseReportTabbedTemplate` | `1016` |
| `FigmaBaseToolTabbedTemplate`, `FigmaBaseToolTabbedHeaderTemplate` | `1017` |
| `FigmaBaseToolMultiGridTemplate`, `FigmaBaseReportMultiGridTemplate` | `1022` |

---

## Validation

- Open assorted tools/reports/sites/contract negotiation: custom tool background art visible where the game supplies **`BackgroundImage`**.
- Image does not steal clicks (**`pickingMode` ignore**).
- JSON: +1 VE, +1 **`SIImage`** RefIds entry; **`game.BackgroundImage.BackgroundImage`** bind present on every listed template.
