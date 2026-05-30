# Match controller panel (UABEA)

- **Patch folder:** `match-controller`
- **Bundle:** `ui-match_assets_all`
- **Dump format:** serialized asset dump (`.json`)
- **Working copies:** `ui-match_assets_all/win/` and `ui-match_assets_all/mac/` (copy from each platform’s `orig` before editing)
- **Stock only:** `win/orig/`, `mac/orig/` — do not modify; keep as the unpatched baseline.

Restyles the in-match **MatchController** playback HUD (BindingRoot) with CTRL panel colours, a **12px** radius, **1px** border, and **`margin-top: 72px`** so it aligns vertically with Match Stats / Replay. **Cannot** be done from Skin Builder alone.

## Implementation notes

- Notation **`[n]`** = **0-based index** into the named array in the JSON dump — see **[Bracket notation and the `references` object](../UABEA-NOTES.md#bracket-notation-and-the-references-object)** in **`UABEA-NOTES.md`**.
- **`m_ValueType`:** **`2`** = float, **`3`** = dimension, **`4`** = color, **`7`** = keyword (`strings` pool).
- Stock **`inlineStyle`** dumps are **byte-identical** on Windows and macOS for this asset; patched **`inlineStyle`** is the same on both platforms.
- **UXML:** No tree edits — only the linked **`inlineStyle`** sheet changes. The BindingRoot already carries **`margin-right-global-gap-extra-large`** and **`padding-horizontal-global-padding-extra-large`** via classes.

---

## Path IDs

| Bundle              | Filename       | Path ID               | Role                          |
| ------------------- | -------------- | --------------------- | ----------------------------- |
| `ui-match_assets_all` | `inlineStyle`  | `2691751170888438562` | USS for **MatchController**   |

---

## Authored style (rule `[0]`)

Target inline style on the **BindingRoot** (matches Skin Builder export):

```css
background-color: #060D17FA;
border-radius: 12px;
border-width: 1px;
border-color: #D9E8ED0D;
padding-top: 16px;
padding-bottom: 16px;
width: 319px;
margin-top: 72px;
right: 8px;
position: absolute;
```

Stock rule **`[0]`** used FM purple-grey fill, **16px** radius, **2px** border, semi-opaque FM border tint, and **`margin-top: 46px`**.

---

## 1. `inlineStyle` (`2691751170888438562`)

### Pooled values (patched target)

**`colors.Array`:**

| Index | Target | Note |
| ----- | ------ | ---- |
| **`[0]`** | `{ r: 6/255, g: 13/255, b: 23/255, a: 250/255 }` | CTRL panel fill **#060D17FA** |
| **`[1]`** | `{ r: 217/255, g: 232/255, b: 237/255, a: 13/255 }` | Border **#D9E8ED0D** |

**`dimensions.Array`:**

| Index | Stock | Patched | Used by rule `[0]` |
| ----- | ----- | ------- | ------------------ |
| **`[0]`** | **2px** | **1px** | `border-*-width` |
| **`[1]`** | **16px** | **16px** | `padding-top` / `padding-bottom` |
| **`[2]`** | **319px** | **319px** | `width` |
| **`[3]`** | **46px** | **72px** | `margin-top` |
| **`[6]`** | **12px** | **12px** | `border-*-radius` (repoint from **`[1]`**) |

**Unchanged on rule `[0]`:** `position` → **`strings[0] = absolute`**.

**`right`:** stock used **`m_ValueType = 2`** / **`floats[0] = 0`**; repoint to **`m_ValueType = 3`**, **`valueIndex = 4`** (**`dimensions[4] = 8px`**) to match authored **`right: 8px`**.

### Rule `[0]` edits

- **`background-color`:** keep **`valueIndex = 0`**; update **`colors[0]`** to **#060D17FA**.
- **`border-left-color`** / **`border-right-color`** / **`border-top-color`** / **`border-bottom-color`:** keep **`valueIndex = 1`**; update **`colors[1]`** to **#D9E8ED0D**.
- **`border-*-width`:** keep **`valueIndex = 0`**; set **`dimensions[0] = 1px`**.
- **`border-*-radius`:** change **`valueIndex` from `1` → `6`** on all four corners so radius stays **12px** while **`padding-top` / `padding-bottom`** remain **16px** via **`dimensions[1]`**.
- **`margin-top`:** keep **`valueIndex = 3`**; set **`dimensions[3] = 72px`**.
- **`right`:** set **`m_ValueType = 3`**, **`valueIndex = 4`** (**`dimensions[4] = 8px`**).

Do **not** modify rules **`[1]`–`[6]`** — they style inner playback buttons and text.

---

## Pitfalls

| Mistake | Symptom |
| ------- | ------- |
| Set **`dimensions[1] = 12px`** instead of repointing radius to **`[6]`** | Padding collapses to **12px** |
| Leave **`margin-top` at 46px** | Controller sits too high vs Match Stats / Replay (**72px** row) |
| Edit **`orig/`** baselines | Loses stock reference for the next FM rebuild |

---

## Quick checklist

| Asset / path ID | Action |
| --------------- | ------ |
| `inlineStyle` `2691751170888438562` | **`colors[0/1]`** CTRL fill + border; **`dimensions[0]`** **1px**; **`dimensions[3]`** **72px**; rule **`[0]`** radius **`valueIndex → 6`**; **`right`** → **`dimensions[4]`** **8px** |

---

## Validation

- In-match highlight playback: controller panel uses dark CTRL fill, subtle border, **12px** corners, sits **72px** from top, **8px** from right.
- JSON: file parses; rule count stays **7**; pools unchanged length (**7** dimensions, **2** colors).
