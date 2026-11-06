# Resident Evil Village: what is known about driving it

Steam build, RE Engine, REFramework (`dinput8.dll`) in front. The full profile is `profiles/resident-evil-village.json`;
this page is the short, growing list of what works, read before the game starts.

## Getting to gameplay

- Launch through Steam (`steam://rungameid/1196590`). The window appears about 40 s after the process.
- With no headset streaming, REFramework's VR start fails (`XR_ERROR_FORM_FACTOR_UNAVAILABLE`) and the game runs flat;
  the same install serves both.
- Title: Enter. Main menu: Continue is highlighted when a save exists; F = OK.
- "Load most recent saved data?" defaults to **No**: press Up (or W) once, then F. An F straight away cancels the load.
- The chapter card after loading waits for F ("F Continue").
- The REFramework overlay opens at every boot: Insert closes it.
- Screens can be told apart by small cut-outs of the menu (a driver keeps them off GitHub); the log has no reliable
  "in gameplay" line.

- **In headset mode (a headset streaming when the game starts), nothing from the PC reaches the menus (2026-10-30):** keys
  by SendInput (virtual key or scancode) and a virtual Xbox pad (ViGEm) were all ignored at the main menu and at "Load most
  recent saved data?". What works is the REFramework MCP: the main menu is `app.GUIManager` (singleton) field
  `exclusiveTarget`; on `app.GUIMainMenu` call `onContinue()` then `onContinueDecide()`. The load question is then an
  `app.GUIDialog` there, its `get_process()` an `app.GUIMisc.DialogTwoButton` (`selectedIndex` 0 = Yes, 1 = No;
  `set_selectedIndex(0)` works) but nothing found yet confirms it without a press. The desktop window does not repaint.
- **Insert** opens and closes the REFramework overlay (Menu-o-matiC knows the key since 2026-10-30).

## On the OpenXR Simulator (no headset)

- **Works with REFramework's Single Frame Multipass (`VR_RenderingTechnique_V2=2`), freezes with Two Frame Sequential (`=1`)
  (2026-11-01, 3 frozen runs on `=1`, 2 good runs on `=2`).** On `=1` the game stops on its very first VR frame (the log's
  last line is "d3d12 textures have been setup"; the simulator's last call is a swapchain release; the window goes "Not
  Responding"). Not the cause: the scope plugin, the REFramework.NET plugin, the Virtual Desktop / OBS mirror / xrFPS /
  Hand Freeze / Window View layers (each tried off). On `=2` it runs at 90 fps, title screen in both eyes.
- Start it by its exe with `XR_RUNTIME_JSON` pointed at the simulator's json and `SteamAppId=1196590`; the PC's own
  runtime (the headset's) stays as it is.
- The simulator remembers its preview window size; a saved height of 15 px gave strip-shaped screenshots. Set
  `window_width` / `window_height` in its `settings.json` with no game running.
- A hung run cannot be quit by the game's own close; ending the process is the only way out.
- On the simulator the title and main menu go by the REFramework connector: `app.GUITitle.onMainMenu()`, then on
  `app.GUIMainMenu` `onContinue()` and `onContinueDecide()`. At "Load most recent saved data?" keyboard Up moves the
  highlight, but no key confirms it (F, Enter, Space tried; `DialogTwoButton.set_timeLimit` did nothing): it wants the
  controller's A, which the simulator alone cannot press. Use the VR Bench layer for buttons.
- The game's own window (class `via`) closes cleanly with WM_CLOSE while on the simulator.
- **Full drive on the simulator, nobody at the PC (2026-11-01, worked):** switch the VR Bench layer on for re8.exe; launch; wait
  about 30 s past "d3d12 textures have been setup"; A (title), A (Continue), LEFT STICK UP (keyboard Up only sometimes
  works), A (Yes), wait about 28 s, A (chapter card). VR Bench writes the A button and the sticks.
- **The game pauses whenever its window is not in front**: bring it forward before every press or screenshot.
- Numpad keys reach the game and its plugins by scancode WITHOUT the extended flag (0x4E = numpad +, 0x48 = numpad 8; with the
  flag the same codes are the arrows). Checked 2026-11-06: numpad + froze a scope frame on the simulator.
- **Number keys 1-4 take out the quick-slot guns** in VR too (this save: 1 W870, 2 LEMI, 3 M1897, 4 F2). The in-headset
  quick-slot gesture (left thumb rest + right stick) cannot be done through VR Bench (no thumb-rest touch).
- VR Bench hand poses: the gun follows the controller; a controller pitched about 35-60 degrees up holds a long gun level.
- Tool idea (2026-11-01): a wait-chain reader (Windows Wait Chain Traversal) said nothing on this freeze, so it is not
  a thread-against-thread lock; the next freeze needs real thread stacks with symbols (x64dbg).

## In gameplay

- Keyboard scancodes reach the game. Synthetic mouse input does NOT reach the game, but it does reach the REFramework
  overlay (its buttons can be clicked).
- In VR (headset streaming), the desktop window stops repainting: screenshots are dead, and only the log tells the state.
- Sound triggers are named (`SND_MOT_RI3046_SE_RELOAD_C`, `SND_MOT_RI_SE_SHOTGUN_MVT_PUMP`, ...), and the id is the RE
  Engine name hash: murmur3_32 of the UTF-16LE name, seed 0xFFFFFFFF (checked on three names).
- Weapon foley (racking, pumping, bolt, reload, the ready / lowered / aim clicks) is played by
  `app.WwiseWeaponMotionSequence.weaponTrigger(GameObject, TreeLayer, tracks, UInt32)` from the upper-body motion. The
  tracks object (`app.WwiseWeaponTriggerTracks` and kin) carries the sound's own Wwise trigger: `get_TriggerId()`,
  `get_TriggerIdName()`. The UInt32 is 1 or 2 for every motion, so it is not the sound.
- **Reloading Lua scripts with nobody at the PC (2026-10-24):** open the REFramework overlay (Insert), scroll its list with
  the mouse wheel over it until "ScriptRunner" shows, click it open, and click "Reset scripts" (top row of that section).
  Synthetic mouse clicks and wheel reach the overlay. The overlay keeps its scroll place between openings.
  After a game restart it opens at the top again; at a 1920-wide window the path that worked every time: wheel down 10
  over the overlay, click ScriptRunner (about 120, 506), wheel down 10, wheel up 6, then "Reset scripts" is at about (178, 216).
- **The game's quick-slot cross** is `app.GUIQuickEquip` (panel `c_layout`; slots `c_top/c_right/c_bottom/c_left`; key
  texts `m_key_T/R/B/L`). A D-pad tap on the virtual pad shows it for a moment.
- **Every gun fires and reloads by itself:** in flat, a gun taken out empty reloads by itself; RT then X gives one shot
  and one reload, which is enough for the game to play all of that gun's handling sounds.
- **Every weapon in the inventory without playing (2026-10-24, testing only):** an item's id is the RE Engine name hash
  (murmur3_32, UTF-16LE, seed 0xFFFFFFFF) of its short name, e.g. `ri3046` (the LEMI); checked on all 7 guns a save owned.
  Through the REFramework MCP: `app.InventoryManager.createItemCore(id, 1, 0, 0)`, then that item's `get_work()`, then
  `activeInventory.addItem(work, false, false, false)`. `createAndAddItem(id, ...)` gives ammo but returns null for guns.
  Back the save folder up first.
- **Putting each gun in hand by script:** `app.PlayerWeaponChange.set_RequestedItem(ItemCore)`, the weapon change reached
  through the player's `app.PlayerUpdaterFPS` (`get_playerWeaponChange`); `getComponent(PlayerWeaponChange)` on the player
  finds nothing. Look the type up with `sdk.typeof(name)`.
- Upper-body motion families: 20xx idle / walk, 21xx ready, 22xx aim, 23xx lowered, 24xx fire (the pump or bolt after a
  shot plays in it), 25xx reload, 3000 seen at a weapon swap.

## The game pad (an Xbox pad through ViGEm works, flat)

- RT alone fires from the hip; LT held aims (with the sniper rifle the scope mod's companion keeps the zoom off). X =
  reload. B = back out of a menu.
- D-pad = the four quick slots (this save: up LEMI, right M1897, down F2 Rifle, left W870 TAC). A direction that is
  already in hand does nothing.
- If someone opens the crafting screen while the pad is driving, a trigger press there crafts: look at the screen before
  sending fire / reload in a row. (2026-10-24: the screen was opened by the player at the PC, not by the game.)
- A gun taken out empty reloads by itself.

## Menus driven so far

- Pause menu (pad Start): **Restart is highlighted first and it is destructive**; Down once = Options. Check the highlight
  before pressing A.
- Options: Controls, Camera, Game Settings, Display, Audio (4 down), ... A enters the right-hand list.
- Changing a value: the **keyboard left / right arrows** step it; the pad's D-pad left does not. Values wrap (one past 0 is
  10 again), so count presses from a known value.
- **Music:** Options > Audio > BGM Volume, set to 0 on 2026-10-24. B twice backs out with no "keep changes?" prompt. It is
  kept in the system save (`data00-1.bin`), not `config.ini`.

## Flat boot and gun takes, nobody at the keys (2026-11-02, worked end to end)

- Boot, flat, about 2 minutes: launch through Steam; about 20 s of logos; **Insert** (the overlay opens at every boot and
  sits over the title), Enter (title), F (Continue), Up, F (Yes), about 35 s of loading, F (chapter card). Each screen was
  checked by a picture before the next key; every key landed first time.
- At a 3440-wide window the overlay's ScriptRunner header sat at about (0.041, 0.360) of the window and "Reset scripts" at
  about (0.0515, 0.378) once it was open (fractions, so other window sizes need a fresh look).
- Guns the quick slots do not hold can be put in hand through a mod script that calls the holster mod's own equip (the
  D-pad path); a plain `set_RequestedItem` does not bring a gun out. One inventory weapon (not a gun, probably a
  throwable) is refused by our equip guard, which is what the guard is for.
- R reloads in flat; an emptied gun reloads fully (the shotguns and the revolver load round by round, about 8-13 s).
- A normal close (the window's close message) stops the game drawing for about 12 s before the process leaves.

## Closing

- WM_CLOSE to the main window: gone in about 2 s, process exits about 12 s later. Wait for the process to leave before
  relaunching, or Steam just focuses the dying one.

## What failed

- The menu cut-outs did not match at a 1920x1080 window (`screen None` all the way), yet the driver's Enter nudges alone
  reached gameplay. Recut the cut-outs at the current window size, or match by relative size.

- A launch script kept in the game folder disappeared with a clean reinstall, and starting a missing script fails
  silently: the driver now falls back to the Steam launch.

## Tool ideas

- Move-o-matiC has no game-pad steps yet. A small helper (a ViGEm pad kept alive, fed one step per line: tap a button,
  hold a trigger, aim-and-fire, wait) drove this game's guns on 2026-10-24; pad steps belong in the tool itself.
- Screen cut-outs should match at any window size (scale to the window, or compare relative positions), so a game run
  at a new size does not fall back to blind key presses.

## "Checking for additional content" (seen 2026-10-25)
- A black screen with a tilted panel: "Checking for additional content. Please do not close the game or turn off the power."
  can sit for 8+ minutes at launch (seen once, VR mode, REFramework overlay open, 02:43). The boot route has no template for
  it; nothing drives past it. Closing the window (WM_CLOSE) works from it.

## Seen 2026-11-04 (flat, driven from the PC)

- The boot route above worked again end to end: Insert, Enter, F, Up, F, about 40 s, F; gameplay about 4 minutes after launch.
- State-o-matiC called the idle player in gameplay "frozen" (still picture, no disk, 3.8 cores, window answering); the
  tool now says "still" for that and needs `--poke KEY` to call a freeze. A chapter card waiting for F looks the same.
- The live-game connector (REFramework.NET) cannot run on this game's REFramework build (no .NET in it); a Lua probe in
  `reframework/autorun` driven by a small json file under `reframework/data` does the reading instead
  (`visceral_re8_shop_probe.lua`: names / manager / open / dump / close).
- The always-on collector (Lanes) saw the launch 3 s in and filed it under visceral-re8-vr (`collect_prefer`).
- **Reloading scripts without a restart works as a recorded route** (`reset-scripts.json`: Insert, wheel 10 down over the
  overlay, click ScriptRunner at about (0.041, 0.360), wheel 10 down, 6 up, click "Reset scripts" at about (0.0515, 0.378),
  Insert; replayed 6 times at a 3440-wide window). Menu-o-matiC got a `wheel` step for it.
- The Duke's shop screens can be created by script (`app.GUIManager.createGUI(nameHash, true)`); the wall (ShopBg) shows in
  gameplay with `set_drawSuspend(false)` + `onShow(true)` on its driver. ⛔ `onShow(true)` on a created ShopBuy with no data
  CRASHES the game (REFramework writes reframework_crash.dmp beside the game).
- The mouse cursor moved over the game window (for the overlay) turns the camera a little once the overlay is closed.
- **The Duke's three-card shop screen can be shown by script in gameplay** (`createGUI(hash("ShopMenu"))` + the driver's
  `show(Parameter)`); it takes keyboard Left / Right; **Esc opens the pause menu on top of it**; `app.GUIManager.suicideGUIShop()`
  closes it cleanly. Never press F (OK) on it with no shop data behind: that opens the Buy screen and crashes the game.
- Menu-o-matiC key names are lower case and short: `esc`, not `Escape` (the error lists every name).
- **Script reload, corrected (2026-11-04 evening):** after a fresh boot ScriptRunner in the REFramework overlay is COLLAPSED:
  Insert, click ScriptRunner at about (0.041, 0.360), click "Reset scripts" at about (0.0515, 0.379), Insert. Later in the
  same boot it stays open: Insert, click Reset at (0.0515, 0.379), Insert. The recorded `reset-scripts` route (with wheel
  turns) only fits one scroll state; use the clicks above until the route checks the overlay first. (3440-wide window.)
- **The Duke with the pad:** at save 3 (next to him) the right stick turns, look up a little, "Open the shop." shows, A
  talks. A virtual Xbox pad (ViGEm) kept alive by a small helper takes taps, sticks and triggers from a command file.
- Keyboard Insert opens REFramework's menu; scripts cannot open it themselves (our plugin presses the key for them).

## Learned 2026-11-04 night (the OpenXR Simulator, the sniper scope)

- The simulator's preview window must be big for pictures: `settings.json` `window_width 2560`, `window_height 1400`,
  `full_render true` (a 1152x201 preview gives 1152x221 pictures). Change it with no game running.
- ⚠️ A click that lands on the simulator's preview starts its mouse-look; Village then feeds it motion and the head spins
  (yaw by thousands of degrees, pitch pinned at 86). ESC by message does nothing; a real ESC into the focused preview stops
  the spin but not the capture; a game restart was the only clean way back. Never click on any window while the preview is
  up: keys to the focused game window, VR Bench buttons and the command files only.
- VR Bench rifle poses: **controller pitch 90 = the rifle level** (0 points it about 45 up; 50 and 70 still up; more than 90
  points it down). Right hand (-0.07, 1.48, -0.10) m, left (-0.07, 1.55, -0.45), both pitch 90, head level at 1.70 m: the
  scope glass sits at (380, 610) with radius 56 px in the left eye of a 1920x1033 picture, showing the magnified world.
  Keys 1-4 take the quick-slot guns out (4 = the rifle on this save).
- The glass shows the bore's view 4x magnified, not what is behind the glass on screen: a colour comparison needs a uniform
  surface filling both (a wall up close, snow at the feet, the overcast sky).
- VR Bench (the virtual controller) must be switched ON before the game is launched; a close-out switches it off, and a launch without it sits at the title screen because the virtual buttons reach nothing (2026-11-04 22:2x). The simulator boot script now switches it on itself.

## 2026-11-05: reloading scripts on the OpenXR Simulator
- The `reset-scripts` route replays without errors with the simulator running (game window 3440x1421, the size the route
  was recorded at), but the scripts were NOT reloaded: the log showed no new "Running script" lines. Restarting the game
  (close by its window, boot again) took about 3 minutes and always worked. Not yet known why the route missed.
- The route needs a Menu-o-matiC that knows the Insert key: older copies stop with `KeyError: 'insert'`.
- Looking at something on the floor right at the character's feet does not work with a still camera: the body covers it.
