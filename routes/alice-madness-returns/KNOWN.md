# alice-madness-returns: what we know about driving it

Read before every launch. Add what you learn the same session (`known FOLDER --add ...`).
Only game facts: no paths, machine names or people.

## Menus

- 2026-10-26: Launch (steam run id 19680) -> autosave warning -> 'press enter' copyright -> PROFILE SELECT -> main menu -> CONTINUE: Enter on each. launch_to_gameplay.json replays it in about a minute.

## Controls

## Save spot

- 2026-10-26: CONTINUE loads the Whitechapel kitchen (the cook and Alice), standing still, in control.

## Hazards

- 2026-10-26: Fullscreen=True can come back in AliceEngine.ini (My Games config); the game then switches the whole screen to 1280x720. Set Fullscreen=False with the game CLOSED (it rewrites the file on exit). Check with windowcheck after launch.
- 2026-10-26: With the OpenXR Simulator, its preview window opens ON TOP of the game window; the menu replay then compares the wrong picture and stops at PROFILE SELECT ('frozen'). Move the preview window off the game first; resume with 'run --from 5'.
- 2026-10-26: An intermittent crash during the level load (game exe, access violation) is known since September; it came once in two loads on this day. Relaunch once before suspecting a mod.

## What failed

- 2026-10-26: Closing: WM_CLOSE to the game window quits cleanly in about 2 s (used three times); no in-game quit route recorded yet.

## Tool ideas

- 2026-10-26: Menu-o-matiC could look for known overlay windows (the OpenXR Simulator preview) covering the game window before a checkpoint, move them aside, and say so, instead of reporting 'frozen'.
