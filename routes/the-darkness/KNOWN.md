# The Darkness (2007, Xbox 360, run as a PC recompilation): what is known about driving it

Read this before launching. The game reads only an Xbox pad; keyboard and mouse do nothing in it.

## Getting to the main menu (dev PC, 2026-10-23)

1. Start the program from its own folder (the game data sits beside it). It opens a 1280 x 720 window.
2. Logos and intro films first; a black screen while it loads (about 50 s in on a slow PC); the title screen
   (THE DARKNESS, PRESS START) about 60-90 s after launch. Presses before the title are lost.
3. Title: START. A save notice follows ("When this icon is displayed..."): A on OK.
4. Main menu: CONTINUE / NEW GAME / CHECKPOINTS / MULTIPLAYER / OPTIONS / EXTRA CONTENT, CONTINUE highlighted.
   With a save present, A on CONTINUE loads it. NEW GAME asks to delete the saves: do not confirm it.
5. About 4 s between presses is safe. A press while a screen is still fading in can be ignored: look, then retry.
6. Left idle on the main menu for about 90 s, the attract trailer starts playing.

## Input that works

- A virtual pad that exists for the whole life of the game. A pad that disappears mid-run (its program exits)
  pauses the game.
- A tap held for about 150 ms registers on every menu screen seen so far.

## Leaving

- Closing the window (the close button, or a WM_CLOSE message) shuts it down cleanly within about 1 s.

## Hazards

- The game runs on while another window is in front; nothing pauses it.
