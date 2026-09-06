# /models — what goes here

The game auto-loads a rigged, animated `.glb` for every character from the
exact paths below. Until a file exists at a given path (or if it fails to
load), that character automatically uses a built-in procedural placeholder
mesh instead — so the game always runs, and each character upgrades itself
the moment you drop the matching file in.

Open the browser console (F12) while playing — it prints exactly which
paths loaded and which fell back to the placeholder, e.g.:
`[models] Could not load "models/aliens/bigchill.glb" — using the built-in
placeholder instead.`

## File format

- **`.glb` only** (binary glTF — mesh, skeleton, textures and animations
  all bundled in one file). Not `.gltf` + separate `.bin`/textures.
- Draco mesh compression is supported (decoder is bundled in `libs/draco/`).
  Meshopt and KTX2 texture compression are **not** — export textures as
  plain PNG/JPG embedded in the glb.
- One skinned mesh per character, ALL of its animation clips baked into
  that same file (not separate files per animation).

## Orientation & scale

- Character should face **+Z** at rotation 0. Blender's default glTF
  export axis conversion (+Y up) already does this correctly if you
  modeled the character facing Blender's -Y.
- Roughly **1.7–2 world units tall** for human-sized forms (matches the
  existing placeholder rig). Bigger/smaller forms (Humungousaur, Echo Echo)
  are rescaled automatically by the game — model at whatever natural size
  looks right and the game will scale it.

## Animation clip names

Name clips with plain words like `Idle`, `Walk`, `Punch1` — matching is
case-insensitive and ignores spaces/underscores/`|`, so Mixamo-style names
like `Armature|mixamo.com|Walking` also match `walk` automatically. Full
alias list is in the `CLIP_ALIASES` constant in the HTML if you want to see
exactly what's matched.

| Canonical name | What it's for |
|---|---|
| `idle` | Standing still |
| `walk` | Normal movement |
| `run` | Sprinting (Shift / sprint toggle) |
| `jumpStart` | Leaving the ground (short, non-looping) |
| `jumpLoop` | Airborne / falling (looping) |
| `jumpEnd` | Landing recovery (short, non-looping) |
| `punch1` | Basic attack |
| `punch2` | Power attack |
| `fly` | Airborne flight (flying forms only) |

A character doesn't need every clip — see the table below for which ones
each character type actually uses. Missing clips just don't play; nothing
breaks.

## Exact file paths & which clips each one uses

**Player (Ben Tennyson, human form)**
- `models/player/ben.glb` — idle, walk, run, jumpStart, jumpLoop, jumpEnd, punch1, punch2

**Aliens** (all 9 use every clip — jump is only reachable in non-flying
forms; Stinkfly and Big Chill fly instead, so their jumpStart/Loop/End
clips are optional):
- `models/aliens/heatblast.glb`
- `models/aliens/xlr8.glb`
- `models/aliens/diamondhead.glb`
- `models/aliens/stinkfly.glb` — idle, walk, run, fly, punch1, punch2 (flies — jump unused)
- `models/aliens/upgrade.glb`
- `models/aliens/echoecho.glb`
- `models/aliens/humungousaur.glb`
- `models/aliens/chromastone.glb`
- `models/aliens/bigchill.glb` — idle, walk, run, fly, punch1, punch2 (flies — jump unused)

(heatblast/xlr8/diamondhead/upgrade/echoecho/humungousaur/chromastone: idle,
walk, run, jumpStart, jumpLoop, jumpEnd, punch1, punch2)

**Enemies** (no jump/power-punch concept — only punch1 for melee):
- `models/enemies/thug.glb` — idle, walk, run, punch1 (melee)
- `models/enemies/gunner.glb` — idle, walk, run (ranged, no punch)
- `models/enemies/brute.glb` — idle, walk, run, punch1 (melee)
- `models/enemies/runner.glb` — idle, walk, run, punch1 (melee)
- `models/enemies/drone.glb` — idle, fly (hovers — always uses fly)
- `models/enemies/alienfoe.glb` — idle, walk, run, punch1 (melee)

**NPCs** (idle/walk only — civilians and allies don't fight or jump):
- `models/npc/citizen.glb` — idle, walk, run (run plays when fleeing danger)
- `models/npc/plumber.glb` — idle, run (Task Force allies move at a run when deployed)

**Bosses** are intentionally NOT part of this system yet — they keep their
existing built-in look for now.

## Testing locally

`GLTFLoader` fetches each `.glb` over HTTP(S), and Chrome blocks that kind
of fetch for files opened directly as `file://`. If models silently stay on
the placeholder even though the files are in place, serve the folder with
a tiny local server instead of double-clicking the html, e.g. from this
folder:

```
python3 -m http.server 8000
```

then open `http://localhost:8000/omniverse-ben10-game-3d-phase4.html`.
(Firefox, and most app-shell/WebView packagers, don't have this
restriction — this is only a Chrome-double-click-file:// quirk.)
