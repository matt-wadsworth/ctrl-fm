# UABEA changes (index)

Manual changes to apply in UABEA during skin development after building the skin.

**Treat this file as the process reference:** folder layout (**`win/orig`**, **`mac/orig`** baselines beside working imports per patch slug, plus any **optional local-only** scratch such as **`PATCHED_TEMP`** that must **never** substitute for **`CHANGES.md`**), branching rules, **`[n]`** semantics, **`references`**, AI workflow, registry tables, and the note template. Individual **`_uabea/<patch-slug>/…_CHANGES.md`** notes stay the source of truth for **exact edits** inside each bundle subset.

---

## Creating new patch folders

Use whenever you introduce **`_uabea/<patch-slug>/`**:

1. **Baselines:** Export stock bundles from UABEA into **`win/orig`** / **`mac/orig`** next to patched **`win/`** / **`mac/`** dumps (exact tree mirrors each **`…_CHANGES.md`** — **never edit** baseline files after they represent stock).
2. **Working dumps:** Copy those JSON assets into **`win/`** / **`mac/`** (siblings of the baseline folder) and edit **only** the copies intended for import.
3. **`PATCHED_TEMP/` (optional, local scratch only):** Ephemeral exports while iterating—they should **never** become the authoritative doc input. Fold results into **`CHANGES*.md`** and checked-in **`win`** / **`mac`** JSON, then discard the scratch folder unless you explicitly keep it offline.
4. **Document:** Add **`_uabea/<patch-slug>/<DESCRIPTIVE_NAME>_CHANGES.md`** using the **[Note template](#note-template)**. Prefer instruction-first wording; record concrete pool values whenever they matter.
5. **Register:** Add a row under **[patch documentation](#patch-documentation)**, extend **[patched bundles](#patched-bundles)** if the bundle is new to the list, append **[Asset index](#asset-index)** rows (**logical filename + Path ID + patch slug**).
6. **Platforms:** Repeat the **same logical** patch Mac-side from that platform’s baseline. IDs and CAB suffixes **differ**; do not blindly paste Windows **`references` / `rid`** blocks into Mac dumps. For **`nav-next_match`**, per-OS **`rebuild-data/win.json`** / **`mac.json`** (see **`NAV_NEXT_MATCH_CHANGES.md` §3**) hold the heavy **`references`** / VE merge blobs—avoid treating a Windows-remapped dump as canonical for Mac rebuilds.
7. **Validate:** JSON parses; smoke test covers the surfaced UI path.

Structural patches (broken **`references`**, large **`m_VisualElementAssets`** diffs, **BindingRemapper** inserts) belong in **[AI-assisted workflow](#ai-assisted-workflow)**—final notes must still list **measurable targets** (`m_Rules` shapes, pooled numbers, checklist of tree goals) even when wholesale JSON diff is simpler than hand-steps.

---

## Working rules

- Stock exports stay under **`win/orig`** / **`mac/orig`** (layout per **`_uabea/<patch-slug>/…`** patch note — typically **`orig/` as a subdirectory of each OS folder** beside working JSON). Patched JSON you re-import lives in **`win/`** and **`mac/`** (**same filenames** as baselines where applicable). Apply the patch note starting from **that platform’s baseline** export; compare **`win`** vs **`mac`** once both match the documented intent.
- Patch docs describe the logical change and target values—not only the incidental shape of one platform dump.
- Use the dumped **filename suffix (Path ID)** together with the logical asset **name** to find the asset again inside a bundle.
- The Path ID is the **`…-PATHID`** tail of UABEA export filenames (it may begin with **`–`**).
- For colours, sizes, pooled dimensions, pooled strings, and similar values, spell out **target literals** in the patch note unless the note explicitly delegates to diff-only workflow.
- Windows and Mac are one logical patch with different identifiers; ignore platform IDs in prose unless divergence is real.
- Docs stay instruction-first.
- **`[n]`** in patch notes = **0-based index** into the named JSON array (rules, **`m_VisualElementAssets`**, pools, **`references.RefIds`**, …) — never “source line”; see **[Bracket notation and the `references` object](#bracket-notation-and-the-references-object)**.

## Dump format

The repo is documented around UABEA Next JSON dumps.

- Patch notes describe the edit in bundle / asset terms, not around disposable export quirks.
- Serialized USS / UXML trees remain easiest as JSON for diff tools.
- **`Binding.m_direct` / `TextBinding.m_direct`** (and the same shape on **`SIImage`**, **`SIText`**, **`SIVisible`**, **`BindableSwitchElement`**, etc.) use the **Path + Nullable wrapper** on **both Windows and macOS**:

```json
"m_direct": {
  "Path": {
    "m_path": "human.team.NextMatchOpponent"
  },
  "Nullable": 0
}
```

  Do **not** leave the legacy flat `"m_direct": { "m_path": "…" }` shape in working imports—UABEA Next expects the wrapper. **`BindingRemapper.Mappings[].to`** and similar mapping structs may still use flat **`m_path`**; only **`m_direct`** on bind fields uses the wrapper.

### Bracket notation and the `references` object

- **`[n]`** always means **0-based index into that array** unless explicitly stated otherwise. Examples: **`rule [2]`** → `m_Rules.Array[2]`; **`element [8]`** → `m_VisualElementAssets.Array[8]`; **`dimensions[4]`** → `dimensions.Array[4]`; **`strings[5]`** → `strings.Array[5]`. Not a Unity **`rid`** unless the text maps index → **`rid`**.
- Root **`references`** (plus **`references.RefIds`**) is the serialized **reference table** for **that asset**. “Leave **`references`** alone” refers to maintaining that table’s coherence; do not casually orphan **`rid`** handlers.
- **`references.RefIds[n]`** selects the nth entry (**0-based**); each carries its own **`rid`**. **`n`** ≠ **`rid`**.

## AI-assisted workflow

Recommended when patching involves USS rule math, pooled remaps, UXML rewires, **`references.RefIds`** growth, or recovering intent from **`orig` ↔ known-good**.

1. Export (or load **`win/orig`** / **`mac/orig`** baselines) alongside whatever deltas you legally have (**committed patched JSON**, your working **`win`** / **`mac`** tree, authoring UXML, prior FM cycle notes—never treat an undocumented lone export folder as canonical).

2. Derive **logical diff** first (**`m_Rules`**, pools, inserted or moved nodes, **`rid`** rewires)—AI works well summarizing—but **carry literal targets forward** manually.

3. Refresh **`CHANGES.md`** with bundles, filenames, Path IDs, **literal pool values**, and structural checklists so the next exporter does not rely on orphaned scratch dumps.

4. Apply edits to working JSON; parse; smoke test in-engine.

Guardrails:

- Do not invent pool values—instructions carry the canonical numbers.
- Do not bake Win-only IDs into generalized instructions unless illustrative.
- After structural edits, verify **`references`**, **`m_Id`** parenting, binds, **`uxmlAssetId`**, **`inlineSheet`** path IDs remain mutually consistent **before import**.

## Patch documentation

Conventions mirror **[Bracket notation and the `references` object](#bracket-notation-and-the-references-object)**.

| Patch folder                    | Document                                                                                                                 |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| `match-dugout_tile`             | [MATCH_DUGOUT_TILE_CHANGES.md](./match-dugout_tile/MATCH_DUGOUT_TILE_CHANGES.md)                                         |
| `match-ctrl_scoreboard`         | [MATCH_CTRL_SCOREBOARD_CHANGES.md](./match-ctrl_scoreboard/MATCH_CTRL_SCOREBOARD_CHANGES.md)                             |
| `match-controller`              | [MATCH_CONTROLLER_CHANGES.md](./match-controller/MATCH_CONTROLLER_CHANGES.md)                                           |
| `match-left_scoreboard`         | [MATCH_LEFT_SCOREBOARD_CHANGES.md](./match-left_scoreboard/MATCH_LEFT_SCOREBOARD_CHANGES.md)                             |
| `match-squad_portraits`         | [SQUAD_OVERVIEW_PLAYER_BLOCK_CHANGES.md](./match-squad_portraits/SQUAD_OVERVIEW_PLAYER_BLOCK_CHANGES.md)                 |
| `messages-unread_indicators`    | [MESSAGES_UNREAD_INDICATORS_CHANGES.md](./messages-unread_indicators/MESSAGES_UNREAD_INDICATORS_CHANGES.md)              |
| `nav-menu_icons`                | [NAV_PORTAL_TAB_ICONS_CHANGES.md](./nav-menu_icons/NAV_PORTAL_TAB_ICONS_CHANGES.md)                                      |
| `nav-next_match`                | [NAV_NEXT_MATCH_CHANGES.md](./nav-next_match/NAV_NEXT_MATCH_CHANGES.md)                                                  |
| `tactics-player_portrait`       | [TACTICS_PLAYERPORTRAIT_CHANGES.md](./tactics-player_portrait/TACTICS_PLAYERPORTRAIT_CHANGES.md)                         |
| `formations-player_portrait`  | [FORMATIONS_PLAYER_PORTRAIT_CHANGES.md](./formations-player_portrait/FORMATIONS_PLAYER_PORTRAIT_CHANGES.md)             |
| `tactics-remove_planner_header` | [TACTICS_PLANNER_HEADER_GRADIENT_CHANGES.md](./tactics-remove_planner_header/TACTICS_PLANNER_HEADER_GRADIENT_CHANGES.md) |
| `tiles-player_report_photos`    | [PLAYER_REPORT_PHOTOS_CHANGES.md](./tiles-player_report_photos/PLAYER_REPORT_PHOTOS_CHANGES.md)                          |
| `tiles-speaktosidepanel_border` | [SPEAK_TO_SIDEPANEL_BORDER_CHANGES.md](./tiles-speaktosidepanel_border/SPEAK_TO_SIDEPANEL_BORDER_CHANGES.md)             |

Reference-only notes:

## Patched bundles

Current patch folders touch these bundles:

- `ui-tiles_assets_all`
- `ui-match_assets_all`
- `ui-widgets_assets_all`
- `ui-panelids-uxml_assets_all`
- `ui-tactics_assets_all`
- `ui-calendar_assets_all`

## Asset index

Use **logical filename + Path ID** suffix to reopen quickly in UABEA.

| Bundle                        | Filename                        | Path ID                | Used by                         |
| ----------------------------- | ------------------------------- | ---------------------- | ------------------------------- |
| `ui-calendar_assets_all`      | `inlineStyle`                   | `7705980741400097515`  | `nav-next_match`                |
| `ui-calendar_assets_all`      | `CurrentDayWidget`              | `2536531161352205035`  | `nav-next_match`                |
| `ui-tiles_assets_all`         | `Dugout_4x8_with_collapse`      | `-389085435411529779`  | `match-dugout_tile`             |
| `ui-tiles_assets_all`         | `inlineStyle`                   | `4355907201153990605`  | `match-dugout_tile`             |
| `ui-tiles_assets_all`         | `inlineStyle`                   | `-812894235568295708`  | `tiles-player_report_photos`    |
| `ui-tiles_assets_all`         | `inlineStyle`                   | `-3307895459461465053` | `tiles-player_report_photos`    |
| `ui-tiles_assets_all`         | `inlineStyle`                   | `-1253022491152235485` | `tiles-player_report_photos`    |
| `ui-tiles_assets_all`         | `inlineStyle`                   | `-4358236461696845827` | `tiles-player_report_photos`    |
| `ui-match_assets_all`         | `Scoreboard`                    | `-6243489578598291996` | `match-ctrl_scoreboard`         |
| `ui-match_assets_all`         | `inlineStyle`                   | `5770178802341094884`  | `match-ctrl_scoreboard`         |
| `ui-match_assets_all`         | `inlineStyle`                   | `2691751170888438562`  | `match-controller`              |
| `ui-match_assets_all`         | `OverviewHeader`                | `-7819803725380563227` | `match-left_scoreboard`         |
| `ui-match_assets_all`         | `inlineStyle`                   | `-2719487159082004763` | `match-left_scoreboard`         |
| `ui-match_assets_all`         | `ContinuePanel`                 | `-398932524086274306`  | `match-left_scoreboard`         |
| `ui-match_assets_all`         | `inlineStyle`                   | `4016413755955533566`  | `match-left_scoreboard`         |
| `ui-match_assets_all`         | `inlineStyle`                   | `8667680411542024723`  | `match-squad_portraits`         |
| `ui-widgets_assets_all`       | `inlineStyle`                   | `-8675747941173082291` | `messages-unread_indicators`    |
| `ui-widgets_assets_all`       | `inlineStyle`                   | `-4684259242089685901` | `messages-unread_indicators`    |
| `ui-widgets_assets_all`       | `inlineStyle`                   | `8251223908227938625`  | `nav-menu_icons`                |
| `ui-widgets_assets_all`       | `navigation-tab-portal-default` | `-3526698279923353279` | `nav-menu_icons`                |
| `ui-widgets_assets_all`       | `SquadOverviewPlayerBlock`      | `8153607655464179241`  | `match-squad_portraits`         |
| `ui-widgets_assets_all`       | `inlineStyle`                   | `-3625496258969266647` | `match-squad_portraits`         |
| `ui-panelids-uxml_assets_all` | `inlineStyle`                   | `4528579247820316033`  | `tiles-speaktosidepanel_border` |
| `ui-tactics_assets_all`       | `TacticalPlannerHeader`         | `-966834888143150418`  | `tactics-remove_planner_header` |
| `ui-tactics_assets_all`       | `TacticsPositionCombined`       | `3127595210179668386`  | `tactics-player_portrait`       |
| `ui-tactics_assets_all`       | `inlineStyle`                   | `1320523011556632994`  | `tactics-player_portrait`       |
| `ui-tactics_assets_all`       | `TacticsMatchPositionFilled`    | `2040735658139592085`  | `formations-player_portrait`    |
| `ui-tactics_assets_all`       | `inlineStyle`                   | `3217575363710297493`  | `formations-player_portrait`    |
| `ui-widgets_assets_all`       | `TacticsPastMatchPositionFilled`| `919276422481836312`   | `formations-player_portrait`    |
| `ui-widgets_assets_all`       | `inlineStyle`                   | `3320105331476789528`  | `formations-player_portrait`    |

## Note template

Each patch note normally includes:

- patch folder slug
- bundle(s)
- every edited asset (**dump filename suffix / Path ID**)
- literals for pooled values or explicit “diff-only” disclaimer with acceptance checks
- enough structure to rebuild after a Football Manager rebuild (tree goals, **`m_Rules` deltas, key class lists**)

Recording that AI-assisted diffs seeded the prose is acceptable when **targets remain explicit**.

## Extras

Not part of the tight maintained patch set but kept as lookups.

### Report gradients

Serialized **`m_SerializedData`** **`rid`** swaps.

| Bundle                  | Filename                        | Path ID | Change                                                |
| ----------------------- | ------------------------------- | ------- | ----------------------------------------------------- |
| `ui-tiles_assets_all`   | `PlayerReportHeader`            | unknown | change `m_SerializedData` `rid` from `1002` to `1001` |
| `ui-tiles_assets_all`   | `NonPlayerReportHeader`         | unknown | change `m_SerializedData` `rid` from `1004` to `1002` |
| `ui-tiles_assets_all`   | `RetiredPlayerReportHeader`     | unknown | change `m_SerializedData` `rid` from `1002` to `1001` |
| `ui-widgets_assets_all` | `PlayerReportPreviewTooltip`    | unknown | change `m_SerializedData` `rid` from `1007` to `1006` |
| `ui-widgets_assets_all` | `NonPlayerReportPreviewTooltip` | unknown | change `m_SerializedData` `rid` from `1008` to `1002` |

### Player portrait scale experiments

#### `ui-styles_assets_default` → `FigmaGeneratedStyles`

Pooled-style tinkering—not a pinned patch workflow.

| Reference | Pool type | Original | Target |
| --------- | --------- | -------- | ------ |
| `5139`    | Dimension | `-8`     | `-26`  |
| `6755`    | Float     | `1.2`    | `1`    |
| `6756`    | Float     | `1.2`    | `1`    |

Multiple numeric labels can collide conceptually—confirm pool entry context before committing.

#### `ui-tiles_assets_all` portrait tile dimensions

| Bundle                | Filename                    | Path ID                | Target edits                                               |
| --------------------- | --------------------------- | ---------------------- | ---------------------------------------------------------- |
| `ui-tiles_assets_all` | unknown portrait tile asset | `-1940069326759809061` | `dimensions[0]` `148 -> 170`, `dimensions[2]` `116 -> 140` |

Add the authoritative dump filename whenever this graduates to a numbered patch folder.
