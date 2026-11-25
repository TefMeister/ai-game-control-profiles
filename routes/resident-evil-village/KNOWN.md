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
- ~~**The game pauses whenever its window is not in front**~~ **Flat: disproved 2026-11-25** (see "Driving it from behind
  other windows" below): it was the keyboard presses that needed the window in front, not the game. On the simulator not
  re-checked yet.
- **REFramework's overlay on the simulator acts where the REAL mouse pointer is** (2026-11-08): clicks sent by message to a spot
  in the window toggled whatever row the pointer happened to sit on, and the `reset-scripts` route reloaded nothing. The desktop
  picture of the overlay can also lag behind. A new autorun script is loaded by restarting the game instead (about 2 minutes
  with `drive.sh`), or by a click with the real pointer.
- The VR Bench presser is now `vb.py <window title> press <button>` (`vb.py Village press a`); `drive.sh` uses it and drives from
  launch into play by itself (2026-11-08, once).
- Numpad keys reach the game and its plugins by scancode WITHOUT the extended flag (0x4E = numpad +, 0x48 = numpad 8; with the
  flag the same codes are the arrows). Checked 2026-11-06: numpad + froze a scope frame on the simulator.
- **A VR Bench controller held perfectly still counts as asleep (2026-11-18):** a mod that lowers sleeping controllers'
  hands (as Visceral RE8 does after 20 unchanged frames) drops the hand to the hip during a still-pose test. Keep the pose
  alive with a tiny wobble, rewritten every 10 ms.
- **World rays (`via.physics.System.castRay`) (2026-11-18):** layer `Terrain` (1) hits nothing here, not even the floor
  under the player; `Bullet` (12) and `Default` (0) do. `getContactCollidable(i)` answers with the hit's owner (walls by
  their area name, e.g. `st10_167_MausoleumArea_Etc`; gate leaves are `Door` under the gate's object).
- **The Duke's gate opens by itself as you walk up to it** (the game's own push, about 8 then 51 degrees): no A needed.
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
- (2026-11-15) Menu-o-matiC's `look` grabs the screen where the window sits, so a window on top (the Claude window)
  is what it saves; a PrintWindow grab of the game's own window would see the game. Pad steps in routes (B / A / stick
  flick) would let a route back out of the shop screens, where the keyboard's Cancel is the right mouse button.

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

## 2026-11-08: flat on purpose, a Lua prompt by files, the new-gun animation
- **Flat even with a headset streaming:** start `re8.exe` from its folder with `SteamAppId=1196590` and `XR_RUNTIME_JSON`
  pointed at a file that does not exist; REFramework's VR start fails (`-51`) and the game runs flat. The boot route above
  then works (Insert, Enter, F, Up, F, about 30 s, F).
- One start hung in REFramework's hook install (the log's last lines "Suspending <thread>", no "Resuming", window not
  responding, CPU still): only ending the process got out; the next start was fine.
- REFramework's Lua in this build has **no `os.remove` and no `sdk.get_tdb`**. A file-driven prompt (a chunk in
  `reframework/data/<dir>/in.lua` run once per new `--seq N` first line) works; give each chunk a NEW number (a reused
  number is silently skipped). Type names can be found by searching `re8.exe`'s text, then `sdk.find_type_definition`.
- The player's movement component is not found by `getComponent(app.PlayerMovement)`; use
  `app.PlayerUpdaterFPS.get_playerMovement()`. `app.PlayerUpdater` throws on `getComponent`; `app.PlayerUpdaterFPS` works.
- **The new-gun animation on demand:** the player's `via.motion.Motion` layers 1, 2, 9, 10, `changeMotion(0, 2007, 0, 0, 2, 0)`
  (melee weapons 2006). It is the action step `Gun.GetWeapon`; forcing that step (`setCurrentNode`) or
  `PlayerMovement.requestUseGetWeapon(item)` did not play it.
- Layer motion names read as `ch01_0000_<Weapon>_<nnn>_<Name>` from `get_HighestWeightMotionNode():get_MotionName()`.
- **The gun plays its own half of the new-gun move:** the weapon's GameObject has its own `via.motion.Motion` (one layer)
  and `via.motion.MotionFsm2`; its `GetWeapon` step plays motion 0 (Wolfsbane), 2 (SYG-12) or 3 (the other guns). Start it
  on the gun's layer 0 in the same frame as the arms' 2007, or the gun parts stand still.
- Animation & Timing's script floods the log when its window is closed (fixed 2026-11-08: it looks for its command file first).

## 2026-11-11: flat run, gun model parts, shells

- The flat boot route worked twice more as one script (the mod's `tools/sim/flat_boot.sh`): launch flat, 45 s, Insert, Enter,
  F, Up, F, 40 s, F. Gameplay about 2 minutes after launch.
- `app.GUIManager.exclusiveTarget` still says `app.GUIDialog` once in play: it does not tell "in play". The player's
  transform answering (`re8vr.player:call("get_Transform")`) does.
- On the flat screen the held gun's object comes from `re8vr.weapon:get_field("Mesh"):call("get_GameObject")`;
  `re8vr.weapon:call("get_GameObject")` gives nothing.
- A gun mesh reports 256 parts (`getPartsEnableCount`); only a few are real. W870: part 0 = the whole gun, part 50 = the
  loose shell alone (the LEMI's rounds are part 50 too). M1897: the loose shell is in part 0 with the gun.
- Finding which part holds a small piece: move the gun's `Body` joint 100 m down at LateUpdateBehavior, write the piece's
  joint 0.35 m in front of the primary camera, switch one part on at a time (`setPartsEnable`) and count pixels of its
  colour in a screen crop; a halving search over 0-255 takes 8 pictures.
- Joint writes on the held gun from a script show at LateUpdateBehavior, PrepareRendering, UpdateMotion and before LockScene.
- An object made with `via.GameObject.create` plus a `via.render.Mesh` component given a gun's mesh has that mesh's joints
  (`getJointByName` works on its transform) and they can be written.
- Tool idea: the part search above as a small command (joint name, colour, crop) instead of one-off scripts.

## 2026-11-11 (later): pictures, the overlay, the simulator's hands

- REFramework's overlay is sometimes open and sometimes closed after the boot's Insert (seen both ways in one morning).
  `reframework:is_drawing_ui()` answers which; press Insert only when it says true, and ask again afterwards (one press
  did not always land). The mod's `tools/sim/shot.py` does this before every picture.
- The game window is not always the full screen width (2380x1344 on a 3440 screen that day): take pictures from the
  window's own rectangle (class `via`), not from fixed screen coordinates.
- On the OpenXR Simulator the game's desktop window is black except for the overlay: pictures come from the simulator
  (its capture), not from the window.
- On the simulator, with the right controller at (0.15, 1.35, -0.30) pitched 35, the handgun's magazine sits near
  (0.16, 0.97, -0.04) in room numbers: about 0.4 m below and 0.3 m behind the controller. The drawn left hand is likewise
  far from its controller, so the hands are out of the headset's view at chest-height poses.
- The game pauses when its window is not in front, and VR Bench poses written meanwhile do nothing: bring the window
  forward before every pose.
- A LEMI's suppressor is a mesh part that does not hang under the `Body` joint: moving `Body` away leaves it.
- Tool idea: `walk.py` could bring the window forward itself and say when the game is paused.
- **How fast the player moves** (`app.PlayerMovement`, reached through `app.PlayerUpdaterFPS.get_playerMovement()`; fields read live 2026-11-11): `WalkMotionMoveSpeed` 1.66 m/s, `JogMotionMoveSpeed` 3.43 (the run), hurt 1.40 / 2.30, crouched 1.20; `get_isSprint`, `get_isWalk`, `get_isMove` say which.
- The LEMI's own bullet scatter in VR, standing still, measured on 12 shots: 1.1 to 3.4 degrees off the aim.
- On the simulator the save's start spot is boxed in: the stick moves the player about 0.6 m back and nothing forward, so speeds cannot be measured by walking there.

## Learned 2026-11-11 (the options Emporium by sticks, the new-gun move)

- The Duke-shop card row answers the left STICK only (its sideways half held about 5 frames); one-frame D-pad taps do nothing on it, though they move the selection inside the Supplies, Gunsmithy and Purse screens.
- The cards are in two lists (three cards, or four with the Kitchen); the unused one keeps a cursor showing on its first card. Ask the screen which card is lit with `rv.py -f q_shopcards.lua`.
- The Emporium can be driven from the desk: `emporium_cmd.json` takes `stick r up|down|left|right`, `stick l left|right` and `where` (the log line says the screen and the lit setting).
- The flat window is black on a simulator run and REFramework menus cover it: for a picture use the simulator's own capture.
- The new-gun move can be played on demand: `newgun_cmd.json` `{"cmd": "play"}`, `state`, `forget`.

## 2026-11-14: Animation & Timing's first run on the simulator (the Wolfsbane)

- The mod's `tools/sim/drive.sh` drove from launch into play first time (about 3 minutes, VR Bench switched on by itself).
- **Any gun in hand by name:** write `equip ri3008` (one line) into `reframework/data/finger_rec/command.txt` (NOT under
  `visceral_re8/`); the log says `weapon: ri3008_Inventory (M1851 Wolfsbane)`. ri3008 = the Wolfsbane.
- The Wolfsbane's joints: `root Body Trigger Hammer Cylinder Five_Bullet00-04 Seven_Bullet00-06 Lid vfx_muzzle vfx_muzzle2`;
  the drum is `Cylinder`. The player's left hand: `L_Hand`, `L_Palm`, `L_MiddleF1..3` on `re8vr.player`'s transform.
- The Animation & Timing window is driven with no real mouse: buttons by `test/click.py` (BM_CLICK), its drawn sliders by
  posting WM_LBUTTONDOWN / UP at a spot along the slider, the clip list by CB_SETCURSEL plus a CBN_SELCHANGE to its parent.
- The whole-hand move and the drum's frame-by-frame turn show only while the left hand is docked on the OPEN drum. On the
  simulator the drawn hands sit 40-50 cm from the pretend controllers (`gunreport: target->hand 46 cm`), and the grip
  zones listed `drum` with no distance, so the robot could not dock there yet.
- One right-trigger pull through VR Bench with the gun 46 cm off its controller fired nothing (no log line).
- Tool idea: a "dock the left hand on <zone>" helper that aims the controller at the DRAWN part (read its world position,
  turn it into room numbers), since the controller and the drawn hand are this far apart on the simulator.

## 2026-11-15: flat, the options shop as three Gunsmithy tabs (PC1)

- **Route `launch_to_gameplay_flat.json`** (closed -> gameplay, last save, no headset streaming): launch, 30 s, Insert
  (the overlay), Enter, F (Continue), Up, F (Yes), 40 s, F (chapter card). Recorded once and replayed 3 of 3 times.
- **Pictures with other windows on top:** a screen grab of the window's place shows whatever covers it. PrintWindow
  on the game's own window (flag 3, full content) gives the real game picture, flat, even when covered.
- **In the shop screens the keyboard's Cancel is the RIGHT MOUSE BUTTON** (the hint shows a mouse). Esc and Backspace do
  nothing there; a right click sent as a window message is ignored (the game reads the mouse raw). The virtual Xbox pad
  works instead: B backs out (rows -> column -> cards), the left stick held about 0.15 s moves between the cards, A picks.
  Keyboard Down / F move and choose inside the Gunsmithy.
- After backing out of the Gunsmithy the lit card is the middle one; two pushes right from there reach "Every Setting"
  (REFramework's full menu), which closes the shop and opens the overlay (Insert closes it).
- The Gunsmithy's column: `lastIndex` follows the lit gun, in the column and in the rows; `currentWeaponIndex` stayed 0.
  The column shows `MaxListRows` = 6 guns on this save; the gun's name and kind are the screen's `nameText` /
  `categoryText`.

## 2026-11-19 (simulator)
- **Doors:** walking near a door starts the game's own open (a sensor 1-3 m out); a door's state is a hash of its name
  (Closed 474011919, LittleOpened 171654102, Opened 3232296810, Push 396010129, Closing 3360157036, Locked 3401490761).
  With our mod (b143 on) doors open only when a hand touches them or on A.
- **Pretend controllers are in ROOM space**, not head space: to push a hand forward along the view, turn the push by the
  simulator head's yaw (`tools/sim/hand_push.py <head yaw>`).
- **Turning toward a target:** new head yaw = old head yaw + the walker's "deg off the view".
- **The straight-line walker cannot reach the shaman's house door** from the Duke's gate (15 m, 3 m lower): it needs a
  recorded route. Tool idea: record the way (gate -> steps down -> the house door) as a Move-o-matiC path.
- The stone wall by the Duke's gate (60 deg left of the gate's approach, 1.3 m) is a good flat-enough wall for hand tests;
  the "wall" 0.7 m from the save spot exists only on collision layer 2, not on the bullet layer the hand rays use.
- A leftover game lock from a session that never ended blocks VR Bench's start: `clash.py status`, then `clash.py release live`.

## 2026-11-23: the real headset lying on the desk, robot hands (no simulator)

- **The desktop window repaints in headset mode on Two Frame Sequential** (`VR_RenderingTechnique_V2=1`); on Single Frame
  Multipass it stays frozen. So with a headset streaming and nobody wearing it, pictures of the game window work.
- VR Bench's hands, buttons and sticks work through the real runtime too: switch the layer on, start `re8.exe` with
  `SteamAppId=1196590` and no `XR_RUNTIME_JSON`. Its room numbers are the same space as REFramework's `vrmod:get_transform`,
  and the drawn hands sit on the controllers (on the simulator they were 40-50 cm off).
- The mod's `tools/sim/drive_real.sh` drives from launch into play (A, A, left stick up, A, 32 s, A); wait until the title
  really shows before the first A (a picture's brightness): an early press once ended in a new game's black intro.
- **Menu rows by robot stick: push for 30 ms.** A 0.1 s push moved two rows on the main menu (Continue -> New Game).
  The mod's `tools/sim/flick.py <up|down> [n]` does one row per flick.
- Main menu rows from the top: New Game, Load Game, Continue. Load Game: flick to it, A, the list ("Traveler Records":
  Auto Save, then slots 1..), flick down to the slot, A, "Load selected saved data?" Yes is lit, A, about 35 s, A.
- "Continue" loads the autosave, not the newest slot.
- A save loaded from a typewriter save starts at that typewriter with the Save prompt up: A opens the slot list (the cursor
  started three rows below where it was expected: look before choosing), A on a slot saves at once.
- VR Bench's B did not back out of the New Game difficulty screen; closing the window from there confirmed nothing.
- **Saving by script does not make a save the game lists:** `app.SaveLoadManager` `StartSave(Object, type, slot, false,
  false)` (type = `app.SaveLoadType.GameSlotHash`) + `SaveAppSlotPrepare()` + `SaveAppSlotRequest()` writes
  `data00NSlot.bin`, but the list shows NO DATA for it (the slot's header is missing). Use a typewriter.
- The headset cannot look down, so the arm hides the hand in most holds. A gun held across the view at 0.3 m shows its
  side; rolled 70 degrees it shows the grip from below with the left palm under it.
- A hand held perfectly still "sleeps" (see 2026-11-18): the mod's `tools/sim/keep.py` rewrites the pose with a wobble.
- A gun taken out by name: `equip ri3046` in `reframework/data/finger_rec/command.txt` still works; guns given by script
  arrive empty.

## 2026-11-23 (afternoon): a start-up hang that is the loader's, and more robot facts

- **REFramework's own start-up can deadlock: a black window that does not answer Windows.** Seen once in about ten starts
  `[verified-live 2026-11-23, n=1]`. Signature: `re2_framework_log.txt` stops growing seconds after launch, its last lines are
  `[Thread.cpp:42] Suspending <id>` after `Initializing WindowsMessageHook` / `Locking loader lock...`, with far fewer
  `Resuming` lines than `Suspending` ones (62 against 17; a good start has them equal, 76 and 76), no
  `textures have been setup`, memory about 1.3 GB instead of 4. It is before any plugin or script of ours loads. State-o-matiC
  says `frozen` ("the window does not answer Windows"). The window's own close cannot work: end the process, start again.
  The mod's `tools/sim/drive_real_slot.sh` checks for it and restarts once by itself.
- The jacket hides the hands from every angle a headset lying on the desk has. For pictures (testing only) the player's body
  mesh can be switched off, material by material (`via.render.Mesh.setMaterialsEnable(i, false)` on `pl1000`); the hands are
  their own meshes (`pl1000HandL` / `pl1000HandR`) and stay. The mod's `tools/sim/q_sleeve.lua`.
- The GM 79's rounds: item 1927571624 (explosive), 1583016682 (flash); a gun says its own with
  `WeaponGunCore.getBulletItemID(Int32 index)`.
- A gun with no kept two-handed grip spot cannot be gripped at the front by a hand that is not aiming: hold the left
  controller where the hand should sit and write `grip keep 1` into `reframework/data/re_scope_cmd.txt`.
- The Wolfsbane's cover takes the right hand within 10 cm of where it held the grip (bring the right controller back there,
  RG held, rounds in it).

## 2026-11-23 (evening): the desk headset asleep, the options window over the title, the save list's cursor

- **A launch with the headset not streaming comes up flat** (the loader's log: `Could not create openxr system:
  XR_ERROR_FORM_FACTOR_UNAVAILABLE`; `vrmod:is_hmd_active()` false; the robot's stick and buttons do nothing). The mod's
  `tools/sim/headset_up.py [wait seconds]` asks the PC's OpenXR runtime for a headset with no game running (UP / DOWN);
  `drive_real_slot.sh` asks it first. The headset lying on the desk dropped out once after the game before had closed and
  was back by itself two minutes later.
- **Started flat, the Visceral options window (the Emporium) stands open over the title and keeps REFramework's overlay
  open**: Insert does not close it (five presses, `reframework:is_drawing_ui()` stayed true). `{"cmd": "close"}` in
  `reframework/data/visceral_re8/emporium_cmd.json` closes it; then one Insert closes the overlay.
- **Load Game list: the 30 ms stick steps do not all land** (6 sent: 2 landed, another launch 3). Always look at the slot
  picture; step the rest and look again. Slot 6 is the row dated 2026-11-23, "Village - Altar".
- Tool idea (2026-11-23): the save-list step should ask the game which row is lit and step until it is the wanted one,
  instead of counting stick flicks.
- A picture of the gun hand from its OUTER side cannot tell a fist from a flat hand (the first finger bones point along the
  grip either way): the mod's `tools/sim/q_late.lua` gives how closed each hand is as drawn (cm, wrist to the middle
  finger's last joint: about 11.8 a fist, 16 flat), read after every writer of the frame.
- `tools/sim/landtest.py <ri id> [long] [toward=0.3]`: does LG at each grip zone's middle (from three sides, and part-way
  toward the neighbouring zone) give that part. The robot's placement is about 3 cm loose: judge by the mod's own
  `LG pressed ... the hand X m from it` lines.

## 2026-11-23 (late evening): the save list's steps, the gun's trigger part, the frame rate while driven

- **Load Game list: 2 s between the 30 ms stick steps lands every step** (three of three, twice); 0.4-1.2 s between them
  landed one of three or four.
- Every gun has a `Trigger` joint (its pivot; the blade hangs below it): a right index finger can be measured against it
  (the mod's `tools/sim/trigfit.py`).
- While a robot drives the game through the desk headset (VR Bench, many live questions a second) it runs at about
  10 frames a second, about 30 at rest: anything timed in seconds (a finger ease) cannot be judged for smoothness then.
- Lua's `os.clock()` in REFramework on Windows is wall time since the process started (the C runtime's clock), not CPU time.

## 2026-11-25: driving it from behind other windows (flat, PC1)

So the person can keep using the PC while a session drives the game. All of this with the game window NOT in front
`[verified-live 2026-11-25, n=3]`, flat (no OpenXR runtime):
- **Launched, the game did not take the front**: the window the person was using stayed in front.
- **It keeps running behind**: frames kept coming (about 160 a second), and the player walked 2.6 m in 2 s on a pretend
  Xbox pad (ViGEm) with `via.Application.get_Active()` reading **false** the whole time. Forcing `set_Active(true)` before
  every application entry made no difference (it is reset to false at once otherwise). The one walk that moved 0 m was
  straight after the chapter card, when the controls are still locked.
- **The pretend pad works from behind for everything**: title (A), main menu (Up / Down, A), "Load most recent saved
  data?" (Up = Yes, A), chapter card (A), walking (left stick).
- **REFramework's overlay closes with Insert sent as window messages** (WM_KEYDOWN / WM_KEYUP to the `via` window), no
  real key, no focus.
- **Pictures from behind**: PrintWindow with PW_CLIENTONLY | PW_RENDERFULLCONTENT on the `via` window gives the real game
  picture while other windows cover it (not when minimized).
- The Lua eval files answer from behind too.
- Not checked yet: VR or the OpenXR Simulator (VR Bench poses were seen doing nothing behind other windows on 2026-11-11,
  which may be the OpenXR session losing focus rather than the game); keyboard keys sent as messages for the game itself
  (quick slots, the numpad hotkeys); the pad's d-pad quick slots from behind.
- **Second run, launch to play fully from behind (2026-11-25 17:08, /lm, flat)** `[verified-live 2026-11-25, n=2 with the
  first day]`: the window watcher (`bgaway.py watch re8.exe`, started before the launch) moved the window off the screen
  3.7 s after start; REFramework's overlay closed by Insert sent as window messages (`bgkey.py <pid> insert`, in
  visceral-re8-vr's tools/sim); then the pretend pad only: A (title), A (Continue, checked lit first), Up (Yes, checked lit),
  A, about 40 s, A (chapter card). Nothing came to the front.
- **Objects we make at run time** (`via.GameObject.create` + `createComponent(via.render.Mesh)`): `setMesh` and
  `set_Material` take the HOLDER (`sdk.create_resource(kind, path):add_ref():create_holder(kind .. "Holder")`), not the
  resource; a bare resource is refused. A material file's first load takes some seconds and the mesh shows the engine's
  dark checker pattern until it is in. On the title screen the guns' textures (and even the GM 79's own mesh from the pak)
  never became ready: test such objects in play.
- **Item ids are the name hash of short names** (murmur3_32, UTF-16LE, seed 0xFFFFFFFF): the ammo is `ri20xx`
  (ri2010 1179972000 handgun ammo, ri2042 1927571624 the GM 79's explosive round, ri2020, ri2040, ri2041, ri2043, ri2050);
  the GM 79 is ri3023 (2576167331). A gun's spec lists the rounds it takes: inventory item
  `<spec>k__BackingField.Weapon.IncludableItems` (the GM 79: 1927571624 explosive, 1583016682 flash). The inventory is
  `app.InventoryManager` -> `get_activeInventory()` -> `<items>k__BackingField` (each `<work>k__BackingField`: ItemID,
  StackSize, IncludeItemID = what the gun is loaded with). The REFramework connector's inventory call fails on this game.
- **On screen, not off it (2026-11-25, Tefa: "i do prefer if you have the games running on the screen, so i can see what you are
  doing, i just need to be able to use the pc at the same time")**: launch with `bgaway.py watch re8.exe <secs> --on-screen`
  (the window stays where it is; the front goes back to the person's window whenever the game takes it). Stop the watcher before
  the person launches the game themselves (it would take the front from their own run).
- **REFramework's start-up deadlock happened on a flat launch (2026-11-25 18:12)**: the log stops on `Suspending` lines (54
  against 17 `Resuming`), no picture, the window answers nothing; tools that touch the window then hang too (bgaway.py now
  moves windows asynchronously). Only ending the process gets out; the next launch was clean (81 / 81).
- **Putting a gun in hand from the eval script**: `rawget(_G, "__re8_holster_equip_item")(itemCore, "why")`, the ItemCore from
  `app.InventoryManager` -> `get_activeInventory()` -> `<items>k__BackingField`. The held gun in flat: the player's
  `app.PlayerUpdaterFPS` -> `get_playerGun()` -> `get_equipWeaponObject()` (`re8vr.weapon` is empty without the headset). In
  Duke's area the game keeps guns away: one equipped there comes out as the player steps out.
- **Turning the camera from behind**: the pretend pad's right stick (visceral-re8-vr tools/sim `bgturn.py`-style, x -1 left .. 1
  right): 0.7 for 0.6 s turned about 90 degrees left in Duke's shop.
- **Duke's shop on the lift, Heisenberg's Factory**: the player starts facing the shop; Duke is about 2 m to the left; the lift's
  way out is to the right, about 4 m (Tefa).
