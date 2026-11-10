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

## Learned 2026-10-24 (faster PC, driven from inside the engine)

- On a fast PC the title screen comes up about 48-51 s after launch and stays until about 89 s, then the attract
  trailer plays. The intro films are colourful, so "many colours on screen" is NOT a title test; comparing against
  a saved title picture is.
- Recognising the save notice and the main menu by picture is unreliable: their background drifts and glows.
  What works unattended: START once on the title, then A every ~4.5 s. A is safe on every screen on the way (OK on
  the save notice, CONTINUE on the main menu, nothing harmful in the films); stop pressing once the level is running.
- CONTINUE from the main menu reaches the opening car scene about 25 s after the title.
- In a build with the developer-menu unlock, X on the main menu opens the developer menu (Load Scriptlayer, Zones,
  Soakmode, Unlock all, CUSTOMIZE CONTROLS, CCTest); B goes back. Y does nothing there. Unlock all opens EXTRA
  CONTENT with everything available. Zones is an empty list.

## Learned 2026-10-28 (the OpenXR Simulator, no headset)

- Head poses: the simulator's own set_head_pose tool; one engine frame through the bridge per pose is enough in the
  opening car scene, which stands still.
- Controllers: the simulator has no tool for them, but it reads a command file `controller_pose_command.json` in its
  exchange folder (beside `head_pose_command.json`): `{"hand": 0 or 1 (left, right), "posX", "posY", "posZ" in
  metres, "yaw", "pitch" in radians, "trigger", "buttonA"}`. Write it to a temporary name, then rename it into place;
  the simulator deletes it once read (within about 50 ms while a game runs). Write it only while a game runs, or it
  waits and teleports the next session's controller.
- ⚠️ posY is measured FROM HEAD HEIGHT, not from the floor: with the head at 1.70 m, posY 1.55 came back from the
  runtime as 3.25. "15 cm below the eyes, 61 cm ahead, 20 cm right" is posX 0.20, posY -0.15, posZ -0.61.
  posX and posZ were not separately checked against a moved head (the head stood at x 0, z 0).
- Both controllers then report as tracked (orientation and position) with the khr/oculus/index/vive/motion bindings.

### Tool ideas
- 2026-10-28: give the simulator's MCP server a set_controller_pose tool that writes this file (same guard as its
  head tool: refuse when no game runs), so no session has to hand-write it.

## Learned 2026-10-29

- A save exists now: the route "START on the title, then A until a level camera" lands on CONTINUE and loads it,
  in play at about 85 s (three runs). Gameplay, not the car scene, so the picture is never quite still (idle sway):
  picture-difference tests need a hand big in view, or numbers instead of pictures.
- The simulator's controller command takes a trigger value; a trigger of 1, then 0, reaches the game as a trigger pull
  through the controller-pad path.

## Learned 2026-11-04 (the VR build on the OpenXR Simulator, home PC)

- **The VR build (DK_XR=1) ignores a plain virtual XInput pad**: its bridge pad driver takes the buttons from the OpenXR
  controllers instead. Drive it with the VR Bench layer: the **menu button held about 1.2 s = START** on the title, then
  A every 4.5 s (CONTINUE, the save notice) reaches gameplay about 90 s after launch. The layer must be switched on for
  the program before the launch (`vrbench.py start <full path to the exe>`; a bare name is refused).
- A launch script for the simulator sits beside the headset ones in the build folder (`dk_vr_sim_hands.ps1`): the same
  settings with the runtime pointed at the simulator; the headset scripts clear that setting.
- The simulator reports 90 frames a second on the title and 74-90 in gameplay with the 1280 x 720 per-eye picture.
- The game's log floods at about 1,200 lines a second in VR (failed draws, empty resolves, our own projection probe) and
  rotates every 20-30 s: read the counters you need within a minute, or let the collector keep the files.

## Learned 2026-11-05

- The VR build (DK_XR=1) on the simulator takes about 10 s to close on the window's close message (the flat build about 1 s): wait before calling it stuck.
- dk_sim_drive.py (the game repo's build-scripts) reaches gameplay with VR Bench switched on for the exe first (`vrbench.py start <full exe path>`), then `vrbench.py stop` afterwards.

## Learned 2026-11-06

- The game's auto exposure overshoots in this port: with it on (engine+656 bit 0 = 1, the game's own value) the picture
  blows out to about 83 % white and pumps; off it sits at about 31 %, steady. VR builds from b014 switch it with the
  bridge var `fx.exposure` (DK_VR_EXPOSURE at launch, 0 = off).
- `dk_sim_drive.py` takes the build folder as its first argument; pass it whenever the build to test is not the
  installed one (a hand-edited path once launched the installed build instead).
- A run writes its log to the `logs/` folder of the build folder it was started from.
- Tool idea (2026-11-06): the drive script could read the bridge until a level camera exists instead of fixed waits.

## Learned 2026-11-10 (measuring the frame rate with nobody in a headset)

- In a level, under the pretend headset, the game does not answer a polite close (the window's close message or a
  plain taskkill): after ~24 s only ending the process works. The menus close cleanly. Before starting another copy,
  check that no copy is left: twice a second one was started beside the first and both readings were spoiled.
- Seven presses of A, 4.5 s apart, after START reach the alley (the level after CONTINUE) on the pretend headset; the
  frame rate there settles within 30 s.
- The game's log says how it is drawing: "Resolve region is empty" and "Failed in backend" lines mean the picture is
  being drawn in strips (the console's anti-aliasing is on); none at all means one pass a frame.
- Finding what the port's threads do without a debugger (2026-11-10): `dk_threads.py` in the mod's repo samples the
  busiest threads; with the linker's map of the same build it names the game function. A thread at 100 % of a core can
  be almost all waiting: here the drawing thread was 80 % inside the console library's "wait for the graphics chip" loop.
- Nsight's GPU trace can start the port itself (it is a plain program; settings go in as `env_pairs`), but it needs the
  NVIDIA Control Panel's "GPU performance counters for all users" switched on, and its output folder to exist already.
- The same build reads 72 to 76 game frames a second in the alley from run to run: differences under ~5 mean nothing.

## Learned 2026-11-10 (tracing the graphics card with nobody at the PC)

- A graphics-card trace tool (NVIDIA Nsight GPU Trace) can start the game itself with the VR settings and take its
  trace 115 s after launch; the usual presses (menu held = START, then A every 4.5 s, seven times) sent at the same
  moment from a second script land in the alley at about 95 s. The trace tool ends the game when it has written its file.
- The trace tool needs "allow access to the GPU performance counters" in the NVIDIA Control Panel (Developer settings),
  and its output folder must exist before the start.
- Closing by the window's close message still ends the game cleanly within a second (six unattended runs).
- Run-to-run spread in the alley stayed within about 1 game frame a second over an hour (72.7, 73.1, 73.0).

### Tool ideas
- 2026-11-10: a "wait until the level camera is live" check from the game's log would replace the fixed 115 s wait.
