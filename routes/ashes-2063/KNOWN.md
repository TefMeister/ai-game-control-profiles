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
  OpenComposite's `openvr_api.dll` in place of the shipped one. Not yet tried.
