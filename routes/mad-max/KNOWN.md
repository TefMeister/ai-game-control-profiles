# Mad Max (2015): what is known about driving it

Read this before launching. Full detail with confidence tags: `profiles/mad-max.json`.

## Getting to gameplay

1. Launch through Steam. The window appears after ~5-20 s; the legal screen (logos, spinner) runs ~50-80 s.
2. **Screenshot before pressing anything at the title.** A press during a slow legal screen is swallowed.
   Title reads PRESS ANY BUTTON: Enter.
3. Main menu: RESUME GAME is highlighted (orange). **NEW GAME is directly below it: verify the highlight.** Enter.
4. ~90 s to a playable world.

## Controls that work (keyboard, scancode SendInput)

- W/A/S/D move; V switches the car to the cockpit camera; R enters a vehicle.
- Hold keys 250-300 ms; shorter taps can be missed.
- Numpad hotkeys need **NumLock on**, or they arrive as navigation keys.
- Settings live in `settings.ini` beside the exe (read at start; edit with the game closed, back it up first).
  Music=0, MotionBlur=0 are set. HQDof=0 does NOT remove the cockpit near blur.

## Leaving

- Quickest clean exit: a normal window close message; the process is gone in ~2-4 s.
- Menu route: Esc, Down x3 to EXIT TO MAIN MENU (verify), Enter, Enter; then Down x7 to EXIT GAME (verify), Enter.

## Hazards

- The car is the save spot's starting state; nothing hostile nearby.
- Window is 784x561 client area, desktop untouched.
- **Direct start** (for headset-link tests): run `MadMax.exe` from its folder with a `steam_appid.txt` holding
  `234140` beside it (Steam running). Started through Steam's launcher, the Steam overlay loops our headset link and
  the game window went black; started directly, it did not (2026-10-15). Fixed in our build the same day, so a
  normal Steam start works again; the direct start stays useful as a control.
