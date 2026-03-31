# Match scoreboard — left aligned (UABEA)

- **Patch folder:** `match-left_scoreboard`
- **Bundle:** `ui-match_assets_all`
- **Dump format:** serialized asset dump (`.json`)
- **Working copies:** `ui-match_assets_all/win/` (copy from `win/orig` or `mac/orig` before editing)
- **Stock only:** `win/orig/`, `mac/orig/` — do not modify; keep as the unpatched baseline.

Edits in **ui-match_assets_all** for in-match **OverviewHeader** and pre-match / interval **ContinuePanel** alignment.

## Implementation notes

- Copy the four JSON assets from **`orig`** into **`win/`** or **`mac/`**, then apply the sections below. Rule indices and `m_VisualElementAssets` positions are for the **current** **`orig`** shape; after a title update, re-export, diff against **`orig`**, and adjust indices if the tree changes.
- **`m_Rules` indices are 0-based.** “Rule `[8]`” is the ninth entry in `m_Rules.Array`.
- **`m_ValueType = 7`** = keyword (`strings` pool), **`3`** = dimension (`dimensions` pool).

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

String enum properties use **`m_ValueType = 7`** (keyword); **`valueIndex`** points into **`strings.Array`**.

For the current dump shape in this repo:

- `strings[1] = flex-start`
- `strings[3] = absolute`
- `strings[5] = flex-end`

- **Property [0]** (`align-self`): set to **`flex-start`**.
  - Current JSON handle: **`m_ValueType = 7`**, **`valueIndex = 1`**
- **Property [1]** (`align-content`): `m_ValueType = 7`, `valueIndex = 1`.
- **Property [3]:** rename **`width`** → **`left`**. In serialized USS this is a **length**, not a float literal:
  - **`m_ValueType = 3`** (Dimension), **`valueIndex = 5`**
  - **Section 1c** must set **`dimensions[5] = 26px`** so `left` resolves correctly (same slot as the updated **`right`** on rule `[12]`).
- **Property [4]** (`top`): if vanilla uses **`auto`** (`m_ValueType = 2`), switch it to a **Dimension** handle so layout stays consistent with the patched sheet:
  - **`m_ValueType = 3`**, **`valueIndex = 13`**
  - Grow **`dimensions`** to **14** entries and set **`dimensions[13] = 0px`**

### b. `OverviewHeader.uxml` — reorder controls

In **`m_VisualElementAssets`** (0-based element index in the dump):

- Element **[7]** → **`m_OrderInDocument = 9`**
- Element **[19]** → **`m_OrderInDocument = 7`**

Swaps **MatchUITacticalOptions** vs an empty flex-grow so tactical options sit to the right.

On element **[7]**, in **`m_Classes`**, **add** `margin-right-global-gap-regular` (gap before stats/playback).

### c. Same `inlineStyle` — Match stats / replay (**SISafeAreaElement**)

Authored style is like: `position: absolute; align-self: flex-end; top: 56px; right: 16px`.

In **`dimensions.Array`** (used by the absolute-position rule for the safe-area / stats block — **rule `[12]`** in the current JSON):

| Index   | Original (matches top / right) | Set to | Note                                     |
| ------- | ------------------------------ | ------ | ---------------------------------------- |
| **[4]** | 56 (`top`)                     | **72** | Vertical offset for Match Stats / Replay |
| **[5]** | 16 (`right`)                   | **26** | Inset from the right edge                |

**Also** (for **section 1a** and a consistent **`dimensions`** table):

- Increase **`dimensions`** from **12 → 14** entries if it is still 12 in the source dump.
- Append:
  - **`[12] = 24px`** (used by **`margin-top`** on **rule `[2]`**)
  - **`[13] = 0px`** (**`top`** on **rule `[8]`** property **[4]**)

Adjusts layout after the header / tactical options changes above.

After adding **`dimensions[12]`**, update **rule `[2]` → `margin-top`** to point at **`valueIndex = 12`** so it resolves to **24px** instead of the old **12px** slot.

---

## 2. Pre-match “Kick off” / half time / full time (`ContinuePanel`)

**ContinuePanel.uxml** (-398932524086274306):

- **Element [8]** — add **`align-items-end`** and **`margin-right-global-gap-regular`** to **`m_Classes`** (keep existing classes, e.g. `flex-grow-class`).

**inlineStyle** (4016413755955533566) — **`flex-start` applies only here** (rule **[2]**), not to the UXML class above:

- **Rule [2]** — set **`align-items`** to **`flex-start`**.
  - JSON handle: **`m_ValueType = 7`**, **`valueIndex = 5`** (`strings[5] = flex-start`)
- **Rule [4]** — **`margin-top` = 4px:**
  - Append a **Dimension** entry **`[4] = 4px`**
  - Point **`margin-top`** at **`valueIndex = 4`**

---

## Quick checklist

| Asset / path ID              | Action |
| ---------------------------- | ------ |
| inlineStyle `-2719487159082004763` | Rule **[8]** align + `width`→`left` + `top` handle; **`dimensions`** **14** slots; **[4]/[5]** 72/26; append **[12]/[13] = 24/0**; rule **[2]** `margin-top` **`valueIndex` → 12** (24px) |
| OverviewHeader `-7819803725380563227` | Elements **[7]** / **[19]** order; element **[7]** class |
| ContinuePanel `-398932524086274306` | Element **[8]** classes |
| inlineStyle `4016413755955533566` | Rule **[2]** `align-items = flex-start` (**`valueIndex` 5**); append **`dimensions[4] = 4px`**; rule **[4]** `margin-top` → **4** |
