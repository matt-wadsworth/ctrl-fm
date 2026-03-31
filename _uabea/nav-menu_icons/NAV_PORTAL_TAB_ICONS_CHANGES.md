# Portal tab icons (navigation) — UABEA changes

- **Patch folder:** `nav-menu_icons`
- **Bundle:** `ui-widgets_assets_all`
- **Dump format:** serialized asset dump (`.json`)
- **Working copies:** `ui-widgets_assets_all/win/` (copy from `win/orig` or `mac/orig` before editing)
- **Stock only:** `win/orig/`, `mac/orig/` — do not modify; keep as the unpatched baseline.

## Implementation notes

- Copy both JSON assets from **`orig`** into **`win/`** or **`mac/`**, then apply the sections below.
- The tint **`m_RuleIndex`** on the `SIImage` must match the **index of the appended rule** in `inlineStyle` (0-based). On the current **`orig`** dump there are **nine** existing rules (**0–8**), so the new tint is **rule `9`**. If the stock sheet gains or loses rules, renumber the image’s **`m_RuleIndex`** accordingly.

## Assets

| Filename | Path ID | Role |
| -------- | ------- | ---- |
| `inlineStyle-CAB-019ad19fde35e70c30c2e7a4cd52c3af-8251223908227938625.json` | `8251223908227938625` | Add icon tint rule |
| `navigation-tab-portal-default-CAB-019ad19fde35e70c30c2e7a4cd52c3af--3526698279923353279.json` | `-3526698279923353279` | Move icon next to text and assign the tint rule |

## `inlineStyle` target

Append one new rule at the end of `m_Rules.Array` for the portal icon tint.

- Property name: `-unity-background-image-tint-color`
- `m_ValueType = 4`
- `valueIndex = 0`

Append one pooled colour:

- `colors[0] = { r: 0.851, g: 0.91, b: 0.929, a: 1 }`

On the current stock **`orig`** dump this becomes rule index **`9`**; the important part is that the **`SIImage`** **`m_RuleIndex`** matches that appended rule.

## `navigation-tab-portal-default` target structure

Find the tab text and icon by hierarchy, not by `m_Id`:

1. Find the `SIText` with class `portal-text`.
2. Find its direct parent `SIVisible`.
3. Find the `SIImage` with class `portal-image`.

Apply these changes:

- Move the `SIImage` so its `m_ParentId` matches the `SIText` parent.
- Add `row-direction-normal` to that shared parent `SIVisible`.
- Add `margin-right-global-gap-small` to the `SIImage` classes and keep `portal-image`.
- Set the `SIImage` `m_RuleIndex` to the new tint rule index from `inlineStyle`.
- Ensure the `SIImage` comes before the `SIText` in document order.

Result: the portal tab label becomes a single horizontal row with the icon tinted to the target colour above.
