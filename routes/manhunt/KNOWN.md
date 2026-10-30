# manhunt: what we know about driving it

Read before every launch. Add what you learn the same session (`known FOLDER --add ...`).
Only game facts: no paths, machine names or people.

## Menus

## Controls

- 2026-10-30: X = the game's own first-person look (Cash stands still; any move key drops it). Keys reach the game as posted window messages; mouse moves only through the proxy's manhunt_vr_input.txt ('M dx dy ms'). (2026-10-30)

## Save spot

- 2026-10-30: launch_to_gameplay loads SAVE SLOT 2. On a PC whose only save is in slot 1 the route stops at save_slot_1 (frozen, still the LOAD GAME list): press Enter once to load slot 1; the level checkpoint matches about 60 s later after the opening scene (2026-10-30).
- 2026-10-30: launch_to_gameplay_slot1.json: the same route for a PC whose only save is in slot 1 (no fingerprint on the load list, a 2.5 s pause then Enter). Ran unattended to gameplay in 156 s, most of it the level's opening scene (2026-10-30).

## Hazards

- 2026-10-30: Walking paths (paths/*.json): the shortest path over the level's own AI path graph, gates inserted as waypoints, walked with forward held and small steering turns. born_again_start_to_hdummy (93 m) stops near its end when the tutorial takes the camera to show the dummy hunter; wait for control, then go on. hdummy_to_hbackalley_b ran into a wall corner below gate Gen_E_L_MeshGLeft_(D)01, which sits a floor higher (2026-10-30).

## What failed

## Tool ideas

- 2026-10-30: Try skipping the level's opening scene (Enter, Space or Esc while it plays): it is most of the ~2.5 min from launch to control (2026-10-30).
