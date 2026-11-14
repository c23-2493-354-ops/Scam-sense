# Room Lighting Budget

**Project:** Scam Sense: Think Before You Click
**Version reviewed:** 0.1.0 (main branch)
**Author:** Chester Guile P. Castillo, Group G17 (game design)
**Date:** 14 Nov 2026
**Related activity:** Activity 10, Performance and Release Verification (Part IV monitoring plan)

## Why this matters

The Activity 10 benchmark found that Level 1 (scene `Game`) and the menu
backdrop (which shows the Level 1 room) are the slowest scenes, and that Level 1
is GPU-limited (GPU time follows frame time). The group's analysis points to
lighting: many lights with soft shadows redraw the large wall and ceiling meshes.

As the game designer, I will add more rooms. This budget turns the measurement
into a design rule, so a new room does not repeat Level 1's cost.

## Lights saved in each scene

Counted from the `Light` components saved in each `.unity` file on `main`
(lights added by scripts at runtime are not included).

| Scene | Lights | Point | Spot | Directional | With shadows | Avg frame time (Part II) |
|---|---|---|---|---|---|---|
| `Game` (Level 1) | 17 | 15 | 1 | 1 | 7 (5 point, 1 spot, 1 directional) | 13.09 ms |
| `MenuBackdrop_Room1` | 44 | 29 | 14 | 1 | 19 (8 point, 10 spot, 1 directional) | about 11–13 ms (Part III) |
| `Room2_InvestigationOffice` | 6 | 5 | 1 | 0 | 4 | 7.02–8.17 ms (Rooms 2–5) |
| `Room3_DigitalTrap` | 11 | 5 | 6 | 0 | 5 | 7.02–8.17 ms (Rooms 2–5) |
| `Room4_DigitalForensicsLab` | 10 | 4 | 6 | 0 | 3 | 7.02–8.17 ms (Rooms 2–5) |
| `Room5_MacLab` | 10 | 1 | 9 | 0 | 3 | 7.02–8.17 ms (Rooms 2–5) |

## What the numbers show

- The two slowest scenes are the only ones with a shadowed directional light,
  and they have the most point lights (15 and 29).
- The faster rooms use mostly spot lights and have 3–5 shadowed lights.
- A shadowed point light lights in every direction, so its shadows cost more to
  render than a spot light's. This is a likely contributor, not a proven cause.
  Proving it would need a controlled test.

## Design budget for new or changed rooms

Use Rooms 2–5 (all above 120 FPS on the group's test PC) as the reference:

| Rule | Budget |
|---|---|
| Total lights saved in the scene | 11 or fewer |
| Lights with shadows | 5 or fewer |
| Shadowed point lights | Prefer spot lights for shadowed ceiling lamps |
| Directional light indoors | Only if the room has windows that need sunlight |
| Lamps that are only decoration | No shadows |

If a room needs to go over budget, run the benchmark build on that room before
merging. Its average frame time should stay under 12 ms, the Rooms 2–5 limit in
the group's monitoring plan.

## How to recount

From the repository root:

```bash
for f in Assets/Scenes/*.unity; do
  echo "$f $(grep -c '^--- !u!108 ' "$f")"
done
```

`!u!108` is Unity's type id for a Light component.
