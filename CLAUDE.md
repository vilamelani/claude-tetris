# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Vanilla JS Tetris (HTML5 Canvas + CSS). No dependencies, no build, no tests, no linter. The README and UI text are in Spanish.

## Running

Open `index.html` directly, or serve the folder (e.g. `python -m http.server`) and visit it in a browser. There is nothing to install or compile.

## Architecture

Three files, all loaded directly by `index.html` (`game.js` is a classic script, not a module):

- `index.html` — static markup: `#board` canvas (300x600), `#next-canvas` (120x120), HUD spans (`#score`, `#lines`, `#level`), and a `#overlay` used for both PAUSA and GAME OVER.
- `style.css` — layout and theming only.
- `game.js` — all game logic, using module-level mutable globals (`board`, `current`, `next`, `score`, `lines`, `level`, `paused`, `gameOver`, `dropInterval`, `animId`, ...) that `init()` resets.

Key points in `game.js`:

- Pieces are matrices of numbers; the number is both the cell occupancy and the index into `COLORS`/`PIECES` (1=I ... 7=L). `board` stores these same indices (0 = empty).
- Game loop: `loop(ts)` is a `requestAnimationFrame` loop accumulating time into `dropAccum`; gravity fires when it exceeds `dropInterval`. Pause and game over stop the loop with `cancelAnimationFrame(animId)`; resume/restart re-enter it (`togglePause`, `init`).
- Locking flow: `lockPiece()` → `merge()` → `clearLines()` (updates score, level, `dropInterval`) → `spawn()` (promotes `next`, ends game if the spawn collides).
- Rotation (`tryRotate`) is clockwise only, with horizontal wall kicks `[0, -1, 1, -2, 2]`; no SRS.
- Scoring: `LINE_SCORES[cleared] * level`, +1 per soft-drop cell, +2 per hard-drop cell. Level = `floor(lines/10)+1`; `dropInterval = max(100, 1000 - (level-1)*90)`.
- Input is a single `keydown` listener using `e.code`; `P` is handled before the paused/gameOver guard.
