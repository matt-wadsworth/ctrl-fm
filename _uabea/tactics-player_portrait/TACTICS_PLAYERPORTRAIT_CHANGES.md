# Tactics Player Portrait UABEA changes

- **Patch folder:** `tactics-player_portrait`
- **Bundle:** `ui-tactics_assets_all`
- **Dump format:** `.json` (UABEA Next) — Win exports in this repo
- **Dumps in repo:** `win/ui-tactics_assets_all/` (`TacticsPositionCombined` + `inlineStyle` JSON)

This note describes edits made to the **Windows** `ui-tactics_assets_all` dumps so you can repeat the **same behaviour** on **Mac** bundles. Mac assets will have different **`m_Id`**, **`rid`**, **`m_PathID`**, and possibly different **`inlineSheet`** linkage — only the **logical** steps and **rule/value intent** are portable.

**Source files (Win, this repo):**

- `win/ui-tactics_assets_all/TacticsPositionCombined-CAB-3334bbe2d7ac7af32e86413e81ff46ff-3127595210179668386.json`
- `win/ui-tactics_assets_all/inlineStyle-CAB-3334bbe2d7ac7af32e86413e81ff46ff-1320523011556632994.json` (referenced by combined asset `inlineSheet.m_PathID`)

---

## 1. `inlineStyle` (USS serialized sheet)

Assume vanilla has **28** rules (`m_Rules.Array` indices **0–27**). After all edits below, the sheet should have **30** rules (indices **0–29**). If your Mac dump already differs, locate rules by **property sets** / **purpose**, not only by index.

### Rule `0` — row `tactics-position__shirt-and-icons`

Target UXML-style result:

- `align-items: stretch` → `strings` index **0**
- `justify-content: flex-start` → `strings` index **1**
- `height: 50px` → `dimensions` index **0** = `50` (px, `unit` 1)
- `top: -50px` → `dimensions` index **1** = `-50` (px)

### Rule `2` — left `BindingRemapper` + `TacticsPlayerRoleMovementIcon`

Target:

- `align-self: flex-end` → `strings` index **3**
- `position: absolute` → `strings` index **4**
- `left: 0px` → use a **dedicated** pooled length **0** (do **not** share the same index as `bottom: 4px`)
- `bottom: 4px` → `dimensions` index **2** = `4` (px)

**Implementation (Win):** `left` uses `valueIndex` **16** → `dimensions[16]` = `0`; `bottom` still `valueIndex` **2** → `dimensions[2]` = `4`.

### Rule `3` — `FMTacticsShirt`

Target:

- `min-height` / `max-height` / `min-width` / `max-width`: **40px** → pooled index **3** = `40` (all four properties point at same `valueIndex`)
- `top: 16px` → pooled index **4** = `16`
- `left: 12px` → add property; pooled index **15** = `12` (Win)

### Rule `7` — right `BindingRemapper` + `TacticsPlayerRoleMovementIcon`

Target:

- Same as rule **2** pattern but `right: 0px` instead of `left`
- `right: 0px` → same **0px** pool entry as left icon (`valueIndex` **16** on Win)
- `bottom: 4px` → still `valueIndex` **2**

### Rules `28` and `29` — player photo (PersonPicture)

Do **not** overload rule **3** for the photo; shirt stays on **3**, photo uses **new** rules at the end of `m_Rules.Array`.

**Rule `28` — outer `VisualElement` wrapping the photo**

- `width` / `height`: **40px** → `dimensions` index **12** = `40`
- `position: absolute` → `strings` index **4**
- `left: 50%` → `dimensions` index **6** = `{ unit: 2, value: 50 }` (percent)
- `margin-left: -28px` → `dimensions` index **13** = `-28`
- `margin-top: 11px` → `dimensions` index **14** = `11`
- `justify-content` / `align-items`: **center** → `strings` index **2**

**Rule `29` — inner `BindingRemapper` (`width: 40px` only)**

- `width` → same **40px** pool as above (`valueIndex` **12** on Win)

### `dimensions` pool (append / set as needed)

Win final pool includes at least:

| Index | Meaning (Win)                                      |
| ----- | -------------------------------------------------- |
| 0     | 50 (row height)                                    |
| 1     | -50 (row top)                                      |
| 2     | 4 (movement icon bottom; shared where appropriate) |
| 3     | 40 (shirt box)                                     |
| 4     | 16 (shirt `top`)                                   |
| 6     | 50% (`left` for photo)                             |
| 12    | 40 (photo width/height)                            |
| 13    | -28 (photo `margin-left`)                          |
| 14    | 11 (photo `margin-top`)                            |
| 15    | 12 (shirt `left`)                                  |
| 16    | 0 (movement icon `left`/`right` = 0)               |

If Mac’s `dimensions.Array` order differs, **preserve values** and **rewire `valueIndex`** on each property so it points at the correct pooled entry — do not assume indices are identical across platforms.

### `strings` pool

Uses shared keyword indices (stretch, flex-start, center, flex-end, absolute, …). If your Mac sheet’s `strings.Array` order differs, **adjust `valueIndex`** on enum properties to match the same keywords.

---

## 2. `TacticsPositionCombined` (UXML tree)

**Do not copy** Win `m_Id` / `rid` / `uxmlAssetId` into Mac wholesale.

### `m_Usings`

Add template import (same GUID as FM):

```json
{
  "alias": "PersonPicture",
  "path": "a3126e03e5bea6348ba86f23b5c5d7de",
  "asset": { "m_FileID": 0, "m_PathID": 0 }
}
```

### Tree insertion (logical)

Under the flex row that contains the shirt (`m_ParentId` = same parent as `FMTacticsShirt` on your platform):

1. After **`FMTacticsShirt`** and its children, **before** the next sibling (e.g. match-player `SIVisible`), insert:
   - `UnityEngine.UIElements.VisualElement` — outer photo wrapper
   - child: `SI.Bindable.BindingRemapper` — `person` → `position.Player`
   - child: template instance **`PersonPicture`**

### `m_RuleIndex` wiring (must match `inlineStyle` after edits)

| Element                                 | Purpose | `m_RuleIndex` |
| --------------------------------------- | ------- | ------------- |
| Row `tactics-position__shirt-and-icons` | rule 0  | **0**         |
| Left movement `BindingRemapper`         | rule 2  | **2**         |
| `FMTacticsShirt`                        | rule 3  | **3**         |
| Right movement `BindingRemapper`        | rule 7  | **7**         |
| Outer photo `VisualElement`             | rule 28 | **28**        |
| Photo `BindingRemapper`                 | rule 29 | **29**        |

### Serialized `references` / `RefIds`

Win added three new blocks (VisualElement + BindingRemapper + TemplateContainer serialized data) with new **`rid`** values and matching **`uxmlAssetId`** = the new elements’ **`m_Id`**. On Mac:

- Generate **new** unique `m_Id` values for the three nodes.
- Add matching **`references.RefIds`** entries; **`uxmlAssetId`** in each `data` block must equal that element’s **`m_Id`**.
- **`m_SerializedData.rid`** on each visual/template row must match the corresponding `RefIds` entry.

### `m_OrderInDocument`

After structural edits, renumber **all** `m_VisualElementAssets` + `m_TemplateAssets` so **`m_OrderInDocument`** is **globally unique** and **contiguous** (Win ended with **78** nodes, **0–77**). A depth-first walk from parent **`0`** is one reliable way to assign order.

---

## 3. Reapply checklist (Mac)

1. Export Mac **`TacticsPositionCombined`** + its **`inlineStyle`** from the Mac CAB (paths/PathIDs will differ).
2. Apply **`inlineStyle`** edits: rules **0, 2, 3, 7**, append **28–29**, dimension/string pool updates, movement-icon **0px** horizontal via a **separate** 0-length pool slot.
3. Apply **combined** edits: **`m_Usings`**, insert **photo** subtree, set **`m_RuleIndex`** as in the table, append **`references`**, fix **`m_OrderInDocument`**.
4. Reimport; verify in-game.

---

## 4. Optional skin parity

`panels/TacticsPositionDetailed.uxml` in this skin mirrors the same layout for non-bundled overrides; keep UXML in sync if you maintain both skin and CAB patches.
