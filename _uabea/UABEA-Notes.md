# UABEA changes (index)

Manual changes to apply in UABEA during skin development (after building the skin).

**Dev notes only** — end users using bundle releases do not need to apply these.

Each patch folder under `_uabea/` has its own note (see table below). Detailed walkthroughs for complex patches live next to their dumps.

---

## Dump file format (`.txt` vs `.json`)

Patches in this repo are exported in **two shapes**:

- **`.txt`** — typical **UABEA** text dumps; edit/reimport with the workflow you already use for that format.
- **`.json`** — use **UABEA Next** for export/import of these serialized assets (not the legacy UABEA `.txt`-only flow).

This may change to a unified type in the future; the markdown notes describe **logical** edits (path IDs, rules, tree structure), not the container format.

**Current snapshot** (what sits next to each note today):

| Patch folder                    | Typical dumps in repo |
| ------------------------------- | --------------------- |
| `match-dugout_tile`             | `.txt`                |
| `match-left_scoreboard`         | `.txt`                |
| `match-squad_portraits`         | `.json`               |
| `messages-unread_indicators`    | `.txt`                |
| `nav-menu_icons`                | `.txt`                |
| `tactics-player_portrait`       | `.json`               |
| `tactics-remove_planner_header` | `.txt`                |
| `tiles-speaktosidepanel_border` | `.txt`                |

---

## Patch documentation

| Patch folder                    | Document                                                                                                               |
| ------------------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| `match-dugout_tile`             | [MATCH_DUGOUT_TILE_CHANGES.md](match-dugout_tile/MATCH_DUGOUT_TILE_CHANGES.md)                                         |
| `match-left_scoreboard`         | [MATCH_LEFT_SCOREBOARD_CHANGES.md](match-left_scoreboard/MATCH_LEFT_SCOREBOARD_CHANGES.md)                             |
| `match-squad_portraits`         | [SQUAD_OVERVIEW_PLAYER_BLOCK_CHANGES.md](match-squad_portraits/SQUAD_OVERVIEW_PLAYER_BLOCK_CHANGES.md)                 |
| `messages-unread_indicators`    | [MESSAGES_UNREAD_INDICATORS_CHANGES.md](messages-unread_indicators/MESSAGES_UNREAD_INDICATORS_CHANGES.md)              |
| `nav-menu_icons`                | [NAV_PORTAL_TAB_ICONS_CHANGES.md](nav-menu_icons/NAV_PORTAL_TAB_ICONS_CHANGES.md)                                      |
| `tactics-player_portrait`       | [TACTICS_PLAYERPORTRAIT_CHANGES.md](tactics-player_portrait/TACTICS_PLAYERPORTRAIT_CHANGES.md)                         |
| `tactics-remove_planner_header` | [TACTICS_PLANNER_HEADER_GRADIENT_CHANGES.md](tactics-remove_planner_header/TACTICS_PLANNER_HEADER_GRADIENT_CHANGES.md) |
| `tiles-speaktosidepanel_border` | [SPEAK_TO_SIDEPANEL_BORDER_CHANGES.md](tiles-speaktosidepanel_border/SPEAK_TO_SIDEPANEL_BORDER_CHANGES.md)             |

**Reference only (no patch folder):** [UABEA_EXTRAS_REFERENCE.md](UABEA_EXTRAS_REFERENCE.md) (reports gradients, portrait scale experiments).

---

## Format reference

For each item in a patch note:

- **Name:** Short label for the element
- **File:** Bundle filename
- **Path ID (or Name):** Asset path ID or name in UABEA
- **Reference:** e.g. Color [3], m_SerializedData, rid, etc.
- **Note:** What it controls / what to do

Each patch note opens with patch-level bullets (**Patch folder**, **Bundle**, **Dump format**, **Dumps in repo**).

---

## Path IDs (all patch files)

Use these to find assets quickly in UABEA (**View → Go To Asset**, or search by path ID).

| Bundle                      | Filename (patch)              | Path ID              | Change                                                                                               |
| --------------------------- | ----------------------------- | -------------------- | ---------------------------------------------------------------------------------------------------- |
| ui-tactics_assets_all       | TacticalPlannerHeader         | -966834888143150418  | Tactics header gradient — change rid (1002 → 1001) or update gradient colours.                       |
| ui-tiles_assets_all         | Dugout_4x8_with_collapse      | -389085435411529779  | Dugout 4×8 tile — set `base-template-grow` element `m_RuleIndex` to `6`.                             |
| ui-tiles_assets_all         | inlineStyle                   | 4355907201153990605  | Dugout 4×8 inline style — add background + border rule at index `6`.                                 |
| ui-widgets_assets_all       | inlineStyle                   | -4684259242089685901 | Processing unread indicator — tweak `Color[3]` (dot BG) and `Color[4]` (icon) as needed.             |
| ui-widgets_assets_all       | inlineStyle                   | -8675747941173082291 | Portal unread indicator — tweak `Color[3]` (dot BG) and `Color[4]` (icon) as needed.                 |
| ui-widgets_assets_all       | inlineStyle                   | 8251223908227938625  | Portal tab inline style — add new background-image tint rule + colour entry.                         |
| ui-widgets_assets_all       | navigation-tab-portal-default | -3526698279923353279 | Portal tab layout — move icon next to text, set `row-direction-normal`, assign icon tint rule index. |
| ui-panelids-uxml_assets_all | inlineStyle                   | 4528579247820316033  | SpeakToSidePanel — border; change `Color[0]`.                                                        |
| ui-match_assets_all         | OverviewHeader                | -7819803725380563227 | In-match scoreboard UXML — see match-left_scoreboard note.                                           |
| ui-match_assets_all         | inlineStyle                   | -2719487159082004763 | OverviewHeader styles + safe area offsets.                                                           |
| ui-match_assets_all         | ContinuePanel                 | -398932524086274306  | Pre-match / interval panel — see match-left_scoreboard note.                                         |
| ui-match_assets_all         | inlineStyle                   | 4016413755955533566  | ContinuePanel styles.                                                                                |

_Squad overview and tactics position portrait assets use the same `ui-widgets` / `ui-tactics` bundles; path IDs and filenames are in their dedicated markdown files._
