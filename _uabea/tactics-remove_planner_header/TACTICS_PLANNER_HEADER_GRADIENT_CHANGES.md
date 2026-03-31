# Tactical planner header gradient (UABEA)

- **Patch folder:** `tactics-remove_planner_header`
- **Bundle:** `ui-tactics_assets_all`
- **Dump format:** serialized asset dump (`.json`)
- **Working copy:** `ui-tactics_assets_all/win/` (copy from `win/orig` or `mac/orig` before editing)
- **Stock only:** `win/orig/`, `mac/orig/` — do not modify; keep as the unpatched baseline.

## Asset

| Filename | Path ID | Role |
| -------- | ------- | ---- |
| `TacticalPlannerHeader-CAB-3334bbe2d7ac7af32e86413e81ff46ff--966834888143150418.json` | `-966834888143150418` | Remove the planner header gradient |

## Target change

In `TacticalPlannerHeader`, on the **`SI.Bindable.SIGradient`** element with class **`frame-tactics-nav-menu`**, change **`m_SerializedData.rid`** from **`1002`** to **`1001`**.

This is the only required change.

## Important

- Do **not** edit the `references.RefIds` list just because it still contains a block with `rid: 1002`.
- Do **not** rewrite the gradient byte array.
- The patch is only the serialized reference switch on the element that currently points at `rid 1002`.
