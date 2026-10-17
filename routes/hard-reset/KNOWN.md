# hard-reset: what we know about driving it

Read before every launch. Add what you learn the same session (`known FOLDER --add ...`).
Only game facts: no paths, machine names or people.

## Menus

- 2026-10-17: Start `hardreset.exe` from its own folder: the window appears in about 4 s, no launcher, no admin prompt (starting through Steam re-runs the DirectX installer, which asks for admin every time).
- 2026-10-17: Intro films: Enter four times, about 3 s apart, reaches the main menu (Escape is not needed).
- 2026-10-17: Main menu "Resume game": left click at 0.497, 0.322 of the window (1280x720 client). The first click after the menu appears can be ignored; click again. "Profile" sits at about y 0.40.
- 2026-10-17: After "Resume game": about 90 s of loading with comic panels, then "Press any key to continue" at the bottom right; Space starts play.
- 2026-10-17: Pause menu: Escape opens and closes it.
- 2026-10-17: Quit: the console (Ctrl + ~), then `quit` and Enter. The console can catch a stray character from the ~ key ("quit?" is then unknown): Backspace a few times and type it again. Closing the window instead can leave the process running.

## Controls

- 2026-10-17: Music volume: `s_sound_musicvolume "0"` in the profile's config.cfg (written while the game is closed). Sound effects: `s_sound_volume`.
- 2026-10-17: Window: `r_fullscreen "0"`, `r_win_pos_x/y`, 1280x720 client, measured windowed.
- 2026-10-17: VR (simulator, VR Bench, mod controls on): the picture follows the head (turns, tilt, lean); the left hand follows the controller left, right, up and down.
- 2026-10-17: VR buttons through the mod: right trigger fire, left trigger alt fire, A jump, B switch gun, X weapon mode back, Y weapon mode on, menu the pause menu (press again to close), left stick forward walks (W, with the mod's keyboard walking), right stick sideways snaps the view 30 degrees.
- 2026-10-17: The game never asked for a game pad while being played (0 asks a second), so walking through a virtual pad does nothing; keyboard walking works.

## Save spot

- 2026-10-17: "Resume game" starts in the alley at the start of the first level, facing down the street.

## Hazards

- 2026-10-17: The game pauses whenever its window loses focus.
- 2026-10-17: With a mod that steers the mouse (aiming by the controller) switched on, the menus cannot be clicked: the cursor is moved away 100+ times a second. Switch such aiming on only once in play. (Fixed later the same day in the Hard Reset VR mod: its aim goes quiet when the camera stops answering and puts the pointer back.)
- 2026-10-17: When the window loses focus the game opens its pause menu (Escape closes it).
- 2026-10-17: In the OpenXR Simulator with a mod that turns controller presses into keys, typing in the console picks up extra letters (`quit` came out as `quiwtf`): switch such a mod's controls off before typing console commands.
- 2026-10-17: A mouse pointer left at the screen edge (outside the game window) can make the game minimise on the next key or button press during the comic "Press any key" pages; check the pointer is over the game picture.
- 2026-10-17: In the OpenXR Simulator, the Space key also counts as the right controller's trigger, so a jump (Space) also fires. Not a game or mod fault; a real headset does not do this.

## What failed

- 2026-10-17: Clicking "Resume game" while the controller aim was steering the mouse: no effect, many times.

## Tool ideas

- 2026-10-17: VR Bench adds a new Controls line on every run, so this file fills with repeats; it should replace its own earlier line about the same thing.
- 2026-10-17: Menu-o-matiC's `click` jumps the cursor without any movement; some menus may need a small movement first (not what was wrong here, but worth a switch).
