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
