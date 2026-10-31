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
- 2026-10-25: The UEVR injector keeps running after the game closes. Close it too when the session ends: it lives in the game folder, so Discord shows "playing Silent Hill 2" for as long as it runs (it ran for four hours once).
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

## Learned 2026-10-29

- Launch for a headset-less run: the exe with XR_RUNTIME_JSON = the OpenXR Simulator's json, SteamAppId and
  SteamGameId 2124490, `-windowed -ResX=1280 -ResY=720`. Then inject UEVR through the UEVR MCP (uevr_setup_game with
  the UEVR build's UEVRBackend.dll as backendDll, launchIfMissing false, skipPluginInstall true).
- UEVR injected that way could not load openxr_loader.dll ("Could not load openxr_loader.dll", no VR, and then the
  PRESS ANY BUTTON screens take no input). Fix: a copy of openxr_loader.dll from the UEVR build folder beside the
  game's exe (SHProto/Binaries/Win64). With it, hmd_active true, runtime OpenXR.
- The warning screen (PRESS ANY BUTTON TO CONTINUE) did not pass on Enter sent by PostMessage this time; the MCP's
  simulated pad A (uevr_input_gamepad a, 0.3 s) passed it at once. Use the pad.
- The main menu widget is created only once the warning has passed (search 'MainMenuWidget_BP_C /Engine/Transient').
  ContinueGame(Movie=null, NonSkippableLen=0) on it loads the save in about a minute; the pawn appears first, the
  PRESS ANY BUTTON TO PLAY screen then wants the pad A too.
- The game's own pad table (vanilla; the rebinding save holds "None"): A interact, B dodge/cancel/skip, X reload,
  Y healing, LB sprint, RB quick turn, LT aim, RT fire/melee, right stick click flashlight, BACK inventory, START
  pause menu, d-pad up map, d-pad left/down/right pistol/shotgun/rifle, left stick click nothing. Read live from the
  RebindableInput object's BindingList (objects of class /Script/InputRebinding.RebindableInput).
- Menu-open signals: the gameplay menu widget (inventory, map, memos) answers HasAnyUserFocus() true while open;
  the pause menu widget (InGameMenuWidget_BP_C) exists only while open.
- With the game in a window the main window handle measured 1744x54 at 100,100 right after launch (an odd handle);
  the backbuffer is 1920x1080 regardless. Not sorted out.
- 2026-10-29: the warning screen takes the pad A only while the game window is IN FRONT (bring it forward first);
  the MCP's pad press did nothing with another window in front, twice. Weapons by function: the weapon manager
  (Combat's SHWeaponManageCmbSubcomp) RequestEquipWeapon(row, Instant) with rows "Pistol", "Shotgun", "Rifle",
  "IronPipe" (the melee rows follow GetLastUsedMeleeWeapon); RequestUnequipWeapon(Instant) empties the hands
  (GetEquippedWeaponName then reads "None"). All proven live.
- 2026-10-29: the game polls the pad (and so a UEVR plugin's XInput callback runs) only after UEVR has "spoofed a
  gamepad connection", which happens at the first VR CONTROLLER input (a simulated trigger pull does it); the MCP's
  pad presses pass screens before that, but a plugin never sees a poll until then. Pull a trigger once after loading.
- 2026-10-29: the map (d-pad up) is the MenuSectionMap_Widget_C widget: IsVisible() true only while the map is open;
  the gameplay menu widget reports no focus for it. The focus script sh2_front.ps1 (recon/2026-10-29-buttons in the
  game repo) needs AttachThreadInput to take the foreground reliably.

## Learned 2026-10-31

- The Continue save stands right at a save point: the pad A after the load (meant for PRESS ANY BUTTON TO PLAY) opens the
  save menu, and a second A can write a save into an empty slot. After ContinueGame, pull a VR trigger instead; the play
  prompt passes by itself. Never press A after the load.
- Escape sent to the game window (PostMessage WM_KEYDOWN) closes the save menu when the pad's back does not reach it.
- The UEVR MCP's simulated pad is applied to the game AFTER a UEVR plugin's XInput callback: a plugin's button rewrites
  (X -> B in menus and so on) cannot be tested with it; its B goes straight to the game.
- The weapon manager's RequestEquipWeapon fails (false) while the save menu is open.
- The map (d-pad up) is a 3D item: pawn.Items.ItemExecutive is a Map_ItemInvestigationExe_BP_C, its ItemContext an
  SHItemBase whose Mesh (a SkeletalMeshComponent) is what a VR script holds and moves.
- The save point's menu widget (GameplaySaveMenu_Widget_C) answers IsVisible() false while closed, true while open.
- Window: the game rewrites ResolutionSizeX/Y and the GFXSettings Resolution back to 1920x1080 by itself (still
  Windowed); -ResX/-ResY on the command line do not hold either. The window measured 1936x1119 every launch.
- With the 21 Sacraments stutter patch installed (an ASI loader beside the exe), F8 toggles that patch: do not send F8.
- 2026-10-31: with UEVR (swap controllers off) the Quest 3 face buttons reach the game as: Quest A = pad A, Quest X =
  pad B (the game's Cancel / back / dodge), Quest B = pad X (the game's reload), Quest Y = pad Y. Seen in the headset.
