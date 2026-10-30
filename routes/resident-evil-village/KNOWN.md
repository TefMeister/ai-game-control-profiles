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
