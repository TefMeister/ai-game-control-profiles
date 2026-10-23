# silent-hill-2: what we know about driving it

Read before every launch. Add what you learn the same session (`known FOLDER --add ...`).
Only game facts: no paths, machine names or people.

## Menus

- 2026-10-24: Content warning (press any button) > main menu with CONTINUE highlighted first > loading > PRESS ANY BUTTON TO PLAY > gameplay. Enter gets past every one.

## Controls

- 2026-10-24: Keys reach the game both by PostMessage to its window and by SendInput with the window in front.

## Save spot

- 2026-10-24: Only one save, 'Continue' loads it: the prison armory with every weapon (a heavy spot, good for frame-rate tests).

## Hazards

- 2026-10-24: A 'Compiling shaders' screen (about a minute) comes before the warning screen whenever the shader cache was invalidated (a VR inject or a changed pak); keys do nothing until it ends.
- 2026-10-24: The Steam launch does nothing if the game is already running: it only focuses the old window, and a plugin file copied meanwhile stays locked and old. Close the game first.
- 2026-10-24: Attach VR (the injector window) BEFORE the route reaches its key steps, or do it between steps: the injector window comes to the front and the route stops at 'the window is not in front'. Resume with run --from 4.

## What failed

## Tool ideas
