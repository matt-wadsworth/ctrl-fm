# Navigation next match / calendar hero (UABEA)

- **Patch folder:** `nav-next_match`
- **Bundle:** `ui-calendar_assets_all`
- **Dump format:** serialized asset dump (`.json`)
- **Stock baseline:** `ui-calendar_assets_all/win/orig/` and `ui-calendar_assets_all/mac/orig/`. **Never edit** these exports.
- **Working imports:** `ui-calendar_assets_all/win/*.json` and `ui-calendar_assets_all/mac/*.json` — **same basenames as each platform’s `orig`** (`inlineStyle-CAB-….json`, `CurrentDayWidget-CAB-….json`). Two logical assets ⇒ **two files per OS**.

This note is written so you can **recreate the patch from stock `orig` alone**. There are **no** golden JSON blobs in-repo (nothing like `PATCHED_TEMP/` assumed to ship with the skin). If you kept a disposable export while developing, dispose of it whenever you prefer—everything needed to reapply is below.

Notation **`[n]`** follows **`../UABEA-Notes.md`**: **0-based** indices into **`m_Rules`**, **`m_VisualElementAssets`**, pools (e.g. **`strings`/`dimensions`**), **`references.RefIds`**, etc.—never source line numbers.

Intent mirror **`nextmatch.uxml`** (workspace root): same bind paths/GUIDs/classes where applicable. The dumped asset differs in incidental IDs and still uses **`SIButton`** for the clickable “card” (**`NextMatchInfo`** serialized name), while **`nextmatch.uxml`** nests that under **`SIVisible`** wrappers—match **behaviour/bindings/classes**, not every XML tag.

Patch order: **`inlineStyle`** (§1), then **`CurrentDayWidget`** (§2)—always §1 **before** you rely on **`m_RuleIndex = 0`** for the slim accent stripe.

---

## Assets

| Logical name       | Typical dump tail (Path ID) | Role                                                    |
| ------------------ | --------------------------- | ------------------------------------------------------- |
| `inlineStyle`      | `7705980741400097515`       | Linked from **`CurrentDayWidget.inlineSheet.m_PathID`** |
| `CurrentDayWidget` | `-2536531161352205035`      | Serialized widget asset                                 |

Filenames repeat the CAB hash for this install; IDs may change when FM rebuilds bundles.

---

## 1. `inlineStyle` (`7705980741400097515`)

Apply to the stock companion sheet tied to **`CurrentDayWidget`** (suffix **`7705980741400097515`**).

Stock ships **two keyword rules**:

- **`m_Rules[0]`** — property **`align-self`** → **`m_ValueType = 7`**, **`valueIndex = 0`** with **`strings[0] = flex-start`**.
- **`m_Rules[1]`** — property **`align-items`** → **`m_ValueType = 7`**, **`valueIndex = 1`** with **`strings[1] = center`**.

**Replace** **`m_Rules`** with exactly **one** rule, three properties in this order:

| Property  | Serialized handle                           | Pool target                                             |
| --------- | ------------------------------------------- | ------------------------------------------------------- |
| `opacity` | **`m_ValueType = 2`**, **`valueIndex = 0`** | **`floats[0] = 0.5`**                                   |
| `width`   | **`m_ValueType = 3`**, **`valueIndex = 0`** | **`dimensions[0]`**: **`unit = 1`**, **`value = 1.0`**  |
| `height`  | **`m_ValueType = 3`**, **`valueIndex = 1`** | **`dimensions[1]`**: **`unit = 1`**, **`value = 20.0`** |

- **`strings.Array`** must be empty after the rewrite (no keyword lookups).
- No other pooled entries are required (**`colors`/`assets`/…** stay empty as in vanilla for this sheet).
- **`m_ValueType = 2`** = float pool, **`m_ValueType = 3`** = dimension pool (**same shorthand as **`match-left_scoreboard`** docs**).

This rule becomes **`inlineStyle` rule `[0]`**. Downstream **`m_RuleIndex = 0`** on **`CurrentDayWidget`** targets this stripe.

---

## 2. `CurrentDayWidget` (`-2536531161352205035`)

Stock **`m_VisualElementAssets`** has **9** visual nodes (**`[0]`** UXML through **`[8]`** last **`SIText`**). A fully patched tree from the same FM lineage has **30** nodes there, and **`references.RefIds`** grows **12 → 33**—use those counts as coarse regression checks after rebuilding on a newer export.

### 2.1 `BindingVariables` (stock **`m_VisualElementAssets[1]`**, **`m_Id = 1677764210`**)

| Field                   | Stock                                    | Patch target                                                                                              |
| ----------------------- | ---------------------------------------- | --------------------------------------------------------------------------------------------------------- |
| **`m_OrderInDocument`** | `1`                                      | **`0`** (child of **`m_ParentId = –913746423`**)                                                          |
| **`m_RuleIndex`**       | `0` (used stock inline **`align-self`**) | **`-1`** (layout comes from **`m_Classes`** now)                                                          |
| **`m_Classes.Array`**   | `[]`                                     | **`row-direction-inverted`**, **`align-items-center`**, **`align-self-flex-start`** (**order as listed**) |

Everything else (**`m_SerializedData`**, **`m_Id`**) stays as-exported unless a future FM build shifts the root table.

---

### 2.2 Morph the calendar shell (**stock `m_VisualElementAssets[2]`**)

Stock node is **`SI.Bindable.SIButton`**, **`m_Id = -1546826748`**, **`m_ParentId = 1677764210`** (BindingVariables).

| Field                      | Patch target                                                                                                                                                                                                                                                                                                                                                                              |
| -------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`m_FullTypeName`**       | **`UnityEngine.UIElements.VisualElement`**                                                                                                                                                                                                                                                                                                                                                |
| **`m_OrderInDocument`**    | **`0`** (first child beneath BindingVariables)                                                                                                                                                                                                                                                                                                                                            |
| **`m_PickingMode`**        | **`0`**                                                                                                                                                                                                                                                                                                                                                                                   |
| **`m_Classes.Array`**      | Replace with (**order**): **`navigation-main-calendar-button-default`**, **`margin-left-global-gap-none`**, **`margin-right-global-gap-regular`**, **`padding-left-global-padding-small`**, **`padding-right-global-padding-regular`**, **`custom-padding-calendar-button`** (**remove**: `margin-horizontal-global-gap-none`, `padding-horizontal-global-padding-regular`, `no-margin`). |
| **`m_SerializedData.rid`** | On the FM build documented here this stayed **`1001`** even after the type swap—**reconfirm** after a title update (`SIButton` vs `VisualElement` blobs sometimes diverge). If UABEA flags corruption, recreate the shell as **`VisualElement`** in-editor and export again.                                                                                                              |

**Children** of **`-1546826748`** (existing stock nodes **`m_Id`**: **`–1997584705`**, **`2028947002`**, **`776248296`** plus their **`SIText`** kids) remain structurally adjacent but see §2.3 for field edits.

---

### 2.3 Re-pack the existing calendar subtree (reuse stock \*\*`m_Id`s)

Parent for this section stays **`VisualElement`** **`-1546826748`** (calendar column).

**(a)** **`calender-background-custom`** VE (**`-1997584705`**):

- **`m_OrderInDocument`**: **`0`** (first child).

**(b)** Two-line metadata row VE (**stock `2028947002`**):

- **`m_OrderInDocument`**: **`1`**.
- **`m_RuleIndex`**: **`–1`** (stock used **`1`**, which depended on vanilla rule `[1]`; that rule disappears when §1 is applied—do **not** leave a dangling index).
- Append classes **`justify-content-flex-start`**, **`align-flex-start`** to **`m_Classes.Array`** (keep **`row-direction-normal`**, **`flex-grow-class`**, **`width-100-percent`**).

**(c)** Children **`SIText`** **`–211380839`** and **`–1732515134`** (**parent `2028947002`**):

- Renumber **`m_OrderInDocument`**: **`0`** then **`1`**.
- Replace typography classes toward **12 px secondary** cues (match **`nextmatch.uxml`** row): both should carry **`global-text-secondary`**, **`body-small-12px-regular`**, **`content-no-wrap`**, **`content-ellipsis`**, **`sitext-hover-cursor`**, **`align-self-flex-start`**, **`margin-right-global-gap-small`**. Strip legacy **`fm-universe-small-14px`** on these rows if still present after export.

**(d)** Highlight row VE (**`776248296`**) still parents headline **`1578189045`**:

- **`m_OrderInDocument`**: **`2`** (third child beneath **`-1546826748`**).
- **`m_RuleIndex`**: **`–1`**.
- **`m_Classes`** keeps **`row-direction-normal`**, **`align-self-stretch-class`**—no **`flex-grow`** on this sleeve.
- **`SIText` `1578189045`**: **`m_OrderInDocument = 0`**; prepend **`global-text-secondary`** to **`m_Classes`** ahead of **`content-no-wrap`**, **`content-ellipsis`**, **`fm-universe-small-14px`**, **`sitext-hover-cursor`**.

---

### 2.4 Add the tertiary accent stripe (new `VisualElement`)

Insert a **third direct child** of **`BindingVariables` (`1677764210`)** (**not** under **`-1546826748`**):

| Field                   | Value                                                                                                                                                |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`m_FullTypeName`**    | **`UnityEngine.UIElements.VisualElement`**                                                                                                           |
| **`m_ParentId`**        | **`1677764210`**                                                                                                                                     |
| **`m_OrderInDocument`** | **`2`** (after **`SIVisible` column §2.5 installs at **`1`\*\*)                                                                                      |
| **`m_RuleIndex`**       | **`0`** (points at §1 single rule)                                                                                                                   |
| **`m_Id`**              | **Must be globally unique inside this asset** (example from authorship: **`–884522701`**)                                                            |
| **`m_Classes`**         | **`player-role`**, **`flex-no-shrink-class`**, **`align-self-center-class`**, **`margin-left-global-gap-none`**, **`margin-right-global-gap-small`** |

You must also append a compatible **`references` / `SerializedData`** block for an empty VE (cloned from another VE entry in **`orig`**, authored in UI Builder/UABEA, or merged by **[AI-assisted workflow](../UABEA-Notes.md#ai-assisted-workflow)** comparing fresh vs patched dumps). **`m_SerializedData.rid`** (**`1130`** on original authorship FM build) cannot be pasted blindly onto other builds unless the surrounding **`references`** rows match slot-for-slot—**prefer UABEA to emit** after you create the node interactively once.

Implementation tip: compose the node visually, export JSON, transplant only the subtree + **`references`** deltas.

---

### 2.5 Add the **`SIVisible` + next-match column** (`m_OrderInDocument = 1`)

Between the calendar column (**`m_OrderInDocument = 0`**) and the accent (**`order = 2`**) insert **`SI.Bindable.SIVisible`** (**`2101001001`** in the lineage used to draft this patch—substitute freshly generated IDs on regeneration):

| Field                   | Target                                                                                                                                                                      |
| ----------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`m_ParentId`**        | **`1677764210`**                                                                                                                                                            |
| **`m_OrderInDocument`** | **`1`**                                                                                                                                                                     |
| **`m_PickingMode`**     | **`1`**                                                                                                                                                                     |
| **`m_Classes`**         | **`row-direction-normal`**, **`align-items-center`**                                                                                                                        |
| Binding                 | **`InvertDirectBinding = 0`**, **`Binding.m_kind = 1`**, **`Binding.m_direct.m_path = human.team.IsClub`** (same logical bind as **`nextmatch.uxml`** root **`SIVisible`**) |

Canonical **child outline** (**types + binding intent**) — **`nextmatch.uxml`** parity in parentheses:

1. **`BindingRemapper`** (**`migrate-mappings` / `binding-mappings` → `human.team.NextMatchOpponent`**).
2. Under remapper **`SIButton`** (`NextMatchInfo` serialized name):

   **`m_PickingMode`** = **`1`**; **`m_Classes`** include **`navigation-main-calendar-button-default`** with asymmetric **`margin`** / **`padding`** classes matching **`nextmatch.uxml`**

   (**`navigation-global-diagetic-container`** + horizontal padding knobs + **`sitext-hover-cursor`** family).
   Preserve **`BindTextFunction` / GUID `70afd3ab18d9fed479f22a064d99f893`** (same **`bind-text-function`** as **`nextmatch.uxml`**).

3. Child **`VisualElement`** `section-button-iphone__image` + **`BindingRemapper` club → `human.team.NextMatchOpponent.ClubForObjectLookupData`**.
4. Inner stretch wrapper + **`SIImage`** **`binding kind=1`** visual-script GUID **`7a35874b5cb0acb4d846df3f46c3a872`** (matches **`nextmatch.uxml`**).
5. Content column **`justify-content-flex-start`**, **`align-items-flex-start`** (some stacks use **`align-flex-start`** enums in dumped class strings—mirror **`nextmatch.uxml`** usage-hints equivalents).
6. Row of three **`SIText`** (**`12px` secondary**):
   - **`human.team.NextMatchDaysText`**
   - spacer **`SIText`** (empty / debug spacer)
   - location line (**authoritative dumped bind**): **`human.team.NextMatchLocationString`** (**`nextmatch.uxml`** used **`human.team.NextFixture.MatchVenueShort`**—keep one coherent path across a skin/game cycle)

7. Second **`BindingRemapper`** (**`Team` → `human.team.NextMatchOpponent`**).

8. Opponent **`SIText`**: **`translation-id` `35204`**, **`text-binding`** visual-script asset GUID **`f97abdf5a74842647ba921d183f4a3eb`**.

Additional authoring labels present in **`references`** (search your export after authoring):

- **`NextMatchContainerWrapper`**, **`NextMatchReportWrapper`**, **`NextMatchTextStack`**, **`NextMatchDateRow`**, **`NextMatchOpponentRow`**, **`NextMatchOpponentLeaguePositionWrapper`**, binds on **`human.team.NextMatchLocationString`** and **`human.team.NextMatchOpponent.LeaguePosition`**.

Treat those structs as authoritative when recreating binds that expanded beyond **`nextmatch.uxml`** in the FM UI.

---

### 2.6 `m_TemplateAssets`

Stock keeps one **`StandardTextTooltip`** template sibling. Preserve the **`m_TemplateAlias`** linkage after structural edits (**count stays one** slot in the FM build audited here)—if indices move, reconnect tooltips exactly as UABEA exports for your target build.

---

### 2.7 `references` hygiene

Growing from **12 → 33** **`RefIds`** means **every new `SerializedData.rid`** you introduce must hook into this table (**MonoBehaviour payloads for `BindingRemapper`, `SIButton`, `SIVisible`, nested `VisualElement/UxmlSerializedData`, etc.**).

Practical playbook:

1. Apply §§2.1–2.3 inside UABEA or JSON with minimal **`references`** delta.
2. Author net-new binds / buttons through UABEA’s UI (**preferred** → correct **`rid`** linkage automatically).
3. If diffing programmatically: after mechanical moves, **`grep`** your working JSON for orphaned **`rid`** pointers (present in **`m_SerializedData`** but absent in **`references.RefIds`**).

Pair with **[AI-assisted workflow](../UABEA-Notes.md#ai-assisted-workflow)** whenever you reconcile **`orig`** against another export that is **not** checked into the repo.

---

## 3. Windows vs Mac — rebuild each platform from **`orig` + this note**, not from the other OS

Do **not** maintain Mac by patching Windows first and bulk-remapping IDs. Windows and macOS each get: **stock `{platform}/orig`** → prose **§1** + **§§2.1–2.3** (topology-faithful edits) → merge **`rebuild-data/{platform}.json`** (below)—the frozen bits that prose does not duplicate verbatim.

### 3.1 Baselines & §1 **`inlineStyle`**

- Whenever the CAB changes, refresh **`win/orig`** and **`mac/orig`** from UABEA. **Filename Path ID suffixes** can differ across OS; match **`inlineStyle`** / **`CurrentDayWidget`** by **bundle + logical asset name**.
- **§1** applies independently to **`win/orig`** and **`mac/orig`**. On this CAB lineage the stock **`inlineStyle`** dumps were byte-identical, so patched outputs should coincide—but still rerun §1 starting from **that** **`orig`** after an FM bump.

### 3.2 §§2.1–2.3 **`CurrentDayWidget`** (mechanical, ID-agnostic prose)

**`m_VisualElementAssets`** stock topology matches (**nine** nodes before inserts). **`m_Id` / `m_ParentId` / `references` / rid wiring** vary by OS. All steps in §§2.1–2.3 are written by **document order**, **types**, **class fingerprints**, or **explicit pool literals** — never “use Windows node id **X**”.

Apply those edits to **`{platform}/orig`** so native Mac IDs survive without a Windows→Mac remap pass.

### 3.3 `rebuild-data/win.json` and `rebuild-data/mac.json`

§§**2.4–2.7** (heavy **`references`**, template slots, VE ordering, new subtrees) are kept as **per-OS rebuild payloads**—everything you still need beyond §§2.1–2.3 to match the checked-in **`win/`** / **`mac/`** imports. Each file snapshots **`ve_order_ids`**, **`extra_elements`** (full **`VisualElement`** blobs keyed by stringified **`m_Id`** for nodes whose id is **not** on that platform’s **`orig`**), **`references`**, and **`m_TemplateAssets`**, derived from **`CurrentDayWidget`** patched vs **`orig`** for this lineage.

| File | Platform |
| ---- | -------- |
| **`rebuild-data/win.json`** | **Windows** import JSON (never reuse for Mac verbatim). |
| **`rebuild-data/mac.json`** | **macOS** import JSON (**not** remapped from **`win/`**). |

Rebuild outline for **`{platform}`** ∈ **`win`**, **`mac`**:

1. Start from **`{platform}/orig`** **`CurrentDayWidget`**; apply §§2.1–2.3.
2. Replace root **`references`** with **`references`** from **`rebuild-data/{platform}.json`** once the mechanical layer is stable (**`references`** assumes the patched table for this lineage).
3. Replace **`m_TemplateAssets`** from **`rebuild-data/{platform}.json`** when it differs from stock §2.6 guidance.
4. Merge each **`extra_elements`** entry into **`m_VisualElementAssets.Array`** and set asset order via **`ve_order_ids`** from the same payload.
5. Lint: JSON parses; no orphaned **`rid`**; new ids do not collide with stock **`orig`** ids on **that** platform.

When authored bits change beyond what §§2.1–2.3 list, refresh **`rebuild-data/{platform}.json`** by diffing **`{platform}/orig`** **`CurrentDayWidget`** against the patched **`CurrentDayWidget`** you trust (typically your working **`{platform}`** CAB export—still **not** ported from the other OS).

**No auxiliary Python ships in this repo** for merges—run disposable automation if you prefer, delete it afterward; this **`NAV_NEXT_MATCH_CHANGES.md`** prose + **`rebuild-data/*.json`** stay the repeatable source of truth.

---

## 4. Final checks before import / release

`json` parses; **`inlineSheet.m_PathID`** still **`7705980741400097515`** beside the edited **`inlineStyle`**.

Smoke in portal: crest bind, weekday copy, opponent line, **`IsClub` gating**, calendar column still clickable if desired, accent stripe visible (§1 **`opacity`** `0.5` on the 1×20 px sleeve).

**In-engine:** Windows path verified for this skin lineage. Re-smoke on **macOS** after importing **`mac/*.json`**; re-smoke both OS after any FM bundle update.
