# 2026-10-14 (`/lm manhunt-2003-vr`, dev PC): the OpenXR Simulator's window covers the game

With the headset flags in, the simulator opens its preview window at 100,100 (1300x415), over Manhunt's window at
130,130. Screen-copy checkpoints then see the simulator, and Menu-o-matiC reported `frozen` at the main menu and
again at the save list `[verified-live 2026-10-14, n=2]`. Moving the simulator window to the right of the game and
giving the game the focus back, then `run ... --from <step>`, finished the route. A small watcher that does the move
as soon as the window appears made a full replay work first time `[verified-live 2026-10-14, n=2]`.

Also: the game now runs at 1280x720 (registry `Video\Mode` 8, `Index` 7 on the dev PC); the recorded 800x600 route
still replayed to the level with every checkpoint matched `[verified-live 2026-10-14, n=1]`.
