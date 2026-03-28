# Match scoreboard — left aligned (UABEA)

- **Patch folder:** `match-left_scoreboard`
- **Bundle:** `ui-match_assets_all`
- **Dump format:** `.txt`
- **Dumps in repo:** `ui-match_assets_all/win/`, `ui-match_assets_all/mac/` (same path IDs; re-export per platform as needed)

Edits in **ui-match_assets_all** for in-match **OverviewHeader** and pre-match / interval **ContinuePanel** alignment.

**Platform note:** `OverviewHeader` / `ContinuePanel` dumps differ between Win and Mac on **`m_Id`**, **`m_ParentId`**, and other instance handles. Apply the **same logical edits** (indices below) to each export; only **`inlineStyle`** blobs can be byte-identical across platforms if handles are aligned (see §1d).

---

## Path IDs

| Bundle              | Filename          | Path ID              | Role                                      |
| ------------------- | ----------------- | -------------------- | ----------------------------------------- |
| ui-match_assets_all | `OverviewHeader`  | -7819803725380563227 | In-match header UXML                       |
| ui-match_assets_all | `inlineStyle`     | -2719487159082004763 | Styles for OverviewHeader + safe area     |
| ui-match_assets_all | `ContinuePanel`   | -398932524086274306  | Kick off / half time / full time panel    |
| ui-match_assets_all | `inlineStyle`     | 4016413755955533566  | Styles for ContinuePanel                  |

---

## 1. In-match scoreboard (`OverviewHeader`)

### a. `inlineStyle` (-2719487159082004763) — move scoreboard container left

Locate **style rule `[8]`** in **`m_Rules`**.

String enum properties use **`m_ValueType = 7`** (keyword); **`valueIndex`** points into **`vector strings`**.

- **Property [0]** (`align-self`): `m_ValueType = 7`, `valueIndex = 1` (`flex-start` in typical dumps — was often `4` / `center` in vanilla).
- **Property [1]** (`align-content`): `m_ValueType = 7`, `valueIndex = 1`.
- **Property [3]:** rename **`width`** → **`left`**. In serialized USS this is a **length**, not a float literal:
  - **`m_ValueType = 3`** (Dimension), **`valueIndex = 5`**
  - **§1c** must set **`dimensions[5] = 26`** (px) so `left` resolves correctly.
- **Property [4]** (`top`): if vanilla uses **`m_ValueType = 2`**, **`valueIndex = 0`** (auto), switch to a **Dimension** handle so layout stays consistent with the patched sheet:
  - **`m_ValueType = 3`**, **`valueIndex = 13`**
  - Grow **`dimensions`** to **14** entries and set **`dimensions[13]`** (e.g. **value = 0**, unit **px**) — see **§1c** / **§1d** for the full array.

### b. `OverviewHeader.uxml` — reorder controls

In **`m_VisualElementAssets`** (0-based element index in the dump):

- Element **[7]** → **`m_OrderInDocument = 9`**
- Element **[19]** → **`m_OrderInDocument = 7`**

Swaps **MatchUITacticalOptions** vs an empty flex-grow so tactical options sit to the right.

On element **[7]**, in **`m_Classes`**, **add** `margin-right-global-gap-regular` (gap before stats/playback).

### c. Same `inlineStyle` — Match stats / replay (**SISafeAreaElement**)

Authored style is like: `position: absolute; align-self: flex-end; top: 56px; right: 16px`.

In **`Dimension dimensions`** (used by the absolute-position rule for the safe-area / stats block — **rule `[12]`** in typical dumps):

| Index   | Original (matches top / right) | Set to | Note                                     |
| ------- | ------------------------------ | ------ | ---------------------------------------- |
| **[4]** | 56 (`top`)                     | **72** | Vertical offset for Match Stats / Replay |
| **[5]** | 16 (`right`)                   | **26** | Inset from the right edge                |

**Also** (for **§1a** and a consistent **`dimensions`** table):

- Increase **`dimensions`** **size** from **12 → 14** if it was 12 in vanilla.
- Append:
  - **`[12]`:** value **24**, unit **px** (used by **`margin-top`** on **rule `[2]`** when matching Windows — **§1d**)
  - **`[13]`:** value **0**, unit **px** (**`top`** on **rule `[8]`** property **[4]**)

Adjusts layout after the header / tactical options changes above.

### d. Optional — align `inlineStyle` with Windows after resizing **`dimensions`**

Vanilla **Mac** may keep **`margin-top`** on **rule `[2]`** at **`valueIndex = 1`** (older slot) while **Windows** uses **`valueIndex = 12`** (24px via **`dimensions[12]`**). After you add **`[12]`** / **`[13]`**, set **rule `[2]` → `margin-top` → `valueIndex = 12`** so the Mac txt matches the Win txt and imports stay predictable.

---

## 2. Pre-match “Kick off” / half time / full time (`ContinuePanel`)

**ContinuePanel.uxml** (-398932524086274306):

- **Element [8]** — add **`align-items-end`** and **`margin-right-global-gap-regular`** to **`m_Classes`** (keep existing classes, e.g. `flex-grow-class`).

**inlineStyle** (4016413755955533566):

- **Rule [2]** — set **`align-items`** to **`m_ValueType = 7`**, **`valueIndex = 5`** (`flex-end`).
- **Rule [4]** — **`margin-top` = 4px:**
  - Append a **`Dimension`** entry (**value = 4**, unit **px**).
  - Point **`margin-top`** at the new slot (**`valueIndex = 4`** if the new entry is index **4** and the array was length **4** before).

---

## Quick checklist

| Asset / path ID              | Action |
| ---------------------------- | ------ |
| inlineStyle `-2719487159082004763` | Rule **[8]** align + `width`→`left` + `top` handles; **`dimensions`** **14** slots; **[4]/[5]** 72/26; **[12]/[13]** as above; optional rule **[2]** `margin-top` → **12** |
| OverviewHeader `-7819803725380563227` | Elements **[7]** / **[19]** order; element **[7]** class |
| ContinuePanel `-398932524086274306` | Element **[8]** classes |
| inlineStyle `4016413755955533566` | Rule **[2]** `align-items`; rule **[4]** `margin-top` + **Dimension** |
