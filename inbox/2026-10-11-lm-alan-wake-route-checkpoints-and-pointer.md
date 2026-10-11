# Alan Wake: route checkpoints and a pointer hazard (2026-10-11, /lm, dev PC)

For `profiles/alan-wake.json` and `routes/alan-wake/launch_to_gameplay.json`. `[verified-live 2026-10-11, n=4 launches]`

- `launch_to_gameplay` step 1 (`remedy_logo`) never matched in four launches: the logo is gone before the check. Step 3
  (`title_press_key`) missed twice, each time because the game was already at the main menu. Working recovery: look,
  then `mom run ... --from 2` (title film), `--from 4` (title) or `--from 6` (main menu, Continue Game highlighted).
  Suggest re-marking those two checkpoints, or making step 1 optional.
- **Hazard:** after moving the game window, the mouse pointer sat over the main menu and its hover moved the highlight
  to "Episodes". Park the pointer outside the window (or verify the highlight) before the Enter on Continue Game.
- **Launch with flags:** `steam://run/108710//-noblur/` shows Steam's "Launch Game with custom arguments" box on the
  first launches of a Steam session; Continue sits at (0.581, 0.602) of the Steam window. The third launch had no box.
- The mod's numpad 7 toggle (read with GetAsyncKeyState) works from plain SendInput with no click.
