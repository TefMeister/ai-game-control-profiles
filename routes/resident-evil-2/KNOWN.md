# Resident Evil 2 (remake): what is known about driving it

Read before the game starts; add what is learned.

## On the OpenXR Simulator (no headset), 2026-11-01
- Runs with REFramework's Single Frame Multipass (`VR_RenderingTechnique_V2=2`): title screen in both eyes at 90 fps.
  (Two Frame Sequential froze Village on the simulator; not tried here.)
- Start it by its exe with `XR_RUNTIME_JSON` pointed at the simulator's json and `SteamAppId=883710`.
- VR Bench gives the controller buttons. To gameplay: A (title "START GAME"), A ("Story"), A ("Continue"): straight into
  the last save, no "load?" question. About 10 s from the title.
- The game pauses whenever its window is not in front (as Village): bring it forward before each press or screenshot.
- Visceral RE2's holster spots are measured from the head: on the simulator (head at 0, 1.7, 0, facing -Z) the right
  hip is at (0.20, 1.08, -0.02), left hip (-0.20, 1.08, -0.02), right shoulder (0.28, 1.48, 0.08), left shoulder
  (-0.28, 1.48, 0.08), room metres. The right hand there + RG takes that slot's gun out, or puts it away if it is the
  one in hand (this save: the minigun at the right shoulder).
- The player: `app.ropeway.PlayerManager` (singleton) `get_CurrentPlayer()`. Finger joints: r_hand_index_0..2,
  r_hand_middle_0..2, r_hand_ring_0..3, r_hand_little_0..3, r_hand_thumb_0..2 (and l_hand_ the same).
- Putting a gun in hand without menus (test script `zz_test_short2.lua` in visceral-re8-vr tools/sim): the player's
  `app.ropeway.survivor.Inventory` component `equipSlot(System.Int32)` with the inventory slot number equips it at once
  (slot 8 = Lightning Hawk in this save). A gun not owned: `InventoryManager.addAndEquipMainWeapon(WeaponType, count, 0)`,
  WeaponType from the enum field named `WP4100` etc. (upper case); memory only, the game saves at typewriters.
- The test hand's left-hand dock on the Lightning Hawk's magazine: (0.24, 1.35, -0.42) with the right hand at
  (0.12, 1.45, -0.42, yaw 90, pitch 35).
- Hold-pose limit: with a right-hand pose the GM 79 hangs muzzle-down; a side view of the gun needs another way (head turn).
- Items by script (test only): `InventoryManager.addItemCount(Item.ID, n)`: 22 = Acid Rounds, 23 = Flame Rounds (the
  GM 79's); 4-7 herbs; 20/21 and the sm71 group are unused ("#Rejected#") items. The inventory holds 20: after ~6 gifted
  guns a gift returns nil (restart the game, it is memory only).
- The simulator preview window can shrink to a strip (1760x440): MoveWindow it back to 1300x760 before screenshots.
- The REFramework log is rewritten at each game start: copy it out before relaunching if a run's lines are needed.

## Flat, nobody at the keys (2026-11-02)
- Boot: about 20 s after the process, Insert (the overlay is open at every start), Enter (START GAME), Enter (Story), Space
  (Continue): straight into the last save.
- A gifted gun (`InventoryManager.addAndEquipMainWeapon`) has no ammo type on its inventory slot, so the game will not reload
  it: `inventory.Slot.set_BulletID(<item id>)` first (handgun ammo 15, magnum 18, SMG 17, grenade rounds 22, fuel 25), then
  `addItemCount`. About 6 gifted guns fill the inventory (memory only): restart the game for more.
- Codes 1100, 1200, 1300, 1500, 7010, 7020, 7030 give nothing in this game.
- ⚠️ Asking InventoryManager for ammo counts while a save is still loading froze the game at its "NOW 10%" loading text
  (every pixel still, no disk reads, two cores busy, Windows still saying it answers). State-o-matiC now calls that frozen.
- The game pauses when its window is not in front; a helper that cannot bring it forward (Windows refuses some requests)
  reads as "the game stopped answering". Menu-o-matiC's focus() gets past that.
- A pick-up screen can be opened by script, no loose item needed (2026-11-05): make a StockItem (`sdk.create_instance`,
  `setStock(<item id>, 0, 0, 1)`; 4 = Blue Herb) and call `GUIMaster.openInventoryGetItemMode(stock, nil)`. Space = Next
  (the item goes to the inventory), then `esc` closes the inventory. The driver's key is spelled `esc`, not `escape`.
- A camera-taking action (a "jack") by script on the simulator (2026-11-05): the RPD hall's east shutter lever
  (`sm42_162_ShutterLever01A_gimmick`, a GimmickToggleSwitch). Find its control (the GimmickControl whose
  `get_MyGimmickBodyObject` is the lever), call `tryOccupy`, `startGimmick`, `requestStartSoloGimmick`, then on the lever
  `requestTurnToggle(not get_IsToggle())`: Leon is carried to the lever and pulls it; the toggle flips, so it repeats.
  Walking there and pressing A never found the prompt. The simulator's head yaw (`set_head_pose`) is what the mod reads as
  the head's turn. The goddess statue's A opens a use-item screen that neither B nor Esc closed on the simulator;
  `NewInventoryBehavior.close()` does.

## The clean rebuild's first runs (2026-11-07)
- State-o-matiC knows the title screen now (`states.json`, taught on the plain game: the logo over the rainy city, START
  GAME below; it looks the same with the mod in). `watch "RESIDENT EVIL 2" --game resident-evil-2` says `title_screen`.
- The plain game (no REFramework) reached the title about 45 s after the exe started; `CloseMainWindow` closed it in under
  15 s (the game's own quit path, no force).
- Steam's verify of the whole game took 40 s when nothing needed replacing.
- `state_o_matic.py` must be run from its own folder (it imports its neighbours by name).
- ⚠️ A start-up can hang inside REFramework's own hook install (the log ends with a run of "[Thread.cpp:42] Suspending" lines
  2 s after the start, before any plugin starts; State-o-matiC says frozen, the window does not answer). Seen once in six
  launches on 2026-11-07. The game's own close does nothing then; the process has to be ended, and the next start was fine.
- Holding a long gun forward on the simulator: the right hand at (0.15, 1.30, 0.05), yaw 29.6, pitch 77.8 points the W-870's
  barrel straight ahead (0, 0, -1). With yaw 0 / pitch 0 it hangs muzzle-down; with the right hand at z -0.35 the fore-end is
  out of the left arm's reach (the drawn wrist stops ~0.35 m short).
- Visceral RE2's tools build (`cmake -DVN_TOOLS=ON`, folder `build-tools`) logs `magreport`, `rackreport`, `shellreport`
  twice a second: the room-space move that puts the left hand on the magwell / slide / port. A drive reads it and moves the
  hand by exactly that (usually 1-2 steps). Never in a player build.
- A gifted gun comes with a nearly empty magazine (W-870: 1 shell, Lightning Hawk: 1 then 7 after a top-up).


## Menus

- 2026-11-12: Gameplay to closed (2026-11-12): route quit_from_gameplay.json. A plain window close does nothing in play; Esc pause > Quit > Up + Enter (Enter alone = No; the confirm box is partly outside the window) returns to the title in ~10 s, then the window close ends the game in ~6 s. REFramework's menu may already be open at the start: Insert hides it.


## Tool ideas

- 2026-11-12: REFramework's menu is taller than the simulator-sized window and does not scroll there, so Reset scripts cannot be clicked; a small always-loaded script that reloads test scripts on a command file would save a full restart.

## Moving the player by script (2026-11-16)
- `Transform.set_Position` on the player, a small step each `UpdateBehavior` frame, moves her and she stays there; the
  game's collision stops her at walls (she slides along them). The player: `app.ropeway.PlayerManager` `get_CurrentPlayer()`.
- `app.ropeway.HitPointController` `set_Invincible(bool)` takes and reads back.


## The pump shotgun on the simulator (2026-11-19)
- The W-870 in hand whoever the save's character is: `visceral-re2-vr` `tools/sim/q_give_w870.lua` through `rv.py -f` (memory only).
- `tools/sim/pump_hold.py <seconds>` holds it level (RG held) with the left controller on the fore-end and LG held; then
  `rv.py -f q_pump_hold.lua` says where the drawn left hand sits in the gun's own axes (+X is the gun's LEFT, +Y up, +Z muzzle).
- The drawn hand snaps onto the hold only when the controller is within 15 cm of it (our REFramework's limit); further out
  the plugin still grips, with the hand left at the controller. The loader's log line says the distance
  ("controller hand 0.180 m from the socket").
- Moving the simulator's HEAD moves the body and breaks reach and the grab: dock first with the head at its default spot,
  THEN move the head for a side view (head -0.45, 1.38, -0.45, yaw -75, pitch -10 shows the fore-end from the left).
  The game's own window is black in VR (only REFramework's menu shows): pictures come from the simulator's capture.
- The simulator's preview shrinks to 580 px and captures come out tiny: `tools/sim/sim_window.py` puts it back (no click).
- Two programs writing VR Bench's controllers at once make one stop silently (a shared temporary file): one writer at a time.
- A reading taken right after a relaunch can be the free hand, not the dock: check the log's "left hand: ... has it" line
  and the loader's distance line before believing a number.
- The quit route (`quit_from_gameplay.json`) worked twice today from play with REFramework's menu open; closed in 6 s after it.

## Tool ideas
- 2026-11-19: a `--front` that does not need Village's pid file (rv.py reads `pid.txt` from the Village tools folder).

## 2026-11-23: the real headset on the desk, robot hands; a save by script
- Two Frame Sequential (`VR_RenderingTechnique_V2=1`) with a headset streaming and nobody wearing it: the desktop window
  repaints (pictures work). VR Bench works through the real runtime: start `re2.exe` with `SteamAppId=883710`, no
  `XR_RUNTIME_JSON`; A, A, A reached the last save (the RPD main hall).
- In headset mode the window's close message DOES close the game from gameplay (about 4 s).
- **Saving by script works and the game lists it:** `app.ropeway.gamemastering.SaveDataManager`
  `requestSaveGameData(getSaveModeFromCurrentScenarioType(), <slot>)`; `getSaveFileDetail(mode, slot)` is nil for a free slot
  (this PC: 0 = auto, 1, 2, 4, 5, 6 used before; 7 written). About 5 s; `get_IsSaveBusy` says when it is done.
- `InventoryManager.addAndEquipMainWeapon(WeaponType, 1, 0)` took ten guns in one go on Claire's save (Matilda 1, SLS 60 10,
  JMB Hp3 3, M19 2, W-870 11, GM 79 42, MQ 11 21, LE 5 23, Chemical Flamethrower 43, Spark Shot 44); Quickdraw Army 4, MUP 8,
  Broom Hc 9, ATM-4 45 and the Anti-tank Rocket 49 were not taken (with four slots free). `hasWeapon(WeaponType)` answers.
  Then `Slot.set_BulletID` per gun (handgun 15, shotgun 16 guessed, SMG 17, magnum 18, acid rounds 22, fuel 25).
- The item box is not an inventory in `InventoryManager.Inventories` (one entry: the player's 20 slots); not found yet.
- 2026-11-23 (afternoon): a gun is put in the hand by script with `Inventory.equipMainSlot(app.ropeway.inventory.Slot)` (the
  slot object from `InventoryManager.<CurrentInventory>._Slots`); it changes about 3 s later, the window in front.
  `equipSlot(Int32)` is accepted and does nothing. `addItemCount(app.ropeway.gamemastering.Item.ID, Int32)` with 16 gave
  shotgun shells. The mod's log tag is `[visceral-native]`; it names the gun (`weapon: WP0000 Matilda`), the left hand's owner
  (`left hand: the slide / fore-end has it`), every magazine state and each rack (`rack: WP0000 slide done`).
- The gun's joints by Lua: `_00` the root (the magazine well is 0 to 3 cm from it), `_01` the slide or fore-end (`_02` on the
  LE 5), `_04` the magazine (it travels with a magazine that is out). The robot tools for it are in visceral-re8-vr
  `dev-archive/native/tools/sim/` (`re_go.py`, `re_seek.py`, `re_pistol.sh`), used with `RV_GAME_DIR`.
- The window's own close closed the game from play in under 10 s again.
