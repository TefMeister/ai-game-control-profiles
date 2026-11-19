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

## Tool ideas

- 2026-11-19: Menu-o-matiC and State-o-matiC should make themselves aware of display scaling at start.
- 2026-11-19: a `--key KEY` for `menu_o_matic.py add`, so a key step can be put into a route without pressing it.
