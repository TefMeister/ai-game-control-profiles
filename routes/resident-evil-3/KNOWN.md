# Resident Evil 3 (remake): what is known about driving it

Read before the game starts; add what is learned.

## On the OpenXR Simulator (no headset), 2026-11-01
- Runs with REFramework's Single Frame Multipass (`VR_RenderingTechnique_V2=2` in `re2_fw_config.txt`: RE3's REFramework
  build keeps RE2's file names, and its log is `re2_framework_log.txt` in the RE3 folder).
- Start it by its exe with `XR_RUNTIME_JSON` pointed at the simulator's json and `SteamAppId=952060`.
- VR Bench gives the controller buttons. To gameplay: FOUR presses of A, about 4 s apart (title "START GAME", "Story",
  "Continue", then the load: one more than RE2 needs; the title takes longer to answer). ~15 s to load.
- The game pauses whenever its window is not in front: bring it forward before each press or screenshot.
- The player: `offline.PlayerManager` `get_CurrentPlayer()` (nil until gameplay has loaded). Gun in hand without menus:
  the `offline.survivor.Inventory` component `equipSlot(System.Int32)` (this save: slot 0 = a G19 if given, 1 = knife,
  4 = M3 Shotgun); a gun not owned: `offline.gamemastering.InventoryManager.addAndEquipMainWeapon(WeaponType, n, 0)`
  (the gun arrives FULLY loaded whatever n says; memory only).
- Items by script: `offline.gamemastering.InventoryManager.addItemCount(Item.ID, n)`; the MGL takes one of ids 35-38.

## Froze at start-up (2026-11-02)
- Started flat with a mod's plugin taken out and REFramework's LooseFileLoader and FirstPerson switched off, the game hung
  inside REFramework's start-up (the log ends with it suspending threads), Windows said it was not answering, and it used no
  processor. Only ending the process got out. Which of the three changes did it is not known yet.
- A screen capture of a window in that state waits forever: ask the window first (State-o-matiC does since 2026-11-02).

## The clean rebuild's first runs (2026-11-07)
- State-o-matiC knows the title (`states.json`, taught on the plain game: the logo on the left over Nemesis).
- The plain game reached the title ~50 s after the exe started; `CloseMainWindow` closed it within 15 s. Steam's verify: 41 s.
- 2026-11-12: `CloseMainWindow` from GAMEPLAY (modded, on the simulator) also closes it within 15 s, no quit route needed (unlike RE2, whose window close does nothing in play), BUT ONLY WITH THE WINDOW IN FRONT (mom_window.focus first): twice it was ignored while the window was behind (the game pauses when not in front). Esc in play showed no pause menu in the simulator's view.
- `sim_boot.py` (Visceral's shared tools, `game.conf`: 4 A presses, 4 s apart) reached gameplay every time today (5 boots).
- Long guns pointing forward on the simulator: right hand (0.15, 1.30, 0.05), yaw 29.6, pitch 77.8 (as RE2). A handgun held
  for the left hand to reach its magwell: right hand (0.12, 1.40, -0.25), yaw 90, pitch 35 (at z -0.42 the arm falls short).
- Gifted guns by script: the G19 arrives with 13 and takes item 31; the M3 arrives with 2 shells and takes item 32; the CQBR
  arrives with 1 round and its ammo item reads 0 (not found by `ammo`).
- RE3's REFramework log is still `re2_framework_log.txt`.

## Guns and rounds by script, and the live question line (2026-11-10)
- A file-in, file-out question line runs in the game (`zz_visceral_eval.lua` in autorun; the Village tool `rv.py` with
  `RV_GAME_DIR` set to the RE3 folder): Lua on the game thread, answers in under a second. The game must be in front.
- The inventory is full at 16 slots in the usual save: a gifted gun silently does not arrive. `Inventory.removeSlot(n)` first.
- The MGL: `InventoryManager.addAndEquipMainWeapon(42, 6, 37)` (weapon type 42, six rounds, the round's item). Round items:
  36 Mine, 37 Explosive, 38 Acid, 39 Flame (35 is not ammo). `addItemCount(item, n)` adds a stack.
- A gun's loaded kind of round is the main slot's `set_BulletID(item)`: the count stays, the next shot is that kind.
- `Inventory.equipSlot(4)` puts the M3 in hand. The right trigger through VR Bench fires; a shot needs about 2 s before the next.
- Hip spots for the robot hands, head at (0, 1.7, 0): left hip (-0.20, 1.08, -0.02), right hip (0.20, 1.08, -0.02); the left
  grip is `squeeze`. Bringing a hand to a spot on the gun: ask the game for the miss in the head's axes and step the hand by
  it (`visceral-re3-vr` `dev-archive/tools/sim-mgl/`), three or four steps to within 6 cm.
- For the robot's right hand (2026-11-10): a gun points level at pitch 78 (pitch 0 points it at the floor), muzzle-up at
  pitch 125. A trigger press of 0.4 s is sometimes missed; 0.8 s is not. The left arm reaches the launcher's drum only
  with the gun held close: right hand (0.08, 1.40, -0.16).
- Which joints carry a mesh: move one joint 1.5 m away each frame and read the gun mesh's `get_WorldAABB`: the box grows
  when the joint carries geometry. Quicker and surer than a screenshot.
- The game's ammo HUD (2026-11-10): the object `GUI_RemainingBullet`, component `offline.gui.EsRemainingBulletBehavior`
  (RE2's is named without the "Es"). `set_TriggerSwitchBullet(true)` + `set_RequestDraw(true)` shows the game's list of the
  gun's kinds of round with icons and counts for about 2 s; `get_IsDraw` says whether it is up. A screenshot asked for
  through the simulator arrives 2 to 3 s later: too late to catch it; read `get_IsDraw` instead.
- Finding an on-screen element: list the scene's `via.gui.GUI` components and their GameObject names (83 of them).
- A gun mesh's materials and their values: `Mesh.get_MaterialNum`, `getMaterialName(i)`, `getMaterialVariableName(i, v)`,
  `setMaterialFloat4(i, v, colour)`. The MGL: material 1 is every round's, value 0 its BaseColor.

## Closing from gameplay, and reading the dodge (2026-11-16)
- `CloseMainWindow` right after SetForegroundWindow was ignored once from gameplay (simulator); restoring the window first
  (ShowWindow 9), foreground, a 2 s wait, then `CloseMainWindow` closed it in 6 s. Seen twice the same day: after play on the
  simulator the FIRST close is ignored even so, the second one works; send it twice.
- The dodge on the simulator: VR Bench left stick pushed (0.3 s first), then the right `stick_click` (0.15 s) = a dodge that
  way (our layout patch). The player's motion layer 0 then plays bank 0 / id 1035; `offline.HitPointController`
  `get_TrackEmergencyDodge` is true for its first ~0.23 s.
- The usual save starts Jill at the save typewriter: right A there opens the SAVE screen, which neither B (VR Bench) nor Esc
  closed on the simulator (2026-11-16); a restart did. Walk back 2 s (left stick down) before testing anything on A.
- Backing up 2 s from the typewriter puts Jill at a WALL MAP: right A there opens the map (`openInventoryMapMode`, camera type 7).
  Not the inventory button. X (left A, Cancel) closes it; Y (left B) opens the inventory. Since b024 A in open floor does nothing.

## The M3's pump hold on the simulator (2026-11-19)
- The M3 into Jill's hand: `visceral-re3-vr` `dev-archive/native/tools/sim/q_give_m3.lua` through `rv.py -f` (it walks the
  slots, slot 4 in the usual save; ask it until it says "the M3 is in hand").
- Step back first: at the typewriter desk a long gun held forward meets the desk, the walls feature turns the gun away
  (the log: "the gun turned at the wrist, away from a wall") and every hand measurement is off. `tools/sim/walk.py 2`
  (left stick back 2 s, 2.2 m) puts Jill in open floor.
- RE2's `pump_hold.py` and `q_pump_hold.lua` work on RE3 as they are. Since b027 the M3's hold is 7 cm further forward
  than the W-870's: left controller at (0.13, 1.29, -0.43) docks ("0.119 m from the socket"); the robot's left arm is then
  at full stretch and the drawn wrist stops about 2.7 cm short of the hold.
- The M3's joints from the gun's root, along the barrel: `_01` (the fore-end) 0.425 m, `_03` 0.070, `_04` 0.170, `_05` 0.216.
- `tools/sim/pump_stroke.py`: one pump by hand (LG, then LG + LT, 9 cm back and home); the log says
  "rack: WP1000 pump done".
- Changing the hold's numbers (the tuning file) while the hand is docked makes the hand let go and the gun swing: move the
  controller by the same amount.
- Head pose for a side view of the fore-end, hand docked first: (-0.45, 1.38, -0.45), yaw -75, pitch -10 (RE2's; fine
  here twice). (-0.5, 1.32, -0.38), yaw -90 broke the dock.
- The window close (`wins.py <pid> via`, the window in front, 2 s wait) closed the game on the FIRST try, twice today.
- ⚠️ `shot.py` (Village's sim tools) runs on any argument, `--help` too, and presses Insert in the game: do not call it to
  read its usage (on the fault list).
