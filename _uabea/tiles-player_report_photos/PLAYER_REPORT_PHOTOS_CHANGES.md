# Player report photos tile — hide overlay (UABEA)

- **Patch folder:** `tiles-player_report_photos`
- **Bundle:** `ui-tiles_assets_all`
- **Dump format:** serialized asset dump (`.json`)
- **Working copies:** `ui-tiles_assets_all/win/` and `ui-tiles_assets_all/mac/` — same filenames as in `win/orig` and `mac/orig` respectively (copy from `orig` before editing on that platform).
- **Stock only:** `win/orig/`, `mac/orig/` — do not modify the canonical baseline if you keep it for diffs; apply the steps below on your working copy before import.

Removes a visible dimension used as an overlay sizing hint and clears the tint on the first pooled colour so player report photo tiles do not show that wash. **Cannot** be done from Skin Builder alone.

## Implementation notes

- After a title update, re-export the bundle, locate the same **`inlineStyle`** assets (match **Path ID** and CAB segment in the filename if names shift), and re-apply the **dimensions** / **colors** edits below on **`win/`** and **`mac/`** working copies — **never** change dumps under **`win/orig/`** or **`mac/orig/`** (those stay vanilla for reference/diffs).
- **`dimensions.Array[n]`** entries are length values: `unit` **1** is typically px; set **`value`** as documented.
- **`colors.Array[n]`** uses float RGBA **0–1**; **`a` = 0** is fully transparent.

---

## Path IDs

| Bundle              | Asset         | Path ID                | Filename suffix (after `--`)                                                 |
| ------------------- | ------------- | ---------------------- | ---------------------------------------------------------------------------- |
| ui-tiles_assets_all | `inlineStyle` | `-812894235568295708`  | `inlineStyle-CAB-651892cf441cf6c29e5bd20791e24f29--812894235568295708.json`  |
| ui-tiles_assets_all | `inlineStyle` | `-3307895459461465053` | `inlineStyle-CAB-651892cf441cf6c29e5bd20791e24f29--3307895459461465053.json` |
| ui-tiles_assets_all | `inlineStyle` | `-1253022491152235485` | `inlineStyle-CAB-651892cf441cf6c29e5bd20791e24f29--1253022491152235485.json` |
| ui-tiles_assets_all | `inlineStyle` | `-4358236461696845827` | `inlineStyle-CAB-651892cf441cf6c29e5bd20791e24f29--4358236461696845827.json` |

---

## Steps

| File (Path ID)           | Field                       | Change                                                                          |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| **-812894235568295708**  | `dimensions.Array[2].value` | Set to **`0.0`** (stock is **78.0**).                                           |
| **-812894235568295708**  | `colors.Array[0].a`         | Set to **`0`** (stock is ~**0.12**; ~**31/255** alpha on a light lavender RGB). |
| **-4358236461696845827** | `dimensions.Array[2].value` | Set to **`0.0`** (stock is **78.0**).                                           |
| **-4358236461696845827** | `colors.Array[0].a`         | Set to **`0`** (stock is ~**0.12**; ~**31/255** alpha on a light lavender RGB). |
| **-3307895459461465053** | `dimensions.Array[2].value` | Set to **`0.0`** (stock is **78.0**).                                           |
| **-3307895459461465053** | `colors.Array[0].a`         | Set to **`0`** (same stock alpha as above).                                     |
| **-1253022491152235485** | `colors.Array[0].a`         | Set to **`0`** (stock is ~**0.043**; ~**11/255** alpha on white).               |

No **`dimensions`** edit on **1253022491152235485** for this patch.
