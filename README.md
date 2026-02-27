# Plants vs. Zombies Clone

A web-based Plants vs. Zombies clone built as a single HTML file using vanilla JavaScript and HTML5 Canvas. No build tools, no dependencies, no external assets — all graphics are drawn with the Canvas API or emoji.

## Running the Game

Open `pvz.html` directly in a browser. No build step or server required.

For live reload during development:

```bash
npx serve .
```

## Plants

| Plant | Cost | Description |
| --- | --- | --- |
| Sunflower | 50 | Produces suns every 20s |
| Peashooter | 100 | Fires peas at zombies in the same row |
| Wall-nut | 50 | High-health blocker with 3-stage damage visuals |
| Cherry Bomb | 150 | 3×3 AoE explosion after a 1s fuse |
| Snow Pea | 175 | Fires slowing projectiles |

## Zombies

| Zombie | HP | Speed |
| --- | --- | --- |
| Basic Zombie | 200 | 20px/s |
| Conehead Zombie | 380 (cone absorbs 180) | 20px/s |

## Canvas Layout

```text
┌─────────────────────────────────────────┐
│  Top HUD (sun counter, wave progress)   │
├──────┬──────────────────────────────────┤
│ Seed │  5×9 Grid (80×100px per cell)    │
│ Card │  Columns 0-8, Rows 0-4           │
│ Bar  │                                   │
│(120px│                                   │
│wide) │                                   │
└──────┴──────────────────────────────────┘
Total canvas: 900×600px
```

## Architecture

Single-file OOP design (`pvz.html`). Key classes:

- **Game** — global state, main loop (`requestAnimationFrame`), input routing
- **Grid** — 5×9 grid, pixel↔cell coordinate mapping
- **SunManager / Sun** — sun spawning, fall animation, click-to-collect Bézier fly
- **SeedPacket** — card UI with cooldown overlay and affordability dimming
- **PlantFactory / PlantManager** — factory pattern for plant creation and lifecycle
- **ProjectileManager** — object pool pattern for bullet reuse
- **ZombieManager / WaveManager** — JSON-driven wave spawning, win/lose conditions
- **ParticleSystem** — configurable particles for visual effects
- **SoundManager** — Web Audio API synthesis, no audio files

## Development Phases

1. **Phase 1** — Canvas, grid, game loop, state management
2. **Phase 2** — Sun system, seed card bar, Sunflower
3. **Phase 3** — Peashooter, projectiles, zombies, wave system
4. **Phase 4** — Wall-nut, Cherry Bomb, Snow Pea; UI polish; sound effects
5. **Phase 5** *(optional)* — Multi-level system, mobile touch support

Versioned backups: `pvz-v1.html` through `pvz-v4.html`
