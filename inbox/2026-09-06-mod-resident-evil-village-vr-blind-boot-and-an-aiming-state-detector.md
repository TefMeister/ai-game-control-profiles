# RE Village — the boot route drives fine blind in VR, and the log gives an "is the player aiming" state detector

Dropped by the modding lane (`/lm`), 2026-09-06 23:40. For `profiles/resident-evil-village.json`.
Nothing here edits the profile; fold in whatever is useful.

## Confirmations of what the profile already says

- **VR mode kills screenshots, again** `[verified-live 2026-09-06, n=2 launches now]`. With the headset
  live on Virtual Desktop, `watch` reported deltas `0.00 ×6` and two BitBlt captures three seconds apart
  were **byte-identical** (same MD5). The existing hazard entry was `n=1`; it is now `n=2`.
- **The `title-to-last-save` route works with no screenshots at all** `[verified-live 2026-09-06, n=1]`:
  `steam://rungameid/1196590` (Steam already running → process in **2 s**, window immediately),
  INSERT, ENTER, wait 8 s, F, UP, F → gameplay **50 s** later. Driven entirely blind, verified by the
  REFramework log rather than by sight.
- **One step did not appear:** no separate load-splash F was needed this time — after the load, the
  plugin's `world[ok-body]` LOCK line arrived without a further keypress. Either the splash absorbed the
  earlier F or it did not show. Worth a `n=` note rather than a change.
- **`process-exit` via WM_CLOSE** worked again, window gone in ~2 s `[verified-live 2026-09-06, n=3]`.
- **Closing the REFramework overlay writes the config.** INSERT triggered `Saving config re2_fw_config.txt`
  in the same second. Anything hand-edited in that file must be edited **before** launch, and will be
  written back on the first overlay toggle — it survived intact here, but the write is real.

## New, and useful beyond this game: a state detector for "the player is actually aiming"

The scope plugin logs, once a second, `bore <N> deg off the gaze`. Measured this launch
`[verified-live 2026-09-06, n=1 launch]`:

| player state | muzzle joint in camera space | bore off the gaze | rifle roll vs camera |
| --- | --- | --- | --- |
| aiming down the scope | (−0.00, −0.03, −0.30) | **3.5–5.9°** | −4 to −14° |
| rifle up, not aimed | ~(0.1, −0.3, −0.4) | ~40–42° | ~−7° |
| rifle lowered | — | ~40° | **~165°** (near inverted) |
| flat ADS (same day, no headset) | (0.00, 0.00, −0.22) | 0.1–0.3° | ~0 |

So **`bore < 20°` is a one-line gate telling an unattended run whether a frame is worth judging** — the
generalisable form is "project the weapon's bore and compare it with the view direction", which needs no
screenshots and works in VR where captures are dead.

**⚠️ The limit of that gate, learned the hard way the same night.** It is for judging *unattended* runs
only. We used it to question a verdict a human observer had given while looking through the scope, and we
were wrong: the off-pose samples were the gaps **between** tests, with the headset on the observer's
forehead. A state detector describes frames; it does not out-rank a person who was watching.
