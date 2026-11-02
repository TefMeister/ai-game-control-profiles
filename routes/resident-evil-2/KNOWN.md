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
