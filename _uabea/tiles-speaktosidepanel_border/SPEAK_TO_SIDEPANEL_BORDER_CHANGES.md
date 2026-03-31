# Speak to side panel — border colour (UABEA)

- **Patch folder:** `tiles-speaktosidepanel_border`
- **Bundle:** `ui-panelids-uxml_assets_all`
- **Dump format:** serialized asset dump (`.json`)
- **Working copy:** `ui-panelids-uxml_assets_all/win/` (copy from `win/orig` or `mac/orig` before editing)
- **Stock only:** `win/orig/`, `mac/orig/` — do not modify; keep as the unpatched baseline.

## Asset

| Filename | Path ID | Role |
| -------- | ------- | ---- |
| `inlineStyle-CAB-2e85ca449b6b5bf1e21dfd7485c9c8ba-4528579247820316033.json` | `4528579247820316033` | Border tint |

## Target colour

Edit **`colors.Array[0]`** to:

- `r` **0.41568628**
- `g` **0.42352942**
- `b` **0.44313726**
- `a` **0**

On the stock `orig` dump, RGB already matches; **`a`** is typically **`1.0`** and must be set to **`0`** so the border is fully transparent while the tint RGB stays the same.
