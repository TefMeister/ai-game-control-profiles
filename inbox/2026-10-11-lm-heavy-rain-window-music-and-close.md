# Heavy Rain: windowed by the menu, music, close from gameplay (2026-10-11, /lm, dev PC)

For `profiles/heavy-rain.json`. Supersedes: profiles/heavy-rain.json entry_points.config_files (ScreenMode=2 = WINDOWED)

- `user_setting.ini` is rewritten, not obeyed, at launch: ScreenMode 2 or 1 with 1280 x 720 in it still gave a
  borderless 1920x1080 start (the menu said BORDERLESS) `[verified-live 2026-10-11, n=3]`.
- Menu route that works unattended (fractions of the game window, which is full screen until applied): Options
  (0.363, 0.486) -> Graphics (0.385, 0.615) -> Display Mode right arrow (0.401, 0.385) once = WINDOWED -> Resolution
  left arrow (0.241, 0.325) from 1920x1080: 1680x1050, 1600x1024, 1600x900, 1440x1080, 1440x900, 1366x768, 1360x768,
  1280x1024, 1280x960, 1280x800, 1280x768, 1280x720 (12 steps; read each) -> F -> **click YES** at (0.195, 0.703) of
  the new 1280x720 window inside the 15 s timer. Kept on the next launch `[verified-live 2026-10-11, n=2]`.
- Music: Options -> Sound (0.808, 0.5) -> MUSIC down arrow (0.456, 0.596), ~3 per click, to 0 -> Enter (VALIDATE).
- Main menu -> gameplay: click CONTINUE (0.67, 0.2); ~35 s to the opening bedroom scene.
- WM_CLOSE + OK on the Win32 box also closes the game from gameplay `[verified-live 2026-10-11, n=2]`.
