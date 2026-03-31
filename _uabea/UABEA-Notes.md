# UABEA changes (index)

Manual changes to apply in UABEA during skin development after building the skin.

Each patch folder under `_uabea/` has its own instruction note. Those patch notes are the source of truth for exact edits.

## Working rules

- Stock exports stay in **`orig/`** per platform (`win/orig`, `mac/orig`). Patched JSON you re-import lives in **`win/`** and **`mac/`** next to it (same filenames). Apply the same patch note on each platform from that platform’s **`orig`**; compare **`win`** vs **`mac`** only after the doc targets are met.
- Patch docs should describe the logical change, not the current state of one platform dump.
- Use the asset filename and Path ID together so the file is easy to find again in a bundle.
- The Path ID used in docs is the last `-...` segment in the dumped filename and may itself start with `-`.
- For colours, sizes, pooled dimensions, pooled strings, and similar style values, record the intended target values directly in the patch note unless a patch note explicitly says otherwise.
- Windows and Mac dumps should be treated as the same logical patch with different IDs and serialized references. Ignore platform-specific IDs in the docs unless the structure truly diverges.
- Docs should stay instruction-first: what to edit, how to find it, and what the target value should be.

## Dump format

The repo is documented around UABEA Next JSON dumps.

- Patch notes should describe the edit in bundle/asset terms, not around a temporary export format.
- When a patch uses serialized USS or UXML trees heavily, JSON is the easiest shape to inspect and compare.

## AI-assisted workflow

AI is useful for the more complex patches, especially when the edit involves one or more of:

- serialized USS rule comparisons
- pooled `dimensions`, `strings`, `colors`, `floats`, or `assets` remapping
- inserted UXML subtrees
- `references.RefIds` additions
- comparing a current dump against the known target layout to recover the intended patch

Recommended workflow:

1. Export the current asset dump and gather the known target values or comparison material you have available.
2. Give AI the current file and the relevant target sections, values, or comparison snippets.
3. Ask for the logical diff first:
   - which rules changed
   - which pooled values changed
   - which tree nodes were inserted, removed, or rewired
   - which `rid` / serialized-data blocks were added or reassigned
4. Convert that into a patch note with:
   - bundle
   - filename
   - Path ID
   - exact target values
   - enough structure detail to recreate the patch on another dump
5. Only then apply the edit to the working dump and verify the final JSON parses cleanly.

Rules for using AI here:

- Use AI to compare, summarize, and draft instructions, not to invent values.
- For colours, dimensions, and other pooled values, the target should come from the recorded patch note values unless the note says otherwise.
- Treat Windows and Mac as the same logical patch; do not let AI hardcode Win-only `m_Id`, `rid`, or other serialized IDs into the documentation unless the note is explicitly using them as an example.
- For structural patches, verify that any inserted node IDs, `rid` values, and `uxmlAssetId` links remain internally consistent before reimporting.
- After AI-assisted edits, validate the final dump by parsing it again and checking the intended asset counts or rule counts.

## Patch documentation

| Patch folder | Document |
| ------------ | -------- |
| `match-dugout_tile` | [MATCH_DUGOUT_TILE_CHANGES.md](C:/Users/ix_ma/Documents/FM%20Skin%20Builder/skins/CTRL/_uabea/match-dugout_tile/MATCH_DUGOUT_TILE_CHANGES.md) |
| `match-left_scoreboard` | [MATCH_LEFT_SCOREBOARD_CHANGES.md](C:/Users/ix_ma/Documents/FM%20Skin%20Builder/skins/CTRL/_uabea/match-left_scoreboard/MATCH_LEFT_SCOREBOARD_CHANGES.md) |
| `match-squad_portraits` | [SQUAD_OVERVIEW_PLAYER_BLOCK_CHANGES.md](C:/Users/ix_ma/Documents/FM%20Skin%20Builder/skins/CTRL/_uabea/match-squad_portraits/SQUAD_OVERVIEW_PLAYER_BLOCK_CHANGES.md) |
| `messages-unread_indicators` | [MESSAGES_UNREAD_INDICATORS_CHANGES.md](C:/Users/ix_ma/Documents/FM%20Skin%20Builder/skins/CTRL/_uabea/messages-unread_indicators/MESSAGES_UNREAD_INDICATORS_CHANGES.md) |
| `nav-menu_icons` | [NAV_PORTAL_TAB_ICONS_CHANGES.md](C:/Users/ix_ma/Documents/FM%20Skin%20Builder/skins/CTRL/_uabea/nav-menu_icons/NAV_PORTAL_TAB_ICONS_CHANGES.md) |
| `tactics-player_portrait` | [TACTICS_PLAYERPORTRAIT_CHANGES.md](C:/Users/ix_ma/Documents/FM%20Skin%20Builder/skins/CTRL/_uabea/tactics-player_portrait/TACTICS_PLAYERPORTRAIT_CHANGES.md) |
| `tactics-remove_planner_header` | [TACTICS_PLANNER_HEADER_GRADIENT_CHANGES.md](C:/Users/ix_ma/Documents/FM%20Skin%20Builder/skins/CTRL/_uabea/tactics-remove_planner_header/TACTICS_PLANNER_HEADER_GRADIENT_CHANGES.md) |
| `tiles-speaktosidepanel_border` | [SPEAK_TO_SIDEPANEL_BORDER_CHANGES.md](C:/Users/ix_ma/Documents/FM%20Skin%20Builder/skins/CTRL/_uabea/tiles-speaktosidepanel_border/SPEAK_TO_SIDEPANEL_BORDER_CHANGES.md) |

Reference-only notes:

## Patched bundles

Current patch folders touch these bundles:

- `ui-tiles_assets_all`
- `ui-match_assets_all`
- `ui-widgets_assets_all`
- `ui-panelids-uxml_assets_all`
- `ui-tactics_assets_all`

## Asset index

Use these filename and Path ID pairs to find the assets quickly in UABEA.

| Bundle | Filename | Path ID | Used by |
| ------ | -------- | ------- | ------- |
| `ui-tiles_assets_all` | `Dugout_4x8_with_collapse` | `-389085435411529779` | `match-dugout_tile` |
| `ui-tiles_assets_all` | `inlineStyle` | `4355907201153990605` | `match-dugout_tile` |
| `ui-match_assets_all` | `OverviewHeader` | `-7819803725380563227` | `match-left_scoreboard` |
| `ui-match_assets_all` | `inlineStyle` | `-2719487159082004763` | `match-left_scoreboard` |
| `ui-match_assets_all` | `ContinuePanel` | `-398932524086274306` | `match-left_scoreboard` |
| `ui-match_assets_all` | `inlineStyle` | `4016413755955533566` | `match-left_scoreboard` |
| `ui-match_assets_all` | `inlineStyle` | `8667680411542024723` | `match-squad_portraits` |
| `ui-widgets_assets_all` | `inlineStyle` | `-8675747941173082291` | `messages-unread_indicators` |
| `ui-widgets_assets_all` | `inlineStyle` | `-4684259242089685901` | `messages-unread_indicators` |
| `ui-widgets_assets_all` | `inlineStyle` | `8251223908227938625` | `nav-menu_icons` |
| `ui-widgets_assets_all` | `navigation-tab-portal-default` | `-3526698279923353279` | `nav-menu_icons` |
| `ui-widgets_assets_all` | `SquadOverviewPlayerBlock` | `8153607655464179241` | `match-squad_portraits` |
| `ui-widgets_assets_all` | `inlineStyle` | `-3625496258969266647` | `match-squad_portraits` |
| `ui-panelids-uxml_assets_all` | `inlineStyle` | `4528579247820316033` | `tiles-speaktosidepanel_border` |
| `ui-tactics_assets_all` | `TacticalPlannerHeader` | `-966834888143150418` | `tactics-remove_planner_header` |
| `ui-tactics_assets_all` | `TacticsPositionCombined` | `3127595210179668386` | `tactics-player_portrait` |
| `ui-tactics_assets_all` | `inlineStyle` | `1320523011556632994` | `tactics-player_portrait` |

## Note template

Each patch note should normally include:

- patch folder
- bundle or bundles
- filename and Path ID for every edited asset
- exact target values for colours, dimensions, strings, rule counts, or rid swaps
- enough tree/rule detail to recreate the patch on a fresh export

For more complex patches, it is fine to note that AI was used to compare dumps and derive the instruction set, but the note should still record the final targets explicitly so it is usable without rerunning the comparison.

## Extras

These are not part of the active maintained patch set, but are worth keeping as reusable references.

### Report gradients

These are simple serialized-data reference swaps.

| Bundle | Filename | Path ID | Change |
| ------ | -------- | ------- | ------ |
| `ui-tiles_assets_all` | `PlayerReportHeader` | unknown | change `m_SerializedData` `rid` from `1002` to `1001` |
| `ui-tiles_assets_all` | `NonPlayerReportHeader` | unknown | change `m_SerializedData` `rid` from `1004` to `1002` |
| `ui-tiles_assets_all` | `RetiredPlayerReportHeader` | unknown | change `m_SerializedData` `rid` from `1002` to `1001` |
| `ui-widgets_assets_all` | `PlayerReportPreviewTooltip` | unknown | change `m_SerializedData` `rid` from `1007` to `1006` |
| `ui-widgets_assets_all` | `NonPlayerReportPreviewTooltip` | unknown | change `m_SerializedData` `rid` from `1008` to `1002` |

If any of these become real maintained patches, add the dumped filename Path ID and move them into dedicated patch notes.

### Player portrait scale experiments

#### `ui-styles_assets_default` -> `FigmaGeneratedStyles`

This is a pooled-style edit rather than a maintained patch note.

| Reference | Pool type | Original | Target |
| --------- | --------- | -------- | ------ |
| `5139` | Dimension | `-8` | `-26` |
| `6755` | Float | `1.2` | `1` |
| `6756` | Float | `1.2` | `1` |

There may be more than one `5139`-like numeric label in a raw dump. Confirm you are editing the intended dimension entry before importing.

#### `ui-tiles_assets_all` portrait tile dimensions

| Bundle | Filename | Path ID | Target edits |
| ------ | -------- | ------- | ------------ |
| `ui-tiles_assets_all` | unknown portrait tile asset | `-1940069326759809061` | `dimensions[0]` `148 -> 170`, `dimensions[2]` `116 -> 140` |

This reference is kept because the Path ID is known, but the dumped filename should be added if this becomes an active maintained patch.
