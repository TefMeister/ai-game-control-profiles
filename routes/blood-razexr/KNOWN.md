# Blood in RazeXR PCVR — what is known (2026-11-28)

Blood (the classic data that Blood: Fresh Supply also ships) played in RazeXR PCVR 1.3.1, a PC OpenXR port of Team
Beef's RazeXR on the Raze engine.

## Starting
- `raze.exe -nosetup -portable -gamegrp <...>\games\blood\BLOOD.RFF` starts Blood. A level straight from the command
  line is `-map e1m2` (a `+map` line does nothing at start-up).
- `-map e1m1` plays the episode's opening film first; Fresh Supply's films are `.ogv` and Raze looks for
  `movie/cs1.smk` ("Unable to open video"), and the level did not start. `e1m2` has no film and starts.
- With large voxel weapon packs the level took about a minute to appear (the main menu shows meanwhile).
- `+give weapons` / `+give ammo` on the command line run before the level exists ("Not in a singleplayer game"), even
  behind `+wait 1500`. Not solved yet.
- Without a headset or OpenXR runtime it starts flat; `-novr` forces flat. Held voxel weapons are drawn only in VR.
- The console command `vrweapons` prints the held-weapon table (each weapon: tile, model yes/no, placement yes/no).

## OpenXR Simulator
- `XR_RUNTIME_JSON` set for the one process works; the session reaches FOCUSED and the level renders (about 90 FPS
  on the Build PC).
- The simulator's window lives inside the game's own process, and it takes keyboard keys too: typed keys moved the
  simulated head and opened game menus. Pick the game window by its title (it contains "BLOOD"), and expect keys to
  reach the simulator as well. Typing into the game under the simulator is not reliable; use command-line switches.
- `vr_weapon_debug <units>` pins the held weapon ahead of the eye (ignores the controller); `vr_weapon_rot_yaw` turns
  it before the weapon's own roll, so combining the two shows a rolled weapon on its end (a test artefact).

## Tool ideas
- A start-of-level hook for "give every weapon" that does not need typing (a small pack script, or a delayed command
  that waits for the level).
