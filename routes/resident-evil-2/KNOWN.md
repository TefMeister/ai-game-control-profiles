# Resident Evil 2 (remake): what is known about driving it

Read before the game starts; add what is learned.

## On the OpenXR Simulator (no headset), 2026-11-01
- Runs with REFramework's Single Frame Multipass (`VR_RenderingTechnique_V2=2`): title screen in both eyes at 90 fps.
  (Two Frame Sequential froze Village on the simulator; not tried here.)
- Start it by its exe with `XR_RUNTIME_JSON` pointed at the simulator's json and `SteamAppId=883710`.
- VR Bench gives the controller buttons. To gameplay: A (title "START GAME"), A ("Story"), A ("Continue"): straight into
  the last save, no "load?" question. About 10 s from the title.
- The game pauses whenever its window is not in front (as Village): bring it forward before each press or screenshot.
