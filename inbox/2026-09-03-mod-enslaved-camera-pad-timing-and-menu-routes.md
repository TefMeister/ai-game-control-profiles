# ENSLAVED — camera control, virtual-pad timing, and the option/quit menu routes

Source: `enslaved-vr` live modding session, home PC, 2026-09-03 (third autonomous run).
For `profiles/enslaved.json`. Create-only drop; the profile's curator folds it in.

---

## ⚠️ There is NO keyboard camera control, and the near-miss is worse than nothing

- **Mouse-look does nothing.** `[disproved 2026-09-03]` A 30-step relative-mouse sweep
  (`SendInput` MOUSEEVENTF_MOVE, the harness `mouse` verb) left consecutive frames
  effectively identical. The existing `turn the camera` binding with `keys: []` is right;
  this adds the explicit negative so nobody re-tries mouse first.
- **`A`/`D` turn the CHARACTER, and the chase-cam trails.** That reads at first like camera
  control and is not: it also translates. Used as a camera it walked Monkey into dense
  foliage, where further presses did nothing (a scan produced a perfectly flat metric), and
  then reversed the camera *inside* a bush.
  **Tell for "stuck against scenery": a scan whose score does not change at all across every
  step.** A flat curve is not "the feature is dead", it is "nothing is moving".
- **⇒ The right stick is the only real aim control on this game.**

## ⭐ The virtual pad needs ~2 SECONDS after creation before the game acts on it

`[verified-live 2026-09-03]` With `time.sleep(1.0)` after `VX360Gamepad()`, a right-stick
scan returned a flat, meaningless score curve — indistinguishable from "the pad does not
work". With `time.sleep(2.2)` the same code panned the camera immediately (mean frame
difference 34.4 against a static baseline).

**Practical rules:**
- Create the pad **once** and keep it alive for the whole routine. Creating and destroying
  it per step re-pays the settle cost every time.
- The pad works on a second machine (home PC): `thumbLX=29490`, `buttons=0x1000` —
  identical readback to the dev PC, so this is a property of the game/driver, not one box.
- This machine carries **two** emulation buses — `Nefarius Virtual Gamepad Emulation Bus`
  and `Virtual Desktop Gamepad Emulation Bus` — both `Status OK`, no conflict observed.

## ⚠️ `vgamepad` can install as an EMPTY package

On this machine `pip install vgamepad` left only `vgamepad-0.1.0.dist-info/` with a RECORD
listing no package files and a 1-byte `top_level.txt`; `import vgamepad` failed while
`pip show` reported it installed. PyPI ships **no wheel** for it, only an sdist whose setup
launches the ViGEmBus MSI — a GUI installer that sat unanswered.

**Workaround that needs no installer** (when the bus driver is already present):
`pip download vgamepad --no-deps`, extract the tarball, copy the inner `vgamepad/` directory
into `site-packages`. The sdist bundles `vgamepad/win/vigem/client/x64/ViGEmClient.dll`, so
nothing else is required.

## Menu routes not previously in the profile

All `[verified-live 2026-09-03]`, all navigate-capture-VERIFY before committing.

| from | to | steps |
| --- | --- | --- |
| `main_menu` | Display options | `Down x4` -> **OPTIONS** -> `Enter` -> `Down x2` -> **DISPLAY OPTIONS** -> `Enter` |
| Display options | change resolution | `RESOLUTION` is the top row and is already highlighted; `Left` steps DOWN the list, `Enter` = ACCEPT (this triggers a device Reset) |
| `gameplay` | `main_menu` | `Escape` -> `Down x3` -> **EXIT TO MAIN MENU** -> `Enter` -> `Enter` (warning: progress since last checkpoint lost) |
| `gameplay` | restart checkpoint | `Escape` -> `Down x2` -> **RESTART FROM LAST CHECKPOINT** -> `Enter` -> `Enter` |
| `main_menu` | `process_exit` | `Down x6` -> **QUIT** -> `Enter`; process gone in ~8 s |
| chapter select | difficulty | after picking a chapter, **EASY sits ABOVE NORMAL** (`Up` from the default), then `Enter`, then `Enter` on "This will reset your last checkpoint" |

Resolution list, descending via `Left` from `1920x1080`: `1680x1050`, `1600x900`, `1360x768`,
`1280x1024`, `1280x960`, `1280x768`, `1280x720`.

## Two more hazards

- **`ESC` is ignored during cutscenes** — it opens no pause menu there, so a route that
  assumes it will is wrong right after a load. `CONTINUE JOURNEY` frequently lands *inside*
  a cutscene; `SPACE`/`ENTER` did not skip one either. Waiting is the only observed exit.
- **⚠️ A cutscene cross-dissolve looks exactly like a broken stereo frame.** Mid-transition
  captures come back heavily doubled and semi-transparent across the whole image. It is the
  game's own dissolve, not the capture blending alternating eyes, and not a proxy defect —
  frames either side are clean. Do not measure across one, and do not report one as an
  artefact.
