# RE2 (2019): menu keys that drive flat, and the launch tool's game folder (2026-10-10, /lm, home PC)

For `profiles/resident-evil-2-2019.json`, from a flat session on the Steam install (no headset linked).

- **Launch:** `python visceral-re2-vr/dev-archive/tools/re2drive.py launch` starts `re2.exe` with the game folder as its
  working directory (a wrong folder gives a black boot, 2026-10-03). The tool's `GAME` is the Steam install again since
  2026-10-10 (it pointed at the deleted `D:\RE2 test copy` from 2026-09-27 to 2026-10-10). `[verified-live 2026-10-10, n=4]`
- **Key names are lowercase** in `re2drive.py key <name>` (`insert`, `enter`, `space`, `tab`, `esc`, `q`, `e`); the
  capitalised names in the route table are rejected with a "known keys" list.
- **In gameplay:** `tab` opens and closes the inventory; inside it `q` = the map tab, `e` = back to items; `esc` opens and
  closes the pause menu. `[verified-live 2026-10-10, n=3 each]`
- **Title → last save** still works as the route says: `insert` (REFramework overlay), `enter`, 6 s, `space` (Story),
  5 s, `space` (Continue), ~35 s; the log line `Found Player pl0000` marks gameplay. `[verified-live 2026-10-10, n=4]`
- **Close:** `re2drive.py close` (WM_CLOSE); the process is gone within 8-10 s. `[verified-live 2026-10-10, n=3]`
