# Tactics player portrait (UABEA)

- **Patch folder:** `tactics-player_portrait`
- **Bundle:** `ui-tactics_assets_all`
- **Dump format:** serialized asset dump (`.json`)

## Assets

| Filename                  | Path ID               | Role |
| ------------------------- | --------------------- | ---- |
| `TacticsPositionCombined` | `3127595210179668386` | UXML tree |
| `inlineStyle`             | `1320523011556632994` | USS rules and pooled values |

## `inlineStyle` target

Final sheet shape:

- `m_Rules.Array` count: `30`
- `dimensions.Array` count: `17`
- `strings.Array` values:
  - `[0] = stretch`
  - `[1] = flex-start`
  - `[2] = center`
  - `[3] = flex-end`
  - `[4] = absolute`
  - `[5] = middle-center`
  - `[6] = ellipsis`
  - `[7] = hidden`

Final `dimensions.Array` values:

| Index | Value | Meaning |
| ----- | ----- | ------- |
| `0`  | `50px`  | shirt row height |
| `1`  | `-50px` | shirt row top |
| `2`  | `4px`   | movement icon bottom |
| `3`  | `40px`  | shirt size |
| `4`  | `16px`  | shirt top |
| `5`  | `16px`  | existing pooled 16px slot |
| `6`  | `50%`   | portrait left |
| `7`  | `8px`   | existing pooled 8px slot |
| `8`  | `2px`   | existing pooled 2px slot |
| `9`  | `6px`   | existing pooled 6px slot |
| `10` | `44px`  | existing pooled 44px slot |
| `11` | `80px`  | existing pooled 80px slot |
| `12` | `40px`  | portrait width and height |
| `13` | `-28px` | portrait margin-left |
| `14` | `11px`  | portrait margin-top |
| `15` | `12px`  | shirt left |
| `16` | `0px`   | left and right movement icon horizontal offset |

Edit these rules to the target layout:

- Rule `2`
  - `align-self = flex-end`
  - `position = absolute`
  - `left = 0px` via `dimensions[16]`
  - `bottom = 4px` via `dimensions[2]`
- Rule `3`
  - `min-height = 40px`
  - `max-height = 40px`
  - `min-width = 40px`
  - `max-width = 40px`
  - `top = 16px`
  - add `left = 12px` via `dimensions[15]`
- Rule `7`
  - same shape as rule `2`, but `right = 0px` via `dimensions[16]`
- Append rule `28` for the outer portrait wrapper:
  - `width = 40px`
  - `height = 40px`
  - `position = absolute`
  - `left = 50%`
  - `margin-left = -28px`
  - `margin-top = 11px`
  - `justify-content = center`
  - `align-items = center`
- Append rule `29` for the inner remapper:
  - `width = 40px`

## `TacticsPositionCombined` target

### `m_Usings`

Add this template import:

```json
{
  "alias": "PersonPicture",
  "path": "a3126e03e5bea6348ba86f23b5c5d7de",
  "asset": { "m_FileID": 0, "m_PathID": 0 }
}
```

### Tree insertion

Under the row that already contains `FMTacticsShirt`, insert:

1. Outer `VisualElement`
2. Child `SI.Bindable.BindingRemapper`
3. Child template instance `PersonPicture`

#### Which array each node lives in (critical)

Unity splits the serialized tree into two lists. **Do not put the template instance in the wrong one** or the portrait may not appear in-game.

| Node | `m_VisualElementAssets.Array` | `m_TemplateAssets.Array` |
| ---- | ----------------------------- | ------------------------ |
| Outer portrait wrapper (`VisualElement`) | yes | no |
| Portrait `BindingRemapper` | yes | no |
| `PersonPicture` template instance (`m_TemplateAlias` / `TemplateContainer`) | **no** | **yes** |

The template row has `m_FullTypeName` empty and `m_TemplateAlias: "PersonPicture"`; it belongs in **`m_TemplateAssets`**, not appended to `m_VisualElementAssets`.

#### `m_OrderInDocument`

- Treat **`m_OrderInDocument` as global** across **`m_VisualElementAssets` and `m_TemplateAssets` together**: each value must appear at most once in the combined set of nodes.
- After edits, renumber so orders are **unique and contiguous** from `0` through `N-1` (depth-first walk from parent `0` is a reliable way). **Per-parent-only** uniqueness is not enough if the same order number is reused in the other array.

Reference IDs for the inserted subtree:

| Node | `m_Id` | `m_RuleIndex` | `rid` |
| ---- | ------ | ------------- | ----- |
| outer portrait wrapper | `1928374651` | `28` | `1084` |
| portrait remapper | `1928374652` | `29` | `1085` |
| `PersonPicture` template | `1928374653` | `-1` | `1086` |

The remapper maps `person` to `position.Player`.

### Added `references.RefIds`

Append the matching serialized-data blocks for:

- `rid 1084` -> `VisualElement/UxmlSerializedData`, `uxmlAssetId 1928374651`, name `PlayerPictureWrap`
- `rid 1085` -> `BindingRemapper/UxmlSerializedData`, `uxmlAssetId 1928374652`, name `PlayerPictureRemapper`
- `rid 1086` -> `TemplateContainer/UxmlSerializedData`, `uxmlAssetId 1928374653`, name `PersonPicture`, `templateId PersonPicture`

Those `uxmlAssetId` values must match the inserted node `m_Id` values.
