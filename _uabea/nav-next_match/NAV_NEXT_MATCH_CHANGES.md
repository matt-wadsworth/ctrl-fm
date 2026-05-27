# Navigation next match / calendar hero (UABEA)

- **Patch folder:** `nav-next_match`
- **Bundle:** `ui-calendar_assets_all`
- **Dump format:** serialized asset dump (`.json`)
- **Stock baseline:** `ui-calendar_assets_all/win/orig/` and `ui-calendar_assets_all/mac/orig/`. **Never edit** these exports.
- **Working imports:** `ui-calendar_assets_all/win/*.json` and `ui-calendar_assets_all/mac/*.json` — **same basenames as each platform’s `orig`** (`inlineStyle-CAB-….json`, `CurrentDayWidget-CAB-….json`). Two logical assets ⇒ **two files per OS**.

This note is written so you can **recreate the patch from stock `orig` alone**. There are **no** golden JSON blobs in-repo (nothing like `PATCHED_TEMP/` assumed to ship with the skin). If you kept a disposable export while developing, dispose of it whenever you prefer—everything needed to reapply is below.

Notation **`[n]`** follows **`../UABEA-Notes.md`**: **0-based** indices into **`m_Rules`**, **`m_VisualElementAssets`**, pools (e.g. **`strings`/`dimensions`**), **`references.RefIds`**, etc.—never source line numbers.

Intent mirror **`nextmatch.uxml`** (workspace root): same bind paths/GUIDs/classes where applicable. The dumped asset differs in incidental IDs and still uses **`SIButton`** for the clickable “card” (**`NextMatchInfo`** serialized name), while **`nextmatch.uxml`** nests that under **`SIVisible`** wrappers—match **behaviour/bindings/classes**, not every XML tag.

Patch order: **`inlineStyle`** (§1), then **`CurrentDayWidget`** (§2)—always §1 **before** you rely on **`m_RuleIndex = 0`** (accent stripe).

---

## Assets

| Logical name       | Typical dump tail (Path ID) | Role                                                    |
| ------------------ | --------------------------- | ------------------------------------------------------- |
| `inlineStyle`      | `7705980741400097515`       | Linked from **`CurrentDayWidget.inlineSheet.m_PathID`** |
| `CurrentDayWidget` | `2536531161352205035`       | Serialized widget asset                                 |

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
- **`floats`**: **`[0.5]`** only; **`dimensions`**: **`[1×1, 20×1]`**.
- **`m_ValueType = 2`** = float pool, **`m_ValueType = 3`** = dimension pool.

This rule becomes **`inlineStyle` rule `[0]`**. Downstream **`m_RuleIndex = 0`** on the accent **`VisualElement`** (§2.4) targets this stripe. **Nation and club opponent icons** both use the stock **`section-button-iphone__image`** class (**26 × 26** square)—no second inline rule.

---

## 2. `CurrentDayWidget` (`-2536531161352205035`)

Stock **`m_VisualElementAssets`** has **9** visual nodes (**`[0]`** UXML through **`[8]`** last **`SIText`**). A fully patched tree from the same FM lineage has **35** nodes there, and **`references.RefIds`** grows **12 → 38**—use those counts as coarse regression checks after rebuilding on a newer export.

All **`m_direct`** bind fields must use the **Path + Nullable** wrapper documented in **[`../UABEA-Notes.md`](../UABEA-Notes.md#dump-format)** on **both** platforms.

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

Between the calendar column (**`m_OrderInDocument = 0`**) and the accent (**`order = 2`**) insert **`SI.Bindable.SIVisible`** (**`2101001001`** / **`NextMatchContainerWrapper`** in the lineage used to draft this patch—substitute freshly generated IDs on regeneration):

| Field                   | Target                                                                                                                                                                      |
| ----------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`m_ParentId`**        | **`1677764210`**                                                                                                                                                            |
| **`m_OrderInDocument`** | **`1`**                                                                                                                                                                     |
| **`m_PickingMode`**     | **`1`**                                                                                                                                                                     |
| **`m_Classes`**         | **`row-direction-normal`**, **`align-items-center`**                                                                                                                        |
| Visibility bind         | **`Binding.m_kind = 2`**, empty **`m_direct.Path.m_path`**, visual-script GUID **`4a1becfc0a0d04816b13fd8f330a9207`** (shows the block when a next fixture exists—not club gating) |
| **`PreviewVisibilityToggle`** | **`1`**                                                                                                                                                              |

**Do not** bind this root **`SIVisible`** to **`team.IsClub`**—nation managers must see the column. Club vs nation **icon** switching lives in §2.8.

Canonical **child outline** (**types + binding intent**) — **`nextmatch.uxml`** parity in parentheses:

1. **`BindingRemapper`** (**`2101001013`**) — **`MigrateMappings` / mapping → `human.team.NextMatchOpponent`**.
2. Inner **`SIVisible`** **`NextMatchReportWrapper`** (**`2101001014`**).
3. Row **`VisualElement`** / serialized **`NextMatchInfo`** (**`2101001002`**) — clickable card:

   **`m_PickingMode`** = **`1`**; **`m_Classes`** include **`navigation-main-calendar-button-default`** with asymmetric **`margin`** / **`padding`** classes matching **`nextmatch.uxml`**
   (**`navigation-global-diagetic-container`** + horizontal padding knobs + **`sitext-hover-cursor`** family).
   Preserve **`BindTextFunction` / GUID `70afd3ab18d9fed479f22a064d99f893`** (same **`bind-text-function`** as **`nextmatch.uxml`**).

4. **§2.8 opponent icon column** — first child **`NextMatchOpponentRemapper`** + **`BindableSwitchElement`** (club **or** nation **`SIImage`**).
5. Content column **`NextMatchTextStack`** (**`2101001009`**) — **`justify-content-flex-start`**, **`align-flex-start`**.
6. Row of **`SIText`** (**`12px` secondary**):
   - **`human.team.NextMatchDaysText`**
   - spacer **`SIText`** (empty / debug spacer)
   - location line: **`human.team.NextMatchLocationString`**
7. Second **`BindingRemapper`** (**`Team` → `human.team.NextMatchOpponent`**).
8. Opponent **`SIText`**: **`translation-id` `35204`**, **`text-binding`** visual-script GUID **`f97abdf5a74842647ba921d183f4a3eb`**.

Additional authoring labels present in **`references`** (search your export after authoring):

- **`NextMatchContainerWrapper`**, **`NextMatchReportWrapper`**, **`NextMatchTextStack`**, **`NextMatchDateRow`**, **`NextMatchOpponentRow`**, **`NextMatchOpponentLeaguePositionWrapper`**, **`NextMatchOpponentRemapper`**, **`BindableSwitchElement_NationOrClub`**, **`NationRemapper`**, **`ClubRemapper`**, **`NextMatchOpponentNationIcon`**, **`NextMatchOpponentClubIcon`**, **`NationImage`**, **`ClubImage`**, binds on **`human.team.NextMatchLocationString`** and **`human.team.NextMatchOpponent.LeaguePosition`**.

Treat those structs as authoritative when recreating binds that expanded beyond **`nextmatch.uxml`** in the FM UI.

---

### 2.8 Nation / club opponent icons (`BindableSwitchElement`)

Insert inside **`NextMatchInfo`** (**`2101001002`**) as **`m_OrderInDocument = 0`** (text stack stays **`1`**).

```
NextMatchOpponentRemapper (2101001028) — MigrateMappings: human.team.NextMatchOpponent
└── BindableSwitchElement_NationOrClub (2101001010) — Binding.m_kind = 1, Path: team.IsClub
    ├── NationRemapper (2101001011) — m_OrderInDocument = 0  [false / nation branch]
    │   └── NextMatchOpponentNationIcon (2101001029) — m_RuleIndex = -1
    │       └── NationImage (2101001030) — SIImage
    └── ClubRemapper (2101001026) — m_OrderInDocument = 1  [true / club branch]
        └── NextMatchOpponentClubIcon (2101001012)
            └── ClubImage (2101001027) — SIImage
```

#### `BindableSwitchElement` (**`2101001010`**)

| Field | Target |
| ----- | ------ |
| **`BindDirectAsMask`** | **`0`** |
| **`Binding.m_kind`** | **`1`** (direct) |
| **`Binding.m_direct`** | **`Path.m_path = team.IsClub`**, **`Nullable = 0`** |
| **`Preview.Mask`** | **`2`** |
| Child **`0`** | Nation branch (**shown when `IsClub` is false**) |
| Child **`1`** | Club branch (**shown when `IsClub` is true**) |

#### `NationRemapper` (**`2101001011`**)

| `from` | `to.m_path` | type id (this lineage) |
| ------ | ----------- | ---------------------- |
| **`nation`** | **`team.Nation`** | **`1145176074`** |
| **`NTcontainer`** | **`team.NationalTeamContainer`** | **`1145176073`** |

#### `ClubRemapper` (**`2101001026`**)

| `from` | `to.m_path` | type id (this lineage) |
| ------ | ----------- | ---------------------- |
| **`club`** | **`team.ClubForObjectLookupData`** | **`1145176067`** |

#### Nation wrapper **`NextMatchOpponentNationIcon`** (**`2101001029`**)

| Field | Target |
| ----- | ------ |
| **`m_RuleIndex`** | **`-1`** |
| **`m_Classes`** | **`section-button-iphone__image`**, **`margin-left-global-gap-small`**, **`margin-right-global-gap-regular`**, **`sitext-hover-cursor`** (same sleeve as club) |

#### Club wrapper **`NextMatchOpponentClubIcon`** (**`2101001012`**)

| Field | Target |
| ----- | ------ |
| **`m_RuleIndex`** | **`-1`** |
| **`m_Classes`** | **`section-button-iphone__image`**, **`margin-left-global-gap-small`**, **`margin-right-global-gap-regular`**, **`sitext-hover-cursor`** |

#### `NationImage` (**`2101001030`**) — **`SIImage`**

| Field | Target |
| ----- | ------ |
| **`m_Classes`** | **`margin-right-global-gap-none`**, **`sitext-hover-cursor`** (match club) |
| **`ScaleMode`** | **`0`** (match club) |
| **`ScaleMode_UxmlAttributeFlags`** | **`0`** |
| **`Binding.m_kind`** | **`2`** (visual function) |
| **`Binding.m_direct`** | empty path + **`Nullable = 0`** |
| Visual-script GUID | **`7a35874b5cb0acb4d846df3f46c3a872`** (nation lookup inputs in **`m_argumentBytes`**) |

#### `ClubImage` (**`2101001027`**) — **`SIImage`**

| Field | Target |
| ----- | ------ |
| **`m_Classes`** | **`margin-right-global-gap-none`**, **`sitext-hover-cursor`** |
| **`ScaleMode`** | **`0`** |
| **`Binding.m_kind`** | **`2`** (visual function) |
| **`Binding.m_direct`** | empty path + **`Nullable = 0`** |
| Visual-script GUID | **`7a35874b5cb0acb4d846df3f46c3a872`** (club lookup inputs in **`m_argumentBytes`**) |

**Implementation notes:**

- Use plain **`SIImage`** nodes—not **`NationIcon` / `ClubIcon` template assets**.
- Nation and club wrappers share **`section-button-iphone__image`** (**26 × 26** square). Wide flag source art will letterbox inside the square slot.

---

### 2.6 `m_TemplateAssets`

Stock keeps one **`StandardTextTooltip`** template sibling. Preserve the **`m_TemplateAlias`** linkage after structural edits (**count stays one** slot in the FM build audited here)—if indices move, reconnect tooltips exactly as UABEA exports for your target build.

---

### 2.7 `references` hygiene

Growing from **12 → 38** **`RefIds`** means **every new `SerializedData.rid`** you introduce must hook into this table (**MonoBehaviour payloads for `BindingRemapper`, `BindableSwitchElement`, `SIImage`, `SIVisible`, nested `VisualElement/UxmlSerializedData`, etc.**).

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
- **§1** applies independently to **`win/orig`** and **`mac/orig`**. On this CAB lineage the stock **`inlineStyle`** dumps were byte-identical; patched outputs should coincide (single accent rule)—still rerun §1 from **that** **`orig`** after an FM bump.
- Every **`m_direct`** block in working imports must use **`Path` + `Nullable`** (see **[`../UABEA-Notes.md`](../UABEA-Notes.md#dump-format)**). Mac and Windows share the same wrapper syntax in UABEA Next.

### 3.2 §§2.1–2.3 **`CurrentDayWidget`** (mechanical, ID-agnostic prose)

**`m_VisualElementAssets`** stock topology matches (**nine** nodes before inserts). **`m_Id` / `m_ParentId` / `references` / rid wiring** vary by OS. All steps in §§2.1–2.3 are written by **document order**, **types**, **class fingerprints**, or **explicit pool literals** — never “use Windows node id **X**”.

Apply those edits to **`{platform}/orig`** so native Mac IDs survive without a Windows→Mac remap pass.

### 3.3 `rebuild-data/win.json` and `rebuild-data/mac.json`

§§**2.4–2.8** (heavy **`references`**, template slots, VE ordering, new subtrees including §2.8 icon switch) are kept as **per-OS rebuild payloads**—everything you still need beyond §§2.1–2.3 to match the checked-in **`win/`** / **`mac/`** imports. Each file snapshots **`ve_order_ids`**, **`extra_elements`** (full **`VisualElement`** blobs keyed by stringified **`m_Id`** for nodes whose id is **not** on that platform’s **`orig`**), **`references`**, and **`m_TemplateAssets`**, derived from **`CurrentDayWidget`** patched vs **`orig`** for this lineage.

**Regression counts (this FM lineage):** **`35`** **`ve_order_ids`**, **`26`** **`extra_elements`**, **`38`** **`RefIds`** — identical on **`win`** and **`mac`**.

| File                        | Platform                                                  |
| --------------------------- | --------------------------------------------------------- |
| **`rebuild-data/win.json`** | **Windows** import JSON (never reuse for Mac verbatim).   |
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

**`inlineStyle`:** one accent rule; **`dimensions`** **`[1, 20]`**; nation and club wrappers both use **`section-button-iphone__image`**.

**`CurrentDayWidget`:** **`35`** VE nodes; **`38`** **`RefIds`**; all **`m_direct`** fields use **`Path` + `Nullable`**.

Smoke in portal:

- **Club manager:** opponent crest loads in **`section-button-iphone__image`** slot; text rows bind.
- **Nation manager:** next-match column **visible**; nation emblem loads in the **same 26 × 26** square slot as club crests.
- Weekday copy, opponent line, calendar column still clickable, accent stripe visible (§1 rule **`[0]`** **`opacity`** `0.5` on the 1×20 px sleeve).

**In-engine:** Re-smoke **both** OS after importing **`win/*.json`** or **`mac/*.json`** respectively; re-smoke after any FM bundle update.
