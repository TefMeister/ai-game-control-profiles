# Contributing

Profiles for games nobody here has touched are welcome, as are corrections to
existing ones.

## What makes a good contribution

**Corrections rank above additions.** A profile that confidently states something
false is worse than one with a gap — it sends the next reader down a path that
cannot work. If you find a wrong entry, change its `confidence` to `disproved`,
leave it in place with a note explaining what actually happens, and add the correct
entry alongside.

**Tag every claim honestly.** `verified-live` means you did it in the running game
and watched it work. If you read it on a wiki, that is `reported`. If you worked it
out from disassembly but never ran it, that is `inferred-static`. Mixing these up is
the main way this kind of document rots.

**Date everything.** Games get patched; bindings change between releases. A claim
without a date cannot be audited later.

**Say which build.** Store version, platform, and any patch level in `game`. A
profile that silently describes a different release is actively misleading.

## Practical notes

- Validate your JSON before opening a PR.
- One game per file, named lower-kebab-case with a year where it disambiguates.
- Keep `notes` concrete. "Sometimes doesn't work" helps nobody; "does not work while
  a menu is open" does.
- Prefer a small honest profile over a large speculative one. Three verified
  bindings beat thirty guessed ones.

## The `inbox/`

If you would rather not restructure an existing profile yourself, drop a file in
`inbox/` named `YYYY-MM-DD-<short-slug>.md` describing what you found, and the
maintainer will fold it in. Create new files there; do not edit someone else's.

## Scope

This repo is about **operating** games — input, navigation, state, entry points. It
is deliberately not about reverse-engineering renderers, modding, or VR conversion.
Deep engine internals belong in engine-specific documentation; link to it rather than
inlining it, so a reader looking for "what dismisses this popup" does not have to
read a renderer teardown.

## What not to submit

- Anything requiring or enabling piracy, DRM circumvention, or cheating in
  multiplayer games against other people.
- Copied game assets, decompiled source, or copyrighted text.
- Credentials, personal data, or anything identifying private individuals.

## Licence

Contributions are accepted under [CC BY 4.0](LICENSE), the same licence as the rest
of the repository.
