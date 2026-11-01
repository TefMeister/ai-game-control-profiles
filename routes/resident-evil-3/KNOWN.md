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
