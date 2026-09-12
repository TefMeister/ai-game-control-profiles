# DOOM 2016 — four profile updates from a live `/lm` run (2026-09-12, home PC `RTX`)

Create-only drop for `profiles/doom-2016.json`'s curator. Everything below was measured in one
unattended launch: launched via Steam, driven from the main menu into gameplay, tested, and closed
through the game's own pause menu. Source write-up:
`doom-2016-vr/modding-notes/2026-09-12-three-rows-fall-in-one-launch-and-the-arrow-keys-were-never-arrows.md`.

---

## 1. `commands` — a console-free command route now exists, and it supersedes the typing dance

The profile's `console` entry documents a scancode + dead-key-flush + `typeroute sendinput`
sequence. **That is no longer the cheapest route and should be marked superseded, not deleted** (it
is still the only way to read the console *buffer* via `conDump`).

```
cmdroute                 -> "idCmdSystem: POINTER (global holds idCmdSystem*)"
cmd <any console text>   -> runs it, no console, no typing, no scancodes
viewpos                  -> the camera from static memory; run TWICE, the cache is one behind
```

- `confidence`: `verified-live`, `date`: `2026-09-12`, `n`: 1 launch.
- `viewpos` returned `pos 1728.000 5440.000 6372.160 pitch -0.000 yaw 30.000` — **the same value
  this profile's own `console.worked_example` recorded on 2026-09-10**, on the other machine and a
  different process. Two independent agreements to printed precision.

## 2. `hazards` — NEW: `cmd r_fullscreen 0` wedges the game

Switching fullscreen live left the window minimised at `-32000,-32000`, the process
`Responding=False`, and the proxy recreating its swapchain in a tight loop (59 recreations, six
inside 100 ms) until it stopped presenting entirely. **Force-kill was the only way out.**

**Do not switch fullscreen at runtime.** Set `r_fullscreen` in
`%USERPROFILE%\Saved Games\id Software\DOOM\base\DOOMConfig.local` with the game **closed**, then
relaunch. `confidence`: `verified-live`, `2026-09-12`, `n=1`.

⚠️ Not established whether this is `r_fullscreen`-specific or applies to any cvar that forces a
swapchain rebuild — only one command was tried, deliberately.

**Also worth recording under `entry_points`:** `r_mode "-1"` + `r_customWidth`/`r_customHeight`
were **ignored** on a fresh launch (asked for 1600×900, got a 3424×1353 client). The engine also
rewrites this file on launch. Resizing the window with `MoveWindow` from outside worked fine and
the game kept rendering — that is the reliable way to get a workable window size.

## 3. `input` — the move backend is the whole story, and `status` misreports it

| backend | `move fwd 150` | result |
| --- | --- | --- |
| `inproc-keystate` (the proxy default) | issued, `input released` | **position unchanged** |
| `sendinput` | issued, `input released` | **moved 358.7 units** |

`verified-live`, `2026-09-12`, `n=1 each`. The step direction was **(0.866, 0.501)** against a view
yaw of **30°** — cos 30° = 0.866, sin 30° = 0.500 — so the player moved forward along its own
facing, confirmed by an independent `viewpos` read rather than by eye.

⇒ **`inproc-keystate` is dead for gameplay** (consistent with `SysKeyboard` never being hooked).
**Always `backend sendinput` before driving movement**, and foreground the window first.

⚠️ **`status` printing `sendinput unavailable — game is not the foreground window` is a foreground
check, not a capability check.** It says "unavailable" for a route that works perfectly the moment
the window is focused. Worth a `notes` line so the next session does not read it as a dead route.

## 4. `hazards` — NEW and important: the PROXY's arrow keys lack `KEYEVENTF_EXTENDEDKEY`

This profile already carries the general primitive — *"arrow keys need `KEYEVENTF_EXTENDEDKEY`;
without it scancode `0x50` is numpad-2, the menu ignores it, and nothing errors"*. **The
`doom-2016-vr` proxy itself has that bug.**

- `key 0x28` (VK_DOWN) sent **five times** at the pause menu: highlight never left `RESUME`.
- An external `SendInput` with scancode `0x50` + `EXTENDED`, seconds later on the same menu: moved
  to `EXIT TO DESKTOP` on the first try.

`verified-live`, `2026-09-12`, `n=1 each`. `key esc` and `key enter` work, which is exactly what
hides it — confirmation works while navigation silently does not.

⚠️ **Any past entry in this profile that says an arrow key did nothing via the proxy should be
re-tested, not trusted** — those keys were never delivered as arrows.

## 5. `routes` — launch → gameplay re-verified, with one correction

The recorded route is correct and ran clean on 2026-09-12 (`verified-live`, `n=1`), with each step
screenshot-verified before committing:

```
main menu (CAMPAIGN tab already focused)  -> Enter
SELECT CAMPAIGN (GAME SLOT 1 highlighted) -> Enter      [NEW SLOT is 2 rows below - never blind-key past it]
MAIN MENU (CONTINUE GAME highlighted)     -> Enter
~45 s load -> "Press [SPACE] to continue" -> Space
gameplay
```

⚠️ **Correction to the recorded steps:** the route says *"Esc raises a quit prompt → Enter on No to
dismiss (or just Up to the tabs)"*. On this launch the **CAMPAIGN tab was already focused at
startup**, so the Esc step is unnecessary and the "Up to the tabs" alternative cannot be relied on
anyway — `key 0x26` does not reach the menu (see §4). The route currently only works because
**every step is Enter on an already-correct default**. It is not general navigation.

`gameplay → process_exit` also ran clean, but **needs the external extended-flag arrows** for the
five Downs to `EXIT TO DESKTOP`; the proxy's own `key 0x28` cannot do it today.

**Helper worth adding to the profile's tooling notes** — a minimal PowerShell `SendInput` that sets
the flag correctly, used for the close above:
`scan 0x48 up / 0x50 down / 0x4B left / 0x4D right`, all with `KEYEVENTF_EXTENDEDKEY`;
`0x1C enter`, `0x01 esc`, `0x39 space` without it.
