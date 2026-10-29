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
