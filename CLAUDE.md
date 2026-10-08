# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A collection of standalone browser games, each as a single self-contained HTML file with inline CSS and JavaScript. No build tools, no package manager, no bundler — open any `.html` file directly in a browser to play.

## Architecture

Each game is one HTML file containing all markup, styles, and logic inline. There is no shared code between games. Games use vanilla JS with no frameworks or external dependencies (aside from Google Fonts links).

### Current Games

- **tic-tac-toe.html** — Two-player Tic Tac Toe with scoreboard. Uses SVG marks, CSS custom properties for theming (light/dark), and `localStorage` for score persistence (`ttt-scores` key).
- **shooter.html** — "PIXEL BLITZ", a canvas-based top-down shooter. 480×480 pixel canvas with a custom bitmap font, sprite system using indexed-color 2D arrays, 10-level progression with bosses at levels 5 and 10, particle effects, and screen shake. Uses `localStorage` for high score (`pb-highscore` key). Controls: WASD/arrows + mouse aim/shoot.

## Conventions

- Games support both light and dark mode via `prefers-color-scheme` media query and `data-theme` attribute on `:root`. Define colors as CSS custom properties.
- Pixel-art games use `image-rendering: pixelated` on the canvas.
- All `localStorage` access is wrapped in try/catch for resilience.
- No external JS libraries — everything is vanilla.

## Git Workflow

- Commit work to Git regularly with clean, descriptive commit messages so progress is never lost.
- Push commits to GitHub frequently to keep the remote up to date.
- Don't batch large changes into a single commit — commit incrementally as meaningful units of work are completed.
