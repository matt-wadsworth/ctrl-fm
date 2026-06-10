# Tactics misc fixes (UABEA)

- **Patch folder:** `tactics-fixes`
- **Bundle:** `ui-tactics_assets_all`
- **Dump format:** serialized asset dump (`.json`)
- **Stock baseline:** `win/orig/` and `mac/orig/` — do not modify; keep as the unpatched baseline.
- **Working imports:** `win/*.json` and `mac/*.json` (siblings of `orig/`, same basenames per platform).

## Asset

| Filename | Path ID | Role |
| -------- | ------- | ---- |
| `TacticalPlannerPhasesPitches-CAB-3334bbe2d7ac7af32e86413e81ff46ff-3412805035020634299.json` | `3412805035020634299` | Team instructions pitch header spacing |

## Target change

In `TacticalPlannerPhasesPitches`, on the **`TacticalPlannerTeamInstructionsPitchHeader`** template instance in **`m_TemplateAssets`**, set **`m_Classes.Array`** to:

```json
[
  "margin-bottom-4",
  "padding-left-global-padding-large",
  "padding-top-global-padding-large"
]
```

Stock has only `"margin-bottom-4"`. This is the only required change.

## Import checklist

1. Re-import the patched JSON into **`ui-tactics_assets_all`** on each platform (`win/` on Windows, `mac/` on macOS).
2. Smoke test the tactical planner team-instructions pitch view; confirm the pitch header has correct top/left padding aligned with global spacing.
