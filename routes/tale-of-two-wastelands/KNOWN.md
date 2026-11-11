# tale-of-two-wastelands: what we know about driving it

Read before every launch. Add what you learn the same session (`known FOLDER --add ...`).
Only game facts: no paths, machine names or people.

## Menus

- 2026-11-09: Main menu: Continue / New / Load / Settings / Credits / Quit on the right; TTW's own self-check shows boxes here when a requirement is missing (ShowOff, JohnnyGuitar, falloutcustom.ini).

## Controls

- 2026-11-10: 2026-11-10: in VR with the pretend hands (VR Bench layer, 32-bit): Y opens the pause menu, B closes it; on the main menu A presses the highlighted Continue. With Stewie's Tweaks setting bAutoContinueGame = 1 (our own override file in a desk-test mod) the newest save loads from the start menu with no press at all.

## Save spot

## Hazards

- 2026-11-10: 2026-11-10: never name this game's window to a tool by the word 'Fallout' alone: a chat program open on a thread titled 'Fallout 3/NV VR ...' matched first, a picture of IT was taken and the quit keys were nearly typed into it (nothing was sent). The game's window belongs to FalloutNV.exe; check the program behind a window before using it. While an add-on has a box up, the game's main window is that box (title 'New Vegas Tick Fix', 'Stewie Tweaks', 'Fatal Error: Missing NVSE Plugin').
- 2026-11-10: 2026-11-10 (later): from Lanes 0.106.2 every Menu-o-matiC / Move-o-matiC / State-o-matiC command takes --exe FalloutNV.exe (and a route file may carry "exe"); with it a window of another program is refused. Every route recorded for this game must carry it. The home PC has 0.106.2 since 2026-11-10 (after a restart of the session); not yet run against the real game.
- 2026-11-10: 2026-11-10: the game opens its pause menu when its window is not in front (the OpenXR Simulator's window takes the front); bNoAltTabPause = 1 and bNoPipboyOnAltTab = 1 in Stewie's Tweaks stop that. Never start the next run before the last FalloutNV.exe has gone: two copies ran once and neither answered a polite close for over a minute.

## What failed

- 2026-11-09: 2026-11-09, main menu (Begin Again debug profile, OneTweak borderless 3440x1440): a Menu-o-matiC click does not move the game's own cursor; Down (SendInput) moves the highlight; Enter and E (SendInput) do NOT select. Untried: key_input post/both, a held key, the mouse moved over the item then clicked.
- 2026-11-10: 2026-11-10: the main menu ignores a WM_CLOSE to its window. A polite 'taskkill /IM FalloutNV.exe' (no /F) closes it.
- 2026-11-10: 2026-11-10: in the OpenXR Simulator the VR mod (TTWVR 1.0.0) shows its menus but never refreshes the 3D world: after a save loads the picture stays on the save's frame or black, and head, sticks and hands change nothing. Upscaler off, smaller eye size, game window in front, Continue by hand: no change. The headset run's log has a '[camtrack] ... the body is solved from the headset and the controllers' line that never appears in the simulator.
- 2026-11-11: a polite 'taskkill /IM FalloutNV.exe' also sends the close message to the OpenXR Simulator's preview window (it lives in the game's process): the simulator stops the session, the VR mod logs 'the headset's session is gone' and connects again, and the game can take 40 s more to close. Measure everything before asking the game to close.
- 2026-11-11: in the simulator the VR mod's pictures follow the head but are black in the world. Suspect: Virtual Desktop's body memory (VirtualDesktop.BodyState) exists, empty, whenever the Virtual Desktop Streamer runs, and the mod then skips its own body solver. Not yet tested with the Streamer quit.

## Tool ideas

- 2026-11-10: 2026-11-10: Menu-o-matiC should take a program name beside a window title (and refuse a window whose program is not the game's): the title-only match is what picked the chat window.
- 2026-11-10: 2026-11-10: the VR Bench tool's own screenshot command gets no picture from this simulator version (the simulator's own screenshot tool does); a desk run should wait for the old game process to be gone before starting the next.
