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
- `sim_boot.py` (Visceral's shared tools, `game.conf`: 4 A presses, 4 s apart) reached gameplay every time today (5 boots).
- Long guns pointing forward on the simulator: right hand (0.15, 1.30, 0.05), yaw 29.6, pitch 77.8 (as RE2). A handgun held
  for the left hand to reach its magwell: right hand (0.12, 1.40, -0.25), yaw 90, pitch 35 (at z -0.42 the arm falls short).
- Gifted guns by script: the G19 arrives with 13 and takes item 31; the M3 arrives with 2 shells and takes item 32; the CQBR
  arrives with 1 round and its ammo item reads 0 (not found by `ammo`).
- RE3's REFramework log is still `re2_framework_log.txt`.
