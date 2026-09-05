# resident-evil-village.json — route re-verified cold (n=3), one new `disproved`, one alias fix

From: `/lm re-village-scope-vr`, home PC, 2026-09-05 late.
Target: `profiles/resident-evil-village.json`. Create-only inbox drop; fold in and delete.

## `routes[title-to-last-save]` — bump reliability

Ran cold from `steam://rungameid/1196590` with nobody at the keyboard, a capture verified at
every step before every commit: overlay open at boot → INSERT → title → ENTER → main menu with
**Continue** underlined → F → "Load most recent saved data?" with **No** highlighted → UP → Yes →
F → "Stronghold / F Continue" → F → gameplay (`world[ok-body] … LOCK` in the log at +3 min from
process start). `confidence: verified-live`, `verified: 2026-09-05`, **n=3 launches**.

## NEW `disproved` entry — clicking the REFramework overlay from outside

```
{
  "action": "click a REFramework ImGui button from outside (Reset scripts)",
  "tried": [
    "user32.mouse_event LEFTDOWN/LEFTUP after SetCursorPos, 0.08 s and 0.35 s holds",
    "SendInput MOUSEEVENTF_ABSOLUTE move, then LEFTDOWN/LEFTUP, 0.20 s hold, window foregrounded"
  ],
  "observed": "HOVER registers (the button highlights in the capture); the click never fires -- no reload lines in the log, no state change, three attempts",
  "consequence": "a Lua script edit costs a relaunch; the overlay is effectively read-only from a driven session",
  "confidence": "disproved",
  "verified": "2026-09-05",
  "notes": "Tools kept in re-village-scope-vr/dev-archive/tools/re8click.py (SendInput) and re8click-legacy-mouse_event.py. Untried: scancode-level mouse via the raw-input path REFramework hooks; a keyboard-only route through ImGui nav (ImGuiConfigFlags_NavEnableKeyboard is probably off)."
}
```

## `input.bindings` — numpad alias note

`re8drive.py num` now accepts `.`, `*`, `+`, `-`, `/` by name (virtual keys 0x6E, 0x6A, 0x6B,
0x6D, 0x6F). The old digit-arithmetic form (`num 14` = `.`, `num 10` = `*`) still works; the
bindings' `notes` fields that mention the arithmetic can say so.

## `input` — camera turn

Relative `SendInput` mouse (`MOUSEEVENTF_MOVE`, dx=60 × 30) turns the camera in flat gameplay when
NOT in the pad-ADS hold `[verified-live 2026-09-05, n=1]`. While `ads 1` is held the game is in pad
mode and ignores it — release, turn, re-hold.
