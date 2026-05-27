# Match scoreboard — left aligned (UABEA)

- **Patch folder:** `match-left_scoreboard`
- **Bundle:** `ui-match_assets_all`
- **Dump format:** serialized asset dump (`.json`)
- **Working copies:** `ui-match_assets_all/win/` (copy from `win/orig` or `mac/orig` before editing)
- **Stock only:** `win/orig/`, `mac/orig/` — do not modify; keep as the unpatched baseline.

Edits in **ui-match_assets_all** for in-match **OverviewHeader** and interval / half-time **ContinuePanel** HUD (scoreboard + Tactics / continue buttons).

## Implementation notes

- Notation **`[n]`** (rules, elements, pools, **`RefIds`**) means **0-based array index** into the named array in the JSON dump — see **[Bracket notation and the `references` object](../UABEA-NOTES.md#bracket-notation-and-the-references-object)** in **`UABEA-NOTES.md`**.
- Copy the four JSON assets from **`orig`** into **`win/`** or **`mac/`**, then apply the sections below. Rule indices and `m_VisualElementAssets` positions are for the **current** **`orig`** shape; after a title update, re-export, diff against **`orig`**, and adjust indices if the tree changes.
- **`m_Rules` indices are 0-based.** “Rule `[8]`” is the ninth entry in `m_Rules.Array`.
- **`m_ValueType = 7`** = keyword (`strings` pool), **`3`** = dimension (`dimensions` pool).
- **`ContinuePanel` tree:** serialized UXML is split into **`m_VisualElementAssets`** (main tree) and **`m_TemplateAssets`** (template instances such as **`dashboard-hud-continue_button-default`**). **Element indices** like “**[8]**” refer to **`m_VisualElementAssets.Array`** only (0-based), not document order (`m_OrderInDocument`).

---

## Path IDs

| Bundle              | Filename          | Path ID              | Role                                      |
| ------------------- | ----------------- | -------------------- | ----------------------------------------- |
| ui-match_assets_all | `OverviewHeader`  | -7819803725380563227 | In-match header UXML                       |
| ui-match_assets_all | `inlineStyle`     | -2719487159082004763 | Styles for OverviewHeader + safe area     |
| ui-match_assets_all | `ContinuePanel`   | -398932524086274306  | Interval / half-time HUD (scoreboard + buttons) |
| ui-match_assets_all | `inlineStyle`     | 4016413755955533566  | Styles for ContinuePanel                        |

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

## 2. Pre-match / interval / half-time HUD (`ContinuePanel`)

Goal: **scoreboard** left inset matches **OverviewHeader** (§1); **Tactics / Instructions / Shouts** and **Address the Team** stay **right-aligned** on the same row, vertically aligned with each other.

### Layout (current dump)

```
m_VisualElementAssets [1]  BindingRoot          rule [0]  padding top/left/right
└─ [2] BindableSwitchElement (flex-grow)
   └─ [3] row
      ├─ … → [7] fm-scoreboard                    rule [2]  align-items flex-start
      └─ [8] flex-grow spacer                     classes   align-items-end, margin-right-global-gap-regular
         └─ … → match-header                      rule [4]  margin-top 4px
            ├─ MatchUITacticalOptions (Tactics …)
            └─ continue button (Address the Team) rule [5]  align-self flex-start
```

- **`padding-top` on rule [0]** lifts the **whole row** (scoreboard + buttons). Tuned to **24px** to match OverviewHeader **`match-header`** **`margin-top`** (§1c).
- **`padding-left` / `padding-right` on rule [0]** (**42px** / **26px**) replace stock **`padding-horizontal-global-padding-regular`** on element **[1]** so the scoreboard left edge and screen right inset match OverviewHeader (**`left: 26px`**, skin **`match-header { margin-right: 26px }`**).
- **`align-items-end` on element [8]** right-aligns the button block inside the flex-grow column. **Do not remove** after padding changes — without it, Tactics / Instructions hug the left beside the scoreboard.
- **`margin-top` on rule [4]** (**4px**) nudges the **`match-header`** button row down slightly. **Do not** set this to **24px** while rule **[0]** already has **`padding-top: 24px`** — that doubled the top inset and misaligned Tactics vs **Address the Team**.

### ContinuePanel UXML (-398932524086274306)

**Element [1]** — drop **`padding-horizontal-global-padding-regular`** from **`m_Classes`** (keep **`row-direction-normal`**). Horizontal inset moves to inlineStyle rule **[0]**.

**Element [8]** (0-based) — the **`UnityEngine.UIElements.VisualElement`** whose stock **`m_Classes`** are only **`flex-grow-class`** (`m_RuleIndex` **`-1`**; Mac **`m_Id`** `-1942089278`, **`m_ParentId`** `1819372858`). Another node also uses **`flex-grow-class`** — pick index **[8]**, not a **`BindableSwitchElement`**.

| Class | Action |
| ----- | ------ |
| **`flex-grow-class`** | keep (stock) |
| **`align-items-end`** | **add** |
| **`margin-right-global-gap-regular`** | **add** |

**What stays stock:** Parsed JSON should match **`orig`** everywhere except **`m_VisualElementAssets.Array[1].m_Classes`** and **`Array[8].m_Classes`**. Do not rewrite **`references`**, **`m_TemplateAssets`**, or other element rows.

**Do not:** Copy **`ContinuePanel`** from Windows into Mac (or mix builds). Element **`m_Id`** values and **`references`** are platform-specific.

### ContinuePanel `inlineStyle` (4016413755955533566)

**`dimensions` pool (patched)** — append slots beyond stock as needed:

| Index | Value | Used by |
| ----- | ----- | ------- |
| **[0]** | **24px** | rule **[0]** **`padding-top`** / **`padding-bottom`** |
| **[4]** | **4px** | rule **[4]** **`margin-top`** |
| **[5]** | **42px** | rule **[0]** **`padding-left`** |
| **[6]** | **26px** | rule **[0]** **`padding-right`** |

Stock **`dimensions[0]`** was **6px**; **`dimensions[4]`** did not exist (rule **[4]** **`margin-top`** pointed at **[2]** = **12px**).

**Rule [2]** — **`fm-scoreboard`** (**element [7]**, **`m_RuleIndex` 2**):

- **`align-items`** → **`flex-start`**
- JSON: **`m_ValueType = 7`**, **`valueIndex = 5`** (`strings[5] = flex-start`)

**Rule [0]** — root row (**element [1]**, **`m_RuleIndex` 0**):

- **`padding-top`** → **24px** (`dimensions[0]`, **`valueIndex` 0**)
- **`padding-left`** → **42px** — add property; **`valueIndex` 5**
- **`padding-right`** → **26px** — add property; **`valueIndex` 6**

**Rule [4]** — continue-side **`match-header`** (**element with class **`match-header`**, **`m_RuleIndex` 4**):

- **`margin-top`** → **4px** — point at **`dimensions[4]`**, **`valueIndex` 4**

### ContinuePanel pitfalls

| Mistake | Symptom |
| ------- | ------- |
| **`padding-top` 24** on rule **[0]** **and** **`margin-top` 24** on rule **[4]** | Tactics row ~24px lower than **Address the Team** |
| Remove **`align-items-end`** from element **[8]** | Button group shifts left, beside scoreboard |
| Keep **`padding-horizontal-global-padding-regular`** on **[1]** while also setting rule **[0]** **`padding-left`** | Double horizontal inset on the left |
| Omit rule **[0]** **`padding-right` 26** after dropping horizontal padding class | Continue / tactics UI clips off the right edge |

---

## Quick checklist

| Asset / path ID              | Action |
| ---------------------------- | ------ |
| inlineStyle `-2719487159082004763` | Rule **[8]** align + `width`→`left` + `top` handle; **`dimensions`** **14** slots; **[4]/[5]** 72/26; append **[12]/[13] = 24/0**; rule **[2]** `margin-top` **`valueIndex` → 12** (24px) |
| OverviewHeader `-7819803725380563227` | Elements **[7]** / **[19]** order; element **[7]** class |
| ContinuePanel `-398932524086274306` | **[1]** drop **`padding-horizontal-global-padding-regular`**; **[8]** add **`align-items-end`**, **`margin-right-global-gap-regular`** |
| inlineStyle `4016413755955533566` | **[2]** `align-items` flex-start; **[0]** pad **24 / 42 / 26**; **[4]** `margin-top` **4px**; **`dimensions[4/5/6]`** = **4 / 42 / 26** |
