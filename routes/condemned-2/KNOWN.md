# Condemned 2: Bloodshot (Xbox 360, run through a static recompilation): what is known about driving it

Read this before launching. The game is a native Windows program made from the Xbox 360 code
(Condemned2Recomp on the ReXGlue SDK), so it is driven with an **Xbox 360 pad**; the keyboard and
mouse path is described upstream as unfinished.

## Getting to gameplay (pad, 2026-10-15)

1. One long-lived virtual Xbox 360 pad for the whole session. **A pad that disappears pauses the game.**
2. Hold each button about 0.35 s. Shorter presses are dropped when the frame rate is low.
3. **The first press after the pad has been idle a while is sometimes lost.** Take a screenshot after
   every menu press and press again if nothing changed.
4. From launch: START, wait ~5 s, five times (logo film, opening film, title, copyright "Press START to
   continue"). The main menu shows with Single Player highlighted.
   On the very first run there was also a "not signed in, sign in now?" prompt (B = no) and a
   brightness screen (A); with a save present they did not appear.
5. A on Single Player: the mission screen with **Continue game** highlighted. **The first A on the main
   menu was ignored in 3 of 4 launches**; screenshot, then press A again. A loads the latest save
   (it overwrites nothing). Replay Mission is the row below it.
6. Loading takes about 1.5-4 minutes on a slow PC. Gameplay opens in the Rock Bottom alley.
7. Tutorial boxes ("PLAYER RESPONSE", "SPECIAL MOVES") pause the view: **B dismisses them.**

## Leaving

- A normal window close message shuts it down cleanly in under a second ("Window closing, shutting
  down... Title terminated").

## Watching the right window

- With headset output switched on, the OpenXR simulator's preview window belongs to the **same
  process** as the game. A tool that picks "the first window of the process" gets the preview, and
  then both screenshots and focus go to the wrong window. Pick the window whose title does not start
  with "OpenXR".

## Settings

- `autoexec.cfg` in the game's `Assets` folder takes console lines. `"MusicEnable" "0"` there sets the
  music volume to 0 before the mixer (seen in the log, 2026-10-15). The main menu still plays a steady
  sound at about the same level with it; that is a static background sound, not music (heard and judged
  2026-10-15), so music is fully off with this one line.
