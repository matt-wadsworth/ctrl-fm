# Dugout 4×8 tile / popups — UABEA changes

- **Patch folder:** `match-dugout_tile`
- **Bundle:** `ui-tiles_assets_all`
- **Dump format:** `.txt` (UABEA text dumps)
- **Dumps in repo:** `ui-tiles_assets_all/win/`, `ui-tiles_assets_all/mac/`

Gives in-match dugout popups a background; tiles are otherwise transparent. **Cannot** be done from Skin Builder alone.

---

## Path IDs

| Bundle                | Filename                   | Path ID              | Change                                                                 |
| --------------------- | -------------------------- | -------------------- | ---------------------------------------------------------------------- |
| ui-tiles_assets_all   | `Dugout_4x8_with_collapse` | -389085435411529779  | Set `base-template-grow` **m_RuleIndex** to **6**.                      |
| ui-tiles_assets_all   | `inlineStyle`              | 4355907201153990605  | Add background + border rule at index **6**.                          |

---

## Steps

| Name                     | Path ID (or Name)                   | Reference                  | Note                                                                                                                                                                                           |
| ------------------------ | ----------------------------------- | -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Dugout 4×8 collapse tile | -389085435411529779                 | class `base-template-grow` | In **Dugout_4x8_with_collapse**, find **base-template-grow** and set **m_RuleIndex** from **-1** to **6**.                                                                                     |
| Dugout 4×8 inline style  | 4355907201153990605 (`inlineStyle`) | Rule index **6**           | Add a **new style rule at index 6**: `background-color`, `border-radius`, `border-width`, `border-color` (e.g. radius **12**, width **1**, your dark BG and light border).                     |
