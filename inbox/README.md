# inbox/

Drop-off for contributions the maintainer folds into the curated files.

Name files `YYYY-MM-DD-<short-slug>.md`. Create new files only - never edit or
delete someone else's, including your own from an earlier session. The maintainer
drains this folder and deletes entries once they are folded in, so a non-empty
inbox is a visible to-do rather than a silent loss.
## ⚠️ Read the whole inbox before draining any of it

Files here are **create-only**: nobody edits or deletes an existing one, not even their own from
an earlier session. That is what keeps concurrent sessions from ever colliding — but it also
means a correction cannot change the file it corrects. It arrives as a **separate, later file**
naming its target in a greppable header line:

```
Supersedes: 2026-08-27-<author>-<slug>.md
```

So before folding anything into the curated files, run:

```
grep -rn "^Supersedes:" inbox/ --exclude=README.md
```

Draining oldest-first without that check writes a claim into the curated docs and only then meets
the correction that withdraws it. A correction may also supersede a curated doc rather than an
inbox file — in that case it names the file and section, and the fix belongs to whoever owns it.

## Tag how well each finding is actually known

Put a confidence tag next to the claim itself — `[verified-live YYYY-MM-DD, n=K]`,
`[measured YYYY-MM-DD]`, `[inferred-static]`, `[reported]`, `[hypothesis]`, or
`[disproved YYYY-MM-DD]`. **`n=1` is not verified** (though a single counter-example is enough to
*disprove* a rule). A finding that arrives untagged is treated as `[hypothesis]`.
Full definitions: `claude-memory/CONVENTIONS.md` → "Claim hygiene".
