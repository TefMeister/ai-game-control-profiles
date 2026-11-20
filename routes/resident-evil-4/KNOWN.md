# Resident Evil 4 (2023): what is known about driving it

Steam build 22377325 (app id 2050650), RE Engine, DirectX 12. Read this before the game starts; add to it the same
session something new is learned. First written 2026-11-19 (first live session, the game as shipped and then with a
pass-through `dinput8.dll` beside the exe).

## Getting to gameplay

- Launch through Steam: `steam://rungameid/2050650`. The process is up within about 5 s and the title picture
  ("START GAME") within about 30 s. No launcher, no intro videos to skip, no first-run questions (2026-11-19, n=4).
- **Title:** Enter. **Main menu** (top to bottom): MAIN STORY, BONUSES, OPTIONS, ONLINE STORE, EXIT. Space confirms,
  Esc goes back, the list wraps (Up from MAIN STORY lands on EXIT).
- **MAIN STORY** opens: CONTINUE, LOAD GAME, NEW GAME, RESULTS. Space on CONTINUE loads the last save: a chapter card
  with a progress bar, then play starts by itself about 25 s later, no key needed.
- Routes: `launch_to_main_menu.json` (replayed 2026-11-19, n=2), `main_menu_to_gameplay.json`,
  `main_menu_to_closed.json`. Taught screen: `main_menu` in `states.json`.

## Quitting

- Main menu, EXIT (Up once from MAIN STORY), Space. "Close the game?" opens with **No** highlighted: Up, then Space.
  The process ends about 2 s later.
- 2026-11-20: at 200 % display scaling Menu-o-matiC's and State-o-matiC's window pictures are the top-left quarter, so `launch_to_main_menu` gets lost at the title. Run them through a wrapper that switches display-scaling awareness on first (visceral-re4-vr `tools/mom_dpi.py`, `tools/som_dpi.py`): every route then runs through (n=3 launches).
- 2026-11-20: taught screens in states.json: `gameplay` (Leon's back, lower left of centre: the HUD hides at rest) and `pause_menu` (its word list, left). The pause menu's blurred scene also matches gameplay's sign, so `pause_menu` must stay listed BEFORE `gameplay` (State-o-matiC takes the first match). `teach` takes the states FILE and grabs the picture itself.
- 2026-11-20: MAIN STORY opens CONTINUE / LOAD GAME / NEW GAME / RESULTS; NEW GAME asks GAME MODE (ASSISTED / STANDARD / HARDCORE; the mouse pointer resting in the middle lights HARDCORE: Up once for STANDARD), then the opening cutscene starts at once. In a cutscene Esc pauses it (Skip Cutscene, Photo Mode, Back); a paused cutscene shows a frozen blurred frame.
- 2026-11-20: a NEW GAME started for a test can be left without an autosave: a polite close request (taskkill without /F) from the paused opening cutscene closes the game in seconds; the save slots stayed as they were, so CONTINUE still loads the old save (n=1).

## Settings

- The settings file is `local_config.ini` beside the exe. On a fresh install it holds only the graphics card and the
  list of screen modes; the `[AppRender]` block appears after the first change made in OPTIONS.
- **Window:** `AppWindowMode=0` is "Window" (set once in OPTIONS, GRAPHICS, Display Mode: Full Screen, Borderless
  Window, Window). The window's size is `AppResolution=<n>`, a position in the list of distinct sizes made from the
  file's own `DisplayMode<i>_Width/Height` lines in order (on a 3440x1440 screen: 7 = 1280x720, 19 = 1920x1080,
  23 = 3440x1440). With `AppWindowMode=0` and `AppResolution=7` the client area measures exactly 1280x720
  (2026-11-19, n=3 launches). ⚠️ The list depends on the monitor: work the number out from the file, never copy it.
- **Music:** OPTIONS, AUDIO, "BGM Volume" (third row), Left until the slider is empty (10 steps). It is kept in the
  save data, not in `local_config.ini`.
- OPTIONS tabs, moved with Q / E: CONTROLS, CAMERA, DISPLAY, GRAPHICS, LANGUAGE, AUDIO, ACCESSIBILITY, SERVER.
  Changes apply at once; Esc leaves and saves.
- Motion blur, depth of field, lens distortion, lens flare and film grain each have a line in `[AppRender]`
  (`AppMotionBlur`, `AppDepthOfField`, `AppLensDistorsion`, `AppLensFrare`, `AppFilmGrainNoise`).

## Input

- Keyboard by SendInput reaches the menus (Enter, Space, Esc, arrows, Q, E).
- The mouse pointer highlights whatever menu row it rests on: keep it off the list, or a key count starts from the
  wrong row.

## Hazards

- NEW GAME sits two rows under CONTINUE; a new game's autosaves would replace the player's own. Look before Space.
- On a screen set to 200 % scaling a capture tool that is not told about the scaling sees only the top-left quarter
  of a fullscreen game. (Our tools: run them with `__COMPAT_LAYER=HighDpiAware` until they handle it themselves.)
- A remote-desktop picture (RustDesk) was black after the session that ran the game fullscreen once; the PC itself was
  fine, and reconnecting the remote desktop brought the picture back (2026-11-19, n=1; the fullscreen run as the cause
  is a guess). The game runs windowed since.
- 2026-11-19: The main menu is drawn about a second before it takes keys: a key sent the moment the picture matches is lost (2026-11-19, n=2: once on the way into play, once on the way out). The routes that start at the main menu now begin with a 2 s wait.
- 2026-11-19: From the Chapter 1 save the village gate stands open and the villagers attack within a minute of walking in; standing still there means death in about 30 s (2026-11-19). In a new game's forest the character twice walked into something by the signpost and stood still for a minute while W was held: walk that path once with Tefa and record it.
- 2026-11-19: State-o-matiC calls ordinary play 'loading' in this game (it streams from disk at 170 to 180 MB/s while walking): do not trust that verdict here; teach a gameplay screen first (2026-11-19, n=2).
- 2026-11-19: Quitting to the menu from play can land on the TITLE screen (START GAME) instead of the main menu (2026-11-19, n=1, after a pause-menu quit in the village with enemies near): gameplay_to_closed then reports 'lost' at its main_menu wait. Press Enter and carry on; the route should accept either screen.
- 2026-11-20: Writing game memory from OUTSIDE while the game draws (a test poke of a render setting) stopped the frame loop for good within a second (n=1); the window kept answering, so State-o-matiC says "still", not "hung". Try such a setting through our own frame step, not from outside.
- 2026-11-20: A debugger attached in debug mode (Ghidrust) that does not really let go keeps a force-closed game alive (exit code set, process kept) until the debugger program ends. Check `CheckRemoteDebuggerPresent` before closing.
- 2026-11-20: Our connector's pipe takes ONE client at a time: a sampler left running (or hung) makes every other ask fail with "nothing is listening". Stop it first.

## Tool ideas

- 2026-11-19: Menu-o-matiC and State-o-matiC should make themselves aware of display scaling at start.
- 2026-11-19: a `--key KEY` for `menu_o_matic.py add`, so a key step can be put into a route without pressing it.
- 2026-11-19: sheet.py / bright.py of the third live session (several small looks tiled into one picture, optionally brightened for dark scenes) saved many pictures: worth a 'looks --sheet' in Menu-o-matiC.
- 2026-11-20: visceral-re4-vr `tools/re4_sample.py` (read connector values again and again, one line per look; `where:type` prints a type name) and `tools/re4_poke.py` (a one-off outside write, prints old values): candidates for the bridge's own tools.

## Controls

- 2026-11-19: In play (2026-11-19): W A S D walk, Tab opens the attache case and Tab closes it again, Esc opens the pause menu. Move-o-matiC's mouse turn may not reach the game (two pictures after a turn looked alike; not examined). A turn to the left must be written after a double dash (turn WINDOW -- -300,0), or the tool reads it as an option.
- 2026-11-19: The mouse turn of Move-o-matiC DOES reach the game (2026-11-19, pictures before and after turn 400,0 in the lane); in the dark forest of a new game the same numbers turned the view far less (not examined). Left click swings the knife and breaks a yellow-marked crate; F picks up what falls out (Pesetas x200 from the crate under the first shed in the village, n=1). Numpad keys did nothing visible in the menus or in play (our mod uses them).
- 2026-11-20: A grab on demand: from the Chapter 1 save, `chapter1_save_to_village_gate` then W held 4 s; a villager grabs Leon about 60 s later (n=1) and a second grab followed; the second killed him (YOU ARE DEAD, CONTINUE with Space reloads the save in about 25 s).

## Menus

- 2026-11-19: A cutscene: Esc pauses it and shows Tab = Skip Cutscene; Tab skips at once (n=1, the new game's opening). NEW GAME (third row under MAIN STORY) asks the game mode (ASSISTED, STANDARD, HARDCORE; the mouse pointer may sit on a row: press Up twice and look before Space), then plays the opening; after the skip Chapter 1 starts in a dark forest. YOU ARE DEAD: CONTINUE is the first row, Space reloads the last save in about 25 s.


## What failed

- 2026-11-19: Reaching the new game's first door (the lodge) by small pictures and timed walking, 2026-11-19: stuck twice by the signpost, given up after about ten moves.
- 2026-11-19: State-o-matiC did not use the taught main_menu screen for three sessions: states.json named its window 're4.exe' while every watch asked for 'Resident Evil 4', so nothing matched. Corrected 2026-11-19 (window 'Resident Evil 4', exe 're4.exe'); a gameplay screen is still to be taught at the next launch.
- 2026-11-20: in play, by SendInput (visceral-re4-vr `tools/re4_key.py`): right mouse button held = aim, left button = fire, R = reload, a relative mouse move turns / tilts the view (10 small steps). The chapter 1 save starts with the handgun's magazine EMPTY (0/21): reload before any firing test (n=1).
- 2026-11-20: spent casings are too small to judge in the dark leaf litter from the over-the-shoulder camera; a casing-lifetime check needs a bright, flat floor or a reading from the game's data.
