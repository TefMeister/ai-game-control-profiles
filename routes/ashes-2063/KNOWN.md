# Ashes 2063 (Enriched), in GZDoomVR: what is known about driving it

Read this before launching. Profile: `profiles/ashes-2063.json`.

⚠️ **Which engine and which files (2026-11-06, Gold Digger).** The command line below starts whichever GZDoomVR it is
given and loads only the game. A test of OUR guns needs our engine build (its version line ends in `-m`, e.g.
`gvr4.13.2.2-6-gf3815e7-m`) and the test pk3s as `-file` arguments; the stock engine logs a plain `gvr4.13.2.2`. Check
the log's version line and its "adding ..." lines before believing a mod result: the 2026-11-06 test-day launches ran
the stock engine with none of our files (fine for testing the tools, useless for testing the mod).

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
- OpenComposite only accepts an engine built on **OpenVR 1.16.8** headers (it asks for IVRSystem_022). A GZDoomVR built on
  newer OpenVR headers asks for IVRSystem_026 and OpenComposite stops it at start with a "Missing interface" box
  ("unsupported interface IVRSystem_026" in its own log). Build a second copy against the 1.16.8 headers for simulator
  runs; the real headset copy can stay as it is (2026-11-08, n=1).
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

## State-o-matiC (2026-11-06)

- Standing still in a level, the picture does not move, so `watch` alone says "menu or loading screen"; a `--poke`
  with an arrow key got no answer (the key did not reach the game). Fixed by teaching the gameplay screen: the HUD's
  health and radiation bar, bottom left, region `0,0.78,0.25,0.17` of the 1280 x 720 window, stored in
  `states.json` here. Pass `--states routes/ashes-2063/states.json` to `watch`; it is not found by itself.
- A minimised game window makes `watch` stop with an error ("no client area") instead of a verdict.
- A frozen game (process suspended) makes `watch` wait until the game runs again, then report what it sees after.
- Closing the window (WM_CLOSE) quits flat GZDoom cleanly; the collector files it as "closed".
