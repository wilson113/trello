# AGENTS.md

## What this is

A single-file Kanban board app (`tablero.html`) — all HTML, CSS, and JS inline. No build step, no dependencies, no package manager. Open the file directly in a browser to run it.

## Architecture

- **Entry point**: `tablero.html` — everything lives here (markup, styles, logic)
- **Persistence**: `localStorage` under key `tablero-v1` (JSON). No backend, no API.
- **State shape**: `{ cur: boardId, boards: [{ id, name, fav, bg, cols: [{ id, t, min, cards: [{ id, t, d, l, ck, cv, done }] }] }] }`
- **UI language**: Spanish (strings, aria-labels, comments)

## Conventions

- Vanilla JS only — no frameworks, no modules, no bundler
- CSS uses custom properties sparingly; most styling is inline in the `<style>` block
- DOM manipulation via `innerHTML` templates + event delegation on `board` and `dlg`
- IDs generated with `Math.random().toString(36).slice(2,9)` (8 chars)
- Images stored as base64 data URLs in `localStorage` (resized via canvas before save)

## Features

- **Pinned cards** — cards with `pinned:true` sort to the top of their column, show a 📌 prefix
- **Fixed columns** — columns with `fixed:true` cannot be deleted or renamed, show a 🔒 prefix
- **Undo toast** — deleting cards/columns or clearing a column shows a 5-second undo toast
- **Column reordering** — columns are draggable (drag from non-card area)
- **Keyboard shortcuts** — `/` focuses search, `N` focuses new card input
- **Custom prompt dialog** — `ask()` replaces `prompt()` for board create/rename (works in all environments)
- **Image size limits** — card covers max 2 MB, board backgrounds max 4 MB
- **Debounced save** — `localStorage` writes are debounced by 300 ms
- **Versioned state** — `S.version` tracks schema version for future migrations

## Gotchas

- `localStorage` quota is ~5 MB — large cover images can fill it silently (only one alert is shown)
- No test suite, no linter, no formatter — verify changes manually in the browser
- Drag-and-drop uses native HTML5 DnD — does not work on touch devices
- `prompt()` is blocked in some environments (e.g., iframe previews) — always use `ask()` instead
