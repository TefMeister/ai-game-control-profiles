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
- Upper-body motion families: 20xx idle / walk, 21xx ready, 22xx aim, 23xx lowered, 24xx fire (the pump or bolt after a
  shot plays in it), 25xx reload, 3000 seen at a weapon swap.

## The game pad (an Xbox pad through ViGEm works, flat)

- RT alone fires from the hip; LT held aims (with the sniper rifle the scope mod's companion keeps the zoom off). X =
  reload. B = back out of a menu.
- D-pad = the four quick slots (this save: up LEMI, right M1897, down F2 Rifle, left W870 TAC). A direction that is
  already in hand does nothing.
- Reloading with no spare ammo but craftable parts opens the CRAFTING screen, and a trigger press there crafts: check the
  ammo before sending reload / fire in a row.
- A gun taken out empty reloads by itself.

## Closing

- WM_CLOSE to the main window: gone in about 2 s, process exits about 12 s later. Wait for the process to leave before
  relaunching, or Steam just focuses the dying one.

## What failed

- The menu cut-outs did not match at a 1920x1080 window (`screen None` all the way), yet the driver's Enter nudges alone
  reached gameplay. Recut the cut-outs at the current window size, or match by relative size.

- A launch script kept in the game folder disappeared with a clean reinstall, and starting a missing script fails
  silently: the driver now falls back to the Steam launch.
