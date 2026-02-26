# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A web-based Plants vs. Zombies clone built as a **single HTML file** (`pvz.html`) using vanilla JavaScript and HTML5 Canvas. No build tools, no dependencies, no external assets — all graphics are drawn with Canvas API or emoji.

## Development

Open `pvz.html` directly in a browser to run the game. No build step or server required.

For iterative development, use a live-reload tool if desired:
```bash
npx serve .
```

## Architecture

The game follows a strict class-based OOP structure. All code lives in one HTML file with embedded `<style>` and `<script>` tags.

### Core Classes

| Class | Responsibility |
|---|---|
| `Game` | Global state, main game loop (`requestAnimationFrame`), input routing |
| `Grid` | 5×9 grid system, pixel↔grid coordinate mapping, cell rendering |
| `GameState` | Enum: `MENU / PLAYING / PAUSED / WIN / LOSE` |
| `SunManager` | Spawns and recycles `Sun` instances |
| `Sun` | Individual sun: fall animation, Bézier fly-to-counter on click |
| `SeedPacket` | Card UI: cooldown overlay, affordability dimming |
| `PlantFactory` | Factory pattern — creates plant instances by type string |
| `PlantManager` | Holds all placed plants, calls their `update()`/`draw()` |
| `Plant` (base) | `gridX, gridY, health, maxHealth`; `update()`, `draw()`, `takeDamage()`, `die()` |
| `Sunflower` | Extends Plant; produces suns every 20s |
| `Peashooter` | Extends Plant; fires when zombies are in same row |
| `WallNut` | Extends Plant; 3-stage damage appearance |
| `CherryBomb` | Extends Plant; 3×3 AoE explosion after 1s |
| `SnowPea` | Extends Plant; slowing projectiles |
| `ProjectileManager` | Object pool pattern for bullet reuse |
| `Projectile` | Moves right at 200px/s; circle-rect collision with zombies |
| `ZombieManager` | Spawns and updates all zombies |
| `Zombie` (base) | State machine: `WALKING → EATING → DYING → DEAD` |
| `BasicZombie` | 200hp, 20px/s |
| `ConeheadZombie` | 380hp (cone absorbs 180), loses cone when depleted |
| `WaveManager` | JSON-driven wave config, triggers spawns, win/lose conditions |
| `ParticleSystem` | General-purpose particle effects (color, size, lifetime configurable) |
| `SoundManager` | Singleton; Web Audio API synthesis, no audio files |

### Canvas Layout

```
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

### Key Design Patterns

- **Object Pool**: `ProjectileManager` reuses `Projectile` instances to avoid GC pressure
- **Factory**: `PlantFactory.create(type, gridX, gridY)` — use this whenever spawning plants
- **State Machine**: Each `Zombie` has a `state` property; transitions drive animation and behavior
- **Wave Config**: Levels are defined as plain JSON objects (see `WaveManager`), not code

### Coordinate System

- Grid origin is top-left of the playfield (after the 120px card bar)
- `grid.toPixel(col, row)` → center pixel of that cell
- `grid.toCell(px, py)` → `{col, row}` or null if outside grid

## Development Phases

Follow the plan in `pvz-development-plan.md` strictly in order. Each phase produces a runnable game:

1. **Phase 1** — Canvas, grid, game loop, state management
2. **Phase 2** — Sun system, seed card bar, Sunflower plant
3. **Phase 3** — Peashooter, projectiles, zombies, wave system
4. **Phase 4** — Wall-nut, Cherry Bomb, Snow Pea; UI polish; Web Audio sound effects
5. **Phase 5** *(optional)* — Multi-level system, mobile touch support

Save a versioned backup after each phase: `pvz-v1.html`, `pvz-v2.html`, etc.
