# Prince of Persia (2008): what is known about driving it

Read this before launching. Written 2026-10-16 from the sessions of 2026-09-07 and 2026-10-16; the full detail,
including the disproved readings, is in `profiles/prince-of-persia-2008.json`.

## Getting to gameplay (fully automatic since 2026-10-16)

1. **Start through Steam** (`steam://rungameid/19980`). Starting the exe directly exits within a second.
2. **The Ubisoft launcher** is an ordinary dialog: send `BM_CLICK` to its "Launch the game!" button. No pixels needed.
3. **Logos, then "PRESS ANY KEY"** about 40 s after the click. Bring the game window truly to the front (tap Alt,
   then `SetForegroundWindow`, and check the foreground window really is the game), then press SPACE and RETURN with
   `keybd_event` using **hardware scancodes** (0x39, 0x1C), each held 300 ms. That reaches the main menu (n=2).
   If the logo is still fading in, the press is lost: take a screenshot, wait a few seconds and press again.
   ⚠️ Left alone, this screen runs a demo reel on a timer. A change of screen is not proof that a key landed.
4. **Main menu** (New game, Load game, Options, Extras, Quit): the game reads keys through DirectInput here, so drive
   it with the project's injector (it writes into the game's own key read). DOWN taps are fine;
   **SPACE must be HELD about 700 ms** to confirm, a short tap is ignored.
5. **Load game:** DOWN once, SPACE held; the save list opens on the newest save; SPACE held again. About 30 s of
   loading, then the Prince stands in the canyon.

## In gameplay

- W A S D move the Prince (held keys through the injector). Mouse movement through the injector turns the camera:
  about five pushes of 80 sideways turn it by roughly a quarter.
- Bound keys: W A S D, the arrows, SPACE, E, R, T, TAB, ESC, PAUSE. RETURN and LEFT CTRL are not bound.

## Hazards

- **No graceful quit yet:** ESC did not open a pause menu on a debug-camera build. The process is closed by force.
- The Discord overlay shows a "Go Live" box for a few seconds after start; it does not block anything.
- Windowed mode exists only through the project's own d3d9 file; the game itself has no windowed switch.

## Not learned yet

- Where the music volume is stored (not in the game's registry keys).
- Whether ESC or TAB pauses on a normal camera build.
