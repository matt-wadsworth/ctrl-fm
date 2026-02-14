# UABEA Changes Required

Manual changes to apply in UABEA during skin development (after building the skin).
**Dev notes only.** End users using bundle releases, do not need to apply these changes.

---

## Format reference

For each item:

- **Name:** Short label for the element
- **File:** Bundle filename
- **Path ID (or Name):** Asset path ID or name in UABEA
- **Reference:** e.g. Color [3], m_SerializedData, rid, etc.
- **Note:** What it controls / what to do

---

## Message indicators

| Name                           | File                         | Path ID (or Name)    | Reference | Note                                            |
| ------------------------------ | ---------------------------- | -------------------- | --------- | ----------------------------------------------- |
| Message Indicator (Dot)        | ui-widgets_assets_all.bundle | -8675747941173082291 | Color [3] | BG Unread indicator (circle/dot)                |
| Message Indicator (Icon)       | ui-widgets_assets_all.bundle | -8675747941173082291 | Color [4] | Color of the icon in the news item (top right)  |
| Processing panel unread (Dot)  | ui-widgets_assets_all.bundle | -4684259242089685901 | Color [3] | Processing - BG Unread indicator (circle/dot)   |
| Processing panel unread (Icon) | ui-widgets_assets_all.bundle | -4684259242089685901 | Color [4] | Processing - Color of the icon in the news item |

---

## Borders & tiles

| Name                | File                       | Path ID (or Name)       | Reference    | Note                                                                                                                             |
| ------------------- | -------------------------- | ----------------------- | ------------ | -------------------------------------------------------------------------------------------------------------------------------- |
| Messages Border     | ui-tiles_assets_all.bundle | -1082094815230004916    | Color [0]    | Border for messages tiles                                                                                                        |
| Messages Background | ui-tiles_assets_all.bundle | PortalMessagesTile.uxml | Remove class | Remove `.midnight-background-colour` so tile uses `.messages-tile-background`; then patch that class in Skin Builder for colour. |

---

## Reports – Gradient Removal

Remove gradients by changing `m_SerializedData` (or equivalent) as below.

| Name                          | File                         | Change                                    | Reference        | Value       |
| ----------------------------- | ---------------------------- | ----------------------------------------- | ---------------- | ----------- |
| PlayerReportHeader            | ui-tiles_assets_all.bundle   | Remove gradient from player               | m_SerializedData | 1002 → 1001 |
| NonPlayerReportHeader         | ui-tiles_assets_all.bundle   | Remove gradient from non-player           | m_SerializedData | 1004 → 1002 |
| RetiredPlayerReportHeader     | ui-tiles_assets_all.bundle   | Remove gradient from player               | m_SerializedData | 1002 → 1001 |
| PlayerReportPreviewTooltip    | ui-widgets_assets_all.bundle | Remove gradient from tooltip (player)     | m_SerializedData | 1007 → 1006 |
| NonPlayerReportPreviewTooltip | ui-widgets_assets_all.bundle | Remove gradient from tooltip (non-player) | m_SerializedData | 1008 → 1002 |

---

## Tactics – TacticalPlannerHeader Gradient

**File:** ui-tactics_assets_all.bundle  
**Element:** TacticalPlannerHeader

- The element uses a **Gradient**.
- Gradient is set at **rid = 1002**.
- The array of units in this item controls the colours used.

**Quick option:** Change the element’s **rid from 1002 to 1001** to remove the gradient entirely (no custom colour/gradient needed).

**Custom gradient (rid 1002):**  
The `"Array"` under this item defines the gradient.

- **First colour (colourA, left side of bar):** starts at **index 39** (16 bytes = 4 × float32 RGBA).
- **Second colour (colourB, right side of bar):** starts at **index 93** (16 bytes = 4 × float32 RGBA).

Replace those 16-byte runs with the output of the hex converter below (Unity expects little-endian float32 per channel).

Structure:

```
"Array": [
  // indices 0–38: metadata
  2, 50, 0, 0, 0, 33, 83, 73, 46, 86, 105, 115, 117, 97, 108, 83,
  99, 114, 105, 112, 116, 105, 110, 103, 46, 67, 111, 108, 111,
  114, 86, 105, 115, 117, 97, 108, 65, 114, 103,

  // indices 39–54: colourA (left) – 4 × float32 RGBA, little-endian
  102, 102, 62, 61, 102, 102, 62, 61, 102, 102, 62, 61, 0, 0, 128, 63,

  // indices 55–92: metadata
  50, 0, 0, 0, 33, 83, 73, 46, 86, 105, 115, 117, 97, 108, 83,
  99, 114, 105, 112, 116, 105, 110, 103, 46, 67, 111, 108, 111,
  114, 86, 105, 115, 117, 97, 108, 65, 114, 103,

  // indices 93–108: colourB (right) – 4 × float32 RGBA, little-endian
  102, 102, 62, 61, 102, 102, 62, 61, 102, 102, 62, 61, 0, 0, 128, 63
]
```

### Hex → Unity gradient bytes (for text colour)

Use this to turn a hex colour into the 16-byte sequence to paste at index 39 or 93.

**Online:** No dedicated tool found that outputs Unity’s little-endian float32 byte list. Easiest is to run the script below in the browser **DevTools → Console** (F12) and call `hexToUnityGradientBytes("#1F1F1F")` (or your hex); then copy the logged array into the bundle.

```javascript
function hexToUnityGradientBytes(hex) {
  const h = hex.replace("#", "");
  const rgba = (h.length === 6 ? h + "FF" : h).match(/.{2}/g).map((b) => parseInt(b, 16) / 255);
  if (rgba.length !== 4) throw new Error("Hex must be 6 or 8 digits.");
  const buf = new ArrayBuffer(16);
  const view = new DataView(buf);
  rgba.forEach((f, i) => view.setFloat32(i * 4, f, true));
  const bytes = [...new Uint8Array(buf)];
  console.log(JSON.stringify(bytes));
  return bytes;
}
// Example: hexToUnityGradientBytes("#1F1F1F");
```
