# Message / news unread indicators (UABEA)

- **Patch folder:** `messages-unread_indicators`
- **Bundle:** `ui-widgets_assets_all`
- **Dump format:** serialized asset dump (`.json`)
- **Working copies:** `ui-widgets_assets_all/win/` (copy from `win/orig` or `mac/orig` before editing)
- **Stock only:** `win/orig/`, `mac/orig/` — do not modify; keep as the unpatched baseline.

## Implementation notes

- Copy both `inlineStyle` JSON files from **`orig`** into **`win/`** or **`mac/`**, then set only **`colors[3]`** and **`colors[4]`** as below. If a future title update changes the colour pool length, re-export the stock bundle and confirm indices **3** and **4** still back the unread dot and icon tint before editing.

## Assets

| Filename (suffix) | Path ID              | Role                    |
| ----------------- | -------------------- | ----------------------- |
| `inlineStyle-CAB-019ad19fde35e70c30c2e7a4cd52c3af--8675747941173082291.json` | `-8675747941173082291` | Portal unread indicator |
| `inlineStyle-CAB-019ad19fde35e70c30c2e7a4cd52c3af--4684259242089685901.json` | `-4684259242089685901` | Processing unread indicator |

## Target colours

Apply the same two pooled colour values to both `inlineStyle` dumps:

| Pool index | Use            | Target RGBA                          |
| ---------- | -------------- | ------------------------------------ |
| `colors[3]` | unread dot fill | `r 0.165`, `g 0.361`, `b 1`, `a 1`   |
| `colors[4]` | unread icon tint | `r 0.369`, `g 0.42`, `b 0.463`, `a 1` |

## Steps

1. Open each `inlineStyle` asset by filename or Path ID.
2. In `colors.Array`, set `colors[3]` and `colors[4]` to the target values above.
3. Leave the rest of the sheet unchanged.
