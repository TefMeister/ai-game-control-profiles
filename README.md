# AI Game Control Profiles

**Machine-readable notes on how to *operate* a PC game programmatically — so an AI
assistant driving that game doesn't have to rediscover the basics every session.**

Not a mod, not a trainer, not an agent. This repository is **data**: which key does
what, how the menus connect, how to tell "I'm in gameplay" from "I'm in a menu",
which entry point drops you where, and which things are known not to work.

## Who this is for

Anyone using an AI model to drive, test, or automate a PC game — for accessibility
work, automated testing, mod development, or research. **It is deliberately not tied
to any one AI vendor.** The profiles are plain JSON and the conventions are plain
English. Hand a profile to whatever model you use and it should be able to work from
it directly.

## Why it exists

An AI driving a game burns an enormous amount of effort re-deriving trivia. A real
example that motivated this repo — one session, one game, all of it re-learned from
scratch or simply got wrong:

- Pressed `ENTER` on a door prompt for several attempts. The action key is `SPACE`.
- Assumed `ESC` dismissed a popup. It opens the journal, which silently invalidated
  three separate experiments before anyone noticed.
- Worked out a menu route, then failed to reproduce it an hour later.
- Used a level-jump shortcut into what turned out to be an interior, then spent a
  long stretch hunting for a door out of it — when the save file already started
  outdoors.

None of that is hard knowledge. It is just knowledge nobody wrote down in a form a
machine could replay. That is the entire problem this repo addresses.

## What's here

| Path | What it is |
| --- | --- |
| [`SPEC.md`](SPEC.md) | The profile format, field by field. Read this first if you are writing one. |
| [`UNIVERSAL.md`](UNIVERSAL.md) | Knowledge that applies to **any** game: platform/API traps, and the method rules for testing against a live game. |
| `profiles/*.json` | One profile per game. |
| `inbox/` | Drop-off for contributions that the maintainer folds in (see CONTRIBUTING). |

## Using a profile with any AI model

1. Pick the profile for your game from `profiles/`.
2. Give the model **that file plus [`UNIVERSAL.md`](UNIVERSAL.md)**. The universal
   file matters more than it looks — most wasted effort comes from method mistakes,
   not missing facts.
3. Ask it to state which `state` it believes the game is in, **and how it verified
   that**, before it sends any input.

The profiles are small enough to paste into a prompt whole.

## The three tiers, and why they are separated

Knowledge about driving games splits into layers that transfer very differently:

1. **Platform / API** — true of Windows, DirectX, DirectInput regardless of game.
   Lives in `UNIVERSAL.md`. Transfers everywhere.
2. **Engine family** — true of an engine (Unreal 2, Unity, id Tech). Transfers to
   other games on that engine. Referenced per profile; the deep material belongs in
   engine-specific documentation elsewhere.
3. **Per game** — keys, menus, save layout, quirks. Transfers nowhere, and is
   exactly what gets re-derived most often. Lives in `profiles/`.

Mixing these is why notes get re-read badly. A model looking for "what dismisses
this popup" should not have to read a renderer teardown to find it.

## Honest scope

- **This is a knowledge base, not an intelligence.** Nothing here learns. It gets
  better because people write down what they verified and correct what they got
  wrong. The compounding is real, but it comes from discipline, not from the data
  being clever.
- **It does not remove the need to look.** Every profile encodes state detection
  precisely because trusting an indirect signal — a number that looked about right —
  is a documented way to waste a session.
- **Profiles are build-specific.** Key bindings and level codes can differ between
  releases, platforms and patches. Every claim is dated and tagged with how it was
  established (see `confidence` in [`SPEC.md`](SPEC.md)).

## Contributing

New profiles and corrections are welcome — including for games nobody here has
touched. See [`CONTRIBUTING.md`](CONTRIBUTING.md). Corrections are valued as highly
as additions: a profile that confidently states something false is worse than one
with a gap, because it sends the reader down a path that cannot work.

## Licence

[CC BY 4.0](LICENSE) — use it, change it, build on it, just credit the source.
