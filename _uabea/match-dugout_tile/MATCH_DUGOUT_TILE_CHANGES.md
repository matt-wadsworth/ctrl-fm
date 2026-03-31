# Dugout 4×8 tile / popups — UABEA changes

- **Patch folder:** `match-dugout_tile`
- **Bundle:** `ui-tiles_assets_all`
- **Dump format:** serialized asset dump (`.json`)
- **Working copies:** `ui-tiles_assets_all/win/` (copy from `win/orig` or `mac/orig` before editing)
- **Stock only:** `win/orig/`, `mac/orig/` — do not modify; keep as the unpatched baseline.

Gives in-match dugout popups a background; tiles are otherwise transparent. **Cannot** be done from Skin Builder alone.

## Implementation notes

- Copy both assets from **`orig`** into **`win/`** or **`mac/`**, then apply this document. If a future title update changes rule counts or pool indices, re-export the stock bundle, diff against **`orig`**, and adjust the steps below.
- After editing, **`m_Rules.Array` length** is **7** (was **6**): append the new rule at index **6**.
- **`border-radius`** and **`border-width`** usually serialize as **four** values (corners / sides). Point all four at the same **`dimensions`** index (**1** for radius, **2** for width) so the shorthand matches a uniform **12px** radius and **1px** border.

---

## Path IDs

| Bundle                | Filename                   | Path ID              | Change                                                                 |
| --------------------- | -------------------------- | -------------------- | ---------------------------------------------------------------------- |
| ui-tiles_assets_all   | `Dugout_4x8_with_collapse` | -389085435411529779  | Set `base-template-grow` **m_RuleIndex** to **6**.                      |
| ui-tiles_assets_all   | `inlineStyle`              | 4355907201153990605  | Add background (**#060D17FA**) + border rule at index **6**.           |

---

## Serialized rule shape

For this asset's serialized USS:

- `background-color` uses **`m_ValueType = 4`** (`colors`)
- `border-radius` uses **`m_ValueType = 3`** (`dimensions`)
- `border-width` uses **`m_ValueType = 3`** (`dimensions`)
- `border-color` uses **`m_ValueType = 4`** (`colors`)

Target handles for the added rule (pool indices):

- `background-color` -> `valueIndex = 0` (`colors[0]`)
- `border-radius` -> each corner `valueIndex = 1` (`dimensions[1]` = **12px**)
- `border-width` -> each side `valueIndex = 2` (`dimensions[2]` = **1px**)
- `border-color` -> `valueIndex = 1` (`colors[1]`)

**Colour values (floats 0–1):**

- `colors[0]` **#060D17FA** → `r = 6/255`, `g = 13/255`, `b = 23/255`, `a = 250/255`
- `colors[1]` **rgba(217, 232, 237, 0.05)** → `r = 217/255`, `g = 232/255`, `b = 237/255`, `a = 0.05`

---

## Steps

| Name                     | Path ID (or Name)                   | Reference                  | Note                                                                                                                                                                                           |
| ------------------------ | ----------------------------------- | -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Dugout 4×8 collapse tile | -389085435411529779                 | class `base-template-grow` | In **Dugout_4x8_with_collapse**, find **base-template-grow** and set **m_RuleIndex** from **-1** to **6**.                                                                                     |
| Dugout 4×8 inline style  | 4355907201153990605 (`inlineStyle`) | Rule index **6**           | Add a **new style rule at index 6** with four properties in this order: `background-color`, `border-radius`, `border-width`, `border-color`.                                                   |
| Dugout 4×8 dimensions    | 4355907201153990605 (`inlineStyle`) | `dimensions.Array`         | Ensure **`dimensions`** contains **3** entries: **`[0] = 8px`** (existing), **`[1] = 12px`** (radius), **`[2] = 1px`** (border width).                                                      |
| Dugout 4×8 colours       | 4355907201153990605 (`inlineStyle`) | `colors.Array`             | Ensure **`colors`** contains **2** entries: **`[0] = #060D17FA`**, **`[1] = rgba(217, 232, 237, 0.05)`**.                                                                                     |
