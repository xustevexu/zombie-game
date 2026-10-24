# Last Light — survival prototype (v4: building, stats, minimap, axe, jump) — 3D browser game, solo + 2-player co-op
- `index.html`        online build (three.js r160 + PeerJS 1.5.4 from pinned jsDelivr CDN). Solo + co-op.
- `index_with_music.html` same as index.html but with your two tracks (music/ folder) base64-embedded, so it plays music when double-clicked from anywhere (~8 MB).
- `music/` drop-in music folder (`manifest.json` + tracks) for `index.html` when served over http. `game_package.zip` bundles index.html, index_with_music.html, music/ and this README.
- `game_offline.html` fully offline single file, three.js bundled in. Solo only (co-op buttons disabled).
- `three.module.js`, `peerjs.min.js` local vendored copies (used by tests; for self-hosting swap the URLs in build.mjs).
- `src/*.js` readable source (config, world, sim, net, view, ui, main, build, structs, stats, minimap, chars, props, audio) + `src/template.html`. Rebuild: `node build.mjs` (needs esbuild in /workspace/buildtools).
- `tests/` Playwright/Node checks. `shots/` screenshots.

## v6.5 graphics overhaul (Oct 23, 2026)
Realistic "7 Days to Die"-style look on Medium and High; Low is unchanged and stays light for phones. Gameplay, colliders and co-op are unchanged.

| Tier | What it includes |
|---|---|
| Low | Original procedural look. No photo assets are downloaded, no post-processing, no shadows, standard fog. |
| Medium | 1K photoscanned PBR textures (ground, bark, planks, brick, concrete, rock, rusty metal), image-based lighting from a 1K HDRI, height fog, half-res bloom plus desaturated grading, denser wind-swayed grass with colour variation, ground scatter ×1 (scanned stones, branches, swaying ferns), scanned rocks and stumps, alpha-card pine, fir and oak foliage, 2048 shadow map. |
| High | Everything in Medium with 2K textures and HDRI, the HDRI also blended into the sky, GTAO ambient occlusion, a 4096 shadow map, scatter ×2, denser and longer grass, and a higher pixel ratio. |

Depth of field is off. There are no cascaded shadows (higher-res, texel-snapped maps only), and there are no scanned trees or skinned characters, because the CC0 options were millions of triangles or had no rig.

**Assets:** load from `assets/` when the game is served over http(s). They fall back to the Poly Haven CDN, for example when the game is opened from file://.

**Bug fixes:**
- Hitting one animal no longer flashes every animal of that kind, because each animal now has its own materials.
- The first piece of meat no longer cooks too fast: frame catch-up time is capped at 0.25 s, so a loading stall can't advance cooking.
- Props no longer spawn inside each other. Spawning is deterministic with overlap rejection (0.35 m margin) for trees, rocks, crates, barrels, cars, ruins and animals; placements that overlap are re-rolled or skipped. Covered by `tests/v65_overlap.mjs` across 200 seeds.

### Asset licenses
All of these are CC0 (public domain) from Poly Haven. No attribution is required, but here it is anyway:

| Asset | Used for | Source |
|---|---|---|
| sparse_grass | grass ground | https://polyhaven.com/a/sparse_grass |
| forest_leaves_02 | forest floor | https://polyhaven.com/a/forest_leaves_02 |
| brown_mud_02 | dirt paths | https://polyhaven.com/a/brown_mud_02 |
| bark_brown_02 | tree bark | https://polyhaven.com/a/bark_brown_02 |
| weathered_planks | wood | https://polyhaven.com/a/weathered_planks |
| red_brick_03 | brick ruins | https://polyhaven.com/a/red_brick_03 |
| concrete_wall_008 | concrete | https://polyhaven.com/a/concrete_wall_008 |
| aerial_rocks_02 | rock texture | https://polyhaven.com/a/aerial_rocks_02 |
| rusty_metal_02 | cars and barrels | https://polyhaven.com/a/rusty_metal_02 |
| rock_07 | rock model | https://polyhaven.com/a/rock_07 |
| rock_09 | rock model | https://polyhaven.com/a/rock_09 |
| namaqualand_stones_01 | stone scatter | https://polyhaven.com/a/namaqualand_stones_01 |
| dry_branches_medium_01 | branch scatter | https://polyhaven.com/a/dry_branches_medium_01 |
| fern_02 | ferns | https://polyhaven.com/a/fern_02 |
| tree_stump_01 | stumps | https://polyhaven.com/a/tree_stump_01 |
| farmland_overcast | HDRI sky and lighting | https://polyhaven.com/a/farmland_overcast |

## v6 update (Oct 23, 2026)
- **Cars:** three detailed variants (sedan, pickup, SUV), with bevelled extruded bodies, wheel arches, tyres with rims (some flat or missing), cracked see-through glass, head and tail lights, bumpers, mirrors and door seams. Procedural faded paint with rust and dirt (normal and roughness maps on Medium and High). Some cars have an open hood. Geometry and materials are shared. The stepped collision matches each body shape.
- **Wildlife:** deer (flee), rabbits (fast), and a boar that charges and hits for 8. Animals graze, wander and flee, and they have animated legs and sounds. Kill them with the axe for raw meat (deer 3, boar 2, rabbit 1) and hide (cloth). Zombies chase and eat animals. There are at most 6 animals. They are host-authoritative and synced in co-op, and they appear on the minimap only with Perception 1+.
- **Cooking:** stand at the tent fire pit or a built campfire and press E with raw meat. Each piece takes 3 s, with a sizzle sound, and you have to stay near the fire. Raw meat gives +18 hunger and a 30% chance of mild sickness (−0.5 HP/s for 20 s). Cooked meat gives +50 hunger and +12 HP. Both go to the hotbar. Hunting and cooking give XP.
- **Head collision:** you can no longer jump through ruin ceilings or built roofs (head height 1.8 m).
- **Swing stamina:** each swing costs about 3.5 stamina (2 bare-handed), less with the Stamina stat. When you are tired, swings are 1.4× slower and do 60% damage, but they are never blocked.
- No external assets were added in v6. Everything is procedural, so there are no new licenses.

## v5 update (Oct 23, 2026)
- **Hotbar** (bottom centre, 9 slots, drawn icons, stack counts). Select with 1–9, mouse wheel, or tap on phones. Slot 1 = axe; an empty slot = bare hands. Slots auto-fill with axe, food, bandages and any build piece you pick in the build menu (selecting a piece slot re-enters build mode). Left click uses the held item (swing / eat / bandage). Held item is shown in first person and on your avatar, and synced in co-op.
- **Cars are climbable**: stepped colliders bumper (~0.56 m) → hood/trunk (~1.0 m) → roof (~1.5 m). Jump up step by step; zombies cannot climb.
- **Fix: looted crates/barrels/chests stayed walk-through.** Root cause: `obstacles()` skipped any loot entry that was no longer "alive", so scavenging removed the collider. Now only felled trees change (to stumps); every scavengeable prop stays solid and standable, host and co-op guest (test: `tests/collide_audit.mjs`, `tests/v5_solo.py`, `tests/v5_coop.py`).
- Objective text tells you to grab the axe by the campfire when you start without one.
- Co-op: guest jumps are smoothed so the host sees the full jump while Space is held.
- **Wider pause & main menu** (~940 px / 80vw): controls in a grouped grid (Movement, Combat & survival, Hotbar, Building, Menus & map) with keycaps; Resume, volume, music, graphics and Quit in a toolbar at the top; no scrolling at 1080p. Phones get one scrollable column showing the touch buttons.

## Graphics upgrade (v2)

Everything is procedural: no downloaded art, models or textures. Textures are generated on a canvas at load time
(`src/texgen.js`); models are composed from geometry (`src/props.js`, `src/chars.js`); the sky is a shader (`src/sky.js`).
The only third-party code besides three.js itself is the MIT-licensed three.js addons vendored in `src/vendor/jsm/`
(EffectComposer, RenderPass, ShaderPass, UnrealBloomPass, OutputPass, BufferGeometryUtils). Credit: three.js authors (MIT).

### Quality setting
Menu -> Graphics: Auto / Low / Medium / High (also `?q=low|medium|high|auto`, remembered in localStorage).
- Low: Lambert materials, no shadows, no post-processing, 128px textures, sparse grass. Auto picks this on touch devices.
- Medium: PBR materials with normal/roughness maps, sun shadows, image-based lighting, clouds, rain, fireflies, more grass.
- High: adds 512px textures, 2048 shadow map, lantern spot-light shadows, MSAA post stack (bloom, vignette, film grain,
  chromatic aberration), mist, lamp lights, dense grass/rain. Auto picks this on desktop.
In Auto, the game steps down one tier if FPS stays under ~22 for 5 s. An explicit choice disables that.

### Other options
`?rain=0|1` forces weather off/on (testing). Rain is deterministic from game time, so both co-op players see the same sky.

### Notes / limitations
- Not measured on a real GPU or Android hardware (only software rendering was available).
- No SSAO or true volumetric god-rays (bloom, sky glow and light-shaft cones are used instead).
- Co-op timeouts were raised (host 20 s, client 20 s) so slow frames do not drop the link.
- Co-op still needs the public PeerJS broker; some networks need a TURN relay.

## What changed in this update

### Starvation bug (root cause + fix)
Hunger drains at 0.24/s from 80 (reaches 0 after ~333 s). At 0 hunger, health drained at 0.8 HP/s. The game had no auto-eat:
food was only eaten by pressing **F**, and the HUD gave no hint (the "Hungry!" objective only appeared when you held *no* food).
So a player with 7 food who never pressed F starved ~125 s later (reproduced headless: death at t = 458 s with 7 food).
Fix: you now **auto-eat** from your pack when hunger <= 15; the HUD shows a status line plus pulsing bars:
"Hungry — eating automatically" / "STARVING — losing health! (press F to eat)" / "Badly hurt — press H to bandage" / "Exhausted".

### Controls (also in-game: main menu and Esc pause screen, PC and phone columns)
| Action | PC | Phone |
|---|---|---|
| Move / sprint | WASD, Shift | left joystick (push to the edge) |
| Jump | Space | JUMP |
| Swing / chop | LMB (hold) | HIT |
| Interact (scavenge, board up, doors, chests, beds, pick up axe) | E | ACT |
| Steel axe (near a workbench) | G | AXE |
| Eat / bandage | F / H | EAT / HEAL |
| Build menu | B (1-9, 0 pick a piece; Tab / T wood<->scrap; R rotate; LMB place; RMB or Esc cancel; X dismantle) | BUILD, then tap pieces + PLACE / ROTATE / CANCEL |
| Character screen | C | STATS |
| Minimap rotate / north-up; full map | M tap; hold M or Tab | tap minimap; press and hold it |
| Pause (solo freezes time; co-op does not) | Esc | Pause |

### v4 changes
**Collision bug (walking through the leaning slab).** Root cause: the slab prop had a collider for its *upright* footprint only, so the tilted
part that sticks out beside the ruin wall was not solid. It now has an oriented box collider that follows the lean, and the leaning
corrugated sheet / crate stack colliders use the same box fix. **Prop audit:** `tests/collide_audit.mjs` walks every prop, wall, tree, rock,
vehicle and piece of junk in the generated world, steps a probe around it and checks it is solid at body height (1088 solids, 0 failures).

**Jump (Space).** Peak about 1.17 m, costs 9 stamina, can be used mid-sprint, you keep air control. Anything lower than 0.4 m you simply walk over;
tables / crates / low walls up to ~1.1 m you hop onto and stand on. Landing thud, no footsteps in the air, guest jumps are synced (host sees height).

**Axe replaces the bat.** First-person and third-person axe model, a whoosh on each swing, a heavy "thunk" when it bites a tree and a meatier
hit sound on zombies. Config flag `START_WITH_AXE` in `src/config.js` (default **true**). Set it to `false` and you start with fists (weak, cannot chop
well) and the axe lies by the tent / campfire; press E to pick it up. The hatchet craft is gone: **G near a workbench forges the steel axe** instead
(3 wood + 4 scrap, more damage, trees fall in 2 swings). Trees take about 3 axe swings.

**Death animation (sinking into the ground).** Root cause: the corpse rotated around the world X axis (not the body's facing) and its height was
pushed to y = -0.12, so zombies and player avatars ended half under the terrain. Bodies now topple backwards (zombies) or forwards (players) about their
feet in the facing direction, are lifted by half their thickness so no limb goes below the ground, relax their limbs, settle with a tiny bounce, lie
for a few seconds, then fade. Same code for remote friend avatars.

**Build system (B).** Pieces: wall, door, window wall, barricade, floor, roof, campfire, storage chest, workbench, torch, spike trap (needs a workbench
nearby), sleeping bag and bed (set your respawn). Wall / door / window / floor / roof come in **wood** and **scrap** tiers (scrap needs a workbench
nearby, T or Tab switches tier). A translucent ghost shows green when valid and red when not, snaps to a 2 m grid (walls snap to cell edges), R rotates, LMB places.
In co-op the host validates every placement and owns all pieces; pieces are in the snapshot. Zombies that cannot reach you attack the nearest piece;
E on a damaged piece repairs it; X in build mode dismantles (half refund). Hold-to-build on phones uses on-screen PLACE / ROTATE / CANCEL buttons.

**XP, levels, stat points (C).** XP from zombie kills, felling trees, scavenging, crafting, building and surviving a night. Level cap 30, 1 stat point per level,
rank cap 10 per stat. Strength (melee damage, chopping speed), Endurance (max health / stamina, recovery), Agility (speed, swing speed), Fortitude (slower hunger),
Perception (zombie proximity warning under the HUD, extra loot chance), Crafting (cheaper building). HUD XP bar, level-up toast + chime. Each player has
their own XP and stats, awarded and spent on the host.

**Minimap.** Upper right (160 px, 108 px on phones). Rotates with you by default; M toggles north-up; hold M or Tab for the full explored map. Shows you (arrow),
your partner, the tent, ruins (red = unsealed, green = sealed), faint trees / rocks, fog of war that clears as you explore, and nearby zombies as dots only
if you have Perception points. Compass N on the rim.

**Clock** is 12-hour ("10:50 AM"). Tutorial and menu text say axe instead of bat.

### Swing, chopping
Swing is a wind-up / strike / recover animation. Damage lands at 0.2 s into the 0.5 s swing.
Trees take **3 axe hits** (2 with the steel axe), shake per hit, fall (animation + sound) and drop logs you walk over
to collect wood. The host is authoritative: trees, hit counts, falls and drops are in the snapshot, so co-op partners see the same.

### Sound
All sound effects are procedural WebAudio (no files): footsteps by surface, swings, hits, chops, tree fall, pickups, eating,
heartbeat when hurt, zombie groans / steps (positional), fire crackle, rain, night crickets, day birds, night sting. The audio
context starts on the first click (browser rule). Master volume + mute in the menu and pause screen. In co-op, game events
(`ev` list in the snapshot) are replayed on the guest so both hear the same hits, groans and falls.

## Music system

Music is optional. Without any files the game plays a very quiet procedural pad, or silence if you turn the pad off.
Music volume is a separate slider (menu and pause), remembered as `ll_mvol`. Pad on/off is `localStorage.ll_pad` ("0" = off).

### States
`menu`, `day` (exploring), `night`, `combat` (a chasing zombie within 16 m or any within 6 m, outdoors; 5 s hold so it does
not flicker), `shelter` (inside a building), `death` and `win` (one-shot stingers), plus an optional `tension` layer
(loop that fades in as zombies get closer at night/day and is kept in sync with the base track).
Fallback chain when a state has no file: menu -> day; night -> day; combat -> night -> day; shelter -> night -> day.
`death` / `win` have no fallback (silence). When a state borrows another state's music the game shapes it so it still feels
different: night = low-passed + quieter, shelter = slightly muffled, combat = quieter. Borrowing keeps the same track playing
(no restart) when going day -> night. Loud events (zombie hit/attack, you getting hurt, tree falls, breaking boards) briefly
**duck** the music to ~40 %.

### Your tracks (current default)
`music/track1.m4a` and `music/track2.m4a` (your files converted to AAC-LC 128 kbps for Safari/iPhone support) are set in
`music/manifest.json` as the `day` playlist; menu, night, combat and shelter use them via the fallback chain.
With 2+ files in a state and no loop points the tracks play as a **playlist**: track 1 crossfades (6 s) into track 2 before it ends,
and back again, forever.

### Where to put files
Next to `index.html`:
```
index.html
music/
  manifest.json
  track1.m4a
  track2.m4a
  night.ogg        (any extra files you add)
```
Formats: whatever the browser decodes (mp3, ogg, wav, m4a/aac; Safari has no ogg).

### manifest.json
Every key is optional. A state's value can be a file name, a list of names, or objects `{file,start,end,gain}`.
```json
{
  "crossfade": 6,
  "menu":   "menu.mp3",
  "day":    ["track1.m4a", "track2.m4a"],
  "night":  ["night_a.ogg", "night_b.ogg"],
  "combat": {"file": "combat.mp3", "gain": 0.9},
  "shelter": "safe.mp3",
  "death":  "death.mp3",
  "win":    "win.mp3",
  "tension": "tension_loop.ogg",
  "loops": { "night_a.ogg": {"start": 4.0, "end": 96.5} },
  "gain":  { "track1.m4a": 0.85 }
}
```
- `loops[file].start/end` (seconds): the track loops between these points forever (one file per state). Without loop points a single file loops whole.
- `gain` per file 0..1 (relative level). `crossfade` seconds used for state changes and playlist hand-over.
- If you name files in the manifest-less way, the folder is not scanned (browsers cannot list folders) — list files in the manifest.

### Test your own music without editing files
Menu -> Music: pick a state in the dropdown and **Choose files**, or drag-and-drop audio onto the drop zone. A file whose
name contains `menu|day|night|combat|shelter|death|win|tension` is auto-assigned to that state when you drop several files.
Local files replace that state's manifest files for this session (they are not uploaded anywhere); **Clear** removes them.

### file:// limitation
Browsers do not allow `fetch()` of `music/manifest.json` from a page opened by double-click (`file://`). Options:
(a) serve the folder (`python3 -m http.server`, then open http://localhost:8000/), (b) use the menu picker/drag-drop,
(c) use **`index_with_music.html`**, which has manifest + tracks embedded (rebuild after changing music/: `node build.mjs`).
Embedded data takes priority over the music/ folder.

### Not verified
Real audio device / how it sounds, real GPU, phones; mixing levels (night low-pass, ducking) are judged only from node
values in headless tests. Co-op was tested on Low only.
