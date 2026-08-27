# Profile format (schema v0.1)

A profile is one JSON file per game in `profiles/`, named after the game in
lower-kebab-case with a disambiguating year where useful: `psychonauts.json`,
`xiii-2003.json`.

> **v0.1 is expected to change.** It was designed from two real games rather than
> from imagination, which keeps it grounded, but two games is not enough to know
> what generalises. If a field fights you, say so in an issue rather than working
> around it silently.

## Design rules

**Every factual claim carries `confidence` and `verified`.** This is the most
important rule in the format. A confident wrong claim costs more than a missing
one, because it sends the reader down a path that cannot work. The values are:

| `confidence` | Means |
| --- | --- |
| `verified-live` | Someone did this in the running game and observed the result. |
| `inferred-static` | Derived from disassembly, config files, or reasoning. Not yet seen to work. |
| `reported` | From a wiki, forum, or another person. Plausible, unconfirmed here. |
| `disproved` | **Tried, does not work.** Kept deliberately — see below. |

**`disproved` entries are kept, never deleted.** They are the highest-value rows in
the file. "`ENTER` does not activate door prompts" stops the next reader repeating a
dead end. Deleting it guarantees somebody repeats it.

**Prefer replayable steps over prose.** `"press DOWN ×3, then SPACE"` as structured
steps can be executed; a paragraph describing the menu cannot.

## Top-level structure

```json
{
  "schema_version": "0.1",
  "game": { ... },
  "input": { ... },
  "states": [ ... ],
  "routes": [ ... ],
  "entry_points": { ... },
  "commands": { ... },
  "hazards": [ ... ]
}
```

Only `schema_version` and `game` are required. Omit what you have not established;
an absent section reads as "unknown", while an empty one reads as "none exist",
and those are different claims.

### `game`

Identifies precisely *which build* the profile describes, because bindings and
level codes vary between releases.

```json
"game": {
  "title": "Psychonauts",
  "year": 2005,
  "developer": "Double Fine",
  "engine": "bespoke in-house",
  "renderer": "Direct3D 9",
  "distribution": "Steam",
  "build_notes": "Verified against the Steam build, 2026-08-27."
}
```

### `input`

How the game takes input, and what each key does.

- `api` — what the game actually polls (`DirectInput8`, `XInput`, Win32 messages,
  …). Matters because it decides whether synthetic input works at all, and where to
  hook if it does.
- `api_notes` — free-text gotchas about that API *for this game*.
- `bindings[]` — one entry per action:

```json
{
  "action": "confirm",
  "keys": [ { "name": "SPACE", "dik": "0x39", "extended": false } ],
  "context": "door prompts, menus, dialogue",
  "confidence": "verified-live",
  "verified": "2026-08-27",
  "notes": "ENTER does NOT work here."
}
```

`dik` is the DirectInput scancode, needed for scancode-level synthetic input.
`extended` must be `true` for arrow keys and similar, or DirectInput sees the
numpad variant instead.

### `states`

**The section that prevents the most wasted work.** A game in a menu ignores camera
input; a game in a cutscene ignores everything. Testing without knowing which state
you are in produces confident nonsense.

```json
{
  "id": "gameplay",
  "description": "Player has control.",
  "detect": [
    { "method": "screenshot", "cue": "HUD icons visible in the lower right" },
    { "method": "telemetry", "cue": "camera position in the tens of thousands" }
  ],
  "confidence": "verified-live",
  "verified": "2026-08-27"
}
```

`method` is typically `screenshot`, `telemetry`, or `log`. **List a visual cue
wherever one exists** — indirect signals are the ones that mislead.

### `routes`

Replayable navigation. Steps are executed in order.

```json
{
  "id": "title-to-loaded-save",
  "goal": "From the title screen to a loaded save.",
  "from_state": "title",
  "to_state": "gameplay",
  "reliability": "needs-verification-between-steps",
  "steps": [
    { "action": "key", "key": "SPACE", "note": "begin" },
    { "action": "verify", "expect_state": "menu-hub" },
    { "action": "key", "key": "DOWN", "repeat": 2 }
  ]
}
```

`action` is `key`, `wait`, `verify`, or `command`. **Put `verify` steps between
anything ambiguous.** A route that assumes each step landed is a route that fails
silently halfway and reports success.

`reliability` is honest prose: `deterministic`, `needs-verification-between-steps`,
`known-flaky`.

### `entry_points`

Fast ways into a known place — save slots, level codes, debug jumps — and **where
each actually lands**, which is not always what its name suggests.

```json
"entry_points": {
  "saves": [ { "slot": 1, "lands": "outdoors, The Neighborhood", "confidence": "verified-live" } ],
  "level_codes": [ { "code": "MMI1", "lands": "Boyd's house INTERIOR", "confidence": "verified-live" } ]
}
```

### `commands`

For games with a console or an internal command dispatcher. Record **which object
or subsystem resolves each command** — commands that look alike are often handled in
completely different places, and one may exist while another does not.

### `hazards`

Things that will bite. Popups that swallow input, autosaves that fire on exit,
states you cannot leave without a specific key.

## Minimal example

```json
{
  "schema_version": "0.1",
  "game": { "title": "Example", "year": 2000 },
  "input": {
    "bindings": [
      { "action": "confirm", "keys": [ { "name": "SPACE", "dik": "0x39" } ],
        "confidence": "verified-live", "verified": "2026-01-01" }
    ]
  }
}
```

That is a legitimate profile. Start there and grow it as things are established.
