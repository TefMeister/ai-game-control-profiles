# Metro Exodus (2019 original edition): what is known about driving it

Read this before launching. Full detail with confidence tags: `profiles/metro-exodus.json`.

## Getting to gameplay

1. Launch through Steam (`steam://run/412020`). Window title `4A Engine`.
2. Intro films: Escape skips most; the opening film wants E ("PRESS E TO SKIP").
3. Main menu (a train cab panel): **E = CONTINUE** once a save exists. Enter acts on whatever button is selected
   (on a fresh install that is NEW GAME: verify first).
4. Loading story screen: **HOLD E** about 3-4.5 s. If nothing happens, hold again, longer.

## Window

- `user.cfg` (in the game's Saved Games folder) `r_fullscreen off` gives a window the size of the desktop; then
  resize it to a 1280x720 client area with SetWindowPos (asynchronous: never block on a frozen game).
- The game has been seen to come up borderless at full screen size even with `r_fullscreen off`, and the setting
  has been found back at `on` once. Measure the window every launch.
- **It minimises and pauses as soon as it loses focus**, even as a small window. Keep it in front; while minimised
  it draws nothing.

## Controls that work (keyboard, scancode SendInput)

- Arrow keys need the extended-key flag (without it Down arrives as numpad 2).
- Camera: relative mouse moves turn the view.
- Pause: Escape (the first Escape after a chapter starts is sometimes lost: look, then press again), wait 2 s.

## Leaving

- Pause menu: Down x7 to QUIT TO MAIN MENU (verify), Enter, Enter on "unsaved progress".
- Main menu: Escape opens QUIT GAME, Enter. The process exits in a few seconds.

## Hazards

- The current save starts in a toxic-air tunnel: the filter runs out and idling there kills the player. Park the
  game on a menu while waiting (losing focus also pauses it).
- Music is muted in `user.cfg` (`s_music_volume 0.00`).
- **With the OpenXR simulator running, holding E (or WASD) also moves the simulator's head** (E lifts it, about
  2 m per second), even with the game in front. After the loading screen's HOLD E, put the head back to standing
  height with the simulator's own head-pose command, and never walk with WASD during a head test.
