# Squad overview player block UABEA changes

- **Patch folder:** `match-squad_portraits`
- **Bundle:** `ui-widgets_assets_all`
- **Dump format:** `.json` (UABEA Next) — Win exports in this repo
- **Dumps in repo:** `ui-widgets_assets_all/win/` (`SquadOverviewPlayerBlock` + `inlineStyle` JSON)

Notes to repeat the **same layout and portrait overlay** on a **vanilla** dump (another game version, **Mac**, or a clean export). Compare against vanilla when unsure; **this repo’s Win patches** are one concrete instance.

## Platform and file identity (read this first)

**Do not treat Win numbers as universal.**

Between **Windows and Mac** (and often between FM builds), these commonly **differ** and must **not** be copied blindly:

- **`m_Id`**, **`m_ParentId`**, **`m_OrderInDocument`**, **`rid`**, **`m_PathID`**, **`uxmlAssetId`** on tree nodes and `MonoBehaviour` script references  
- **`inlineSheet.m_PathID`** may still **name** the same logical asset (`inlineStyle-…-3625496258969266647`) but the **numeric PathID** can differ per platform or export  
- **`m_Rules.Array` index** may match vanilla **only if** your vanilla rule count matches ours before edits (see below)  
- **`dimensions.Array` / `colors.Array` / `strings.Array` indices** are a **pool**: vanilla may use **fewer** entries and different **ordering** after edits. Always **append** new lengths or **rewire `valueIndex`**, never assume index **18** on Mac is the same semantic slot as on Win unless you verify  

**Portable:** logical USS (what property, what pixel/% value), tree **structure** (sibling order, parent), template **GUID** for `PersonPicture`, and binding **paths** (`person`, etc.).

**Patched Win copies in this repo:**

- `match-squad_portraits/ui-widgets_assets_all/win/inlineStyle-CAB-019ad19fde35e70c30c2e7a4cd52c3af--3625496258969266647.json`
- `match-squad_portraits/ui-widgets_assets_all/win/SquadOverviewPlayerBlock-CAB-019ad19fde35e70c30c2e7a4cd52c3af-8153607655464179241.json`

---

## 1. Vanilla → patched summary (Win, this CAB)

| Asset | Vanilla | Patched (Win) |
| ----- | ------- | ------------- |
| `inlineStyle` `m_Rules` count | **31** (indices 0–30) | **34** (0–33): **3 new rules** at end |
| `inlineStyle` `dimensions` count | **11** (0–10) | **24** (0–23): **13 new** + changed **`[5]`** |
| `inlineStyle` `strings` | ends at **`absolute`** | add **`relative`** (USS `position: relative` on kit row) |
| `SquadOverviewPlayerBlock` `m_Usings` | **1** entry | **2** (+ `PersonPicture`) |
| Tree nodes | — | **+2** `m_Id` (wrapper + template instance); **Win-only IDs** in table below |

**Rules that change on Win (same index as vanilla, content differs):** **12, 13, 14, 15, 19, 20, 21, 27**  

**New rules on Win (append):** **31, 32, 33**

---

## 2. `SquadOverviewPlayerBlock` (UXML tree)

### `m_Usings`

Add (same FM template as tactics portrait work):

```json
{
  "alias": "PersonPicture",
  "path": "a3126e03e5bea6348ba86f23b5c5d7de",
  "asset": { "m_FileID": 0, "m_PathID": 0 }
}
```

### Tree insertion (logical)

Under the **horizontal kit row** (`row-direction-normal` containing goals \| `SIAspectRatioFitter`+`FMTacticsShirt` \| assists), **after** `FMTacticsShirt`’s parent fitter chain, **before** the next sibling in that row:

1. **`VisualElement`** — outer wrapper for the overlay (Win name `SquadPlayerPictureWrap`).  
2. Child: **`PersonPicture`** template instance (`templateId` / alias `PersonPicture`).  

**Bindings:** The block already exposes `person` on the data context; **no extra `BindingRemapper`** was required on Win for the instance (same as tactics note — verify on your dump).

### `m_RuleIndex` (Win example only)

| Role | Win `m_Id` (example) | `m_RuleIndex` |
| ---- | -------------------- | ------------- |
| Picture wrapper | `2019847551` | **31** |
| `PersonPicture` inner | `2019847552` | **32** |
| Name wrapper (`ShirtName` parent chain) | `1251298332` | **33** |

On another platform, find the same **elements by hierarchy / name / type**, then set **`m_RuleIndex`** to the **matching rule indices** in **your** edited `inlineStyle`.

### Serialized `rid` / `m_SerializedData`

Regenerate consistent **`rid`** values and `MonoBehaviour` blob references when cloning; **do not** paste Win `rid` **1047** / **1048** into Mac without UABEA/Unity resolving references.

---

## 3. `inlineStyle` — rule-by-rule intent

Assume **vanilla** has **31** rules. After edits, **34** rules. If your file already differs, locate rules by **property names** and **element** (via `m_RuleIndex` on the block).

### Rules **12** — Main area frame (`height: 120px` strip under position header)

| Change | Unpatched (typical) | Target intent |
| ------ | ------------------- | ------------- |
| `height` pooled length | `120` (`valueIndex` **5**) | **`130`** — slightly shorter than earlier 138px experiment |
| `padding-top` | *(absent)* | **`18px`** — dedicated pool entry (Win **`dimensions[22]`**) |
| Other props | padding, radius, `justify-content: flex-end`, `flex-shrink: 0` | unchanged |

### Rule **13** — Info column (kit row + name; vanilla `height: 64px`)

| Change | Unpatched (typical) | Target intent |
| ------ | ------------------- | ------------- |
| `height` | `valueIndex` **7** → **64** | Point at **new** length **128px** (Win **`dimensions[18]`**) — room for portrait + spacing |
| `justify-content` | `space-between` | **`flex-start`** — stop pinning name to bottom of fixed height |

### Rule **14** — Kit row (goals \| shirt \| assists)

| Change | Unpatched (typical) | Target intent |
| ------ | ------------------- | ------------- |
| `align-items` | `flex-start` | **`center`** — vertically centre row content in tall row |
| `position` | *(absent)* | **`relative`** — positioning context for absolute photo; **append `relative` to `strings`** if missing |
| `padding-top` | *(absent)* | **`8px`** (often shared **`valueIndex`** for `8px`, Win **`dimensions[3]`**) |
| `min-height` | *(absent)* | **`80px`** (Win **`dimensions[20]`**) |
| `padding-bottom` | *(absent)* | **`32px`** (Win **`dimensions[17]`**) |

### Rules **15** and **21** — goal and assist columns (`flex-grow: 0`, `width: 20%`)

| Change | Vanilla | Target intent |
| ------ | ------- | ------------- |
| `margin-top` | *(absent)* | **`42px`** on Win via **dedicated** pool slot (**`dimensions[23]`**) — **do not** reuse **`dimensions[10]`** if it is already **16×16** icon size elsewhere |

### Rule **19** — `SIAspectRatioFitter` (shirt box)

| Change | Unpatched (typical) | Target intent |
| ------ | ------------------- | ------------- |
| `width` / `height` | **32** (`valueIndex` **2**) | **70** (Win **`dimensions[14]`**) |

### Rule **20** — `FMTacticsShirt`

| Change | Vanilla | Target intent |
| ------ | ------- | ------------- |
| `opacity` | *(absent)* | **0** (`floats[0]`) — hide shirt; face shows from `PersonPicture` |

### Rule **27** — `layout-divider-solid-horizontal` instance

| Change | Unpatched (typical) | Target intent |
| ------ | ------------------- | ------------- |
| `opacity` | *(absent)* | **0** — hide divider line (margin may remain) |

### New rule **31** — `SquadPlayerPictureWrap` (Win)

Typical USS:

- `width` / `height`, `position: absolute`, `left` / `top`, `margin-left`, `margin-top`, `justify-content` / `align-items: center`  
- Win uses pooled **`dimensions[15]`** = **60** (face box), **`[16]`** = **-30** (`margin-left`), **`[19]`** = **-2** (`margin-top` nudge)  
- **`left` / `top`** often **0** via floats pool  

Tune **`margin-top`** / **`margin-left`** for alignment with shirt column.

### New rule **32** — inner `PersonPicture` / binding child (Win)

- Often **`width` / `height`** = **100%** (`valueIndex` **0** → percent pool)

### New rule **33** — name wrapper (`1251298332` on Win)

- **`margin-top`**: **14px** (Win **`dimensions[21]`**) — spacing below portrait row  

---

## 4. `dimensions` pool (Win reference only)

Vanilla had **11** entries. Patched Win **extends** the array; **`[5]`** changes from **120 → 130**.

| Win index | Role (patched) |
| --------- | -------------- |
| **5** | Main area **height** **130** |
| **14** | Shirt fitter / photo box **70** (and paired height on rule 19) |
| **16** | Photo **`margin-left`** **-30** |
| **17** | Kit row **`padding-bottom`** **32** |
| **18** | Info column **height** **128** |
| **19** | Picture wrap **`margin-top`** **-2** (nudge) |
| **20** | Kit row **`min-height`** **80** |
| **21** | Name **`margin-top`** **14** |
| **22** | Main area **`padding-top`** **18** |
| **23** | Goals / assists **`margin-top`** **42** |

Other indices **11–13**, **15** etc. participate in rule **31** geometry; **reconstruct on your dump** by reading the patched Win JSON or by applying USS and letting Unity/UABEA serialize.

---

## 5. `colors`

| Index | Unpatched (typical) | Patched intent |
| ----- | ------------------- | -------------- |
| **0** | `a ≈ 0.39` | **`a = 0`** — card tint fully transparent |
| **1** | light lavender `~0.9 / 0.9 / 0.98`, `a ≈ 0.082` | **`r 0.851, g 0.91, b 0.929, a 0.05`** — position header bar, slightly softer |

---

## 6. `strings`

- Append **`"relative"`** if rule **14** uses **`position: relative`** and your vanilla sheet has no keyword yet.  
- **`valueIndex` for `relative`** on Win is **9** only because it is the **tenth** string; if your `strings.Array` order differs, point `position` at the correct index.

---

## 7. Applying on a new vanilla file

1. Export **vanilla** `SquadOverviewPlayerBlock` + its **`inlineStyle`** (same logical CAB names as above, or locate by `m_Name` / content).  
2. Edit **`inlineStyle`** first: append **`strings`** / **`dimensions`** / **`colors`** as needed, then **mutate rules 12–15, 19–20, 21, 27**, then **append rules 31–33**.  
3. Edit **`SquadOverviewPlayerBlock`**: **`m_Usings`**, tree nodes, **`m_RuleIndex`** on the three elements.  
4. Reimport into the bundle; fix **broken references** (`rid`, `PathID`) with your tool’s workflow.  

If JSON has **trailing garbage** after the root `}`, parse with **`JSONDecoder().raw_decode`** or trim before merge.

---

## 8. Related

- Same `PersonPicture` template GUID as tactics: `_uabea/TACTICS_PLAYERPORTRAIT_CHANGES.md`  
- Original UXML shape (pre-patch): `row-direction-normal` header; main area `120px` + `flex-end`; inner `64px` column `space-between`; kit row `space-between` + `align-items: flex-start`
