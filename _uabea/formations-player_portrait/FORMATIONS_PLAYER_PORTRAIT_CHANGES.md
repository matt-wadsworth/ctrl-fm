# Formation position portraits (UABEA)

- **Patch folder:** `formations-player_portrait`
- **Bundles:** `ui-widgets_assets_all`, `ui-tactics_assets_all`
- **Dump format:** serialized asset dump (`.json`)
- **Stock baseline:** `*/win/orig/` and `*/mac/orig/` under each bundle folder. **Never edit** these exports.
- **Working imports:** `*/win/*.json` and `*/mac/*.json` (siblings of `orig/`, same basenames per platform).

Notation **`[n]`** follows **`../UABEA-Notes.md`**: **0-based** indices into **`m_Rules`**, **`m_VisualElementAssets`**, **`m_TemplateAssets`**, pools, **`references.RefIds`**, etc.

Two formation tiles share the same layout pattern (shirt slot + role pill + icon row). Apply the **same logical** portrait and stylesheet edits on each tile; only the UXML binding path and a few **`m_RuleIndex`** offsets differ between bundles.

**Patch order (each tile):** that tile’s **`inlineStyle`** first, then its UXML asset.

**macOS:** Patched working copies live in **`*/mac/`** (generated from **`mac/orig/`** + Windows topology with native IDs). **Do not** copy Windows UXML wholesale — remap **`m_Id`** / **`m_ParentId`** / **`uxmlAssetId`** per the table below. Stock **`inlineStyle`** dumps are byte-identical Win/Mac for this lineage; patched **`inlineStyle`** is copied from **`win/`**. Re-run **`apply_mac_patch.py`** after FM rebuilds if Windows dumps change.

## Assets

| Bundle | Filename | Path ID | Role |
| ------ | -------- | ------- | ---- |
| `ui-widgets_assets_all` | `TacticsPastMatchPositionFilled` | `919276422481836312` | Past-match formation position tile (UXML) |
| `ui-widgets_assets_all` | `inlineStyle` | `3320105331476789528` | USS for past-match tile |
| `ui-tactics_assets_all` | `TacticsMatchPositionFilled` | `2040735658139592085` | Live-match formation position tile (UXML) |
| `ui-tactics_assets_all` | `inlineStyle` | `3217575363710297493` | USS for live-match tile |

## Regression counts (this FM lineage, Windows)

| Asset | Stock | Patched |
| ----- | ----- | ------- |
| `TacticsPastMatchPositionFilled` | `30` VE, `0` TA, `30` RefIds | `29` VE, `1` TA, `30` RefIds |
| `TacticsMatchPositionFilled` | `28` VE, `0` TA, `28` RefIds | `27` VE, `1` TA, `28` RefIds |
| Past-match `inlineStyle` | `19` rules, `11` dimensions | `20` rules, `13` dimensions |
| Match `inlineStyle` | `18` rules, `11` dimensions | `19` rules, `13` dimensions |

---

## `inlineStyle` target (both bundles)

Final sheet shape is the same on both patched Windows exports; only **`m_Rules.Array` length** and some **rule indices** differ (past-match has one extra stock rule — see per-tile tables below).

### Pools

- `strings.Array` (unchanged vs stock): `column-reverse`, `stretch`, `center`, `space-around`, `flex-start`, `middle-center`, `pre`, `flex-end`, `hidden`, `absolute`
- `colors.Array`:
  - `[0]` = white at **5%** alpha: `{ r: 1, g: 1, b: 1, a: 0.05 }` (role pill fill and borders; was dark `#131727` at `colors[0]` on stock)
  - `[1]` = grey `{ r: 0.41960785, g: 0.41960785, b: 0.41960785, a: 1 }` (unchanged; no longer used for role borders)
- `dimensions.Array` (patched — append **`[11]`** and **`[12]`** vs stock):

| Index | Value | Meaning |
| ----- | ----- | ------- |
| `0` | `72px` | tile width |
| `1` | `60px` | tile height |
| `2` | `16px` | role height / small box |
| `3` | `30px` | role width |
| `4` | `8px` | corner radius |
| `5` | `1px` | role border width (all sides) |
| `6` | `50%` | percentage slot |
| `7` | `4px` | icon offset |
| `8` | `36px` | portrait slot width and height (was **`32px`** on stock at `[8]`) |
| `9` | `-6px` | slot margin-bottom |
| `10` | `100%` | remapper / inner fill |
| `11` | `-7px` | portrait `margin-top` (**new** pool slot) |
| `12` | `1px` | portrait `margin-left` (**new** pool slot) |

### Shared rule edits

| Rule | Change |
| ---- | ------ |
| Role pill | `background-color` → `colors[0]`. All four `border-*-color` → `colors[0]` (stock used `colors[1]` grey outline). Keep `border-*-width` → `dimensions[5]` (`1px`). |
| Icon row | **Remove** the `overflow: hidden` property (`strings[8]`). Stock had it on the icon-row rule; patched drops it so shifted portraits are not clipped. |
| Portrait slot (`SIAspectRatioFitter`) | `width` and `height` → `dimensions[8]` (`36px`; stock used `32px`). |
| Portrait remapper wrapper | `width` and `height` → `dimensions[10]` (`100%`) — unchanged shape vs stock shirt remapper rule. |
| **Append** final rule | `width` / `height` → `dimensions[8]`; `margin-top` → `dimensions[11]` (`-7px`); `margin-left` → `dimensions[12]` (`1px`). Bound to **`PersonPicture`** via UXML `m_RuleIndex` (see per-tile table). |

### Per-bundle rule indices

Use these **`m_RuleIndex`** values when wiring nodes or reading dumps:

| Element / purpose | Past-match (`3320105331476789528`) | Live match (`3217575363710297493`) |
| ----------------- | ------------------------------------ | ----------------------------------- |
| Role pill | `7` | `7` |
| Icon row (remove `overflow`) | `12` | `11` |
| `SIAspectRatioFitter` (36×36 slot) | `14` | `13` |
| Shirt `BindingRemapper` (100% box) | `15` | `14` |
| `PersonPicture` portrait nudge | `19` | `18` |

**Stock → patched rule counts:** past-match `19` → `20`; live match `18` → `19`.

---

## UXML target (both tiles)

### `m_Usings`

Add:

```json
{
  "alias": "PersonPicture",
  "path": "a3126e03e5bea6348ba86f23b5c5d7de",
  "asset": { "m_FileID": 0, "m_PathID": 0 }
}
```

### Shirt → portrait (reuse stock remapper)

Stock already has a **`BindingRemapper`** under the position icon column with **`FMTacticsShirt`** as its child in **`m_VisualElementAssets`**. Do **not** add a new outer wrapper.

1. **Remove** the **`FMTacticsShirt`** visual node and its **`references.RefIds`** handler (`FMTacticsShirt/UxmlSerializedData`).
2. **Keep** the existing shirt **`BindingRemapper`** (same **`m_Id`** / parent as stock).
3. **Append** one row to **`m_TemplateAssets`** (not **`m_VisualElementAssets`**): **`PersonPicture`** template instance (`m_TemplateAlias: "PersonPicture"`, empty `m_FullTypeName`), parent = that remapper, **`m_OrderInDocument`** immediately after the remapper (stock shirt used order `10`).
4. On the remapper’s **`Mappings`**, add **`from: "person"`** with the tile-specific **`to.m_path`** below. Keep existing shirt/index mappings (`teamselectionindex`, `playerindex`, etc.).

#### Which array each node lives in

| Node | `m_VisualElementAssets` | `m_TemplateAssets` |
| ---- | ----------------------- | ------------------ |
| Existing shirt `BindingRemapper` | yes | no |
| `PersonPicture` template instance | **no** | **yes** |

#### `m_OrderInDocument`

Renumber so orders stay **unique and contiguous** across **`m_VisualElementAssets`** and **`m_TemplateAssets`** after removing the shirt node and adding the template row (depth-first from root is reliable).

### Bindings

| Tile | Remapper `person` → |
| ---- | ------------------- |
| `TacticsPastMatchPositionFilled` | `Position.player` |
| `TacticsMatchPositionFilled` | `Position.matchplayer.person` |

### Windows reference IDs (illustrative — verify on your export)

| Tile | Portrait remapper `m_Id` | Icon column parent `m_Id` | `PersonPicture` `m_Id` | Stock shirt removed `m_Id` |
| ---- | ------------------------ | ------------------------- | ------------------------ | --------------------------- |
| Past-match (Win) | `2087287248` | `-146308557` | `1928374653` | `-1495218538` |
| Past-match (Mac) | `-340578286` | `1720793205` | `1928374653` | `371883224` |
| Live match (Win) | `403061674` | `-577835425` | `1928374653` | `-137175522` |
| Live match (Mac) | `-770846247` | `-1751743346` | `1928374653` | `-1311083443` |

**`references` `rid` for `PersonPicture`:** past-match uses **`1029`** (Win/Mac); live match uses **`1027`** (Win/Mac) — fewer stock RefIds on that tile.

`PersonPicture` uses **`m_Id` `1928374653`** on both tiles (same as **`tactics-player_portrait`**). **`m_RuleIndex`** for that node: **`19`** (past-match), **`18`** (live match). Remapper rule indices: **`15`** / **`14`** respectively; fitter: **`14`** / **`13`**.

Append **`TemplateContainer/UxmlSerializedData`** under **`references.RefIds`** for `PersonPicture` (`uxmlAssetId` must match **`1928374653`**). Remove the shirt serialized block; **`RefIds` array length** should match stock after the swap.

---

## Import checklist

For **each** tile type, re-import **both** JSON files into the correct bundle:

1. **`ui-widgets_assets_all`:** `inlineStyle` (`3320105331476789528`) + `TacticsPastMatchPositionFilled` (`919276422481836312`)
2. **`ui-tactics_assets_all`:** `inlineStyle` (`3217575363710297493`) + `TacticsMatchPositionFilled` (`2040735658139592085`)

Use **`win/`** dumps on Windows and rebuilt **`mac/`** dumps on macOS.

**In-engine:** Smoke formation views that show filled past-match and live-match position tiles; confirm portraits align (final offsets **`margin-top: -7px`**, **`margin-left: 1px`**), role pills read as light glass (white 5%), and heads are not clipped on the icon row.

### Portrait tuning history (past-match led)

Iterated on past-match first, then mirrored on live match: `margin-top` **`-10` → `-4` → `-6` → `-9` → `-7`**; horizontal **`+2px` → `+1px`**; slot/picture **32 → 36px** to reduce bottom bleed; role pill fill/borders restyled as above.

---

## Windows vs macOS

| Asset | Windows | macOS |
| ----- | ------- | ----- |
| Both `inlineStyle` | Apply **§ `inlineStyle` target** to **`win/orig`** → import **`win/`** | Same pool/rule targets on **`mac/orig`** for each Path ID |
| Both UXML tiles | Working copies in **`win/`** | Rebuild from **`mac/orig/`** only; mirror topology and bindings, native IDs and **`rid`** values |

**`TacticsPastMatchPositionFilled` / `TacticsMatchPositionFilled` on macOS**

1. Copy all four assets from **`mac/orig/`** into **`mac/`** per bundle folder.
2. Patch both **`inlineStyle`** sheets to the targets above.
3. On each UXML tile, mirror the Windows topology: **`PersonPicture`** import, remove **`FMTacticsShirt`**, template under the stock shirt remapper, **`person`** mapping, and rule indices from the per-bundle table — using Mac-native **`m_Id`** / **`rid`** (do not paste Windows **`references`** blocks verbatim unless your export already matches).
