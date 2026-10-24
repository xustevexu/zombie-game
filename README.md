# Last Light — survival prototype (v4: building, stats, minimap, axe, jump) — 3D browser game, solo + 2-player co-op
- `index.html`        online build (three.js r160 + PeerJS 1.5.4 from pinned jsDelivr CDN). Solo + co-op.
- `index_with_music.html` same as index.html but with your two tracks (music/ folder) base64-embedded, so it plays music when double-clicked from anywhere (~8 MB).
- `music/` drop-in music folder (`manifest.json` + tracks) for `index.html` when served over http. `game_package.zip` bundles index.html, index_with_music.html, music/ and this README.
- `game_offline.html` fully offline single file, three.js bundled in. Solo only (co-op buttons disabled).
- `three.module.js`, `peerjs.min.js` local vendored copies (used by tests; for self-hosting swap the URLs in build.mjs).
- `src/*.js` readable source (config, world, sim, net, view, ui, main, build, structs, stats, minimap, chars, props, audio) + `src/template.html`. Rebuild: `node build.mjs` (needs esbuild in /workspace/buildtools).
- `tests/` Playwright/Node checks. `shots/` screenshots.

## v7.3 — the open world, the road to Kessler, co-op avatars (Oct 24, 2026)
- **Co-op avatars** are now the Quaternius animated characters (same as the townspeople), with walking, crouching and swinging animations kept in sync over the network.
- **No more hard borders.** The land outside Harlow streams in as 64 m chunks (trees, rocks and bushes from Kenney's Nature Kit, all solid) out to a rocky ridge ~1 km away. Low-tier phones draw 5x5 chunks, Medium/High 7x7.
- **The road out of Harlow** runs ~820 m north to the fenced **Kessler research site**. On the way: a **crashed car with skeletons and a note**, a **police roadblock**, an **overrun camp** (zombies rise when you get close), and a **hidden cabin** in the woods west of the road. Notes at the crash, roadblock and cabin (or asking the mayor about the road) reveal the research-site waypoint; there's no waypoint before that. Reaching the gate plays a short scene — the site itself is the next batch.
- **Road gang:** three raiders guard the road. When they stop you: **pay** ($20, 2 food or 3 scrap), **talk** (better odds as you level), **fight** (they hit hard; each one drops cash, scrap and food) or **flee**. Paying holds for the day.
- **CC0 assets:** Kenney car, suburban, commercial, road, survival and nature kits (the road cars, the roadblock, the gang van, tents, the research-site props and the wild scatter) and Quaternius deer, raccoon, boar and chicken (animated; the boxy chicken is gone; the rabbit became a raccoon since there is no CC0 animated quadruped rabbit). Credits in `assets/CREDITS.md`.
- **Zombies** now path round to an opening, or bash a closed door or your wall when there's no way round.
- Not done (in `next_batch.md`): the town's own buildings, the fort and the town streets are still the existing models (their interiors, loot and colliders are built on them); the minimap only covers the town; the roadside poles and the farm wrecks have no colliders; the inside of the research site.

## v7.2 — real people, sleep, slower days (Oct 24, 2026)
- **Day length is now 7.5 real minutes** (was 3): `DAY_LEN` 450 s. Hunger and thirst drain per real second, so per real minute they are unchanged. Grow-pot stages take 3 real minutes (was 2.5). NPC schedules follow the clock and still line up; horde nights are counted in game days, so they come ~2.5x less often in real time; weekly rent is 7 game days (~52 real minutes).
- **Sleep:** use your bed at night (after ~5 PM) to fade out and wake at 7 AM; time, plants, hunger and NPC schedules move on. Blocked with a horde on, a zombie within 25 m, or in daytime. Co-op: everyone has to be in bed.
- **Townspeople and zombies** are Quaternius Universal Base Characters with the Universal Animation Library (CC0, see `assets/CREDITS.md`): per-job outfits and tools, real walk/work/talk animations; zombies are darker, torn and bloody, shamble and scratch, and play a death animation. LOD: far people animate every 2nd/4th frame; all share 2 meshes + 2 clip libraries. Opening `index.html` from `file://` keeps the old procedural people (browsers block the .glb fetch) — use the hosted page or a local server.
- **Fixes:** Harlow waypoint only after the radio works; no grass inside buildings; doors look like doors; melee needs line of sight (no hitting through walls either way, knockback no longer pushes zombies through walls); the gate eye-slot moves with the gate; no stray blocks on the axe; warm dim flickering night lights; darker matte fridge; pause menu centred and scrollable; thin flush chest straps; packaging counts a portion off when it goes in the bag and back only on Take out / Leave; Gus's locked room only in Gus's rental, the bought house is all yours (with a second bed).
- Not done this round (left in `next_batch.md`): animated CC0 wildlife, CC0 cars, homes, fort buildings, streets and props; zombies pathing round to an opening / bashing doors when blocked; player avatars still procedural.

## v7.0.1 — playtest fixes + town loot (Oct 23, 2026)
- Harlow signpost: the board hangs clear of the post so the arrow shows; the road-sign note is pinned to the post (no floating plank); all sign text (signpost, street signs, shop signs) is sized to fit its board.
- Shop and street signs pick up faint moonlight at night so they stay readable.
- Screen-space AO fades out when your view is right up against a wall, so building corners no longer go black.
- The leaning concrete slabs are gone.
- Hardware basement: the stair opening is clear (no floor over it), the stairwell walls and the underside of the stairs are solid, the bottom end is open into the storeroom, the toppled rack sits against the wall, and walking under the stairs no longer pops you upstairs.
- Hordes spawn outside town (never inside or behind the fort), follow the street corners, stop at the closed fort, and a packed horde can no longer shove zombies through walls.
- Loot: homes are enterable (doorway on the street side) with kitchen cupboards, fridges and closets; town wrecks have trunk + glovebox loot; crates, barrels and dumpsters along the streets and a crate by every garage; yard trees are full-size and choppable; inside the fort only junk piles can be scavenged.
- Pot placement follows the crosshair down to the floor (0.6–3.4 m), so pots can be placed while standing inside the shack.

## v7.0 — Harlow outskirts, a real basement, placeable grow pots (Oct 23, 2026)
- **Shovel & digging**: crafting tells you exactly what you got and where it went (e.g. "Shovel added to your hotbar…"). The shovel gets its own hotbar slot and shows in your hand. With it selected you can dig **anywhere on natural ground** (grass, dirt, yards, fields, forest). Use E ("Dig") or LMB. You can't dig on roads, sidewalks, paved lots, floors, inside the fort or underground. Each dig leaves a small hole, and the same spot needs ~90 s before you can dig it again. The marked soil patches (tilled rows, stone ring, stake) are easy to spot and always diggable. The quest no longer points to a soil patch; it just says to dig on open ground.
- **One Build / Craft / Grow menu**: B and G now open the same menu with tabs. Every item shows its cost against what you carry; anything you can't afford is greyed out and says what you're missing (e.g. "Shovel: 3 wood + 2 scrap. You have 1 wood").
- **Your shack**: the gate guardhouse is rebuilt (thin walls, boarded window, roof, a door you open/close with E, matching colliders). Gus now says it's yours; map label "Your shack".
- **Seeds & soil**: the dispensary plant gives exactly 1 seed, once (no respawn). Harvests yield ~1.2 seeds on average (seed chance upgradable). Dirt patches around town (shack lot, gate verge, park strip, fort planters, yards) — dig with a shovel; nearest soil shows on the map and quest hint.
- **Surroundings**: tree lines, forest, fields, 5 farms (house/barn/shed/fences), side roads, power poles, wrecks, rocks and brush — instanced so Low stays fast.
- **Zombies**: daytime cap-cull no longer touches horde, chasing or basement zombies, and despawns sink away instead of falling over like a kill.


- **Outskirts:** about 40 varied homes along Birch Ln, Elm St, Cedar Ave and Oak Ave. They have 1 or 2 storeys, gable, hip or flat roofs, siding or brick, porches, broken or boarded windows, attached or detached 1- or 2-car garages, fences, mailboxes, yard trees and overgrown lawns. There are also street-name signs, sidewalks and abandoned cars. Everything is built as merged geometry with shared materials, so Low quality stays fast. The camp → gate road stays clear.
- **New storefronts:** a diner, a pharmacy and a gas station. Every shop has a weathered, non-glowing painted sign; the dispensary's has a green cross.
- **Harlow sign:** the arrow now has its own panel on the left, and the back face reads "CAMPSITES". The paint is plain, with no glow.
- **Gate eye-slot:** it scrapes open to show Gus's eyes when you talk to him and shuts when you leave. This is synced in co-op.
- **Hardware basement:** unlock the padlocked door with Gus's key, walk down the stairs into a dark, lantern-lit storeroom with smashed shelves and broken tools, and take the pot and soil from the shelf (E). 1–2 zombies wait down there.
- **Your shack:** Gus now tells you the guardhouse by the gate is yours to grow in. It is a closed building with four walls, a boarded window, a roof and a door that opens and closes with E. The colliders match.
- **Grow pots are placeable** (B → Grow pot). They use the ghost preview (green or red tint, R rotates). They are allowed in your shack, anywhere outside the fort, or by Gus's house or your room inside it.
  - E fills the pot with soil, then plants a seed.
  - The plant shows visible stages: seedling → small → vegetative → flowering → ready.
  - Look at a pot to see its progress label and bar.
  - Water it once a day (E) to grow faster.
  - Harvesting cuts the plant down and gives you buds.
  - Visuals update live for host and guest (fixed: v6.9 showed pots only after Continue). Old v6.9 pots migrate automatically.
- **Quest markers** on the minimap and full map, labels (Hardware, Dispensary, Harlow gate, Your shack), and an on-screen waypoint with distance (**N** toggles it).
- **Ctrl+W safety:** crouch moved to **C** (Z toggles). Left Ctrl only crouches under Keyboard Lock in fullscreen, which the game enters on start. Leaving asks "Leave the game?" and autosaves first. The character screen moved to **K**.
- **Fix:** horde zombies no longer "drop dead" in daytime. The cap cull skips horde and chasing zombies and only removes zombies out of sight (45 m+). Only real kills play the fall; despawns sink away quietly.
- **Walk-in Harlow, no teleports**: the gate doors physically swing open (with a sound) when Gus lets you in ("Gate's open, come on in.") and whenever an allowed player walks up, then close behind you so zombies stay out. No warp, fade or loading anywhere — the E-warp is gone.
- **Gus's house & your room**: a real 2-bedroom interior — openable front door, living room + kitchen, hall, Gus's (locked) bedroom and your room with a bed (sleep = heal + save), a storage chest and space to place pots, a torch or a bench. "Your room" is on the map and becomes the objective after you're let in. Hank's butcher shop and the house for sale are walk-in too; other houses stay closed.
- **Harlow bustles**: 19 townsfolk with names, jobs and a daily routine on the game clock — they leave home in the morning, walk their street lanes to work (butcher chopping, guards patrolling the walls, farmers hoeing, carpenter hammering at the north wall, mechanic under a car, cook and laundry at the fire, water carrier with buckets, trader, medic, preacher, kids playing, Gus at the gate slot), eat lunch together at the cook fire, go home at dusk; at night only the guards are out; during a horde everyone hides. Look at someone to see name + job, E to talk. Far NPCs update less often (phone-friendly). Ambient life: chimney smoke, street lanterns at night, laundry lines, a chicken coop, market stalls.
- **Packaging mini-game**: E at the shack bench opens a bench view — drag (or tap) a bud from the jar into the baggie, let the scale settle, drag along the zip (or tap SEAL); the bag drops onto the pile. Mouse + touch, Esc leaves. "Package all" unlocks after 6 by hand.
- **Discoverability**: the objective line always names the exact action, key and count; context prompts whenever something is possible; one-time tips (shovel, pot, planting, harvest, packaging, rent); the journal has a HOW TO section for every mechanic.
- **Phone HUD**: toasts have their own stacking area, inventory chips are a compact grid, the status warning sits below the inventory — checked at 390×844 and 844×390.

## v6.9 — the town of Harlow (Oct 23, 2026)
- **Harlow**: follow the road north past the ruins to a walled fort block (10 houses per side, a gate, an eye-slot). Gus lives behind the gate.
- **Entry quest (weed tutorial)**: Gus's key → hardware-store basement (pot + soil) → dispensary planter (seeds) → plant in the shack by the gate → harvest → package at the bench → bring Gus 5 baggies.
- **Rent**: you get a room in Gus's house for 5 baggies a week. Miss it and you're locked out (pay 10 to get back in) and left hunting. You can also buy the house for sale ($400).
- **Customers (like Schedule 1)**: 12 named townspeople. Give one a free sample and they're signed up. After that they come to you and buy. The journal (J) lists them.
- **Town life**: Mayor Ruth's warning, a town reputation score, and Hank the butcher (sells food and arrows, buys meat). Kill a townsperson and the whole town turns on you. There are no police.
- **Bow and arrows** (slot 7, quiet), plus crafting for planters, buckets, a shovel, dirt, and compost → good soil (bigger harvests).
- **Roaming hordes** of 50–100 zombies. Harlow goes dark and silent while one passes.
- **Quality of life**:
  - Crouch (Left Ctrl held, Z toggle, CROUCH button on phones; synced in co-op).
  - Captions stay on screen longer the longer they are. Click or press Q to dismiss them, and read old ones in the journal log.
  - Pinned objective.
  - The camera stays under the tent canvas, and the bedroll is now inside the tent.
  - The Harlow sign arrow points north.
  - The game autosaves every 60 s and on Quit, and the title screen has **Continue saved game**.

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
| Interact (scavenge crates, barrels, dumpsters, cars, cupboards, fridges, closets, fort junk piles; board up, doors, chests, beds, pick up axe, car hood/battery, radio, read notes) | E | ACT |
| Crouch (quieter, slower; zombies notice you at half range) | C (hold) or Z (toggle); Left Ctrl only in fullscreen with Keyboard Lock (Chrome) | CROUCH |
| Build / Craft / Grow menu — one menu with tabs (Q / E or click to switch). Craft: bow, arrows, steel axe (near a workbench). Grow: grow pot, planter, bucket, shovel, dig dirt, compost. Each item shows its cost and what you have; greyed items list what's missing | B (G opens it on the Craft tab; 1-9 pick) | BUILD/CRAFT or CRAFT |
| Bow (quiet; needs arrows) | 7, LMB | slot 7, HIT |
| Dig dirt (shovel selected in the hotbar, on grass / dirt) | E or LMB | ACT / HIT |
| Talk to townspeople, knock at the Harlow gate, grow (pots), package (bench) | E (1-9 pick a dialogue option) | ACT |
| Journal (objective, quest, customers, grow slots, caption log); dismiss newest caption | J; Q or click | JOURNAL; tap caption |
| Eat / bandage | F / H | EAT / HEAL |
| Build menu | B (1-9, 0 pick a piece; Tab / T wood<->scrap; R rotate; LMB place; RMB or Esc cancel; X dismantle) | BUILD, then tap pieces + PLACE / ROTATE / CANCEL |
| Character screen | K | STATS |
| Quest waypoint on/off | N | — |
| Road gang: Pay / Talk / Fight / Flee (opens when they stop you) | E, then 1-4 | ACT, then tap |
| Open/close your shack door or a house front door, use a pot (soil, seed, water, harvest), sleep in your bed (night only: skips to 7 AM) | E | USE |
| Packaging bench: drag bud → baggie, drag zip / SEAL, Take out (returns the portion); leave | mouse drag, Space fills/seals; Esc or E | drag / tap, SEAL, Leave |
| Place a grow pot (the ghost follows the crosshair to the floor; look down to place it as close as ~0.6 m) | B → Grow tab → Grow pot (R rotates, LMB places) | BUILD/CRAFT, then PLACE |
| Minimap rotate / north-up; full map | M tap; hold M or Tab | tap minimap; press and hold it |
| Pause (solo freezes time; co-op does not; autosaves) | Esc | Pause |

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

## v6.6 – v6.8

**v6.6:** High-tier dark "slabs" on phones are fixed. They came from the GTAO ambient-occlusion depth pre-pass, which drew transparent and alpha-card geometry (foliage, mist, glass, sky) as if it were solid. That pre-pass now skips non-opaque materials. The portrait phone HUD was also re-laid out so the clock, stats, buttons and objective box no longer overlap.

**v6.7 (models):** I looked for realistic rigged CC0 or CC-BY zombies and first-person arms.
- Quaternius zombies on poly.pizza are CC0 and animated, but too cartoony, so they were not used.
- Quaternius Universal Base Characters and the Universal Animation Library (CC0, on itch.io) need a browser download.
- Mixamo and Sketchfab need an account.

What shipped is improved procedural models:
- Zombies get mottled skin, torn clothing with alpha cut-outs, lathe-shaped limbs, ribs, a hinged jaw with teeth, and clawed hands.
- The first-person view gets gloved hands with knuckles, sleeves and cuffs.
- Low stays light.

**v6.8 (new opening):** You wake inside your tent on a camping trip, and something is very wrong.
- There is one house nearby, with a broken radio on its table.
- The nearby car has a battery: open the hood [E], take the battery [E], then repair the radio [E].
- The broadcast points you to the fortified town of **Harlow**, which is then marked on the map. A road sign at the edge of the woods points the way.
- Notes and overgrowth hint that far more than one night has passed.
- There is no more "survive 3 nights" win and no horde nights. Wandering zombies and the day/night cycle remain.
- In co-op either player can do any step, and the state is synced.
- Dead trees now have round, tapered limbs attached to the trunk.
- Pines and firs have denser foliage.
- Opened crates and barrels are hollow, with dark interiors, rims, and the lid swung open or set aside.
