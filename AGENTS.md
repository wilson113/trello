# AGENTS.md

## What this is

A single-file Kanban board app (`index.html`) — all HTML, CSS, and JS inline. No build step, no dependencies, no package manager. Open the file directly in a browser to run it.

## Architecture

- **Entry point**: `index.html` — everything lives here (markup, styles, logic)
- **Persistence**: `localStorage` under key `tablero-v1` (JSON) for the board data; images live in **IndexedDB** (`tablero-img`, store `img`, key = ref, value `{b: Blob, t: createdAt}`). No backend, no API.
- **State shape**: `{ version, cur: boardId, boards: [{ id, name, fav, bg, cols: [{ id, t, min, fixed, cards: [{ id, t, d, l, ck: [{ t, x, items?: [{ t, x }] }], cv, done, pinned }] }] }] }`
- **UI language**: Spanish (strings, aria-labels, comments)

## Conventions

- Vanilla JS only — no frameworks, no modules, no bundler
- CSS has no custom properties; all styling lives in the `<style>` block (plus some inline styles in templates)
- A CSP `<meta>` blocks network access and external resources (`default-src 'none'`; only `data:`/`blob:` images and inline script/style). Don't add external fonts, scripts or `fetch` without relaxing it deliberately
- DOM manipulation via `innerHTML` templates + event delegation on `board` and `dlg`. Any user-controlled text must go through `esc()` or `textContent` (e.g. `toast()` uses `textContent`)
- `render()` is async (coalesced with `requestAnimationFrame`). If you need the new DOM right away (focus, scroll), use `renderNow()`
- IDs generated with `crypto.getRandomValues` (always 8 chars, base36)
- Images (`cv` on cards, `bg` on boards) are stored as refs `idb:<id>` pointing at IndexedDB blobs, resized via canvas (JPEG .72) before saving. Render with `src(ref)` / `imgTag(cls, ref)`, never put the raw value in `src`/`url()`. Blob object URLs are cached in `IMGU` and preloaded at startup (`loadImgs`)
- Fallback: if IndexedDB is unavailable or a write fails, the image is kept as a `data:` URL inside the state (old behavior). `src()` handles both

## Features

- **Pinned cards** — cards with `pinned:true` sort to the top of their column, show a 📌 prefix
- **Fixed columns** — columns with `fixed:true` cannot be deleted or renamed, show a 🔒 prefix
- **Undo toast** — deleting cards/columns/boards, clearing a column or discarding an untitled card shows a 5-second undo toast (the toast needs `pointer-events:auto` while visible; it is `none` when hidden)
- **Column reordering** — columns are draggable (drag from non-card area)
- **Keyboard shortcuts** — `/` focuses search, `N` focuses new card input
- **No native dialogs** — never use `alert()`, `confirm()` or `prompt()` (blocked in sandboxed iframes). Use `ask()` for text input (Enter confirms), `pick()` for choices (callback receives the **index**, not the name), `toast(msg)` for messages and `toast(msg, undoFn)` for destructive actions. Undo closures must capture the board/column they act on, not call `B()`
- **Toast layering** — `#toast` is a `popover="manual"` so it shows above open modal dialogs (it is not clickable while a modal is open, so only put undo buttons on toasts shown after the dialog closes)
- **Move card** — "Mover a…" lets you pick any board (including the current one) and any column; transparent PNGs get a white background when resized to JPEG
- **Image storage flow** — on startup `initP` opens IndexedDB, migrates any `data:` images found in the state to blobs (`externalize`), preloads blobs, saves the slimmed state and garbage-collects unreferenced blobs older than 24 h (`gcImgs`). Export converts refs back to `data:` URLs so backup files are self-contained; import converts them to blobs again. Imports never accept `idb:` refs (`sImg(v, ref)` only allows them for the app's own state)
- **Image size limits** — source images max 10 MB (covers and backgrounds); they are resized via canvas before saving
- **Safe import** — `normState`/`normBoard` validate and sanitize everything read from `localStorage` or an imported JSON (IDs regenerated on import, only `data:image/*` accepted for `cv`/`bg`, unknown labels dropped, duplicate board names get a `(2)` suffix)
- **Save flush** — pending save is written immediately on `visibilitychange`/`pagehide`
- **Debounced save** — `localStorage` writes are debounced by 300 ms (`save()`; `flush()` writes now)
- **Corrupt data rescue** — if `localStorage` holds invalid JSON (or no boards), the raw text is copied to `tablero-v1-corrupto` before falling back to the seed, and a toast says so
- **Multi-tab sync** — the `storage` event adopts the other tab's state (without re-saving) and shows a toast; unsaved edits from the last ~300 ms in this tab are dropped
- **Save failure banner** — if `localStorage.setItem` fails, a persistent red banner (`#warn`) offers Exportar; it hides again when a save succeeds
- **Scroll preserved** — `renderImpl` keeps each column's `scrollTop` and the board's `scrollLeft` when re-rendering the same board
- **Versioned state** — `S.version` tracks schema version for future migrations

## Gotchas

- `localStorage` now only holds text/refs, so its ~5 MB quota is rarely a problem; image quota is the browser's IndexedDB quota. Orphaned blobs (replaced covers, deleted cards) stay for 24 h before GC so undo and other tabs keep working. `navigator.storage.persist()` is not requested (Firefox prompts for it)
- No test suite, no linter, no formatter — verify changes manually in the browser
- Drag-and-drop uses native HTML5 DnD — does not work on touch devices
- **Touch drag** — long-press (350 ms) on a card or on a column's header/empty area starts a drag with a floating ghost, edge auto-scroll (board horizontally, column lists vertically) and drop highlighting; a quick swipe still scrolls. Implemented with `touchstart/move/end` on `board` (state in `TP`) and shares the drop logic with mouse DnD through `dropCardAt()` / `dropColAt()`. Mouse keeps using native HTML5 DnD. Disabled while search results are shown
- Known gaps: no way to create sub-tasks in the UI (only displayed/deleted if present in imported data); cards are not keyboard-movable; labels are color-only; touch drag only tested with emulated touch events, not on a physical device
