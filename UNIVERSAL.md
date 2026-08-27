# Universal: what applies to every game

Two kinds of knowledge live here. **Platform facts** (true of Windows/DirectX/
DirectInput regardless of game) and **method rules** (how to run an experiment
against a live game without fooling yourself).

The method rules are listed first, deliberately. In practice they prevent more
wasted work than the technical facts do — the expensive mistakes are usually not
"I didn't know the API", they are "I measured the wrong thing and believed it".

---

# Part 1 — Method rules

## 1. Verify the game's state *visually*, immediately before every test

**The single most expensive mistake.** A game sitting in a menu ignores camera
input, mouse-look, and most memory writes. Testing in that state produces a
confident negative result that is pure noise.

Worked example: a session ran three consecutive camera experiments while a pause
journal was open on screen, and concluded from them that *"a real mouse does not
rotate the camera in this game"* — writing it into permanent notes as a finding. The
game had mouse-look the whole time. A screenshot had been captured each time and
never opened; only a derived number was checked, and it happened to look plausible.

- Look at the actual image. Not a metric derived from it.
- Verify **immediately** before the test, not a few steps earlier — menus can open
  on their own (see §2).
- If a state check is cheap, repeat it *after* the test too.

## 2. Assume anything can change state under you

Popups appear on triggers. Tutorial prompts block input. Toggle keys mean an
"open menu" press can close one. A test that starts in gameplay may not end there.

## 3. Log **before** the call, not after

When driving something that can crash or hang, log the intent *before* making the
call and flush it, then log completion after.

Worked example: a harness logged each command only on completion. The first command
it ever ran crashed the game, and the log was therefore empty — the cause had to be
reconstructed indirectly from a telemetry stream stopping and CPU dropping to zero.
Logging first would have named the exact call. The change costs one line.

## 4. One variable at a time, and freeze what you can

Freeze the camera or pause the world if the game allows it, so two captures differ
*only* by the thing under test. Comparing two moving scenes is how ambiguous results
happen.

## 5. Run A/B/A, not A/B

Change the thing, then change it back. If the first and third measurements do not
match, something else moved and the middle one means nothing.

## 6. Distinguish "the write did not land" from "the write is not read"

When poking memory, always **read the value back afterwards**. Two very different
failures look identical from outside:

- **Value survives, nothing happens** → you found a field nothing consumes. Wrong
  target; look elsewhere.
- **Value is gone / restored** → the engine overwrote it. Right target possibly,
  **wrong timing**; you need a different point in the frame.

A session hit both on the same object within an hour and could only tell them apart
by reading back. Without that, both look like "it didn't work".

## 7. Prefer engine-native paths over fighting the engine

If the engine already knows how to do the thing — a console command, a script
binding, its own input handling — feeding it through that path is usually more
robust than writing state underneath it, because the engine's own downstream logic
(smoothing, clamping, culling, validation) comes along for free.

## 8. "Not found" is only as strong as your tool's floor

A string search that reported a cheat command absent from a binary was wrong: the
extraction tool had a 4-character minimum and silently dropped every 3-character
token. The command existed and was live. Know your tool's thresholds before
treating absence as evidence.

---

# Part 2 — Platform facts

## DirectInput: devices of the same class share one vtable

**Patching a method on "the mouse" patches it for the keyboard too.**

DirectInput device objects created from the same class share a single vtable. Code
that checks `GUID_SysMouse` at `CreateDevice`, then patches vtable slot 9
(`GetDeviceState`), has patched that slot for **every** device — keyboard included.

If the hook body then only sanity-checks the buffer *size*, a 256-byte keyboard
state buffer passes a `size >= 12` test happily, and writes intended as mouse axis
deltas land in the **key-state array** instead.

Observed consequence: injecting a mouse delta set bytes at the start of the keyboard
state array. `DIK_ESCAPE` is index 1, so the harness was effectively synthesising
Escape presses — a pause menu kept opening "by itself", silently invalidating three
experiments, while the mouse never saw the delta because the keyboard poll consumed
it first.

**Fix:** record the specific device *instance* pointer at `CreateDevice` and require
`device == that pointer` inside the hook. Keep the size check as a second line of
defence, not the only one.

## DirectInput: immediate vs buffered

`GetDeviceState` (immediate) and `GetDeviceData` (buffered) are different paths and a
game may use either, or both for different devices. Hooking one and seeing no effect
does not mean injection is impossible — check whether the game reads the other.

## Synthetic input needs real foreground

`SendInput` goes to whatever window has focus. Driving a game this way requires the
game window genuinely foreground, and nobody touching the physical keyboard or mouse
meanwhile. `AllowSetForegroundWindow` grants are consumed by the *next*
`SetForegroundWindow` call, so re-grant immediately before each attempt rather than
once at the start.

Injecting inside the process (hooking the game's own input polling) avoids all of
this and keeps working when the window is not focused.

## Games commonly stop rendering or ticking when unfocused

Many engines poll foreground state every frame and idle when they do not have it.
That stalls any automation waiting on the game to advance. Some can be told not to;
otherwise, lying to that one query (redirecting the foreground check) keeps the
engine ticking while unfocused.

## Capturing a Direct3D window

`PrintWindow` with `PW_RENDERFULLCONTENT` (`0x2`) captures many D3D windows that a
plain screen copy renders black, and works without the window being foreground. Fall
back to a screen-region copy if it fails.

## Do not act from a render-path hook

A per-frame hook is not automatically a safe place to *act*. If the call chain to
your hook passes through anything named `Draw`, `Render`, `Present` or `Repaint`,
you are inside the renderer, and calling into the engine's command or script system
from there can fault.

Reading state there is fine. Acting needs a hook on the simulation side — an entity
tick, the world update, the input phase. If your only per-frame hook is on the render
path, use it to *queue* and drain the queue from a simulation-phase hook.

*(Learned on Unreal Engine 2 / Direct3D 8, where a camera-update hook turned out to
be reached from inside the engine's `Draw`, and executing a console command there
caused an immediate general protection fault. The specific symptom will differ by
engine; the hazard class does not.)*

---

## Tooling notes (PowerShell, since it is the common Windows glue)

- **Variables are case-insensitive.** A loop counter `$r` silently clobbers an array
  named `$R`. Give arrays distinct names.
- **`GetPixel` per pixel is unusably slow.** Use `LockBits` plus one
  `Marshal.Copy` for any image analysis.
- **PowerShell 5.1 lacks `&&`, `||`, ternary and null-coalescing.** Use `;` with
  `if ($?)`.
- **`if` is not an expression inline.** `$x = $a + (if (...) {1} else {2})` is a
  parse error; assign the `if` to a variable first.
