# Match CTRL scoreboard (UABEA)

- **Patch folder:** `match-ctrl_scoreboard`
- **Bundle:** `ui-match_assets_all`
- **Dump format:** serialized asset dump (`.json`)
- **Working copies:** `ui-match_assets_all/win/` and `ui-match_assets_all/mac/` (copy from each platform’s `orig` before editing)
- **Stock only:** `win/orig/`, `mac/orig/` — do not modify; keep as the unpatched baseline.

Moves the in-match **Scoreboard** HUD to the **left** of the screen with CTRL panel colours, asymmetric team blocks, and absolute positioning for clock / scores / centre overlay. Replaces stock FM purple styling and reworks several UXML nodes (club-colour bindings, steppers, no progress bar). **Cannot** be done from Skin Builder alone.

Works with **`match-left_scoreboard`** (OverviewHeader / ContinuePanel insets) — both target the same left-edge rhythm.

## Implementation notes

- Notation **`[n]`** = **0-based index** into the named array in the JSON dump — see **[Bracket notation and the `references` object](../UABEA-NOTES.md#bracket-notation-and-the-references-object)** in **`UABEA-NOTES.md`**.
- **`m_ValueType`:** **`2`** = float, **`3`** = dimension, **`4`** = color, **`7`** = keyword (`strings` pool).
- **`m_Properties.Array` order matters** on each rule; insert new properties in the correct sibling position rather than replacing whole rules unless intentional.
- **Regression counts (both OS, this FM lineage):** **`Scoreboard`** — **`44`** `m_VisualElementAssets`, **`3`** `m_TemplateAssets`, **`51`** `references.RefIds`; **`inlineStyle`** — **`33`** rules, **`41`** dimensions, **`12`** colors.
- **UXML:** Stock → patched is a **structural** diff (node count, `m_Id`, templates, `references`). Rebuild Mac from **`mac/orig`** using this note’s **layout targets**; do **not** paste Windows **`Scoreboard`** JSON wholesale into Mac. For Windows, diffing committed **`win/`** against **`win/orig/`** is the fastest way to re-apply the tree after a title update.
- **Patch order:** **`inlineStyle`** first, then **`Scoreboard`**.
- **macOS:** Stock **`inlineStyle`** dumps are **byte-identical** across OS; patched **`inlineStyle`** matches on both platforms. **`Scoreboard`** uses the same logical tree as Windows but keeps **shared synthetic `m_Id`s** for new nodes and remaps **stock** IDs through **`mac/orig` ↔ `win/orig`** index alignment. Run **`apply_mac_patch.py`** in this folder to regenerate **`mac/`** from **`win/`** + **`mac/orig`**.

---

## Path IDs

| Bundle              | Filename       | Path ID               | Role                                      |
| ------------------- | -------------- | --------------------- | ----------------------------------------- |
| `ui-match_assets_all` | `Scoreboard`   | `-6243489578598291996` | In-match scorebug / clock / score UXML   |
| `ui-match_assets_all` | `inlineStyle`  | `5770178802341094884`  | USS for `Scoreboard`                      |

---

## Layout (patched)

Left-anchored **420×40** bar. Major slices (absolute unless noted):

```
|-- ~6px --| home block 80px |-- scores 140px @ left 54 --|-- overlay 285px @ 151 --|-- clock 105px @ 180 --|
```

| Rule   | Role (approx.)              | Key layout |
| ------ | --------------------------- | ---------- |
| **[1]**  | Root row                    | `width` / `min-width` **420px**, `height` **40px**, `position` **relative** |
| **[8]**  | Home side capsule           | `position` **absolute**, `left` **6px**, `width` **80px**, leading radii |
| **[26]** | Centre score stack          | `position` **absolute**, `left` **54px**, `width` **140px** |
| **[25]** | Wide centre overlay         | `position` **absolute**, `left` **151px**, `width` **285px** |
| **[4]**  | Clock frame                 | `position` **absolute**, `left` **180px**, `width` **105px** |
| **[10]** | Home score text container   | `position` **relative**, `width` **140px**, `padding-right` **32px**, trailing radii on left block |
| **[14]** | Away score text container   | `position` **relative**, `width` **140px**, `padding-left` **32px**, trailing radii on right block |

Stock (for comparison): root **`316px`** wide; clock block used **`justify-content: flex-end`** without fixed `left`; home/away blocks were flex-driven with **`2px`** radii and FM purple **`colors[0]`** / ultra-violet accent **`colors[1]`**.

---

## 1. `inlineStyle` (`5770178802341094884`)

### Pooled values (patched target)

**`strings.Array`** (12 entries):

| Index | Value |
| ----- | ----- |
| `[0]` | `center` |
| `[1]` | `column` |
| `[2]` | `stretch` |
| `[3]` | `flex-end` |
| `[4]` | `hidden` |
| `[5]` | `flex` |
| `[6]` | `flex-start` |
| `[7]` | `absolute` |
| `[8]` | `middle-center` |
| `[9]` | `ellipsis` |
| `[10]` | `relative` |
| `[11]` | `visible` |

**`floats.Array`:** `[0] = 0`, `[1] = 1`, `[2] = 2`, `[3] = 1` (stock used two floats; grow to four if needed).

**`colors.Array`** (12 entries):

| Index | Target | Note |
| ----- | ------ | ---- |
| `[0]` | `{ r: 20/255, g: 29/255, b: 34/255, a: 1 }` | CTRL panel fill **#141D22** (main blocks) |
| `[1]` | same as `[0]` | duplicate slot for rules that referenced stock `[0]` |
| `[2]` | `{ r: 1, g: 1, b: 1, a: 1 }` | white score / clock text |
| `[3]`–`[8]` | saturated primaries | club-colour binding targets (red, blue, green, yellow, magenta, orange) |
| `[9]` | `{ r: 1, g: 1, b: 1, a: 0.25 }` | translucent white |
| `[10]` | `{ r: 1, g: 1, b: 1, a: 0.05 }` | faint white |
| `[11]` | `{ r: 251/255, g: 197/255, b: 0, a: 1 }` | gold accent |

Stock had only **`colors[0]`** (FM purple-grey) and **`colors[1]`** (ultra-violet text). Repoint rules that used **`valueIndex = 0`** for backgrounds to **`colors[1]`** where the patched sheet uses the CTRL fill twice.

**`dimensions.Array`** (41 entries):

| Index | Value |
| ----- | ----- |
| `[0]` | `420px` |
| `[1]` | `40px` |
| `[2]` | `8px` |
| `[3]` | `3px` |
| `[4]` | `100%` |
| `[5]` | `105px` |
| `[6]` | `10px` |
| `[7]` | `5px` |
| `[8]` | `36px` |
| `[9]` | `24px` |
| `[10]` | `140px` |
| `[11]` | `80px` |
| `[12]` | `3px` |
| `[13]` | `90%` |
| `[14]` | `1px` |
| `[15]` | `12px` |
| `[16]` | `16px` |
| `[17]` | `32px` |
| `[18]` | `-18px` |
| `[19]` | `0px` |
| `[20]` | `60px` |
| `[21]` | `-12px` |
| `[22]` | `54px` |
| `[23]` | `180px` |
| `[24]` | `285px` |
| `[25]` | `8px` |
| `[26]` | `14px` |
| `[27]` | `151px` |
| `[28]` | `6px` |
| `[29]` | `4px` |
| `[30]` | `12px` |
| `[31]` | `4px` |
| `[32]` | `27px` |
| `[33]` | `7px` |
| `[34]` | `2px` |
| `[35]` | `5px` |
| `[36]` | `2px` |
| `[37]` | `100%` |
| `[38]` | `-7px` |
| `[39]` | `44px` |
| `[40]` | `36px` |

Stock highlights: **`dimensions[0] = 316px`**, **`[2] = 2px`** radius, **`[7] = 188px`** min-width on old centre rule, no slots **`[20]`–`[40]`**.

### Rule edits (instruction targets)

Apply after pools match the table above. Resolved values below use patched indices.

- **Rule `[0]`** — sheet root: `width` / `height` → **`420px`** / **`40px`** (`dimensions[0]`, `[1]`). Stock: **316px**.
- **Rule `[1]`** — main row: add `min-width`, `width`, `height` **420/40**, `align-items` **center**, `position` **relative**; keep flex-grow / opacity handles as stock.
- **Rule `[2]`** — compact leading block (competition icon column): `background-color` → **`colors[1]`**; radius **8px** on all corners (`dimensions[2]`); `align-items` **flex-start**; fixed **60×40** (`dimensions[20]`, `[1]`); horizontal padding **8/10** (`[2]`, `[6]`); `margin-right` **0** (`[19]`).
- **Rule `[3]`** — icon chip: **27×27**, `margin-left` **3px**, `margin-top` **7px**.
- **Rule `[4]`** — **clock frame**: switch to **`position: absolute`**, `left` **180px** (`dimensions[23]`), `width` **105px** (`[5]`), drop stock **`justify-content: flex-end`** + vertical padding; radius **8px**; CTRL fill **`colors[1]`**.
- **Rule `[8]`** — **home outer capsule**: `position` **absolute**, `left` **6px** (`[28]`), `width` **80px** (`[11]`), CTRL fill, **8px** radius, `align-items` **flex-start**.
- **Rule `[10]`** — **home score column**: `width` **140px** (`[10]`), `padding-right` **32px** (`[17]`), leading radii only.
- **Rule `[14]`** — **away score column**: same width, `padding-left` **32px**, trailing radii only.
- **Rule `[25]`** — centre overlay: `position` **absolute**, `left` **151px** (`[27]`), `width` **285px** (`[24]`), `height` **40px**.
- **Rule `[26]`** — centre score stack: `position` **absolute**, `left` **54px** (`[22]`), `width` **140px** (`[10]`).
- **Rules `[11]` / `[13]` / `[19]`** — score text: `color` → **`colors[2]`** (white); adjust `margin-top` / padding to **`dimensions[25]`** / **`[15]`** as in patched dump.
- **Rules `[31]` / `[32]`** — added-time stepper ticks: **44×5** bars, `position` **absolute**, `top` **-7px** (`[38]`), anchored **`left` / `right` 36px** (`[40]`).
- **Rules `[27]` / `[28]`** — `fm-universe-20px` clock / stat text: `margin-top` **4px** (`[29]`).

**Rule count:** **`35` → `33`** — several stock text-only rules collapse into class-driven layout; verify **`m_Rules.Array`** length after edits.

---

## 2. `Scoreboard` (`-6243489578598291996`)

### UXML goals

| Area | Stock | Patched |
| ---- | ----- | ------- |
| **`m_Usings`** | `CompIcon` only | Add **`ClubIcon`** (`path` `9947ca486d6d8c7478a50294c6151796`) |
| **`m_VisualElementAssets`** | **48** nodes | **45** nodes — remove **`SIProgressBar`**; add club-colour / comp-colour / stepper / switch wiring |
| **`m_TemplateAssets`** | **1** (class `margin-global-gap-small`) | **3** templates (classes cleared on template roots) |
| Root row **[3]** classes | `margin-right-global-gap-none`, `padding-horizontal-global-padding-none` | add **`align-items-center`** |
| Leading block **[4]** | `padding-horizontal-global-padding-extra-small` | **`align-items-center`**, **`border-radius-leading-small`**, drop extra horizontal padding class |
| Comp icon capsule | static CTRL fill via inline rule **[2]** `background-color` | **`SIStyleSetter` `CompIconBg`** → **`comp.SecondaryColor`** (root remapper **`comp`** → **`match.Competition`**); wrapper **`fm-club-primary-color`**; rule **[2]** keeps size/radius only (no static fill) |
| Clock frame | `margin-right-global-gap-small`, `padding-horizontal-global-padding-regular` | **`margin-right-global-gap-none`**, **`justify-content-center`**, **`border-radius-trailing-small`** |
| Team blocks | generic `heading-small-16px` / ultra-violet text classes | **`fm-club-primary-color`** / **`fm-club-secondary-color`** wrappers + **`fm-universe-20px`**, **`white`**, **`tile-title-color`** on labels |
| Steppers | `row-direction-normal` (2×) | **`hidden_unselected_steppers`**, **`row-direction-always-normal`**, **`justify-content-flex-start`** / **`flex-end`** (rules **`[31]`** / **`[32]`**) |
| Score visibility | progress bar node present | **No** **`SIProgressBar`** — scores use **`SIVisible`** + text only |

### Element ↔ rule map (patched, by class)

Use **`m_RuleIndex`** on the element when matching styles in UABEA:

| Classes (subset) | `m_RuleIndex` | Role |
| ---------------- | ------------- | ---- |
| Root row under `BindingRemapper` | **1** | 420px bar |
| `border-radius-leading-small` + `fm-club-primary-color` (comp) | **2** | Competition icon leading capsule (dynamic fill) |
| `scorebug-broadcast-graphics-sitv-scorebar-clock-frame` | **4** | Match clock |
| `border-radius-global-border-small` + `justify-content-center` (home) | **8** | Home score shell |
| `fm-club-primary-color` (home) | **10** | Home primary bind block |
| `fm-club-primary-color` (away) | **14** | Away primary bind block |
| Centre overlay `VisualElement` under rule **25** parent | **25** | Wide middle overlay |
| `SIStepper` (start / end) | **31** / **32** | Added-time markers |

Because **`m_Id` / `m_ParentId`** change when nodes are inserted or removed, prefer **class + `m_OrderInDocument` + `m_RuleIndex`** to find nodes after re-export; fall back to **`win/` ↔ `win/orig/`** diff.

### `references`

Patched **`references.RefIds`** count is **53** (stock **49**; +2 for **`CompIconBg`** `SIStyleSetter` / VE metadata). Any UXML edit must keep **`m_SerializedData.rid`**, template **`rid`**, and **`RefIds`** consistent — validate JSON parses before import.

**Win-only test (2026-05):** Re-import **`win/Scoreboard`** + **`win/inlineStyle`**; competition leading block should tint from **`match.Competition.SecondaryColor`**. Re-run **`patch_comp_icon_color.py`** if re-exported from UABEA. Mac: not updated until win verified.

---

## Pitfalls

| Mistake | Symptom |
| ------- | ------- |
| Leave root width at **316px** | Bar does not align with **`match-left_scoreboard`** **26px** inset |
| Clock rule **[4]** still **`flex-end`** without **`left: 180px`** | Clock floats to wrong edge |
| Copy Windows **`Scoreboard`** into Mac | Broken tree / missing binds |
| Drop **`align-items-center`** on root row **[3]** | Vertical misalignment between clock and team rows |
| Reintroduce **`SIProgressBar`** | Duplicate / clipped score layout |

---

## Quick checklist

| Asset / path ID | Action |
| --------------- | ------ |
| `inlineStyle` `5770178802341094884` | Pools: **41** dims, **12** colors, **12** strings; root **420×40**; absolute slices **6 / 54 / 151 / 180** px; **8px** radius; CTRL fill **#141D22** |
| `Scoreboard` `-6243489578598291996` | Add **`ClubIcon`** using; remove progress bar; club-colour classes; steppers; **3** templates; diff tree vs **`orig`** |

---

## macOS rebuild (`apply_mac_patch.py`)

1. Ensure **`win/`** holds the known-good Windows patch and **`mac/orig/`** is stock.
2. Run: `python apply_mac_patch.py` from **`match-ctrl_scoreboard/`**.
3. Writes **`mac/inlineStyle`** (copy of **`win/inlineStyle`**) and **`mac/Scoreboard`** (deep-copy **`win/Scoreboard`** with **`m_Id` / `m_ParentId` / `uxmlAssetId`** remapped via aligned **`orig`** rows).
4. New nodes introduced only in the Windows patch keep their **Windows `m_Id`s** when they do not collide with **`mac/orig`** (this FM lineage: **no collisions**).
5. Overlay children at **`m_OrderInDocument` 13 / 14** must parent to the rule **`[25]`** overlay node (**`m_Id` `-582934710`** on this export), not dangling IDs.

**Win vs Mac comparison (expected):** **`inlineStyle`** bytes identical; **`Scoreboard`** — same **`m_Usings`**, **`inlineSheet`**, pools (via sheet), per-element **classes / `m_RuleIndex` / `m_OrderInDocument`**, template count, **`RefIds`** count; **`m_Id` / `m_ParentId`** differ only where stock rows were remapped to Mac natives.

---

## Validation

- In-match: scorebug sits **left**, clock and scores readable, added-time ticks visible, no horizontal clip at **420px** width.
- JSON: both files parse; **`inlineSheet.m_PathID`** on **`Scoreboard`** still **`5770178802341094884`**.
- Re-smoke on **both** OS after importing **`win/*.json`** or **`mac/*.json`** respectively.
