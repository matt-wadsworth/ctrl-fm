# UABEA Changes Required

Manual changes to apply in UABEA during skin development (after building the skin).

**Dev notes only -** End users using bundle releases do not need to apply these changes.

---

## Format reference

For each item:

- **Name:** Short label for the element
- **File:** Bundle filename
- **Path ID (or Name):** Asset path ID or name in UABEA
- **Reference:** e.g. Color [3], m_SerializedData, rid, etc.
- **Note:** What it controls / what to do

---

## Path IDs (all patch files)

Use these to find assets quickly in UABEA (View → Go To Asset, or search by path ID).

| Bundle                      | Filename (patch)              | Path ID              | Change                                                                                                   |
| --------------------------- | ----------------------------- | -------------------- | -------------------------------------------------------------------------------------------------------- |
| ui-tactics_assets_all       | TacticalPlannerHeader         | -966834888143150418  | Tactics header gradient – change rid (1002 → 1001) or update gradient colours.                           |
| ui-tiles_assets_all         | Dugout_4x8_with_collapse      | -389085435411529779  | Dugout 4x8 tile – set `base-template-grow` element `m_RuleIndex` to `6`.                                 |
| ui-tiles_assets_all         | inlineStyle                   | 4355907201153990605  | Dugout 4x8 inline style – add background + border rule at index `6`.                                     |
| ui-tiles_assets_all         | inlineStyle                   | -1082094815230004916 | Messages border – adjust `Color[0]` for messages tile border.                                            |
| ui-tiles_assets_all         | PortalMessagesTile            | -8955157084615708340 | Messages tile – update background class and padding classes/strings (see “Messages Background/Padding”). |
| ui-widgets_assets_all       | inlineStyle                   | -4684259242089685901 | Processing unread indicator – tweak `Color[3]` (dot BG) and `Color[4]` (icon) as needed.                 |
| ui-widgets_assets_all       | inlineStyle                   | -8675747941173082291 | Portal unread indicator – tweak `Color[3]` (dot BG) and `Color[4]` (icon) as needed.                     |
| ui-widgets_assets_all       | inlineStyle                   | 8251223908227938625  | Portal tab inline style – add new background-image tint rule + color entry.                              |
| ui-widgets_assets_all       | navigation-tab-portal-default | -3526698279923353279 | Portal tab layout – move icon next to text, set `row-direction-normal`, assign icon tint rule index.     |
| ui-panelids-uxml_assets_all | inlineStyle                   | 4528579247820316033  | SpeakToSidePanel - Border, Change colour[0]                                                              |

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

| Name                | File                       | Path ID (or Name)       | Reference      | Note                                                                                                                                                                                                                  |
| ------------------- | -------------------------- | ----------------------- | -------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Messages Border     | ui-tiles_assets_all.bundle | -1082094815230004916    | Color [0]      | Border for messages tiles                                                                                                                                                                                             |
| Messages Background | ui-tiles_assets_all.bundle | PortalMessagesTile.uxml | Remove class   | Remove `.midnight-background-colour` so tile uses `.messages-tile-background`; then patch that class in Skin Builder for colour.                                                                                      |
| Messages Padding    | ui-tiles_assets_all.bundle | PortalMessagesTile.uxml | Change classes | Change `.padding-horizontal-global-padding-large` to `padding-left-global-padding-regular` and `padding-right-global-padding-extra-small`. Add the original class to the second element with `.row-direction-normal`. |

---

## Portal Icons

Portal tab icons (e.g. Portal, Squad) need structural changes in **navigation-tab-portal-default** plus a new style rule in the shared **inlineStyle** so the SIImage can be tinted. Both assets are in **ui-widgets_assets_all.bundle**.

**Summary:** Move the SIImage element into the same parent as the tab text (SIText), set that parent to flex row, and apply an inline style to the icon for colour (background-image tint).

**Note:** Do not rely on m_Id or array indices — they can differ per platform. Locate elements by **type** and **class names** (or by following the hierarchy in your dump).

### 1. navigation-tab-portal-default (Path ID: -3526698279923353279)

In **m_VisualElementAssets**:

| Find by type + class                                                               | Change                                                                                                                                                                                                                                                                                                                                                                        |
| ---------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **SIImage** with class `portal-image`                                              | **m_ParentId:** set to the same value as the SIText’s parent (the SIVisible that currently contains the tab text). **m_RuleIndex:** set to the index of the new tint rule you add in inlineStyle (below). **m_Classes:** add `margin-right-global-gap-small` (keep `portal-image`). **m_OrderInDocument:** use a value so the icon is first before the SIText in that parent. |
| **SIVisible** that is the direct parent of the **SIText** with class `portal-text` | **m_Classes:** add `row-direction-normal` (flex row). This container will then have both the SIImage and the SIText as children.                                                                                                                                                                                                                                              |

Resulting structure: the “text” container (the SIVisible that has the portal text) has two children — SIImage first, then SIText — and uses `row-direction-normal`; the SIImage's **m_RuleIndex** points at the new tint rule in inlineStyle.

### 2. inlineStyle (Path ID: 8251223908227938625)

This stylesheet is referenced by **navigation-tab-portal-default** (inlineSheet). Add one new rule and one new colour so the portal icon can be tinted.

| What to change    | Detail                                                                                                                                                                                                 |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **m_Rules**       | **Add a new rule** at the next index (e.g. if you currently have 9 rules, add at index 9). One property: `m_Name` = `-unity-background-image-tint-color`, `m_ValueType` = 4 (color), `valueIndex` = 0. |
| **vector colors** | **Add one ColorRGBA** (e.g. r=0.851, g=0.91, b=0.929, a=1). The new rule's’s valueIndex 0 refers to the first entry in vector colors.                                                                  |

- **Original:** 9 rules, 0 entries in vector colors.
- **Patched:** 10 rules (one new rule appended), 1 entry in vector colors.

Use the **index of that new rule** as the SIImage's **m_RuleIndex** in navigation-tab-portal-default. Adjust the ColorRGBA values to match your skin.

---

## Scoreboard – Left Aligned

### 1. In‑match scoreboard (`OverviewHeader.uxml`)

**File:** ui-match_assets_all  
**Assets:**

- `OverviewHeader.uxml` – path ID **-7819803725380563227**
- `inlineStyle` – path ID **-2719487159082004763**

#### a. inlineStyle – move scoreboard container to the left

In the `inlineStyle` asset, locate **style index `[8]`** (notation `7-1` = `m_ValueType = 7`, `valueIndex = 1`):

- **Rule [0]:** set `m_ValueType = 7`, `valueIndex = 1`
- **Rule [1]:** set `m_ValueType = 7`, `valueIndex = 1`
- **Rule [3]:**
  - Change the property name from **`width`** to **`left`**.
  - Set `m_ValueType = 2`, `valueIndex = 0`

These shifts move the scoreboard block towards the left edge.

#### b. OverviewHeader.uxml – re‑order controls

In `OverviewHeader.uxml`, update the **m_OrderInDocument** for two elements (using their existing IDs):

- Element at index **[7]** → set `m_OrderInDocument` to **9**.
- Element at index **[19]** → set `m_OrderInDocument` to **7**.

This swaps the relative order of the MatchUITacticalOptions and an empty flex-grow container, so the MatchUITacticalOptions moves to the right.

Also on element **[7]**, in **m_Classes**, **add** the class - `margin-right-global-gap-regular`

This adds a gap between the MatchUITacticalOptions and the stats/playback that follow.

### 2. Pre‑match “Kick Off” / Half Time / Full Time scoreboard alignment (`ContinuePanel.uxml`)

**File:** ui-match_assets_all  
**Asset:** `ContinuePanel.uxml` (-398932524086274306) – `inlineStyle` (4016413755955533566)

`ContinuePanel.uxml` (-398932524086274306):

- **Element [8]** - add **`align-items-end`** and **`margin-right-global-gap-regular`** to **m_Classes**.

`inlineStyle` (4016413755955533566):

- **Rule [2]** - Change `align-items` to `m_ValueType = 7`, `valueIndex = 5`.
- **Rule [4]** - Change `margin-top` to **4px** (will need to add it to Dimensions).

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
  const rgba = (h.length === 6 ? h + "FF" : h)
    .match(/.{2}/g)
    .map((b) => parseInt(b, 16) / 255);
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

---

## Dugout popups/tiles

These are to give the in match popups a background colour, as the tiles are transparent otherwise. Unfortunately we cannot patch this with SB.

| Name                     | File                       | Path ID (or Name)                   | Reference                  | Note                                                                                                                                                                                           |
| ------------------------ | -------------------------- | ----------------------------------- | -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Dugout 4x8 collapse tile | ui-tiles_assets_all.bundle | -389085435411529779                 | class `base-template-grow` | In `Dugout_4x8_with_collapse`, find the element with class `base-template-grow` and change its `m_RuleIndex` from `-1` to `6`.                                                                 |
| Dugout 4x8 inline style  | ui-tiles_assets_all.bundle | 4355907201153990605 (`inlineStyle`) | Rule index `6`             | Add a **new style rule at index 6** that defines `background-color`, `border-radius`, `border-width`, `border-color` (radius 12, width 1, using your chosen dark BG and light border colours). |

---

## Extras (not currently applying – notes kept for reference)

### Reports – Gradient Removal

Remove gradients by changing `m_SerializedData` (or equivalent) as below.

| Name                          | File                         | Change                                    | Reference        | Value       |
| ----------------------------- | ---------------------------- | ----------------------------------------- | ---------------- | ----------- |
| PlayerReportHeader            | ui-tiles_assets_all.bundle   | Remove gradient from player               | m_SerializedData | 1002 → 1001 |
| NonPlayerReportHeader         | ui-tiles_assets_all.bundle   | Remove gradient from non-player           | m_SerializedData | 1004 → 1002 |
| RetiredPlayerReportHeader     | ui-tiles_assets_all.bundle   | Remove gradient from player               | m_SerializedData | 1002 → 1001 |
| PlayerReportPreviewTooltip    | ui-widgets_assets_all.bundle | Remove gradient from tooltip (player)     | m_SerializedData | 1007 → 1006 |
| NonPlayerReportPreviewTooltip | ui-widgets_assets_all.bundle | Remove gradient from tooltip (non-player) | m_SerializedData | 1008 → 1002 |

### Player Portrait – sizes/scale

#### 1. Margin and scale (styles)

**File:** ui-styles_assets_default  
**Asset:** FigmaGeneratedStyles

| Reference | Type      | Original | Change to |
| --------- | --------- | -------- | --------- |
| [5139]    | Dimension | -8       | -26       |
| [6755]    | Float     | 1.2      | 1         |
| [6756]    | Float     | 1.2      | 1         |

- There may be more than one [5139]; ensure you edit the **Dimension** value (margin).

#### 2. Image/container dimensions (tiles)

**File:** ui-tiles_assets_all  
**Path ID:** -1940069326759809061

- **View → Go To Asset**, enter path ID: `-1940069326759809061`
- Scroll to the **Dimensions** array:

| Index | Meaning          | Original | Change to |
| ----- | ---------------- | -------- | --------- |
| [0]   | Container height | 148      | 170       |
| [2]   | Image width      | 116      | 140       |
