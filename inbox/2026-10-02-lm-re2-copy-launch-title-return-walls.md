# resident-evil-2-2019 — launching the private copy, a return-to-title with no input, walls stop a walk (2026-10-02, /lm, home PC)

For `profiles/resident-evil-2-2019.json`. Create-only drop; the curator folds it in.

## routes / title-to-last-save — the launch step, when the game is a private copy

- `Start-Process -FilePath "D:\RE2 test copy\re2.exe" -WorkingDirectory "D:\RE2 test copy"` (PowerShell, Steam
  running, `steam_appid.txt` in the folder) reaches the title in ~35 s `[verified-live 2026-10-02, n=2]`. A
  `cmd /c start` of the same exe from a Git-Bash shell hung the shell and never started the game `[n=1]`. The old
  `steam://rungameid/883710` step starts the STORE install, not the copy.
- The rest of the route held both times (INSERT, ENTER, SPACE → Story, SPACE → Continue, PLAYER BOUND ~26 s), with
  `Continue` the default highlight each time. The autosave notice did not appear on either launch today.

## hazards

- **The game returned to the title screen by itself once**: 3.5 s after a shot, during a 3 s rest with no key sent,
  the player object was gone and the title flow re-ran (`[visceral_title] title flow state 1…8`); the window was found
  MINIMISED a few seconds later and had to be restored (`ShowWindow(SW_RESTORE)` + `SetForegroundWindow`). No crash,
  no error line, the save reloaded fine `[verified-live 2026-10-02, n=1]`. Cause unknown; something stole focus is
  the suspect. Check `IsIconic` before blaming a capture, and re-verify the state by capture before the next key.
- **A held walk key against an obstacle does not walk.** With W held into the crates/table the walk loop played for
  only 2–5 frames at a time and the character stood still, so "shots while walking" were really shots standing
  `[verified-live 2026-10-02, 7 reps]`. Alternate W and S between reps, or turn first (`mouse_look.py`).

## input — the numpad from Lua, and the game's own aim/fire from a script

- `reframework:is_key_down(0x60+n)` (virtual key) sees `re2drive.py num n` presses `[verified-live 2026-10-02]`.
- `app.ropeway.InputSystem.setForce(64, true)` is a latch (held until `setForce(64, false)`), seen by
  `InputSystem.isOn(64)` the same frame; `setForce(256, true)` for 8 frames fires one real shot (`Equipment.requestFire`
  hooked, ammo 12 → 6 over six shots) `[verified-live 2026-10-02, n=19]`.
- The game ran at ~200 fps flat on this machine; frame counts in probe logs are game frames, not 60 Hz.
