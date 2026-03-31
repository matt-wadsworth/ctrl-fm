# Match scoreboard — left aligned (UABEA)

- **Patch folder:** `match-left_scoreboard`
- **Bundle:** `ui-match_assets_all`
- **Dump format:** serialized asset dump (`.json`)
- **Working copies:** `ui-match_assets_all/win/` (copy from `win/orig` or `mac/orig` before editing)
- **Stock only:** `win/orig/`, `mac/orig/` — do not modify; keep as the unpatched baseline.

Edits in **ui-match_assets_all** for in-match **OverviewHeader** and pre-match / interval **ContinuePanel** alignment.

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

**ContinuePanel** (-398932524086274306) — **`m_VisualElementAssets`**:

- **Element [8]** (0-based) — the **`UnityEngine.UIElements.VisualElement`** whose **`m_Classes`** in stock are only **`flex-grow-class`** (`m_RuleIndex` **`-1`**; in the current Mac dump, **`m_Id`** `-1942089278`, **`m_ParentId`** `1819372858`). (Another node earlier in the tree also uses **`flex-grow-class`** — pick the **`VisualElement`** at index **[8]**, not **`BindableSwitchElement`**.) **Add** **`align-items-end`** and **`margin-right-global-gap-regular`** (keep **`flex-grow-class`**).

**What stays stock:** For the same game build, **`mac/orig`** vs the working **`mac/`** copy should match **everywhere else** when parsed as JSON: the root **`references`** object (**`references.RefIds`** and nested serialized data), **`m_ContentHash`**, **`m_TemplateAssets`**, **`m_SerializedDataOverride`** / **`m_ElementIdsPath`**, **`m_PickingMode`**, and all other **`m_VisualElementAssets`** rows. A structural diff should show **only** **`m_VisualElementAssets.Array[8].m_Classes.Array`** growing from one entry to three. If your editor or UABEA **reformats** the file (e.g. compact **`m_argumentBytes`** vs one number per line), line counts will differ wildly; **semantic** equality to **`orig`** except for **[8]** classes is what matters.

**Do not:** Copy **`ContinuePanel`** from **Windows** into **Mac** (or mix builds). Element **`m_Id`** values and the **`references`** object are platform/build-specific; bad merges look like “fix picking” or **`m_ElementId`** rewrites — **start from your platform’s `orig`**, apply **only** the **[8]** class list above, and leave **`references`** alone unless you know the asset is corrupted.

**inlineStyle** (4016413755955533566) — rule **[2]** applies to **`fm-scoreboard`** (**`m_VisualElementAssets` element [7]**, **`m_RuleIndex` 2**), not to element **[8]** above (that row uses **USS classes** only):

- **Rule [2]** — set **`align-items`** to **`flex-start`**.
  - JSON handle: **`m_ValueType = 7`**, **`valueIndex = 5`** (`strings[5] = flex-start`)
- **Rule [4]** — **`margin-top` = 4px** (e.g. **`SISafeAreaElement`** / match header in this sheet’s wiring):
  - Append a **Dimension** entry **`[4] = 4px`**
  - Point **`margin-top`** at **`valueIndex = 4`**

---

## Quick checklist

| Asset / path ID              | Action |
| ---------------------------- | ------ |
| inlineStyle `-2719487159082004763` | Rule **[8]** align + `width`→`left` + `top` handle; **`dimensions`** **14** slots; **[4]/[5]** 72/26; append **[12]/[13] = 24/0**; rule **[2]** `margin-top` **`valueIndex` → 12** (24px) |
| OverviewHeader `-7819803725380563227` | Elements **[7]** / **[19]** order; element **[7]** class |
| ContinuePanel `-398932524086274306` | **`m_VisualElementAssets` [8]** — add **`align-items-end`**, **`margin-right-global-gap-regular`**; nothing else (verify: parsed JSON vs `orig` = only this **`m_Classes`** change) |
| inlineStyle `4016413755955533566` | Rule **[2]** `align-items = flex-start` (**`valueIndex` 5**); append **`dimensions[4] = 4px`**; rule **[4]** `margin-top` → **4** |
