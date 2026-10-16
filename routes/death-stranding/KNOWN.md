# Death Stranding Director's Cut: what is known about driving it

Read this before launching. First learned 2026-10-16 (one session, so every line is n=1 unless it says otherwise).

## Window

- Display settings are the plain-text `settings.cfg` beside `ds.exe`. With the game closed, set `window_mode` to 1,
  `rendering_width` 1280, `rendering_height` 720 and the window rect (`window_left/top/right/bottom`) to
  320, 180, 1600, 900. The next start is a 1280x720 window and the desktop is not resized.
- The in-game Graphics Settings can do the same, but a change made there was lost once when the game was closed by
  force. Use the file.

## Getting to gameplay

1. Start through Steam (`steam://rungameid/1850570`). About 60 s to the main menu on a modest PC; the very first start
   prepares shaders for longer.
2. Main menu (top to bottom): Continue, New Game, Load Game, Load Game (Death Stranding Save Data), Options, Update
   Overview, Open Source Licenses. **Keyboard only:** arrow keys and Enter sent as hardware scancodes work; mouse
   clicks are ignored.
3. **Continue** (Enter on the first entry) loads the last save. Loading took about 2.5 minutes.

## In gameplay

- W walks (held key). Mouse movement turns the camera.
- Options: Audio has only a master volume (no music slider). Graphics lists Depth of Field and Motion Blur near the
  bottom; the list ends on a Confirm button (keep pressing Down to reach it, then Enter).

## Closing

- Closing the window (a normal close request, not a forced kill) exits cleanly.
