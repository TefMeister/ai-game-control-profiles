# Resident Evil 2 (remake): what is known about driving it

Read before the game starts; add what is learned.

## On the OpenXR Simulator (no headset), 2026-11-01
- Runs with REFramework's Single Frame Multipass (`VR_RenderingTechnique_V2=2`): title screen in both eyes at 90 fps.
  (Two Frame Sequential froze Village on the simulator; not tried here.)
- Start it by its exe with `XR_RUNTIME_JSON` pointed at the simulator's json and `SteamAppId=883710`.
- VR Bench gives the controller buttons. To gameplay: A (title "START GAME"), A ("Story"), A ("Continue"): straight into
  the last save, no "load?" question. About 10 s from the title.
- The game pauses whenever its window is not in front (as Village): bring it forward before each press or screenshot.
- Visceral RE2's holster spots are measured from the head: on the simulator (head at 0, 1.7, 0, facing -Z) the right
  hip is at (0.20, 1.08, -0.02), left hip (-0.20, 1.08, -0.02), right shoulder (0.28, 1.48, 0.08), left shoulder
  (-0.28, 1.48, 0.08), room metres. The right hand there + RG takes that slot's gun out, or puts it away if it is the
  one in hand (this save: the minigun at the right shoulder).
- The player: `app.ropeway.PlayerManager` (singleton) `get_CurrentPlayer()`. Finger joints: r_hand_index_0..2,
  r_hand_middle_0..2, r_hand_ring_0..3, r_hand_little_0..3, r_hand_thumb_0..2 (and l_hand_ the same).
