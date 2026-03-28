# Message / news unread indicators (UABEA)

- **Patch folder:** `messages-unread_indicators`
- **Bundle:** `ui-widgets_assets_all`
- **Dump format:** `.txt`
- **Dumps in repo:** `ui-widgets_assets_all/win/`, `ui-widgets_assets_all/mac/`

Manual edits in **ui-widgets_assets_all** for unread dots and icons on portal and processing views. End users of bundle releases do not apply these.

---

## Path IDs

| Bundle                | Filename      | Path ID              | Change                                                                                    |
| --------------------- | ------------- | -------------------- | ----------------------------------------------------------------------------------------- |
| ui-widgets_assets_all | `inlineStyle` | -4684259242089685901 | Processing unread — tweak `Color[3]` (dot BG) and `Color[4]` (icon) as needed.            |
| ui-widgets_assets_all | `inlineStyle` | -8675747941173082291 | Portal unread — tweak `Color[3]` (dot BG) and `Color[4]` (icon) as needed.                |

---

## What to change

| Name                           | File                         | Path ID (or Name)    | Reference | Note                                            |
| ------------------------------ | ---------------------------- | -------------------- | --------- | ----------------------------------------------- |
| Message Indicator (Dot)        | ui-widgets_assets_all.bundle | -8675747941173082291 | Color [3] | BG Unread indicator (circle/dot)                |
| Message Indicator (Icon)       | ui-widgets_assets_all.bundle | -8675747941173082291 | Color [4] | Color of the icon in the news item (top right)  |
| Processing panel unread (Dot)  | ui-widgets_assets_all.bundle | -4684259242089685901 | Color [3] | Processing — BG Unread indicator (circle/dot)   |
| Processing panel unread (Icon) | ui-widgets_assets_all.bundle | -4684259242089685901 | Color [4] | Processing — Color of the icon in the news item |

Adjust colour indices to match your skin palette.
