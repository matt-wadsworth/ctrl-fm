# Tactical planner header gradient (UABEA)

- **Patch folder:** `tactics-remove_planner_header`
- **Bundle:** `ui-tactics_assets_all`
- **Dump format:** `.txt`
- **Dumps in repo:** `ui-tactics_assets_all/win/`, `ui-tactics_assets_all/mac/`

**Asset:** **TacticalPlannerHeader** (Path ID **-966834888143150418**). The element uses a **Gradient** at **rid = 1002**; the **Array** under that item defines colours.

---

## Quick option

Change **rid from 1002 → 1001** to remove the gradient (no custom colours).

---

## Custom gradient (keep rid 1002)

**First colour (colourA, left):** byte run starting at **index 39** (16 bytes = 4 × float32 RGBA, little-endian).  
**Second colour (colourB, right):** starting at **index 93**.

Replace those 16-byte runs with bytes from the hex helper below (Unity little-endian float32 per channel).

Structure (illustrative):

```
"Array": [
  // indices 0–38: metadata
  2, 50, 0, 0, 0, 33, 83, 73, 46, 86, 105, 115, 117, 97, 108, 83,
  99, 114, 105, 112, 116, 105, 110, 103, 46, 67, 111, 108, 111,
  114, 86, 105, 115, 117, 97, 108, 65, 114, 103,

  // indices 39–54: colourA (left) — 4 × float32 RGBA
  102, 102, 62, 61, 102, 102, 62, 61, 102, 102, 62, 61, 0, 0, 128, 63,

  // indices 55–92: metadata
  50, 0, 0, 0, 33, 83, 73, 46, 86, 105, 115, 117, 97, 108, 83,
  99, 114, 105, 112, 116, 105, 110, 103, 46, 67, 111, 108, 111,
  114, 86, 105, 115, 117, 97, 108, 65, 114, 103,

  // indices 93–108: colourB (right)
  102, 102, 62, 61, 102, 102, 62, 61, 102, 102, 62, 61, 0, 0, 128, 63
]
```

### Hex → Unity gradient bytes

Run in browser **DevTools → Console** (F12):

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

Paste the logged array at index **39** or **93** as needed.
