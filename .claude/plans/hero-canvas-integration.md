# Port the animated particle/dot canvas background into the hero

## Context
You want to bring in the mouse-reactive, connecting-dots canvas animation from `bscottnz/portfolio-site` (specifically `src/heroCanvas.js`/`src/bgCanvas.js`), but ported as plain vanilla JS/CSS with no build step — your site is a hand-written static HTML/CSS/JS site (no `package.json`, no bundler), and you want to keep it that way.

The source repo runs this canvas in two places: `heroCanvas.js` (full dot-count, mouse-following, confined to the hero) and `bgCanvas.js` (a sparser, scroll-reactive variant behind the rest of the page). To keep this a focused, additive change rather than a redesign, I'll port just the hero variant into your existing `.hero` section (`index.html:60`) — the effect that's most visually distinct — and adapt its colors to your "Cyan CLI" theme (`--accent: #2DD4CF` from `style.css:19`) instead of the source's blue/pink palette.

I'll also fix two real bugs in the source while porting it:
- `createDots()` in the original does `dots.array.push(new Dot())` **inside the render loop**, so the dots array grows unbounded every frame (a memory leak that gets worse over time). The port will build the dots array once and mutate positions in place on each frame.
- The original redraws via `setInterval(1000/30)`. The port uses `requestAnimationFrame` instead, and pauses via the Page Visibility API when the tab isn't visible.

The port will also respect `prefers-reduced-motion` (matching the posture your existing `main.js` already takes for scroll-reveal) by not rendering the canvas at all when that's set.

## Changes

**`index.html`** — inside `<section class="hero" id="top">` (`index.html:60`), add a canvas element as the first child, before `.wrap`:
```html
<canvas class="hero-canvas" id="heroCanvas" aria-hidden="true"></canvas>
```
Add `<script src="hero-canvas.js"></script>` right before the existing `<script src="main.js"></script>` (`index.html:534`).

**`style.css`** — near the existing `.hero` rules (`style.css:153`):
- `.hero` gets `position: relative; overflow: hidden;`
- `.hero-canvas` is `position: absolute; inset: 0; width: 100%; height: 100%; z-index: 0; pointer-events: none;` (never intercepts clicks/taps on hero content)
- `.hero .wrap` gets `position: relative; z-index: 1;` so text stays above the canvas
- Under the existing `@media (prefers-reduced-motion: reduce)` block (`style.css:403`), hide `.hero-canvas` (`display: none`) as a CSS-level backstop in addition to the JS check

**New file `hero-canvas.js`** (same IIFE style as `main.js`, no dependencies) — adapted from the source's `heroCanvas.js`:
- Bails out immediately if `#heroCanvas` is missing, canvas isn't supported, or `prefers-reduced-motion: reduce` is set
- Sizes the canvas to its containing `.hero` element (not `document.body`/`window`), recalculating on a debounced `resize`
- Builds the dot array once (tiered count by hero width, same breakpoints as the source: fewer dots on narrow viewports); connects nearby dots with lines whose opacity fades with distance, same as source
- Uses `--accent` (`#2DD4CF`) as the dot/line color instead of the source's blue/pink mix, with per-dot opacity variance for depth
- Tracks mouse position relative to the hero's bounding rect (`mousemove` on the hero element, not `window`), so the lead dot follows the cursor only while it's over the hero
- Driven by `requestAnimationFrame`; the loop is cancelled on `visibilitychange` (tab hidden) and resumed when visible again

## Verification
- Open `index.html` directly in a browser (or via a static file server) and confirm the dots render and connect behind the hero text, follow the mouse while hovering the hero, and don't obstruct the "View work"/"Get in touch" buttons or the nav.
- Resize the window across the tiered breakpoints and confirm dot count/behavior adjusts without the animation breaking or piling up canvases.
- In DevTools, emulate `prefers-reduced-motion: reduce` and confirm the canvas doesn't render/animate.
- Switch tabs away and back; confirm (via a quick console log or performance tab) the animation loop pauses while hidden.
- Check the other pages (`cs-330.html`, etc.) are unaffected since the new script/markup only targets `#heroCanvas` on `index.html`.
