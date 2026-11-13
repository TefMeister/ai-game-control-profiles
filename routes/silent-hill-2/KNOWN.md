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
- 2026-11-06 (headset runtime, nobody wearing it): attach UEVR only once the warning screen is up (attached during the
  splash it hung and the game died). Ten 'Failed to initialize Framework' lines are normal before it comes up. Enter by
  PostMessage passes the warning and the 'press any button' in VR; the main menu's ContinueGame loads the save; 1 takes the
  pistol out; F toggles the torch. State-o-matiC says 'frozen' while the plugin's frame counter runs on: a false alarm in VR.

- 2026-10-24: A 'Compiling shaders' screen (about a minute) comes before the warning screen whenever the shader cache was invalidated (a VR inject or a changed pak); keys do nothing until it ends.
- 2026-10-24: The Steam launch does nothing if the game is already running: it only focuses the old window, and a plugin file copied meanwhile stays locked and old. Close the game first.
- 2026-10-25: The UEVR injector keeps running after the game closes. Close it too when the session ends: it lives in the game folder, so Discord shows "playing Silent Hill 2" for as long as it runs (it ran for four hours once).
- 2026-10-24: Attach VR (the injector window) BEFORE the route reaches its key steps, or do it between steps: the injector window comes to the front and the route stops at 'the window is not in front'. Resume with run --from 4.
- 2026-10-24: 2026-10-24: With UEVR attached but no headset connected (Virtual Desktop runtime idle), UEVR logs 'Failed to initialize Framework on DirectX 12' over and over, and the PRESS ANY BUTTON TO PLAY screen takes no input at all (keys, clicks, gamepad). Either have the headset on, or launch with XR_RUNTIME_JSON pointing at the OpenXR Simulator's json so the simulator plays the headset.
- 2026-10-24: 2026-10-24: Started straight from its exe (not through Steam) the game does not know the Steam user, writes a fresh profile in the SaveGames root and shows NO Continue (the menu is New Game first; a blind Enter opens the New Game settings). Fix: set SteamAppId=2124490 and SteamGameId=2124490 in the environment before starting the exe; then the save is found.

## What failed

- 2026-11-06: UEVR attached about a minute after the Steam launch with the headset connected but not worn: UEVR's log stopped
  right after 'Hooked DirectX 12' (its VR side never came up, no OpenXR line), the game died within a few minutes with no crash
  folder written, and the session only noticed when it tried to close it. Next time: a State-o-matiC verdict right after the
  attach, and attach only once the warning screen is up.

## Tool ideas
- 2026-11-06: State-o-matiC should treat a UEVR plugin heartbeat in the profile log as 'alive' and not call the window frozen.
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

## Learned 2026-11-02
- On the simulator with UEVR injected through the MCP: the warning's PRESS ANY BUTTON took a plain Enter key press (pad A
  through the MCP did nothing this time, window in front); Enter on CONTINUE, about 90 s of loading, Enter at PRESS ANY
  BUTTON TO PLAY. The prompt appears only some seconds after the warning text: a press before it is lost.
- The window title is "Silent Hill 2" followed by two spaces.
- The MCP's own Lua state finds objects (`uevr.api.find_uobject(full name)`, a dot not a colon) but cannot call their
  functions; a per-frame job (reading bones) belongs in a real UEVR Lua script. The player's body mesh is CharacterMesh0.

## Learned 2026-11-05

- The mod's own launcher starts a headset-less run in one step: `SH2VR.exe play --sim` (Steam must be running) installs, starts the game with the OpenXR Simulator and the Steam ids, and loads UEVR 8 s after the window shows; no UEVR MCP setup call is needed (its plugin is already in the profile).
- After a reinstall the first start compiles shaders: the screen stays black for about a minute and presses are lost. Then Enter passes the warning, and the main menu's ContinueGame loads the save.
- The game plays with no InputRebinding.sav and does not write a new one.
- Each long gun's mesh has a `Holder_L` socket: the game's own left-hand spot (on the shotgun it sits on the pump, bone_forearm); the game's IK puts the body's left hand exactly there.
- The weapon manager's RequestEquipWeapon takes positional arguments through the MCP (`["Shotgun", true]`); named ones return false. It also returns false when that gun is already in hand.
- The MCP's held left trigger does not make the game aim while the mod runs (IsInRangedAim stays false), and SHDebug_Character_Combat_ForceRangedAim does nothing in this build.
- For a dev run with a freshly built plugin, start the exe directly (XR_RUNTIME_JSON + the Steam ids) and then `SH2VR.exe attach`: `play` would put the package's own plugin back first.

## Learned 2026-11-05 (evening)

- The UEVR MCP connector plugin (uevr_mcp.dll 1.0.0) in the profile's plugins folder costs about 7 ms per frame (it
  rewrites a crash-breadcrumb file several times a frame). Keep it out of the profile; copy it in only for a run that
  needs it, and out again. Measured on the simulator: 19 ms with it, 12-13 ms without, A/B/A.
- UEVR's VR_SynchronizationMode is where in the frame it waits for the headset (0 Early / 1 Late / 2 Very Late), not
  on/off. Early measured 1.8 ms faster than Very Late here (n=2 each) and is the setting in the profile now.
- Driving without the connector, by keys: find the game window from the process (FindWindow by the title returned
  nothing), post Enter at the warning, Enter on CONTINUE (first item), wait about 95 s, Enter at PRESS ANY BUTTON TO
  PLAY. Done four times today. The mod's launcher `play --sim --no-install` starts the game with the installed plugin.
- Never attach the ghidrust debugger connector to the live game: its debug attach froze every thread and its detach
  left them frozen; only stopping the connector's own process freed the game (it survived). Sample waits with the game
  repo's waitstacks.py instead (suspends one thread for well under a millisecond).
- The graphics card's load (nvidia-smi) and the plugin's `alive:` heartbeat line are enough to judge a frame-time
  change; the simulator's status file reports a constant 11.1 ms, not a measurement.

## Learned 2026-11-10 (measuring performance with nobody in the headset)
- The Quest 3 through Virtual Desktop asks this game for 2688 x 2880 per eye. The OpenXR Simulator cannot go wider than
  4096 in total: at that size VR silently does not start and the game runs flat (the plugin's heartbeat says
  `headset off`). Use 2048 x 2194 per eye and raise `r.ScreenPercentage` to 131 to load the card as the headset does.
- After CONTINUE the "press any button to play" screen can still be up when the script thinks it is in gameplay (the world
  is drawn behind it, so frame times react to settings): press Enter a second time ~12 s later, and look once.
- The game's graphics choices live in GameUserSettings.ini, in the `GFXSettings` line: `Adv_Supersampling` (0 off, 4 DLSS),
  `Adv_DLSSQuality` (3 = Quality, the engine then runs r.ScreenPercentage 66.7), `AdvQualityIsCustom`. The game can reset
  them by itself: compare with a backup when the frame rate is suddenly worse.
- Live values without the connector: the plugin (b004+) applies `name value` lines from `sh2vr_devcvars.txt` in the profile
  and logs each value before and after; `cvar_ab.py` in the mod's repo does A/B/A with it.
- UEVR's AFW build (2026-11-10): the frame warp runs only with `VR_RenderingMethod=3` in the profile's config.txt (0 Native
  Stereo, 1 Synchronized, 2 Alternating, 3 Alternate Framewarp) AND the game's DLSS on. With 3 the simulator's "left eye"
  screenshot is one eye (with 0 it is both eyes squeezed into one crop). F toggles the torch (by PostMessage to the window).
- Switching rendering steps off one after another (`waterfall.py` in the mod's repo) gives a per-step cost without a trace
  tool; Nsight's GPU trace cannot attach to a game it did not start.


## Learned 2026-11-11

- The plugin looked for the crosshair class once a second at the main menu, where it is not loaded: about 20 ms a second (a hitch). It now looks only in play. A class that is not loaded costs a full object search each time it is asked for.
- `SH2VR.exe play --sim` from a freshly packed release folder is a quick smoke test of a package: the heartbeat line in the profile log must say "headset on".
- `run-sim.ps1 <label>` (the game repo, dev-archive/tools/optimiser) still works end to end: about 4 minutes, leaves the game running; close it by its window.

## Learned 2026-11-12

- Real headset (Quest 3, Virtual Desktop): with UEVR's `VR_RenderingMethod=3` (alternate-eye warp) the game crashed about 10 s after the injection (`AFR swapchains not created` over and over in the UEVR log, then an access violation); with 0 it started normally. n=1 each. The simulator ran 3 without it.
- `SH2VR.exe play --no-install` from the package folder starts the game through Steam and injects by itself for a real-headset run too; it returns once UEVR is loaded.

## Learned 2026-11-12 (evening)

- New Game from the main menu by keys: Down + Enter (New Game), Down four times + Enter (Start Game), about 30 s of
  loading, Enter at PRESS ANY BUTTON TO PLAY; the opening cutscene (3 min 46 s) starts at once. New Game did not
  replace the Continue save; copy the save folder away first anyway and put it back after.
- The game's own "in a cutscene" answer: SHCharacterStatics.IsCharacterInCutscene(pawn) (true for the whole opening
  cutscene, false in play).
- UEVR's VR_2DScreenMode must not be set from the engine tick (the game thread): the game died at once. Set from
  another thread it switches both ways without trouble.
- After a crash the Unreal crash window (CrashReportClient) keeps the plugin file locked until it is closed.
- The OpenXR Simulator records flicker incidents by itself (its flicker_incidents folder, one folder per session,
  incident.json says why): read those after a run before asking anyone what they saw.

## Learned 2026-11-12

- A blind key route (Enter, Down, Enter ...) can lose its first Enter on the warning screen: every later key then lands one step off and the main menu opens CREDITS instead of NEW GAME. Check the picture before each key: the warning has a white code square at the lower middle; the marked menu line has a red bar (CONTINUE first); the New Game settings page has a red START GAME bar. With the VR injector the window draws the menu in its upper two thirds, plain it fills the window.
- New Game from the main menu: Down, Enter, then Down x4 to START GAME, Enter, about 32 s of loading, Enter at PRESS ANY BUTTON TO PLAY: the opening cutscene starts at once and runs about 3 min 40 s (first picture about 11 s in). New Game does not replace the Continue save.
- The plain game (no injector) starts straight from its exe with SteamAppId and SteamGameId 2124490 in the environment and -windowed.

## Learned 2026-11-13

- James's body (CharacterMesh0): 388 bones; `head_bn` 15, `eye_r` 17, `eye_l` 22, `eye_Rt` 52, `eye_Lt` 53. Material
  slot 10 is the eyeballs (`CH_James_Eyes`). At a cutscene's start the game builds `<that slot's material>_Animated`
  for it natively (no Blueprint call): whatever sits in the slot then is what the cutscene's eyes are made from.
- The cutscene signals the game sends: `CutscenePlayer.OnLevelSequencePlay`, `PlayerController.ClientSetCinematicMode`,
  the sequence's Director BP entry point (opening: `C001_MS_Parking_Toilet`).
- jbusfield's profile switch `fix_no_raytracing_visual_glitches` toggles ray tracing on/off every
  `fix_visual_glitch_seconds` (10): the picture dips darker that often. Ray tracing on removes the need for it.
- The UEVR connector's Lua cannot call functions; `uevr_batch` with `call_method` + `args` can (e.g. `GetBoneName`
  with `BoneIndex`, `HideBoneByName`).
- A screen-area recording (ffmpeg gdigrab with a window title) films whatever overlaps the window; record the
  window itself (OBS window capture, or PrintWindow).

## Learned 2026-11-13 (the frame warp on the real headset)

- UEVR's alternate-eye frame warp (`VR_RenderingMethod` 3) must be switched on AFTER the start: started with 3 it crashes
  on Virtual Desktop; set to 3 once the picture is up (the connector's set_vr_setting with key/value, or the mod's own
  step from build b015) it runs. Keep 0 in config.txt.
- Virtual Desktop writes its own OpenXR log in its ProgramData folder (`OpenXR.log`): every refused call is a line
  `xr<Call> failed with XR_ERROR_...`. It is rewritten at every start: copy it right after a run that went wrong.
- The connector's `uevr_exec_command quit` closes the game cleanly in about 10 s; the injector window stays and must be
  closed too (two were found running after two runs).
- From the Steam launch the warning screen is up after about 75 s; Enter (connector key tap) passes the warning, Enter on
  CONTINUE, about 45 s of loading, Enter at PRESS ANY BUTTON TO PLAY. Done three times in a row with a look before each.
- With nobody wearing the headset (lying connected through Virtual Desktop) the game keeps drawing: frame times can be
  measured on the real headset's size without a person.
- The plugin's heartbeat (`alive: frame N, X ms avg`) every 5 s is enough for A/B work: skip the first reading after a
  change. Ray tracing's cost sits mostly in the reflections (`r.Lumen.Reflections.HardwareRayTracing`).
