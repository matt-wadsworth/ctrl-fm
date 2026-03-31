# Squad overview player block (UABEA)

- **Patch folder:** `match-squad_portraits`
- **Bundles:** `ui-widgets_assets_all`, `ui-match_assets_all`
- **Dump format:** serialized asset dump (`.json`)

## Implementation notes

Work from **stock bundle exports** (your **`orig`** folder or platform dump) into the patched copies. If a new game version shifts indices, pool sizes, or rule order, adjust the steps below to match the new dump, then update this document so it stays accurate.

**Guardrails (stylesheet):**

- **`m_Properties.Array` order matters.** Unity UI serializes each rule as an ordered list; re-ordering or dropping unrelated properties changes cascade and can break layout.
- **Prefer additive edits:** when adding one property (e.g. `padding-top`), insert it in the correct position relative to siblings—do not replace an entire rule with a shortened list unless you intend to remove those properties.
- **Rule 12:** insert `padding-top` **after** `padding` and **before** `border-radius`. Keep `height`, `padding`, `border-radius`, `align-self`, `justify-content`, and `flex-shrink` as in the table.
- **Rule 14:** the block has **seven** properties in a fixed order (see table); partial replacements that omit `justify-content`, `align-self`, or `position` will look wrong.

## Assets

| Bundle | Filename | Path ID | Role |
| ------ | -------- | ------- | ---- |
| `ui-widgets_assets_all` | `SquadOverviewPlayerBlock` | `8153607655464179241` | UXML tree for the squad overview player block |
| `ui-widgets_assets_all` | `inlineStyle` | `-3625496258969266647` | USS rules and pools for the squad overview player block |
| `ui-match_assets_all` | `inlineStyle` | `8667680411542024723` | Match squad row background colours |

## `ui-widgets_assets_all` `inlineStyle` target

Final sheet shape:

- `m_Rules.Array` count: `34`
- `dimensions.Array` count: `24`
- `strings.Array` values:
  - `[0] = stretch`
  - `[1] = center`
  - `[2] = bold`
  - `[3] = middle-center`
  - `[4] = flex-end`
  - `[5] = column`
  - `[6] = space-between`
  - `[7] = flex-start`
  - `[8] = absolute`
  - `[9] = relative`
- `colors.Array` values:
  - `[0] = { r: 0.039215688, g: 0.05882353, b: 0.11764706, a: 0 }`
  - `[1] = { r: 0.851, g: 0.91, b: 0.929, a: 0.05 }`

Final `dimensions.Array` values:

| Index | Value |
| ----- | ----- |
| `0`  | `100%` |
| `1`  | `110px` |
| `2`  | `32px` |
| `3`  | `8px` |
| `4`  | `20%` |
| `5`  | `130px` |
| `6`  | `12px` |
| `7`  | `64px` |
| `8`  | `6px` |
| `9`  | `17px` |
| `10` | `16px` |
| `11` | `62px` |
| `12` | `50%` |
| `13` | `-31px` |
| `14` | `70px` |
| `15` | `60px` |
| `16` | `-30px` |
| `17` | `32px` |
| `18` | `128px` |
| `19` | `-2px` |
| `20` | `80px` |
| `21` | `14px` |
| `22` | `18px` |
| `23` | `42px` |

### Canonical `m_Rules` (widgets `inlineStyle`)

Apply **exact property order** as listed. String pool indices: `stretch=0`, `center=1`, `bold=2`, `middle-center=3`, `flex-end=4`, `column=5`, `space-between=6`, `flex-start=7`, `absolute=8`, `relative=9`.

| Rule | Role | Properties (in order) |
| ---- | ---- | ----------------------- |
| `12` | Kit column | `height` → `130px` (dim `5`); `padding` → `100% 8px 8px 8px` (string `0` + dims `3`); **`padding-top` → `18px` (dim `22`)** — insert **after** `padding`, **before** `border-radius`; `border-radius` → `100% 100% 12px 12px`; `align-self` → `stretch`; `justify-content` → `flex-end`; `flex-shrink` → `0` |
| `13` | Kit / shirt stack | `height` → `128px` (dim `18`); `flex-direction` → `column`; `justify-content` → `flex-start`; `align-items` → `center`; `flex-shrink` → `0`; `align-self` → `stretch`; `padding-bottom` → `6px` (dim `8`) |
| `14` | Middle column (numbers) | `justify-content` → `space-between`; `align-items` → `center`; `align-self` → `stretch`; `position` → `relative`; `padding-top` → `8px` (dim `3`); `min-height` → `80px` (dim `20`); `padding-bottom` → `32px` (dim `17`) |
| `15` | Name column | `flex-grow` → `0`; `width` → `20%` (dim `4`); `margin-top` → `42px` (dim `23`) |
| `19` | Shirt / crest box | `align-items` → `center`; `width` → `70px`; `height` → `70px` (dim `14`) |
| `20` | Hidden / spacer layer | `width` → `100%` (dim `0`); `height` → `100%` (dim `0`); `opacity` → `0` |
| `21` | Name column (duplicate row slot) | Same as rule `15`: `flex-grow` → `0`; `width` → `20%`; `margin-top` → `42px` |
| `27` | Row chrome | `margin-bottom` → `6px` (dim `8`); `opacity` → `0`; `align-self` → `stretch` |
| `31` | Portrait wrapper `SquadPlayerPictureWrap` | `width` / `height` → `60px` (dim `15`); `position` → `absolute`; **`left` → `50%` and `top` → `50%`** (both dim `12`); `margin-left` → `-30px` (dim `16`); `margin-top` → `-2px` (dim `19`); `justify-content` / `align-items` → `center` |
| `32` | Inner `PersonPicture` | `width` / `height` → `100%` (dim `0`) |
| `33` | Name wrapper spacing | `margin-top` → `14px` (dim `21`) |

**Note on rule `31`:** positioning uses **`left` + `top` at `50%`** plus negative `margin-left` / `margin-top` for centering—not only `left: 50%` with `margin-left` alone. Omitting `top` or swapping property order relative to the table can misplace the portrait.

## `SquadOverviewPlayerBlock` target

### `m_Usings`

Add:

```json
{
  "alias": "PersonPicture",
  "path": "a3126e03e5bea6348ba86f23b5c5d7de",
  "asset": { "m_FileID": 0, "m_PathID": 0 }
}
```

### Tree insertion

Inside the kit row (`row-direction-normal`), insert:

1. Outer portrait wrapper `VisualElement`
2. Child template instance `PersonPicture`

#### Which array each node lives in

| Node | `m_VisualElementAssets.Array` | `m_TemplateAssets.Array` |
| ---- | ----------------------------- | ------------------------ |
| Portrait wrapper (`VisualElement`) | yes | no |
| `PersonPicture` template (`m_TemplateAlias` / `TemplateContainer`) | **no** | **yes** |

On Win exports, insert the wrapper **after** the last direct child of the kit row that already exists before the name column (the sibling block whose parent is the `row-direction-normal` row); then append the template row to **`m_TemplateAssets`**, not to `m_VisualElementAssets`.

#### `m_OrderInDocument`

Renumber so each value is **globally unique** across **`m_VisualElementAssets` and `m_TemplateAssets` together**, typically contiguous `0 … N-1` after a depth-first walk from parent `0`.

Reference IDs for the inserted nodes:

| Node | `m_Id` | `m_RuleIndex` | `rid` |
| ---- | ------ | ------------- | ----- |
| portrait wrapper | `2019847551` | `31` | `1047` |
| `PersonPicture` template | `2019847552` | `32` | `1048` |

Also set the existing name wrapper `m_Id 1251298332` to `m_RuleIndex = 33`.

### Added `references.RefIds`

Append the matching serialized-data blocks:

- `rid 1047` -> `VisualElement/UxmlSerializedData`, `uxmlAssetId 2019847551`, name `SquadPlayerPictureWrap`
- `rid 1048` -> `TemplateContainer/UxmlSerializedData`, `uxmlAssetId 2019847552`, name `PersonPicture`, `templateId PersonPicture`

## `ui-match_assets_all` `inlineStyle` target

Only the pooled row colours change.

Set `colors.Array` to:

| Index | Target RGBA |
| ----- | ----------- |
| `0` | `r 1`, `g 0.95686275`, `b 0.95686275`, `a 0` |
| `1` | `r 0.9019608`, `g 0.9019608`, `b 0.98039216`, `a 0.06` |
| `2` | `r 0.9019608`, `g 0.9019608`, `b 0.98039216`, `a 0` |
| `3` | `r 0.039215688`, `g 0.05882353`, `b 0.11764706`, `a 0` |

That preserves the intended subtle highlight on colour slot `1` and makes the other row background slots fully transparent.
