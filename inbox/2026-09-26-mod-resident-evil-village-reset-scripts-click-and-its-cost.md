# inbox: RE Village — reloading REFramework's Lua from outside, and why not to

From the modding lane, 2026-09-26 (home PC, flat, 1920×1440 window). For `profiles/resident-evil-village.json`.

## The click path (client coordinates, overlay open)

1. `Insert` opens the REFramework overlay.
2. Click the **`ScriptRunner`** header at **(146, 658)** — it is COLLAPSED in every fresh process, and stays open once clicked.
3. Click **`Reset scripts`** at **(224, 691)**.
4. `Insert` closes the overlay. `[re8-camprobe] loaded` (or any script's "loaded" line) in the framework log says the reload landed;
   `autostart: DONE` follows when the scope rig has rebuilt (~1 s after the rifle is seen).

Verified four times 2026-09-26 `[verified-live, n=4]`. Clicking (224, 691) with the header still collapsed hits nothing and the
scripts do not reload — the log stays silent, which is the tell.

## The cost, for the scope project specifically

A reset rebuilds the scope rig before the mirror-layer hooks are re-armed, so every tool that walks the render layers reports
"no parent"; a `rerig` re-captures them, but the second `rerig` in a process re-uses the `.rtex` texture, the plugin's catcher
stays PENDING, and the glass freezes on a dead texture. **Prefer a relaunch (about 3 minutes to gameplay) to a reset** whenever the
experiment touches the rig's layers. Words that only touch our own objects (`camlua`, `cammake`) survive a reset fine.
