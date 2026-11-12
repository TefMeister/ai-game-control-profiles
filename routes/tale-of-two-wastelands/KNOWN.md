# tale-of-two-wastelands: what we know about driving it

Read before every launch. Add what you learn the same session (`known FOLDER --add ...`).
Only game facts: no paths, machine names or people.

## Menus

- 2026-11-09: Main menu: Continue / New / Load / Settings / Credits / Quit on the right; TTW's own self-check shows boxes here when a requirement is missing (ShowOff, JohnnyGuitar, falloutcustom.ini).

## Controls

- 2026-11-10: 2026-11-10: in VR with the pretend hands (VR Bench layer, 32-bit): Y opens the pause menu, B closes it; on the main menu A presses the highlighted Continue. With Stewie's Tweaks setting bAutoContinueGame = 1 (our own override file in a desk-test mod) the newest save loads from the start menu with no press at all.
- 2026-11-12: in VR with the pretend hands: Activate (a door, an item) is the A button on the hand that points at it, and it has to be HELD about one second; a short tap only shows the prompt. The walk stick (left, forward) and the turn stick (right) work as on a real controller.

## Save spot

- 2026-11-12: the rung-1 test save stands inside Springvale Elementary School beside its double door, which is on the left (90 degrees). Pointing the right hand at it and holding A for a second loads the Capital Wasteland outside in about 10 s. The game writes an autosave on the way: put the save folder back after a run (the desk check does it by itself with --outside).

## Hazards

- 2026-11-10: 2026-11-10: never name this game's window to a tool by the word 'Fallout' alone: a chat program open on a thread titled 'Fallout 3/NV VR ...' matched first, a picture of IT was taken and the quit keys were nearly typed into it (nothing was sent). The game's window belongs to FalloutNV.exe; check the program behind a window before using it. While an add-on has a box up, the game's main window is that box (title 'New Vegas Tick Fix', 'Stewie Tweaks', 'Fatal Error: Missing NVSE Plugin').
- 2026-11-10: 2026-11-10 (later): from Lanes 0.106.2 every Menu-o-matiC / Move-o-matiC / State-o-matiC command takes --exe FalloutNV.exe (and a route file may carry "exe"); with it a window of another program is refused. Every route recorded for this game must carry it. The home PC has 0.106.2 since 2026-11-10 (after a restart of the session); not yet run against the real game.
- 2026-11-10: 2026-11-10: the game opens its pause menu when its window is not in front (the OpenXR Simulator's window takes the front); bNoAltTabPause = 1 and bNoPipboyOnAltTab = 1 in Stewie's Tweaks stop that. Never start the next run before the last FalloutNV.exe has gone: two copies ran once and neither answered a polite close for over a minute.
- 2026-11-12: after a polite close the game can take over a minute to go; wait until no FalloutNV.exe and no Mod Organizer is left before the next start. One start in nine today crashed 5 s in (rung 5, an error in the C runtime) and passed on the next try.

## What failed

- 2026-11-09: 2026-11-09, main menu (Begin Again debug profile, OneTweak borderless 3440x1440): a Menu-o-matiC click does not move the game's own cursor; Down (SendInput) moves the highlight; Enter and E (SendInput) do NOT select. Untried: key_input post/both, a held key, the mouse moved over the item then clicked.
- 2026-11-10: 2026-11-10: the main menu ignores a WM_CLOSE to its window. A polite 'taskkill /IM FalloutNV.exe' (no /F) closes it.
- 2026-11-10: 2026-11-10: in the OpenXR Simulator the VR mod (TTWVR 1.0.0) shows its menus but never refreshes the 3D world: after a save loads the picture stays on the save's frame or black, and head, sticks and hands change nothing. Upscaler off, smaller eye size, game window in front, Continue by hand: no change. The headset run's log has a '[camtrack] ... the body is solved from the headset and the controllers' line that never appears in the simulator.
- 2026-11-11: a polite 'taskkill /IM FalloutNV.exe' also sends the close message to the OpenXR Simulator's preview window (it lives in the game's process): the simulator stops the session, the VR mod logs 'the headset's session is gone' and connects again, and the game can take 40 s more to close. Measure everything before asking the game to close.
- 2026-11-11: in the simulator the VR mod's pictures follow the head but are black in the world. Suspect: Virtual Desktop's body memory (VirtualDesktop.BodyState) exists, empty, whenever the Virtual Desktop Streamer runs, and the mod then skips its own body solver. Not yet tested with the Streamer quit.
- 2026-11-12: the frozen 3D world of 2026-11-10 / 11 in the OpenXR Simulator is explained and stepped around. With DLSS on, the VR mod has its helper program draw each eye straight into the headset runtime's own pictures through an old-style shared handle; the simulator's pictures are plain, so the mod silently repeated the previous picture. Our own desk-test add-on makes those pictures shareable (old kind, flag 0x2; the newer kind 0x802 does not work) and also answers "no Virtual Desktop body memory", so the Virtual Desktop Streamer may keep running. With it the eye is live at 90 a second.

## Tool ideas

- 2026-11-10: 2026-11-10: Menu-o-matiC should take a program name beside a window title (and refuse a window whose program is not the game's): the title-only match is what picked the chat window.
- 2026-11-10: 2026-11-10: the VR Bench tool's own screenshot command gets no picture from this simulator version (the simulator's own screenshot tool does); a desk run should wait for the old game process to be gone before starting the next.
- 2026-11-12: TTWVR 1.0.0's weapon, Pip-Boy and pick-up controls are not written anywhere we found (its DLL names holsters on the "L shoulder" / "R shoulder", a wrist Pip-Boy ("SavedWristBoy", bPipboyVR) and a hand grab (bHandGrab), nothing about buttons). In the simulator, the right hand at the right shoulder + RG held 1 s brought nothing into the hand (rung 1's save). Asked Tefa for the buttons; the desk check's weapon / Pip-Boy / pick-up steps wait for that.
- 2026-11-12: the desk check (ba-ttr-vr-bridge `tools/rung_simcheck.py`) needs the VR Bench hands ON for FalloutNV.exe, or the pause press and the walk do nothing while every other step passes; it now switches them on itself.
- 2026-11-12 (later): TTWVR's own controls, in short (the author's pinned notes, via Tefa): weapons are held with a GRIP, drawn from either shoulder (holsters on both hips and both shoulders); attack = trigger; A / X = activate (the pointing hand); B = VATS; Y = pause; right stick down / up = sneak / jump; grip = grab items; the Wrist-Boy (Pip-Boy on the left wrist): right trigger near it = on / off, left trigger near it = the paused Pip-Boy, hold left trigger near it = flashlight. Height: hold both arms straight out and hold both triggers.
- 2026-11-12, simulator, rung 1's save, VR Bench hands: right hand at the right shoulder (room 0.18, 1.48, +0.08 with the head at 0, 1.70, 0), RG held, brought forward still held = a pistol in the right hand `[verified-live, n=1]`. With the hand at yaw 40 / pitch 70 the pistol points ahead (at pitch 0 it points at the floor). Firing (RT with RG held) showed no flash or mark in the pictures (save without ammo? not established). RT with the right hand at the left wrist (left hand at -0.12, 1.40, -0.35, not turned) showed no Wrist-Boy screen: the wrist probably has to face the eyes.
- 2026-11-12 (Tefa): in rung 1's save the 10mm pistol's spare ammo sits on the LEFT CHEST; the pistol is probably empty, so firing needs a magazine from there first.
- 2026-11-12 (simulator, rung 1): reload-and-fire NOT shown yet. Tried: LG at the left chest (room -0.12, 1.42, -0.06; Tefa: "LG on the left chest grabs the ammo"), the hand under the pistol and let go (4 spots), the rack with LT on the slide pulled back 7 cm (3 spots; Tefa: "LT racks it i think"), then RT: no change in the pictures above the still-to-still noise. Two blind spots: the left Pip-Boy forearm hides the left hand from the eye, and the drawn arm does not reach far-out hand positions; and a shot has no picture signal we can catch reliably. Next time: a "did it fire" signal first (the console's item count of 10mm rounds, or turn the head down so the hand is seen), then the reload spots.
