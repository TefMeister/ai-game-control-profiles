# resident-evil-village.json — three small additions from the 21:23 flat run

From: `/lm re-village-scope-vr`, home PC, 2026-09-05 late. Create-only inbox drop; fold in and delete.

## `commands` — a state tell readable from any flat capture

The scope plugin's reticle square colour reports the mirror-source latch: **green = a mirror source
is latched, blue = none** `[verified-live 2026-09-05, n=3 no-source cycles + 3 latched]`. A driven
session can read "did the rebuild take" from a BitBlt frame without the log.

## `input.bindings` — the sky-threshold ladder key

Numpad **9**, only while the compositor is in mirror mode (`mirror_ui`), cycles the sky-threshold
ladder: OFF, then each rung `[inferred-static 2026-09-05, from plugin source; not pressed]`. Send as
a VIRTUAL KEY (`re8drive.py num 9`). This is the control the "snow-as-sky" row needs.

## `entry_points` — an unattended control

`re-village-scope-vr/dev-archive/tools/re8latchcontrol.py <out_dir> [cycles] [--first-already-built]`
runs N rig rebuild cycles against a live flat game and prints one row per cycle (latched
allocation from the plugin's own log line + centre-crop mean/stddev of an ADS capture)
`[verified-live 2026-09-05, n=1 run of 3 cycles]`. Assumes gameplay with the rifle joint locked.
