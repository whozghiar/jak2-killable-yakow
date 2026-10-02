# Mod Readme — Yakow Killable & Behaviors (Jak 2)

> - **Game:** Jak 2
> - **Branch:** `jak2/features/yakow_killable`
> - **Target Entity:** `yakow` (`goal_src/jak2/levels/city/farm/yakow.gc`)

---

## 1. Description & Features

This mod enhances the Yakow animals located at the Hip Hog farm in Jak 2 by bringing back authentic Jak 1-style behaviors and adding a full combat/death loop.

- **Jak 1-Style Behaviors:**
  - **Flee Mechanic (`run-away`):** When approached or attacked by Jak, Yakows turn and flee in the opposite direction.
  - **Graze System (`graze` / `graze-kicked`):** Yakows alternate between idle grazing and active walking, with a dedicated reaction when kicked mid-graze.
  - **Kick Reaction (`kicked`):** Authentic animation selection between the traveling kick (`yakow-kicked-ja`) and the stationary kick (`yakow-kicked-in-place-ja`), depending on the Yakow's movement vector at the moment of impact.
- **Combat, Alert & Death Mechanics (`die`):**
  - **Krimzon Guard Alert Trigger ("Hands off the cow!"):** Striking a Yakow is treated as a crime and immediately sets the Krimzon Guard alert to Level 1 via `(send-event *traffic-manager* 'set-alert-level 1)`, summoning nearby guards to protect the farm.
  - Yakows now have 3 hit points (`default-hit-points = 3`) instead of being invulnerable, and `damage-amount-from-attack` returns 1 so every attack registers.
  - Upon death, the Yakow drops **6 dark eco pills** dispersed in a ~1.5 m radius around its position.
  - Plays the classic `"yakow-die"` cry (via the base `enemy` `dying` hook), then dissolves using the engine's generic `death-default` effect: **purple particles tracing the mesh/skeleton outline** as it fades out, layered with the `"enemy-fizz"` sound baked into that effect — the exact same system used by civilians and Crimson Guards. See the dedicated engine tip: [`jak2_lisp_instructions.md`](../jak2_lisp_instructions.md).

## 2. Technical Architecture & Tooling

- **Modified / Created Files:**
  - `goal_src/jak2/levels/city/farm/yakow.gc`: Added `run-away`, `graze`, `graze-kicked`, `kicked`, `die` states; new tracking fields (`grazing`, `walk-run-blend`, `walk-turn-blend`, `run-mode`, `home-base`); overrode `damage-amount-from-attack` and `general-event-handler`. Every behaviour change is gated on `*mod-yakow-killable-enable*`.
  - `goal_src/jak2/pc/debug/yakow-killable-menu.gc` *(new)*: defines the `*mod-yakow-killable-enable*` flag and registers `Debug ▸ Mods ▸ yakow-killable`. Resident in GAME.CGO (see the Modding Changes Log for why it can't live in the farm DGO).
  - `goal_src/jak2/dgos/game.gd`: adds `yakow-killable-menu.o` right after `mods-menu.o`.
  - `decompiler/config/jak2/ntsc_v1/{joint-node-info.min.json, art-group-info.min.json, type_casts.jsonc}`: Skeleton/joint bindings and type-cast hints for the `yakow` skeleton and its `code`/`method` overrides, required for the decompiler to resolve the animation-driving code cleanly.
  - *No custom external 3D models required:* Uses native in-game Jak 2 models, animations, and sound effects.
- **Reused Engine Systems (no new engine code needed):**
  - `nav-enemy` / `enemy` base states and event dispatch (`goal_src/jak2/engine/nav/nav-enemy.gc`, `goal_src/jak2/engine/ai/enemy.gc`) — hit-point handling, `dying`, death-flag bookkeeping.
  - The generic merc death-dissolve effect (`goal_src/jak2/engine/gfx/foreground/merc/merc-death.gc`, `goal_src/jak2/engine/game/effect-control.gc`) via `(do-effect (-> self skel effect) 'death-default 0.0 -1)`.
  - `birth-pickup-at-point` for the dark eco pill drops (standard pickup-spawn helper, no custom pickup logic).

## 3. How to Test & Play

1. Set the active game to Jak 2:
   ```bash
   task set-game-jak2
   ```
2. Hot-recompile in the REPL:
   ```lisp
   (mi)
   ```
   Or in batch mode: `./goalc.exe --game jak2 -c "(mi)"` (must report `Successfully built all N targets`).
3. Boot the game and travel to the Hip Hog Farm in Haven City:
   ```bash
   task boot-game
   ```
4. **Enable the mod** (OFF by default — mandatory non-regression rule): open the
   debug menu and go to **`Debug ▸ Mods ▸ yakow-killable ▸ Enable`**. With it off,
   Yakows are the stock invulnerable farm animals. The choice sticks even as the
   farm level streams in and out (`define-perm`).
5. Attack a Yakow with melee punches/spins or weapons to observe:
   - The kick animation and fleeing behavior on non-lethal hits.
   - The immediate **Krimzon Guard Alert Level 1** trigger upon striking the animal.
   - On the killing blow (3rd hit): the `"yakow-die"` cry, the purple mesh-dissolve particle effect with its "fizz" sound, and 6 dark eco pills scattering around the corpse's former position.

## 4. Demonstration Video

[![Demonstration Video](https://img.youtube.com/vi/njKxjCuEpcU/maxresdefault.jpg)](https://youtu.be/njKxjCuEpcU)

▶️ **[Watch the demonstration video on YouTube](https://youtu.be/njKxjCuEpcU)**

## 5. Current Status & Investigations

- **Stable / working as intended:** flee, graze, kick reactions, Krimzon Guard alert trigger ("Hands off the cow!"), HP-based death, dark eco pill drops, and the purple death-dissolve VFX + classic cry.
- **Not yet investigated:** whether killed Yakows should respawn on level re-entry/task reset like other farm entities, and whether repeated kills should be capped per play session (no reward-farming guard is currently in place beyond the natural respawn rules inherited from `nav-enemy`/entity persistence).
- **Tip discovered and now documented separately** (previously undocumented in this repo): the generic `death-default` purple particle system is available to *any* skeleton-having `process-drawable` for free via `do-effect` — see [`jak2_lisp_instructions.md`](../jak2_lisp_instructions.md) for the full mechanism, code pattern, and pitfalls (in particular: always `suspend-for` before `cleanup-for-death`, or the particles never get a chance to spawn).

## 6. Modding Changes Log

| Date | Touched/Created Files | Technical Description | Objective |
|------|-----------------------|-----------------------|-----------|
| 2025-07-19 | `goal_src/jak2/levels/city/farm/yakow.gc` | Added Jak 1-style states (`run-away`, `graze`, `graze-kicked`, `die`), added tracking fields (`grazing`, `walk-run-blend`, `run-mode`, `home-base`), set `damage-amount-from-attack` to 1. | Recreate Jak 1 Yakow behaviors in Jak 2 with dark eco drop. |
| 2026-07-27 | `goal_src/jak2/levels/city/farm/yakow.gc` | Triggered the Krimzon Guard alert (`set-alert-level 1` via `*traffic-manager*`) on an incoming attack, calibrated hit points to 3 HP. | Punish the player for attacking the herd ("Hands off the cow!"). |
| 2026-08-13 | `goal_src/jak2/levels/city/farm/yakow.gc` | Polished `kicked` state (traveling vs in-place kick based on nav travel), raised HP to 4 hits, drop 6 dark eco pills, replaced particle effect with `group-land-poof-drt`. | Authentic Jak 1 feel, robust death VFX and balanced reward. |
| 2026-08-16 | `goal_src/jak2/levels/city/farm/yakow.gc`, `docs/modding/jak2_lisp_instructions.md`, `docs/modding/current_mod/yakow_killable_readme.md` | Replaced the placeholder dust poof (`group-land-poof-drt` + manual `"enemy-fizz"`) in the `die` state's `:code` with `(do-effect (-> self skel effect) 'death-default 0.0 -1)`, the generic engine death-dissolve effect (purple mesh/skeleton-outline particles), followed by a 1s `suspend-for` so it can play out before cleanup. The classic `"yakow-die"` sound was already playing via `(dying self)` in `:enter` and is unaffected. Documented the underlying generic death-effect engine system as a standalone modding tip (kept isolated, the aggregated `jak2_modding_utilities.md` was intentionally left untouched). Relocated this readme from the legacy `docs/mods/` path to the mandated `docs/modding/current_mod/` path and made it fully bilingual. | Give the Yakow the same authentic death VFX used by other Jak II enemies instead of a generic landing-dust placeholder, and bring the mod's documentation into compliance with the modding directive. |
| 2026-09-08 | `goal_src/jak2/pc/debug/yakow-killable-menu.gc` (new)<br>`goal_src/jak2/dgos/game.gd`<br>`goal_src/jak2/levels/city/farm/yakow.gc` | **Runtime on/off via the unified Debug ▸ Mods tab (mandatory procedure).** Added `*mod-yakow-killable-enable*` (`define-perm`, `#f` default, survives farm level reloads). New resident **GAME.CGO** file `yakow-killable-menu.gc` registers `(mods-menu-register "yakow-killable" …)` and re-declares the flag — it lives in GAME.CGO, not the CFA/CFB farm DGO, because the mods-menu registry never unregisters and a builder compiled into a streamed level would dangle when the farm unloads. `.o` wired into `game.gd` after `mods-menu.o`. Every behavioural delta in `yakow.gc` is now gated on the flag: `damage-amount-from-attack` (→ 0 = stock invulnerable when off), the `'attack` / `'hit-knocked` / `'hit` branches of `general-event-handler` (stock path when off), the `run-away` triggers in `idle`/`active`/`graze` `:post`/`:trans`, the `kicked` state body (stock in-place kick → `active` when off) and its `graze-kicked` redirect, and the `die` override (only its *additions* — the pill drop, default-pickup suppression and purple VFX — are gated; the structural teardown always runs, so a scripted/forced kill of an otherwise-invulnerable yakow still despawns it cleanly with no special drops). Also stripped the leftover `(format #t "yakow: …")` console spam. The `:default-hit-points 4` and `:run-acceleration/​run-turning-acceleration (meters 3)` static bumps are left as-is: both are unobservable while disabled (invulnerable yakow never loses HP; `run-acceleration` is only read by `nav-enemy-method-166`, called solely from the gated `run-away` state). | Comply with CLAUDE.md golden rules #2/#3: mod OFF by default, switchable at runtime from Debug ▸ Mods, without editing `default-menu*.gc`. |
