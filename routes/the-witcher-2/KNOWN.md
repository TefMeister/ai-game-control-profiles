# The Witcher 2: Assassins of Kings Enhanced Edition: what is known about driving it

Read this before launching. First learned 2026-10-16 (one session, so every line is n=1 unless it says otherwise).

## Getting to gameplay

1. **Direct start (preferred):** with Steam running and a `steam_appid.txt` holding `20920` beside `bin\witcher2.exe`,
   start `witcher2.exe` from `bin\`. No launcher window, no Steam first-run step. Without that file the exe hands
   itself back to Steam, which opens the game's own launcher.
2. **Through Steam:** the game's launcher appears (a picture with a text menu). "Launch game" sits at about
   (0.55, 0.65) of the launcher window; a click there works.
3. Intro films and logos: press Esc about four times, 4 s apart. The main menu follows.
4. Main menu (top to bottom): ARENA (highlighted), NEW GAME, LOAD GAME, OPTIONS, EXTRAS, QUIT. The list does not
   wrap: Up from ARENA does nothing.
5. **The quickest way into play, no save needed:** NEW GAME, then on "Witcher 1 saves unavailable" Down once to NEW
   GAME, Enter; the difficulty list opens on EASY with the question "Do you want to begin the Tutorial?". **Space**
   accepts (Enter does not). About 60 s of loading, then two help panels: Tab closes each. Geralt stands on a jetty
   at night and can move.
6. **The tutorial cannot be saved** ("You cannot save at this time" on F5), so every visit repeats step 5.

## Controls that work (keyboard, scancode SendInput)

- Menus: arrow keys and Enter. **The mouse cursor ignores synthetic clicks in the game's menus**; use keys.
- Single taps are sometimes lost. Menu steps sent one at a time, about 1 s apart, landed every time.
- Sliders (Options > Sound and Music) move in small steps: **hold** Left or Right instead of tapping. Holding Left
  for 6 s took music from 10 to 0.
- W held for 1.2 s walks Geralt forward.
- Esc in play opens RESUME GAME / LOAD GAME / OPTIONS / EXIT TO MAIN MENU; Down x3 then Enter reaches the main menu
  with no question asked.

## Settings

- The live settings are `Documents\Witcher 2\Config\User.ini` (created on the first start); the copy in the game's
  `bin\config\` is only the template. Edit with the game closed, back it up first.
- Window: `[Rendering] Fullscreen=0`, `[Viewport] Width=1280`, `Height=720` gives a 1280x720 window and leaves the
  desktop alone.
- Music: `[Sound] MusicVolume=0`.

## Leaving

- Main menu, Down x5 to QUIT (verify the highlight), Enter: the game closes at once, no question asked.

## Hazards

- On the first start Steam runs a setup step (DirectX and Visual C++ installers, one registry key) that asks for
  administrator rights every time until someone allows it. While that prompt is up the game cannot start its
  graphics and shows "Direct3D 9 device initialization error!". The direct start above avoids it.
- Clicking OK on that error lets the failed copy keep running behind another error box; a second copy can then
  start beside it. Check for two `witcher2.exe` before driving.

## What did NOT work (kept so nobody repeats it)

- Binding the console actions in `Input_QWERTY.ini` (`IK_Tilde` and `IK_NumPad0` to `CHEAT_Console`, `IK_NumPad1` to
  `CHEAT_DebugPages`): nothing opened, at the main menu or in play, while W did move Geralt.
