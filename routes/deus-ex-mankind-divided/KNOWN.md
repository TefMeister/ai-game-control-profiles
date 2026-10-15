# Deus Ex: Mankind Divided (v1.19 build 801.0): what is known about driving it

Read this before launching. Full detail with confidence tags: `profiles/deus-ex-mankind-divided.json`.

## Getting to gameplay

1. Launch through Steam with `-nolauncher` (`steam://run/337000//-nolauncher/`): skips the small launcher window.
   The game window appears after about 45-50 s, titled `Deus Ex: Mankind Divided v1.19 build 801.0`.
2. Logos: Escape skips each (three presses, a few seconds apart).
3. Main menu: STORY / BREACH / JENSEN'S STORIES / EXTRAS / OPTIONS / SHOP / SQUARE ENIX / QUIT. Arrow keys
   (extended-key flag) move, Enter selects; the selected row is white. Wait until the menu is fully up (check
   the screen) before pressing Enter: two quick presses right after the window appears were lost.
4. STORY -> CONTINUE (first row) -> Enter. Loading takes about 2 minutes on a slow PC, then a story card with
   `Continue [SPACE]`: press Space. Gameplay follows in about 10 s.
5. Escape in gameplay opens PAUSE (RESUME first); safe place to wait.

## Window

- Registry `HKCU\Software\Eidos Montreal\Deus Ex: MD\Graphics`: `Fullscreen` 0, `ExclusiveFullscreen` 0,
  `WindowWidth`/`UserWindowWidth` 1280, `WindowHeight`/`UserWindowHeight` 720 gives a 1280x720 client window; the
  desktop keeps its own size. Export the key before changing it; set it while the game is closed.

## Sound

- Music: OPTIONS -> AUDIO -> Music Volume (fourth row) -> hold Left about 4 s -> 0 %. Saved on leaving the page.

## Screen effects (for VR)

- In OPTIONS -> GRAPHICS and the same registry key: Motion Blur, Depth of Field, Bloom, Lens Flares, Sharpen,
  Chromatic Aberration (registry only seen so far), Volumetric Lighting.

## Leaving

- A window close message only opens `Quit game? Yes/No`. **Enter answers Yes** (the default); a mouse click on Yes
  works in a window but did not in fullscreen side-by-side. Check the process is gone before relaunching.

## Side-by-side 3D (fullscreen only)

- Registry `Stereoscopic3DMode` 1 = side-by-side, with `Fullscreen` 1 and `ExclusiveFullscreen` 1 (the game refuses
  it in a window). It ran at the desktop's 1920x1080 although `FullscreenWidth`/`Height` said 1280x720.
- It starts minimised: bring the window to the front. Keyboard menus work as usual; mouse clicks land in the wrong
  place.
- Put `Fullscreen` 0, `ExclusiveFullscreen` 0 and `Stereoscopic3DMode` 0 back afterwards.

## Hazards

- The save on this machine starts on a rooftop in the first mission; pause while waiting.
