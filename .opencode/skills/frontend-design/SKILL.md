# Frontend Design

## Design System

- **Font stack**: `system-ui, -apple-system, "Segoe UI", Roboto, sans-serif`
- **Base font size**: 14px, line-height 1.4
- **Colors**:
  - Text primary: `#172b4d`
  - Text secondary: `#44546f`, `#626f86`, `#8590a2`
  - Accent: `#579dff` (blue)
  - Success: `#22a06b` (green)
  - Danger: `#ae2e24` (red)
  - Background: `#f1f2f4` (column), `#fff` (cards)
  - Top bar: `#000` background, `#fff` text
  - Label colors: red `#f87168`, orange `#fca700`, yellow `#e2b203`, green `#4bce97`, blue `#579dff`, purple `#9f8fef`
- **Border radius**: 4px (buttons/inputs/labels), 8px (cards/menu items), 12px (columns/dialogs)
- **Shadows**: `0 1px 1px rgba(9,30,66,.25)` (cards), `0 8px 16px rgba(9,30,66,.4)` (menu), `0 4px 12px rgba(0,0,0,.3)` (toast)
- **Spacing**: 4px base unit, gaps of 6/8/10/12px

## Layout

- **Top bar**: 44px height, black background, contains board selector + search + create button
- **Board**: horizontal scroll (`overflow-x:auto`), columns stretch to full height (`align-items:stretch`)
- **Column**: 272px width, flex column with header + scrollable cards + add button
  - Collapsed: 48px width, vertical text (`writing-mode:vertical-rl`)
- **Card**: white background, 8px radius, cover image max 260px height, labels row, title with done checkbox, meta row
- **Home view**: white background, max-width 960px, tile grid with 72px color header

## Components

- **Buttons**: `.tb` (top bar, semi-transparent white), `.cr` (create, blue), `.btn` (default, subtle), `.btn.del` (danger, red text)
- **Inputs**: transparent with focus ring `inset 0 0 0 2px #579dff`
- **Dialog**: `border-radius:12px`, `max-height:92vh`, backdrop `rgba(0,0,0,.6)`
- **Toast**: fixed bottom center, dark background, auto-hide after 5s
- **Menu**: fixed position, white background, shadow, appears on column `⋯` click
- **Ask dialog**: custom replacement for `prompt()`, used for board create/rename

## Interaction Patterns

- Hover: outline `2px solid #579dff` on cards/tiles
- Focus-visible: `outline: 2px solid #579dff; outline-offset: 1px`
- Dragging: opacity 0.4
- Drop target: `outline: 2px dashed #579dff`
- Contenteditable: `outline: 2px solid #579dff; background: #fff`
- Done card: green checkmark circle
- Pinned card: 📌 prefix in title
- Fixed column: 🔒 prefix in title

## Conventions

- All UI text in Spanish
- Use `esc()` for HTML escaping (handles `& < > ' "`)
- CSS is inline in `<style>` block — no external files
- Prefer flexbox over grid (except home tiles: `grid-template-columns:repeat(auto-fill,minmax(210px,1fr))`)
- Animations: keep minimal, use CSS transitions only
- Responsive: board scrolls horizontally on small screens, columns stay 272px
- Single-file constraint: all CSS/JS inline in `tablero.html`

## When designing new features

- Match existing border-radius, shadow, and spacing values
- Use the existing color palette — don't introduce new colors without reason
- Keep the single-file constraint: all CSS/JS inline in `tablero.html`
- Test in browser preview after every visual change
- Mobile: ensure touch targets are at least 44px, but note drag-and-drop won't work on touch
- Consider both expanded and collapsed column states
- Consider both home view and board view contexts
