# UABEA extras (reference only)

**Not** part of the active patch set in this repo — kept for repeatability on other dumps or experiments.

---

## Reports — gradient removal

Remove gradients by changing **m_SerializedData** (or equivalent):

| Name                          | File                         | Change                          | Reference        | Value       |
| ----------------------------- | ---------------------------- | ------------------------------- | ---------------- | ----------- |
| PlayerReportHeader            | ui-tiles_assets_all.bundle   | Remove gradient from player     | m_SerializedData | 1002 → 1001 |
| NonPlayerReportHeader         | ui-tiles_assets_all.bundle   | Remove gradient from non-player | m_SerializedData | 1004 → 1002 |
| RetiredPlayerReportHeader     | ui-tiles_assets_all.bundle   | Remove gradient from player     | m_SerializedData | 1002 → 1001 |
| PlayerReportPreviewTooltip    | ui-widgets_assets_all.bundle | Tooltip (player)                | m_SerializedData | 1007 → 1006 |
| NonPlayerReportPreviewTooltip | ui-widgets_assets_all.bundle | Tooltip (non-player)            | m_SerializedData | 1008 → 1002 |

---

## Player portrait — sizes / scale

### 1. Margin and scale (styles)

**File:** ui-styles_assets_default  
**Asset:** FigmaGeneratedStyles

| Reference | Type      | Original | Change to |
| --------- | --------- | -------- | --------- |
| [5139]    | Dimension | -8       | -26       |
| [6755]    | Float     | 1.2      | 1         |
| [6756]    | Float     | 1.2      | 1         |

There may be more than one **[5139]**; edit the **Dimension** (margin) occurrence you intend.

### 2. Image / container dimensions (tiles)

**File:** ui-tiles_assets_all  
**Path ID:** -1940069326759809061

**View → Go To Asset** with path ID **-1940069326759809061**. In **Dimensions**:

| Index | Meaning          | Original | Change to |
| ----- | ---------------- | -------- | --------- |
| [0]   | Container height | 148      | 170       |
| [2]   | Image width      | 116      | 140       |
