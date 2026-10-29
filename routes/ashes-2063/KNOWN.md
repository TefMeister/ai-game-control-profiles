# Ashes 2063 (Enriched), in GZDoomVR: what is known about driving it

Read this before launching. Profile: `profiles/ashes-2063.json`.

## Getting to gameplay (flat, 2026-10-18)

1. Skip every menu from the command line: `-skill 3 +map MAP01`, plus `+give pumpaction +give shotgunammo 40
   +use pumpaction` to start holding the shotgun. Window: `+vr_mode 0 +vid_fullscreen 0 +win_w 1280 +win_h 720`
   (client area measures 1264 x 681).
2. Music off: `+snd_musicvolume 0` (written into the config, so it stays off).
3. **Two windows, same process.** GZDoom first opens a startup screen (the game's art under a red name bar) and
   only later the game window. Wait for the window whose title has " - " in it (`Outskirts - ASHES 2063:
   Enriched`). A screenshot of the first window just shows the title art, which looks like a stuck title card.
4. On a slow PC the level is ready about 15-25 s after launch.

## Leaving

- The console (grave key), then `quit`: closes cleanly (exit code 0), once the game window is in front.

## Headset

- The VR build of GZDoom talks OpenVR, not OpenXR. An OpenXR-only runtime (such as the OpenXR Simulator) needs
  OpenComposite's `openvr_api.dll` in place of the shipped one. **Works (2026-10-29, n=4 runs):** a copy of the
  engine folder with OpenComposite's dll, `XR_RUNTIME_JSON` pointing at the simulator, `+vr_mode 10`; VR Bench plays
  the hands, the simulator's head command and screenshot give two-eye pictures with nobody in front.
- Under that route the game **crashes on close** (access violation, exit 0xC0000005), whatever mods are loaded. Close it
  with a window close (no typing needed) and expect that exit code; the log is complete up to the close.
- On a slow PC the simulator's screenshots repeat the same frame for a second or more: take several, a second apart.
- The level window can appear within 3 s once the files are cached; wait another ~6 s before the first picture, or
  it shows the fading-in title.
- `+set` on an archived setting is written into the config file and stays for the next run: put test switches back
  on the command line every run (a "switch it off" comparison run left one off for the next run).

## Moving the player without the keyboard

- A tiny event-handler pk3 can push the player (`pmo.Vel.XY = Actor.AngleToVector(pmo.angle, speed)` in `WorldTick`,
  started by `Level.maptime`) and print a marker line to the console; read the marker from the log. The start of
  MAP01 faces a wall: turn first toward the longest of 16 `LineTrace`s (about 400 units open behind the start).
