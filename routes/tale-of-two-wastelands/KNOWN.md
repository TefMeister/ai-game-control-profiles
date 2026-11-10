# tale-of-two-wastelands: what we know about driving it

Read before every launch. Add what you learn the same session (`known FOLDER --add ...`).
Only game facts: no paths, machine names or people.

## Menus

- 2026-11-09: Main menu: Continue / New / Load / Settings / Credits / Quit on the right; TTW's own self-check shows boxes here when a requirement is missing (ShowOff, JohnnyGuitar, falloutcustom.ini).

## Controls

## Save spot

## Hazards

- 2026-11-10: 2026-11-10: never name this game's window to a tool by the word 'Fallout' alone: a chat program open on a thread titled 'Fallout 3/NV VR ...' matched first, a picture of IT was taken and the quit keys were nearly typed into it (nothing was sent). The game's window belongs to FalloutNV.exe; check the program behind a window before using it. While an add-on has a box up, the game's main window is that box (title 'New Vegas Tick Fix', 'Stewie Tweaks', 'Fatal Error: Missing NVSE Plugin').
- 2026-11-10: 2026-11-10 (later): from Lanes 0.106.2 every Menu-o-matiC / Move-o-matiC / State-o-matiC command takes --exe FalloutNV.exe (and a route file may carry "exe"); with it a window of another program is refused. Every route recorded for this game must carry it; until this PC has 0.106.2, no keys or clicks to this game through those tools.

## What failed

- 2026-11-09: 2026-11-09, main menu (Begin Again debug profile, OneTweak borderless 3440x1440): a Menu-o-matiC click does not move the game's own cursor; Down (SendInput) moves the highlight; Enter and E (SendInput) do NOT select. Untried: key_input post/both, a held key, the mouse moved over the item then clicked.
- 2026-11-10: 2026-11-10: the main menu ignores a WM_CLOSE to its window. A polite 'taskkill /IM FalloutNV.exe' (no /F) closes it.

## Tool ideas

- 2026-11-10: 2026-11-10: Menu-o-matiC should take a program name beside a window title (and refuse a window whose program is not the game's): the title-only match is what picked the chat window.
