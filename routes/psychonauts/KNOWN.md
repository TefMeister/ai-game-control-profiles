# Psychonauts (2005, Steam, 32-bit, d3d9) - what is known about driving it

First written 2026-10-29.

## Window and sound
- Windowed by default in this setup: `DisplaySettings.ini` beside the exe, `FullScreen=false`, 640x480.
- Music (and every other volume) is 0 in `AudioSettings.ini` beside the exe (`MusicVolume=0.000000`).

## Keys
- The game reads the keyboard through DirectInput, so only real OS key events reach it: SendInput with SCANCODE
  flags, game window genuinely in front. Arrows need the EXTENDED flag (otherwise they arrive as numpad keys).
- Title "Press [key] to begin": Space.
- Main menu (Raz standing on the big brain): walk Raz onto a card with the arrows; the blue card in the middle is
  Continue. Directions depend on the menu camera, so walk a step, look, repeat; small steps (100-450 ms holds).
- Conversations: arrows pick a line, Space chooses. Down x4 then Space picks the last line ("I'd better go") in
  Ford's lab conversation.

## Faster than the menu
- With the VR mod's automation switched on, the command `level CAJA` jumps from the title screen straight into
  Sasha's Lab (Ford is there and starts talking). That is the quickest way into play.

## Hazards
- With an OpenXR Simulator in use, its preview window lives inside the game's process and takes keyboard focus
  back: keys go to the preview and the game never sees them. Minimise the preview (without activating it) and put
  the game window in front before every key. Finding the game window by the process's "main window" picks the
  preview; find it by its exact title "Psychonauts".
- The menu walk to the Continue card misses in a repeatable way when done blind; it needs looking between steps.

## Tool ideas
- 2026-10-29: a Menu-o-matiC route for title -> Continue with a look between steps, so the blind walk is replaced.
