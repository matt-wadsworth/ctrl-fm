# Match scoreboard — left aligned (UABEA)

- **Patch folder:** `match-left_scoreboard`
- **Bundle:** `ui-match_assets_all`
- **Dump format:** `.txt`
- **Dumps in repo:** `ui-match_assets_all/win/` (add `mac/` here if you export Mac)

Edits in **ui-match_assets_all** for in-match **OverviewHeader** and pre-match / interval **ContinuePanel** alignment.

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

Locate **style index `[8]`** (notation `7-1` = `m_ValueType = 7`, `valueIndex = 1`):

- **Rule [0]:** `m_ValueType = 7`, `valueIndex = 1`
- **Rule [1]:** `m_ValueType = 7`, `valueIndex = 1`
- **Rule [3]:**
  - Rename property from **`width`** to **`left`**
  - `m_ValueType = 2`, `valueIndex = 0`

### b. `OverviewHeader.uxml` — reorder controls

Update **m_OrderInDocument** (by existing element IDs):

- Element at index **[7]** → **m_OrderInDocument = 9**
- Element at index **[19]** → **m_OrderInDocument = 7**

Swaps **MatchUITacticalOptions** vs an empty flex-grow so tactical options sit to the right.

On element **[7]**, in **m_Classes**, **add** `margin-right-global-gap-regular` (gap before stats/playback).

### c. Same `inlineStyle` — Match stats / replay (**SISafeAreaElement**)

Authored style is like: `position: absolute; align-self: flex-end; top: 56px; right: 16px`.

In **Dimensions**:

| Index   | Original (matches top / right) | Set to | Note                                     |
| ------- | ------------------------------ | ------ | ---------------------------------------- |
| **[4]** | 56 (`top`)                     | **72** | Vertical offset for Match Stats / Replay |
| **[5]** | 16 (`right`)                   | **26** | Inset from the right edge                |

Adjusts layout after the header / tactical options changes above.

---

## 2. Pre-match “Kick off” / half time / full time (`ContinuePanel`)

**ContinuePanel.uxml** (-398932524086274306):

- **Element [8]** — add **`align-items-end`** and **`margin-right-global-gap-regular`** to **m_Classes**

**inlineStyle** (4016413755955533566):

- **Rule [2]** — set `align-items` to `m_ValueType = 7`, `valueIndex = 5`
- **Rule [4]** — change `margin-top` to **4px** (add to **Dimensions** if needed)
