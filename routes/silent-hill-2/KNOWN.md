# silent-hill-2: what we know about driving it

Read before every launch. Add what you learn the same session (`known FOLDER --add ...`).
Only game facts: no paths, machine names or people.

## Menus

- 2026-10-24: Content warning (press any button) > main menu with CONTINUE highlighted first > loading > PRESS ANY BUTTON TO PLAY > gameplay. Enter gets past every one.
- 2026-10-24: 2026-10-24: The main menu's own functions can be called through the UEVR MCP instead of the cursor: the live MainMenuWidget_BP_C (search 'MainMenuWidget_BP_C /Engine'), HasSlots() says whether a save exists, ContinueGame(Movie=null, NonSkippableLen=0) loads the newest save. In VR mode the window mirror does not draw the menu text, so the recorded checkpoints do not match; the functions are the reliable way.

## Controls

- 2026-10-24: Keys reach the game both by PostMessage to its window and by SendInput with the window in front.

## Save spot

- 2026-10-24: Only one save, 'Continue' loads it: the prison armory with every weapon (a heavy spot, good for frame-rate tests).

## Hazards

- 2026-10-24: A 'Compiling shaders' screen (about a minute) comes before the warning screen whenever the shader cache was invalidated (a VR inject or a changed pak); keys do nothing until it ends.
- 2026-10-24: The Steam launch does nothing if the game is already running: it only focuses the old window, and a plugin file copied meanwhile stays locked and old. Close the game first.
- 2026-10-24: Attach VR (the injector window) BEFORE the route reaches its key steps, or do it between steps: the injector window comes to the front and the route stops at 'the window is not in front'. Resume with run --from 4.
- 2026-10-24: 2026-10-24: With UEVR attached but no headset connected (Virtual Desktop runtime idle), UEVR logs 'Failed to initialize Framework on DirectX 12' over and over, and the PRESS ANY BUTTON TO PLAY screen takes no input at all (keys, clicks, gamepad). Either have the headset on, or launch with XR_RUNTIME_JSON pointing at the OpenXR Simulator's json so the simulator plays the headset.
- 2026-10-24: 2026-10-24: Started straight from its exe (not through Steam) the game does not know the Steam user, writes a fresh profile in the SaveGames root and shows NO Continue (the menu is New Game first; a blind Enter opens the New Game settings). Fix: set SteamAppId=2124490 and SteamGameId=2124490 in the environment before starting the exe; then the save is found.

## What failed

## Tool ideas
- The game can rewrite its own window setting to exclusive fullscreen 1920x1080 between launches (seen 2026-10-24,
  after the previous run was closed with the console `quit` command). Fix with the game closed, in
  `%LOCALAPPDATA%\SilentHill2\Saved\Config\Windows\GameUserSettings.ini`: `FullscreenMode=2`,
  `LastConfirmedFullscreenMode=2`, `PreferredFullscreenMode=2`, `ResolutionSizeX/Y=1280/720` (and the
  `LastUserConfirmed...` pair). Check the window measures 1280x720 before driving.
- Turning the headset off while UEVR is attached closes the game (the VR session is lost): finish the test first.
