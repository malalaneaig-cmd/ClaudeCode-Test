# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A collection of standalone browser games, each as a single self-contained HTML file with inline CSS and JavaScript. No build tools, no package manager, no bundler — open any `.html` file directly in a browser to play.

## Development

There is no build step. To run a game, open the HTML file in a browser:
```
start shooter.html
```

To verify JavaScript syntax without a browser:
```
node -e "const fs=require('fs'); const html=fs.readFileSync('FILENAME','utf8'); const m=html.match(/<script>([\s\S]*)<\/script>/); if(m){try{new Function(m[1]);console.log('OK')}catch(e){console.log(e.message)}}"
```

## Architecture

Each game is one HTML file containing all markup, styles, and logic inline. There is no shared code between games. Games use vanilla JS with no frameworks or external dependencies (aside from Google Fonts links).

### Games

- **tic-tac-toe.html** (389 lines) — Two-player local Tic Tac Toe with SVG marks, scoreboard, and `localStorage` persistence (`ttt-scores` key).
- **tik-tak-toe.html** (575 lines) — Single-player Tic Tac Toe vs AI. Three difficulty levels (easy=random, medium=hybrid, hard=minimax with alpha-beta pruning). Uses `localStorage` keys `ttt-sp-scores` and `ttt-sp-diff`.
- **shooter.html** (1324 lines) — "PIXEL BLITZ", a canvas-based top-down shooter. The most complex game, structured in 10 labeled sections:

#### shooter.html Internal Architecture

| Section | Purpose |
|---------|---------|
| 1. Constants & Config | Canvas size (480×480), gameplay tuning, 10 level definitions |
| 2. Pixel Font | 5×7 bitmap font stored as bitmask integers, `drawText`/`textWidth` |
| 3. Sprite Data & Drawing | Indexed-color 2D arrays with 4-entry palettes, `drawSprite`, `cycleAnim` |
| 4. Utilities | Math helpers: `dist`, `angleTo`, `normalize`, `clamp`, `rng`, `rngInt`, `pick` |
| 5. Entity Management | Factories: `createBullet` (object pool), `createEnemy`, `createBoss`, `spawnParticles`, `createFloatingText` |
| 6. Game State | `game` object, `resetGame`, `startLevel`, `getLevelConfig` (supports infinite scaling beyond level 10) |
| 7. Input Handling | Keyboard (WASD/arrows) + mouse (aim/shoot) with canvas coordinate scaling |
| 8. Update Functions | `updatePlaying` (movement, shooting, spawning, enemy AI, collisions, death sequences), `updateTransition`, `updatePlayerDying` |
| 9. Render Functions | Drawing pipeline: background → bullets → enemies → player → particles → floating text → HUD, plus menu/transition/game-over/pause screens |
| 10. Game Loop | `requestAnimationFrame` with delta-time capping at 50ms, state-based dispatch |

**Key systems:** Sprites use a palette index array (0=transparent, 1-3=colors) drawn as filled rects. Bullets use object pooling with dead-slot reuse. Particles recycle oldest when at capacity (300 max). Enemies have a `dying` state with shrink/flash animation before removal. Player death uses a `PLAYER_DYING` game state with a 0.6s animation before `GAME_OVER`.

**Game states:** `MENU` → `PLAYING` → `LEVEL_TRANSITION` or `PLAYER_DYING` → `GAME_OVER`. Also `PAUSED`.

**Enemy types:** Grunt (chase), Dasher (seek→windup→charge→cooldown FSM), Tank (slow/tough chase), Shooter (maintain distance, strafe, fire bullets). Bosses are Shooter variants at scale 3 with multi-bullet spreads and a phase change at 50% HP.

## Conventions

- Games support both light and dark mode via `prefers-color-scheme` media query and `data-theme` attribute on `:root`. Define colors as CSS custom properties.
- Pixel-art games use `image-rendering: pixelated` on the canvas.
- All `localStorage` access is wrapped in try/catch for resilience.
- No external JS libraries — everything is vanilla.
- Each game's `localStorage` keys are namespaced to avoid collisions (e.g., `pb-highscore`, `ttt-scores`, `ttt-sp-scores`).

## Git Workflow

- **Commit and push after every meaningful change.** As you work, commit to Git and push to GitHub regularly so we never lose progress. Don't wait until the end of a task — commit incrementally as each unit of work is completed.
- Write clean, descriptive commit messages that explain *why*, not just *what*.
- Don't batch large changes into a single commit — smaller, focused commits are easier to revert if needed.
