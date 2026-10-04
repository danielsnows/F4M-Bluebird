# Replication prompt — Secret

> Complete instructions for an AI coding agent. It contains everything needed to rebuild the animated hero «Secret» identically: structure, styles, texts, animation timings and curves, mobile view and links to the resources (images, video and fonts).

## Role and goal
Act as a senior front-end developer specialized in web animation. Build a single-screen page (hero) that exactly reproduces the animated design «Secret» described here, without adding or removing anything.
Published reference version: https://secret-snows.vercel.app — use it to compare your result visually.

## 1. Overview
Hero «Secret — Kyoto», designed at 1440×810, with a dark atmosphere around a red torii gate. Behind everything, full-bleed, a scroll-driven video. The automatic intro (0 → 2.2 s) brings in the giant word «Secret» (letters S-e-c-r-e-t), the menu (Shrines, Landscapes, Treasures, Lanterns, Eternity), the title «Exploring ancient pathways for deep wisdom.», the vertical Japanese text, the image thumbnail and the four numbered texts 01–04. Scrolling (up to 5.15 s) makes the «Secret» letters and the initial content leave, and the final block appears: «Unveiling mystical temples for pure spirit.», the paragraph about Edo, the arrow and the «Exploring» cards.

## 2. Deliverable and technical rules
- A single `index.html` file with the CSS inside `<style>` and the JavaScript inside `<script>`. Plain (vanilla) JavaScript, no frameworks and no build step.
- The only external dependency: Lenis 1.3.26 for smooth scrolling: `<script src="https://cdn.jsdelivr.net/npm/lenis@1.3.26/dist/lenis.min.js"></script>` before your script (if it fails to load, everything must work with native scrolling).
- Images, video and fonts are ALWAYS loaded from the absolute URLs in section 13 «Resources». Do not download them, do not inline them as base64 and do not use any other resources.
- Every visible text is real, selectable and editable HTML text (never text turned into an image or into SVG paths). The vector shapes in the Appendix are only icons and decoration.
- Lists use `<ul>`/`<li>` (inside `<nav aria-label="…">` where indicated) and links use clickable `<a href="#">`, with `aria-label` where indicated.
- Head: `<html lang="en">`, `<meta charset="utf-8">`, `<meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover">`, `<title>Secret — Kyoto</title>`, `<meta name="description" content="Secret: unveiling mystical temples for pure spirit. Exploring ancient pathways for deep wisdom.">`, `<meta name="theme-color" content="#011416">`.
- Respect `prefers-reduced-motion: reduce`: the loader works the same, but there is no intro, no smooth scrolling and no snap; t stays fixed on the final state (t = 5.15 s) even when scrolling.
- Do not add elements, texts, effects or sections that are not in this specification.

## 3. Base structure (common HTML + CSS)
```html
<body>
<div class="loader" id="loader" aria-hidden="true">
  <div class="loader-in"><span>SECRET</span><div class="loader-bar"><i id="loaderBar"></i></div><span id="loaderPct">000</span></div>
</div>
<main class="hero" id="hero" aria-label="Secret — Kyoto">
  <div class="stage" id="stage">
    <!-- one layer (div.blk) per «Layer» in section 9, in the same order: later ones are on top -->
  </div>
</main>
<script>/* engine from section 6 */</script>
</body>
```
```css
:root{--bg:#011416;--ink:#e3ffee;color-scheme:dark}
html,body{background:var(--bg);margin:0}
body{overflow-x:clip;-webkit-font-smoothing:antialiased;-moz-osx-font-smoothing:grayscale;text-rendering:geometricPrecision}
html.is-locked,html.is-locked body{overflow:hidden}
.hero{position:relative;height:calc(100vh + var(--track,400vh));height:calc(100svh + var(--track,400vh))}
.stage{position:sticky;top:0;height:100vh;height:100svh;overflow:hidden;background:var(--bg)}
.blk{position:absolute;left:0;top:0;width:0;height:0}          /* one layer */
.blk *{box-sizing:border-box}
.blk li{list-style:none;margin:0;padding:0}
.blk ul,.blk nav{position:absolute;left:0;top:0;width:0;height:0;margin:0;padding:0;list-style:none} /* lists: boxless containers */
.blk a{color:inherit;text-decoration:none;cursor:pointer;-webkit-tap-highlight-color:transparent}
.blk a.lk{position:absolute;left:0;top:0;right:0;bottom:0;display:block;border-radius:inherit} /* link covering the whole box */
.blk a:focus-visible{outline:2px solid currentColor;outline-offset:3px}
.blk *{pointer-events:none}
.blk .t,.blk .t *,.blk a,.blk a *{pointer-events:auto}   /* only texts and links receive the pointer */
.loader{position:fixed;inset:0;z-index:50;display:grid;place-items:center;background:var(--bg);transition:opacity .9s cubic-bezier(.22,1,.36,1),visibility .9s}
.loader.is-done{opacity:0;visibility:hidden;pointer-events:none}
.loader-in{display:grid;justify-items:center;gap:14px;color:var(--ink);font:500 11px/1 ui-monospace,SFMono-Regular,Menlo,monospace;letter-spacing:.24em}
.loader-bar{width:160px;height:1px;background:color-mix(in srgb,var(--ink) 20%,transparent);overflow:hidden}
.loader-bar i{display:block;height:100%;width:100%;background:var(--ink);transform:scaleX(0);transform-origin:left}
```
- All design elements are `position:absolute`. Texts have `margin:0`. Images and vector shapes use `display:block`.

## 4. Coordinate system, layers and scaling
- Reference design (frame): **1440×810 px**. Every px measurement in this document is in units of that design.
- The stage `.stage` fills the whole window (W × H = current width × height of the stage).
- Each **layer** (`div.blk`) is a zero-size absolute container scaled by a factor z (with `zoom: z`, or with `transform: translate(…) scale(z)` and `transform-origin: 0 0`; both are valid as long as the on-screen result is the same). Inside a layer, top-level elements use **frame coordinates**, and the design point (X, Y) is drawn on screen at:
  - **BLEED LAYER (cover)**: z = max(W/1440, H/810); screen = ((W − 1440·z)/2 + X·z, (H − 810·z)/2 + Y·z). It always covers the screen (the excess is cropped).
  - **CONTAINED LAYER (contain, anchor ax, ay)**: z = s = min(W/1440, H/810); mx = (W − 1440·s)/2; my = (H − 810·s)/2; screen = (mx·(1+ax) + X·s, my·(1+ay) + Y·s). Anchors are −1, 0 or 1: −1 = stuck to the left/top edge, 0 = centered, 1 = stuck to the right/bottom edge.
- The «design box» of each layer (x, y, width×height) is its reference rectangle in the frame (its content may extend beyond it); its top-left corner is used to place the layer in the mobile view.
- Nested elements use local coordinates of their container (left/top relative to the parent).
- Recompute everything on every `resize`.

## 5. Fonts
Declare these fonts with `@font-face{font-family:'…';src:url(URL) format('woff2');font-weight:…;font-style:normal}` and use them with the stack `'Font', system-ui, sans-serif`:
- **Onest** (variable font, weight range 100–900 (font-weight:100 900)): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/secret/assets/fonts/Onest.woff2
- **Unbounded** (weight 300): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/secret/assets/fonts/Unbounded-300.woff2

## 6. Animation engine
- There is a single timeline **t** (seconds). Each element has a static state (section 9) and, where «↳ animation» is given, properties that change with t. On every frame (`requestAnimationFrame`) all properties are evaluated for the current t and applied to the styles.
- Notation: `property: t0→t1s v0→v1 (curve)` = between t0 and t1 the property goes from v0 to v1 with that easing curve. Before its first segment the property equals the v0 of the first segment; between segments it keeps the last value reached; after the last segment it keeps the final value. `(linear)` = linear. `(step-end)` = keeps v0 and jumps to v1 at the end of the segment.
- `jump from v0 to v1 at t`: equals v0 before t and v1 from t on (inclusive). Numbers are interpolated as they are: a rotation from −3 to +3 rad turns 6 rad (no shortest-arc logic).
- Static value = the one shown in the static state of the line (for example `opacity:0.8`); if not shown: opacity 1, scales 1, offsets and rotations 0.
- Composite track `base: … | add: … | multiply: …`: value = (base, or the static value if there is no base) + the sum of all «add» tracks, and that result × the product of all «multiply» tracks. Each track is evaluated with the rules above (before its first segment it equals its v0).
- Meaning of the properties:
  - **x, y**: position of the element's top-left corner in its container's coordinates (if its static state shows `pos(x,y)` it is applied as left/top; if it shows `transform translate(x,y) rotate(a)`, as a transform with `transform-origin:0 0` and left/top = 0).
  - **width, height**: width / height in px.
  - **opacity**.
  - **rotation(rad)**: `transform: translate(x,y) rotate(a rad)` with `transform-origin:0 0` (rotates around the top-left corner). If the static transform includes `scale(1,-1)` (flip), it is kept after `rotate`.
  - **content-scale**: `scale(k)` appended at the end of the transform (scales the element and its content from its top-left corner; the given size is before scaling).
  - **offsetX, offsetY**: offset in px added to x / y (to left/top if the element uses `pos`, or inside the translate; in a group: `translate(offsetX, offsetY)`).
  - **rotation(°), scaleX, scaleY**: rotation in degrees and scale around the center of the box, or around the point given in brackets (pivot or the group's `transform-origin`). On an element: `transform: translate(x,y) rotate(a rad) [scale(k)] translate(cx,cy) rotate(r deg) scale(sx,sy) translate(−cx,−cy)` (if its static state is a `matrix(...)`, it goes in place of `rotate(a rad)`), with (cx, cy) = box center or the given pivot.
  - **radius**: border-radius in px. **background / text color rgba**: interpolate each channel [r, g, b, a] (r, g, b from 0 to 255). **blur**: `filter: blur(px)`.
- If an element's static state is `transform matrix(...)`, that matrix is its transform (with `transform-origin:0 0`); if it also animates x/y: `transform: translate(x,y) matrix(...)`.
- **Group**: absolute container at (0,0) with zero size and no box of its own; its children use the coordinates of the group's container. Its opacity and transforms affect all its children.
- If an element's computed opacity (its own, including the multipliers and fades from sections 10 and 11, not counting its ancestors') is ≤ 0.001, give it `visibility:hidden` (and remove it when it becomes visible again), so it never receives clicks or focus while hidden.
- Easing curves used:
  - `E1` = spring with mass=1, stiffness=80, damping=20, normalized to 2 s (see formula)
  - `E3` = cubic-bezier(0, 0, 0.58, 1)
  - `E4` = cubic-bezier(1, 0, 0, 1)
  - `E5` = cubic-bezier(0.16, 1, 0.3, 1)
  - `E6` = spring with mass=1, stiffness=80, damping=20, normalized to 0.907 s (see formula)
  - `E8` = spring with mass=1, stiffness=100, damping=15, normalized to 0.78 s (see formula)
  - `E9` = spring with mass=1, stiffness=80, damping=20, normalized to 1.5 s (see formula)
  - `E10` = spring with mass=1, stiffness=80, damping=20, normalized to 1.5 s (see formula)
  - `E11` = cubic-bezier(0.25, 0.1, 0.25, 1)
  - `E13` = spring with mass=1, stiffness=80, damping=20, normalized to 1 s (see formula)
  - `E14` = cubic-bezier(0.09, 0.55, 0.5, 1)
  - `E15` = cubic-bezier(0.3, -1.2, 0.45, 1)
  - `E16` = cubic-bezier(0.15, 0.52, 0.5, 1)
- `cubic-bezier(x1, y1, x2, y2)` curves work as in CSS: given the time progress p, solve the parameter u such that X(u) = p and return Y(u).

Spring formula with mass m, stiffness k, damping c and normalized duration D:
- ω0 = √(k/m), ζ = c / (2·√(k·m)).
- If ζ < 1: ωd = ω0·√(1−ζ²); x(τ) = 1 − e^(−ζ·ω0·τ)·(cos(ωd·τ) + (ζ·ω0/ωd)·sin(ωd·τ)).
- If ζ = 1: x(τ) = 1 − e^(−ω0·τ)·(1 + ω0·τ).
- If ζ > 1: q = √(ζ²−1), r1 = −ω0·(ζ−q), r2 = −ω0·(ζ+q); x(τ) = 1 + (r2·e^(r1·τ) − r1·e^(r2·τ)) / (r1 − r2).
- easing(p) = x(p·D) / x(D) for 0 < p < 1; 0 if p ≤ 0; 1 if p ≥ 1.

## 7. Playback (intro and scrolling)
- Total timeline duration: 5.15 s. Automatic intro: from t = 0 to t = 2.2 s. The rest (2.2 → 5.15 s) is driven by scrolling.
- `.hero` height = 100vh + track. track = round((5.15 − 2.2) × 70) = **206vh** on desktop and round((5.15 − 2.2) × 80) = **236vh** on mobile (use exactly these values; they are set through the CSS variable `--track`). `.stage` is sticky, so the stage stays fixed while the track is scrolled.
- While loading: `html.is-locked` (overflow hidden), `history.scrollRestoration = 'manual'`, `scrollTo(0,0)`.
- When the loader gets the `is-done` class, play the intro: t advances in real time (linearly) from 0 to 2.2 s in 2.2 s. During the intro scrolling is blocked (Lenis stopped; if Lenis is not available, keep `is-locked` until the intro ends) and scroll events do not change t. When it ends, t = 2.2 and scrolling is enabled.
- Smooth scrolling with Lenis: `new Lenis({lerp:0.085, wheelMultiplier:0.9, smoothWheel:true})`, `lenis.stop()` until the intro ends, then `lenis.start()`; call `lenis.raf(now)` in the requestAnimationFrame loop. Without Lenis, use native scrolling.
- Scroll → time mapping: p = clamp((scrollY − hero.offsetTop) / (hero.offsetHeight − stage.clientHeight), 0, 1); t = 2.2 + p × (5.15 − 2.2).
- Pauses (holds), in seconds: [2.2 – 2.6], [4.85 – 5.15]. Snap (Lenis only; no snap without Lenis): the direction is the sign of the last scroll change; 170 ms after the last scroll event (every event restarts the timer), if t is not inside a pause (with a ±0.02 s margin), scroll with `lenis.scrollTo` to the start of the next pause when scrolling down, or to the end of the previous pause when scrolling up. Duration = clamp(|Δt| × 0.45, 0.6, 1.8) s; easing easeInOutCubic (x < 0.5 ? 4x³ : 1 − (−2x+2)³/2). No snap with `prefers-reduced-motion` or during another snap (the «snapping» state is released in `onComplete` or, as a safety net, after 2.2 s).
- On every resize: recompute layers, track and t from the current scroll position.
- Video («scrub» mode, driven by the timeline): `<video muted playsinline preload="auto">` with its poster; it never plays by itself. For reliable seeking, download the MP4 with `fetch` as a Blob (reporting progress to the loader) and set `video.src = URL.createObjectURL(blob)` (if that fails, use the direct URL). On every frame, if `readyState ≥ 2` and it is not `seeking`: target = min(max(0, t), duration − 0.04); if |currentTime − target| > 0.012, `currentTime = target`.

## 8. Loader and preloading
- The loader covers the screen with the `--bg` color and shows «SECRET», a 160×1 px bar and a 3-digit percentage (`000` → `100`) in the `--ink` color.
- Weighted progress: fonts (`document.fonts.ready`) weight 1; each `<img>` element on the stage weight 0.4, even if it repeats a file (it counts once it has loaded —or failed— and `img.decode()` has finished; the video poster does not count); the video weight 6 (its fetch download progress). Percentage = Math.round(progress × 100) with 3 digits. The bar uses `transform: scaleX(progress)` and never goes backwards.
- Wait until everything finishes (14 s maximum; if it runs out, continue without cancelling anything); then set the video `src` (the Blob URL if already downloaded, otherwise the direct URL) and wait for its `loadeddata` or `error` (3 s maximum). Then remove `is-locked` from `<html>` (except in the no-Lenis case described in section 7), add `is-done` to the loader (it fades out in 0.9 s) and start the intro.

## 9. Layers and elements
Notation of each line: `[eN]` = suggested identifier; `box` = div; `text` = div with text (the content in quotes, keeping capitalization, line breaks and spaces); `group` = boxless container; `vector` = box containing an inline SVG; `<a href="#">`, `<ul>`, `<li>`, `<nav>` = semantic tag to use; `«name»` = layer name in the design (for reference only, also handy as `data-name`); `pos(x,y)` = left/top; `size A×B` = width×height; after `|` come the literal CSS styles. Every `text` element gets `class="t"` (used by the pointer-events rule in section 3); boxes, groups and vectors may use `class="b"`, `"g"` and `"v"`. Decorative layers (border, glass, inner shadow, background blur, fill layer) are absolute elements (for example `<i>`) covering their container (given `inset`) with inline `pointer-events:none` and `border-radius:inherit` unless stated otherwise. Images go inside an absolute container `inset:0; overflow:hidden; border-radius:inherit`. The order of the lines is the stacking order (later is on top).

#### Layer bk0 «Fondo» — BLEED LAYER (cover), design box x=0 y=0 1440×810
- [e1] box «Animation» pos(0,0) size 1440×810
  - fill layer (covers the whole box, stacked in this order): background:linear-gradient(#e4ffef,#e4ffef)
  - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/secret/assets/img/435caeb929.webp: absolutely positioned `<img alt="">` at (0,0), 1672×941 px, max-width:none, transform-origin 0 0 and transform matrix(0.8612,0,0,2.7839,0,-1809.685); the container clips it (overflow hidden, same border-radius)
  - fill layer (covers the whole box, stacked in this order): background:linear-gradient(#011416,#011416)

#### Layer bk1 «Background Image» — BLEED LAYER (cover), design box x=0 y=0 1449.06×810
- [e2] box «Background Image» transform translate(-4.52,0) rotate(0rad) size 1449.06×810
    ↳ animation: scaleX: multiply: 0→2s 1.36→1 (E1); scaleY: multiply: 0→2s 1.36→1 (E1) [rotation/scale around the box center]
  - video: `<video muted playsinline preload="auto" poster="https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/secret/assets/video/078efbe522.jpg" data-src="https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/secret/assets/video/078efbe522.mp4">` with position:absolute; inset:0; width:100%; height:100%; object-fit:cover; display:block (the `src` is assigned from JavaScript once preloading finishes, see sections 7 and 8)

#### Layer bk2 «Main Title» — CONTAINED LAYER (contain, anchor x=0, y=1), design box x=-35.67 y=373 1511×333
- [e3] box «Main Title» pos(-35.67,373) size 1511×333
  - [e4] text pos(0,125) size 314×333 | white-space:pre-wrap; text-align:center; text-box:trim-both cap alphabetic; font-family:'Unbounded'; font-weight:300; font-size:444.23px; line-height:1.2; letter-spacing:-0.06em; background:linear-gradient(180deg, #e4ffef 0%, #89998f 54.89%, #000000 100%); -webkit-background-clip:text; color:transparent | TEXT: "S"
      ↳ animation: opacity: 0.591→1.376s 0→1 (E3); offsetY: base: 0.591→1.376s 100→-40 (E5) | add: 2.626→3.626s 0→400 (E4)
  - [e5] text pos(285.49,125) size 314×333 | white-space:pre-wrap; text-align:center; text-box:trim-both cap alphabetic; font-family:'Unbounded'; font-weight:300; font-size:444.23px; line-height:1.2; letter-spacing:-0.06em; background:linear-gradient(180deg, #e4ffef 0%, #89998f 54.89%, #000000 100%); -webkit-background-clip:text; color:transparent | TEXT: "e"
      ↳ animation: opacity: 0.659→1.457s 0→1 (E3); offsetY: base: 0.659→1.457s 100→-40 (E5) | add: 2.666→3.666s 0→400 (E4)
  - [e6] text pos(570.99,125) size 299×333 | white-space:pre-wrap; text-align:center; text-box:trim-both cap alphabetic; font-family:'Unbounded'; font-weight:300; font-size:444.23px; line-height:1.2; letter-spacing:-0.06em; background:linear-gradient(180deg, #e4ffef 0%, #89998f 54.89%, #000000 100%); -webkit-background-clip:text; color:transparent | TEXT: "c"
      ↳ animation: opacity: 0.719→1.533s 0→1 (E3); offsetY: base: 0.719→1.533s 100→-40 (E5) | add: 2.741→3.741s 0→400 (E4)
  - [e7] text pos(841.48,125) size 209×333 | white-space:pre-wrap; text-align:center; text-box:trim-both cap alphabetic; font-family:'Unbounded'; font-weight:300; font-size:444.23px; line-height:1.2; letter-spacing:-0.06em; background:linear-gradient(180deg, #e4ffef 0%, #89998f 54.89%, #000000 100%); -webkit-background-clip:text; color:transparent | TEXT: "r"
      ↳ animation: opacity: 0.779→1.586s 0→1 (E3); offsetY: base: 0.779→1.586s 100→-40 (E5) | add: 2.812→3.812s 0→400 (E4)
  - [e8] text pos(1021.97,125) size 314×333 | white-space:pre-wrap; text-align:center; text-box:trim-both cap alphabetic; font-family:'Unbounded'; font-weight:300; font-size:444.23px; line-height:1.2; letter-spacing:-0.06em; background:linear-gradient(180deg, #e4ffef 0%, #89998f 54.89%, #000000 100%); -webkit-background-clip:text; color:transparent | TEXT: "e"
      ↳ animation: opacity: 0.84→1.644s 0→1 (E3); offsetY: base: 0.84→1.644s 100→-40 (E5) | add: 2.891→3.891s 0→400 (E4)
  - [e9] text pos(1307.47,125) size 215×333 | white-space:pre-wrap; text-align:center; text-box:trim-both cap alphabetic; font-family:'Unbounded'; font-weight:300; font-size:444.23px; line-height:1.2; letter-spacing:-0.06em; background:linear-gradient(180deg, #e4ffef 0%, #89998f 54.89%, #000000 100%); -webkit-background-clip:text; color:transparent | TEXT: "t"
      ↳ animation: opacity: 0.9→1.706s 0→1 (E3); offsetY: base: 0.9→1.706s 100→-40 (E5) | add: 2.964→3.964s 0→400 (E4)

#### Layer bk6 «Background Images» — CONTAINED LAYER (contain, anchor x=0, y=1), design box x=-237.27 y=535.43 1914.54×352.31
- [e10] group «Background Images»
  - [e11] box «Background Image» transform translate(1670.65,543.43) rotate(3.12rad) scale(1,-1) size 1274.25×317.88
      ↳ animation: offsetX: add: 0→2s 1100→0 (E1) | add: 2.86→3.86s 0→1100 (E4)
    - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/secret/assets/img/2d6e5cec2a-9bc7eb.webp: absolutely positioned `<img alt="">` at (0,0), 2374×800 px, max-width:none, transform-origin 0 0 and transform matrix(0.5365,0,0,0.4666,0,-38.576); the container clips it (overflow hidden, same border-radius)
  - [e12] box «Background Image» transform translate(-222.17,535.43) rotate(0.05rad) size 1108.3×296.19
      ↳ animation: offsetX: add: 0→2s -1100→0 (E1) | add: 2.86→3.86s 0→-888 (E4)
    - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/secret/assets/img/2d6e5cec2a-9bc7eb.webp: absolutely positioned `<img alt="">` at (0,0), 2374×800 px, max-width:none, transform-origin 0 0 and transform matrix(0.4666,0,0,0.4666,0,-38.576); the container clips it (overflow hidden, same border-radius)

#### Layer bk7 «Footer» — CONTAINED LAYER (contain, anchor x=0, y=1), design box x=41 y=776 1359×20
- [e13] box «Footer» pos(41,776) size 1359×20
    ↳ animation: offsetY: add: 2.626→3.626s 0→200 (E4)
  - [-] list <nav> aria-label="Sections"
    - [-] list <ul>
      - [e14] box <li> «Footer Section» pos(0,0) size 207×20
          ↳ animation: opacity: 0.8→1.2s 0→1 (E5); offsetY: 0.8→1.2s 20→0 (E5)
        - [-] link <a href="#"> that covers its container's whole box (inset:0)
          - [e15] text pos(0,0) size 28×20 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Onest'; font-weight:100; font-size:28px; line-height:normal; text-transform:uppercase; color:#e4ffef | TEXT: "01"
          - [e16] text pos(53,0) size 154×20 | opacity:0.5; white-space:pre-wrap; text-align:left; text-box:trim-both cap alphabetic; font-family:'Onest'; font-weight:400; font-size:10px; line-height:1.3; color:#e4ffef | TEXT: "Discover the ancient torii gates hidden within sacred pathways. "
      - [e17] box <li> «Footer Section» pos(367.67,0) size 228×20
          ↳ animation: opacity: 0.9→1.3s 0→1 (E5); offsetY: 0.9→1.3s 20→0 (E5)
        - [-] link <a href="#"> that covers its container's whole box (inset:0)
          - [e18] text pos(0,0) size 34×20 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Onest'; font-weight:100; font-size:28px; line-height:normal; text-transform:uppercase; color:#e4ffef | TEXT: "02"
          - [e19] text pos(59,0) size 169×20 | opacity:0.5; white-space:pre-wrap; text-align:left; text-box:trim-both cap alphabetic; font-family:'Onest'; font-weight:400; font-size:10px; line-height:1.3; color:#e4ffef | TEXT: "Witness the serene gardens where cherry blossoms gently unfold. "
      - [e20] box <li> «Footer Section» pos(756.33,0) size 215×20
          ↳ animation: opacity: 1→1.4s 0→1 (E5); offsetY: 1→1.4s 20→0 (E5)
        - [-] link <a href="#"> that covers its container's whole box (inset:0)
          - [e21] text pos(0,0) size 36×20 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Onest'; font-weight:100; font-size:28px; line-height:normal; text-transform:uppercase; color:#e4ffef | TEXT: "03"
          - [e22] text pos(61,0) size 154×20 | opacity:0.5; white-space:pre-wrap; text-align:left; text-box:trim-both cap alphabetic; font-family:'Onest'; font-weight:400; font-size:10px; line-height:1.3; color:#e4ffef | TEXT: "Embrace the tranquil spirit of temples bathed in morning light. "
      - [e23] box <li> «Footer Section» pos(1132,0) size 227×20
          ↳ animation: opacity: 1.1→1.5s 0→1 (E5); offsetY: 1.1→1.5s 20→0 (E5)
        - [-] link <a href="#"> that covers its container's whole box (inset:0)
          - [e24] text pos(0,0) size 36×20 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Onest'; font-weight:100; font-size:28px; line-height:normal; text-transform:uppercase; color:#e4ffef | TEXT: "04"
          - [e25] text pos(61,0) size 166×20 | opacity:0.5; white-space:pre-wrap; text-align:left; text-box:trim-both cap alphabetic; font-family:'Onest'; font-weight:400; font-size:10px; line-height:1.3; color:#e4ffef | TEXT: "Explore the mystical shrines adorned with lanterns and incense. "

#### Layer bk8 «Vertical Text» — CONTAINED LAYER (contain, anchor x=1, y=-1), design box x=1400 y=114 10×247
- [e26] text pos(1400,114) size 10×247 | white-space:pre; text-align:center; text-box:trim-both cap alphabetic; font-family:'Onest'; font-weight:400; font-size:10px; line-height:4.02; color:#e4ffef | TEXT: "ま
る
そ
の
瞬
間
つ"
    ↳ animation: offsetX: add: 0.593→1.5s 48→0 (E6)

#### Layer bk9 «Container» — CONTAINED LAYER (contain, anchor x=1, y=0), design box x=1045 y=246 240×128
- [e27] box «Container» pos(1045,246) size 240×128 | overflow:hidden
    ↳ animation: opacity: base: 0.7→2s 0→1 (E5) | multiply: 2.86→3.71s 1→0 (E4); offsetY: base: 0.7→2s -30→0 (E5) | add: 2.626→3.626s 0→-80 (E4)
  - [e28] box «Right Image» transform translate(-42,-15) rotate(0rad) size 329×184
      ↳ animation: opacity: 0.3→1.2s 0→1 (E5); offsetY: 0.3→1.2s -20→0 (E5); scaleX: 0.3→1.3s 0.95→1 (E5); scaleY: 0.3→1.3s 0.95→1 (E5) [rotation/scale around the box center]
    - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/secret/assets/img/57315fc752.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)
  - [e29] vector «Vector 1» transform translate(102,36.87) rotate(0rad) size 37.23×50.13
      ↳ animation: opacity: 1.08→1.773s 0→1 (E5); offsetY: 1.08→1.86s -15→0 (E5); scaleX: 1.08→1.86s 0.8→1 (E8); scaleY: 1.08→1.86s 0.8→1 (E8) [rotation/scale around the box center]
    - vector drawing SVG-1 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box

#### Layer bk10 «Container» — CONTAINED LAYER (contain, anchor x=-1, y=-1), design box x=52 y=160 285×201
- [e30] group «Container»
  - [e31] box «Frame 1597882320» pos(52,301) size 210×60 | overflow:hidden
    - [e32] text pos(0,0) size 210×60 | opacity:0.8; white-space:pre-wrap; text-align:left; font-family:'Onest'; font-weight:300; font-size:14px; line-height:1.4; color:#e4ffef | TEXT: "Inhabit a serene, contemplative realm named Kyoto, designed for grace and balance."
        ↳ animation: offsetY: add: 0.5→2s 64→0 (E9) | add: 2.626→3.554s 0→100 (E4)
  - [e33] box «Frame 1597882319» pos(52,160) size 285×108 | overflow:hidden
    - [e34] text pos(0,0) size 285×108 | white-space:pre-wrap; text-align:left; font-family:'Onest'; font-weight:400; font-size:28px; line-height:normal; text-transform:uppercase; color:#e4ffef | TEXT: "Exploring ancient pathways for deep wisdom."
        ↳ animation: offsetY: add: 0.5→2s -104→0 (E10) | add: 2.626→3.554s 0→-100 (E4)

#### Layer bk11 «Header» — CONTAINED LAYER (contain, anchor x=0, y=-1), design box x=24 y=18 1392×50
- [e35] group «Header»
  - [e36] box <a href="#"> aria-label="Secret — Home" «Logo» pos(24,18) size 55×50
      ↳ animation: opacity: 0→0.4s 0→1 (E11); offsetY: 0→0.4s -25→0 (E11)
    - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/secret/assets/img/72bef4076d.webp: absolutely positioned `<img alt="">` at (0,0), 658×902 px, max-width:none, transform-origin 0 0 and transform matrix(0.6089,0,0,0.6009,-218.622,-430.409); the container clips it (overflow hidden, same border-radius)
  - [e37] group <a href="#"> aria-label="Menu" «Menu Icon»
      ↳ animation: opacity: 0.48→0.88s 0→1 (E11); offsetY: 0.48→0.88s -25→0 (E11)
    - [e38] vector «Line 4» pos(1384.51,36) size 15.74×1
      - vector drawing SVG-2 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
    - [e39] vector «Line 2» pos(1384.51,42.47) size 31.49×1
      - vector drawing SVG-3 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
    - [e40] vector «Line 3» pos(1400.26,48.94) size 15.74×1
      - vector drawing SVG-2 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
  - [e41] box «Nav Bar» pos(444.08,40) size 552×7
    - [-] list <nav> aria-label="Main navigation"
      - [-] list <ul>
        - (list of 5 <li> elements with the SAME inner structure; the first one is fully detailed as the template and the others only state what changes)
        - [e42] text <li> pos(0,0) size 44×7 | white-space:pre; text-align:right; text-box:trim-both cap alphabetic; font-family:'Onest'; font-weight:500; font-size:10px; line-height:normal; text-transform:uppercase; color:#e4ffef | TEXT: "Shrines" | inline link: the text is the content of an <a href="#"> inside the element
            ↳ animation: opacity: 0.08→0.48s 0→1 (E11); offsetY: 0.08→0.48s -25→0 (E11)
        - [e43] <li> card same as the template | pos(114,0) size 68×7 | texts: "Landscapes"
            ↳ card animation: opacity: 0.16→0.56s 0→1 (E11); offsetY: 0.16→0.56s -25→0 (E11)
        - [e44] <li> card same as the template | pos(252,0) size 59×7 | texts: "Treasures"
            ↳ card animation: opacity: 0.24→0.64s 0→1 (E11); offsetY: 0.24→0.64s -25→0 (E11)
        - [e45] <li> card same as the template | pos(381,0) size 53×7 | texts: "Lanterns"
            ↳ card animation: opacity: 0.32→0.72s 0→1 (E11); offsetY: 0.32→0.72s -25→0 (E11)
        - [e46] <li> card same as the template | pos(504,0) size 48×7 | texts: "Eternity"
            ↳ card animation: opacity: 0.4→0.8s 0→1 (E11); offsetY: 0.4→0.8s -25→0 (E11)

#### Layer bk12 «Step into the serene and meditative realm of Edo, a land where discipline and grace converge in perfect balance. Here, every moment calls you to reflect and embrace the wisdom of nature and the mastery of ancient martial arts. Picture yourself walking through bamboo forests, where gentle winds whisper in the leaves, and sacred shrines stand as guardians of a proud warrior legacy. In Edo, life finds rhythm, guiding you to discover yourself in a tranquil harmony that strengthens the mind and nurtures deep inner calm.» — CONTAINED LAYER (contain, anchor x=-1, y=0), design box x=393 y=213 233×340
- [e47] text pos(393,213) size 233×340 | opacity:0.8; white-space:pre-wrap; text-align:left; font-family:'Onest'; font-weight:300; font-size:14px; line-height:1.4; color:#e4ffef | TEXT: "Step into the serene and meditative realm of Edo, a land where discipline and grace converge in perfect balance. Here, every moment calls you to reflect and embrace the wisdom of nature and the mastery of ancient martial arts. Picture yourself walking through bamboo forests, where gentle winds whisper in the leaves, and sacred shrines stand as guardians of a proud warrior legacy. In Edo, life finds rhythm, guiding you to discover yourself in a tranquil harmony that strengthens the mind and nurtures deep inner calm."
    ↳ animation: opacity: multiply: 3.796→4.071s 0→1 (linear); offsetY: add: 3.796→4.796s 60→0 (E13)

#### Layer bk13 «Unveiling mystical temples for pure spirit.» — CONTAINED LAYER (contain, anchor x=-1, y=-1), design box x=82 y=141 263×144
- [e48] text pos(82,141) size 263×144 | white-space:pre-wrap; text-align:left; font-family:'Onest'; font-weight:400; font-size:28px; line-height:normal; text-transform:uppercase; color:#e4ffef | TEXT: "Unveiling mystical temples for pure spirit."
    ↳ animation: opacity: multiply: 3.689→3.964s 0→1 (linear); offsetY: add: 3.689→4.689s 60→0 (E13)

#### Layer bk14 «Container-cards» — CONTAINED LAYER (contain, anchor x=1, y=1), design box x=835 y=482 635.09×280.25
- [e49] group «Container-cards»
  - [-] list <ul>
    - [e50] box <li> «Frame 11» transform translate(835,482) rotate(0rad) size 235×280.2 | overflow:hidden; background-color:#dee9f0
        ↳ animation: opacity: multiply: 3.689→3.955s 0→1 (E14); offsetY: add: 3.689→4.354s 100→0 (E15); scaleX: multiply: 3.689→4.208s 0.6→1 (E16); scaleY: multiply: 3.689→4.208s 0.6→1 (E16) [rotation/scale around the box center]
      - [-] link <a href="#"> that covers its container's whole box (inset:0)
        - [e51] box «Right Image» pos(-189,-30) size 601×337
          - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/secret/assets/img/57315fc752.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)
        - [e52] box «Rectangle 7» pos(-1,116) size 237×164 | opacity:0.7; background:linear-gradient(180deg, rgba(0,0,0,0) 0%, #000000 100%)
        - [e53] text pos(15,234) size 111×26 | white-space:pre; text-align:left; font-family:'Onest'; font-weight:400; font-size:20px; line-height:normal; text-transform:uppercase; color:#e4ffef | TEXT: "Exploring"
        - [e54] group «Group 29»
          - [e55] group «Group 26»
            - [e56] vector «Line 10» pos(207.42,11.42) size 16.86×16.86
              - vector drawing SVG-4 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
            - [e57] vector «Line 12» pos(200.73,11.42) size 22.85×1
              - vector drawing SVG-5 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
            - [e58] vector «Line 11» pos(223.58,11.42) size 1×22.85
              - vector drawing SVG-6 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
    - [e59] box <li> «Frame 12» transform translate(1090,520) rotate(0rad) size 203.04×242.1 | overflow:hidden; background-color:#dee9f0
        ↳ animation: opacity: multiply: 3.796→4.062s 0→1 (E14); offsetY: add: 3.796→4.462s 100→0 (E15); scaleX: multiply: 3.796→4.315s 0.6→1 (E16); scaleY: multiply: 3.796→4.315s 0.6→1 (E16) [rotation/scale around the box center]
      - [-] link <a href="#"> that covers its container's whole box (inset:0)
        - [e60] box «Right Image» pos(-163.3,-25.92) size 519.71×290.93
          - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/secret/assets/img/08643cab4d.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)
        - [e61] box «Rectangle 7» pos(-17,110) size 237×132 | opacity:0.7; background:linear-gradient(180deg, rgba(0,0,0,0) 0%, #000000 100%)
        - [e62] text pos(12.96,202.18) size 96×22 | white-space:pre; text-align:left; font-family:'Onest'; font-weight:400; font-size:17.28px; line-height:normal; text-transform:uppercase; color:#e4ffef | TEXT: "Exploring"
        - [e63] group «Group 29»
          - [e64] group «Group 26»
            - [e65] vector «Line 10» pos(179.22,9.87) size 14.57×14.57
              - vector drawing SVG-7 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
            - [e66] vector «Line 12» pos(173.44,9.87) size 19.74×0.86
              - vector drawing SVG-8 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
            - [e67] vector «Line 11» pos(193.17,9.87) size 0.86×19.74
              - vector drawing SVG-9 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
    - [e68] box <li> «Frame 13» transform translate(1313.04,575) rotate(0rad) size 157.04×187.25 | overflow:hidden; background-color:#dee9f0
        ↳ animation: opacity: multiply: 3.89→4.156s 0→1 (E14); offsetY: add: 3.89→4.555s 100→0 (E15); scaleX: multiply: 3.89→4.409s 0.6→1 (E16); scaleY: multiply: 3.89→4.409s 0.6→1 (E16) [rotation/scale around the box center]
      - [-] link <a href="#"> that covers its container's whole box (inset:0)
        - [e69] box «Right Image» pos(-126.3,-20.05) size 401.97×225.02
          - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/secret/assets/img/04cb002912.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)
        - [e70] box «Rectangle 7» pos(-40.04,64) size 237×123 | opacity:0.7; background:linear-gradient(180deg, rgba(0,0,0,0) 0%, #000000 100%)
        - [e71] text pos(10.02,156.38) size 75×17 | white-space:pre; text-align:left; font-family:'Onest'; font-weight:400; font-size:13.36px; line-height:normal; text-transform:uppercase; color:#e4ffef | TEXT: "Exploring"
        - [e72] group «Group 29»
          - [e73] group «Group 26»
            - [e74] box «Line 10» transform translate(138.85,18.66) rotate(-0.78rad) scale(1,-1) size 15.27×0.67 | background-color:#e4ffef
            - [e75] box «Line 12» transform translate(134.14,7.96) rotate(0rad) scale(1,-1) size 15.27×0.67 | background-color:#e4ffef
            - [e76] box «Line 11» transform translate(149.74,22.90) rotate(-1.57rad) scale(1,-1) size 15.27×0.67 | background-color:#e4ffef

#### Layer bk15 «arrow» — CONTAINED LAYER (contain, anchor x=-1, y=0), design box x=257 y=389 39×39
- [e77] group <a href="#"> aria-label="Explore" «arrow»
    ↳ animation: opacity: multiply: 3.796→4.071s 0→1 (linear); offsetY: add: 3.796→4.796s 60→0 (E13)
  - [e78] group «Group 26»
    - [e79] vector «Line 10» pos(268.42,400.42) size 16.86×16.86
      - vector drawing SVG-4 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
    - [e80] vector «Line 12» pos(261.73,400.42) size 22.85×1
      - vector drawing SVG-5 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
    - [e81] vector «Line 11» pos(284.58,400.42) size 1×22.85
      - vector drawing SVG-6 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box

## 10. Final adjustments
No additional adjustments: everything is described in the previous sections.

## 11. Mobile view
- It activates when the stage width is < 768 px or the width/height ratio is < 0.82 (add the class `is-m` to `<html>`). Mobile reference design: 390×844 px.
- sm = min(W/390, H/844); ox = (W − 390·sm)/2; oy = (H − 844·sm)/2.
- Listed contained layer with {x, y, z, ax, ay}: its scale is sm·z (z = 1 if not given) and the top-left corner of its «design box» is placed on screen at (ox·(1+ax) + x·sm, oy·(1+ay) + y·sm) (ax, ay = 0 if not given); the rest of its content keeps its position relative to that box.
- Bleed layer: z = max(W/1440, H/810)·(given z or 1); screen = ((W − 1440·z)·fx + X·z, (H − 810·z)·fy + Y·z), with fx, fy = 0.5 unless another value is given.
- Contained layers that are not in the list are hidden on mobile.
- Per-element adjustments: «hide» = `display:none`; «shift dx, dy» = move the element by that distance in px of its container (if there is also «scale ×z», it is scaled from its top-left corner); «multiply its animated offsets by [kx, ky]» = multiply the total value of offsetX by kx and of offsetY by ky (the mobile «shift dx, dy» is not multiplied); «fade out between t1 and t2» = multiply its opacity by clamp((t2 − t)/(t2 − t1), 0, 1) from t1 on; «place the element point (rx, ry) at (mx, my) of the mobile design at scale e×sm» = draw the element at scale Se = e·sm with its top-left corner (animated x, y) at (ox + mx·sm + (x − rx)·Se, oy + my·sm + (y − ry)·Se); «width» = fixed width in px; «transition … between t1 and t2» = go from its layer's normal placement (the mobile one) to that placement with smoothstep interpolation (u²·(3 − 2u)) of scale and origin between those times.
- On mobile the track uses 80 vh per second (see section 7).
Mobile layer placement:
- layer «Header»: x=16, y=16, z=1
- layer bk10 «Container»: x=24, y=236, z=0.92
- layer bk9 «Container»: x=126, y=446, z=1
- layer «Main Title»: x=6.12, y=578, z=0.25
- layer «Background Images»: x=-283.62, y=672, z=0.5, ay=1
- layer «Footer»: x=16, y=812, z=0.8, ay=1
- layer «Vertical Text»: x=368, y=120, z=1
- layer «Unveiling mystical temples for pure spirit.»: x=24, y=104, z=1
- layer «Step into the serene and meditative realm of Edo, a land where discipline and grace converge in perfect balance. Here, every moment calls you to reflect and embrace the wisdom of nature and the mastery of ancient martial arts. Picture yourself walking through bamboo forests, where gentle winds whisper in the leaves, and sacred shrines stand as guardians of a proud warrior legacy. In Edo, life finds rhythm, guiding you to discover yourself in a tranquil harmony that strengthens the mind and nurtures deep inner calm.»: x=24, y=262, z=0.95
- layer «arrow»: x=320, y=112, z=1
- layer «Container-cards»: x=20.38, y=622, z=0.55, ay=1
Unlisted non-bleed layers are hidden on mobile.
Per-element mobile adjustments:
- [e41] «Nav Bar»: hide
- [e37] «Menu Icon»: shift dx=-1034, dy=0 (design units of its layer)
- [e20] «Footer Section»: hide
- [e23] «Footer Section»: hide
- [e17] «Footer Section»: shift dx=-147.7, dy=0 (design units of its layer)
- [e4] «S»: multiply its animated offsets by [1,3]
- [e5] «e»: multiply its animated offsets by [1,3]
- [e6] «c»: multiply its animated offsets by [1,3]
- [e7] «r»: multiply its animated offsets by [1,3]
- [e8] «e»: multiply its animated offsets by [1,3]
- [e9] «t»: multiply its animated offsets by [1,3]

## 12. Semantics and accessibility
- `<main class="hero" aria-label="Secret — Kyoto">`; the loader has `aria-hidden="true"`.
- Lists with `<ul>` and `<li>`; menus inside `<nav aria-label="…">`. Each `<a href="#">` uses the given `aria-label` when its content is not text. If a link covers a whole box, it goes inside it as an absolute `<a class="lk">` covering it (`inset:0`).
- Decorative images with `alt=""`; decorative SVGs with `aria-hidden="true"`.
- Only texts and links receive pointer events (CSS in section 3) and invisible elements get `visibility:hidden`, so nothing that cannot be seen can ever be clicked.
- Visible focus: `outline: 2px solid currentColor; outline-offset: 3px`.

## 13. Resources (absolute URLs)
All resources are hosted in the public repository `danielsnows/F4M-Bluebird` and served by jsDelivr (CDN with CORS enabled):
- `assets/fonts/Onest.woff2` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/secret/assets/fonts/Onest.woff2
- `assets/fonts/Unbounded-300.woff2` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/secret/assets/fonts/Unbounded-300.woff2
- `assets/img/04cb002912.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/secret/assets/img/04cb002912.webp
- `assets/img/08643cab4d.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/secret/assets/img/08643cab4d.webp
- `assets/img/2d6e5cec2a-9bc7eb.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/secret/assets/img/2d6e5cec2a-9bc7eb.webp
- `assets/img/435caeb929.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/secret/assets/img/435caeb929.webp
- `assets/img/57315fc752.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/secret/assets/img/57315fc752.webp
- `assets/img/72bef4076d.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/secret/assets/img/72bef4076d.webp
- `assets/video/078efbe522.jpg` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/secret/assets/video/078efbe522.jpg
- `assets/video/078efbe522.mp4` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/secret/assets/video/078efbe522.mp4

## 14. Appendix: vector shapes
Each SVG fills 100% of its element's box (`position:absolute; inset:0; width:100%; height:100%; overflow:visible`, `preserveAspectRatio="none"`, `aria-hidden="true"`). If an element says «with fill #xxxxxx (instead of #yyyyyy)», use the same SVG changing that fill color. If an SVG with internal `id`s (masks, gradients) is used more than once, give each copy unique ids and update its `url(#…)` references.

**SVG-1**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 38 51" xmlns="http://www.w3.org/2000/svg"> <path d="M2 50.1282V4.12817L34 29.4029L14.8 44.5677" stroke="white" stroke-width="4"></path> </svg>
```

**SVG-2**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 16 1" xmlns="http://www.w3.org/2000/svg"> <line stroke="#E4FFEF" x1="-4.37114e-08" x2="15.745" y1="0.5" y2="0.499999"></line> </svg>
```

**SVG-3**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 32 1" xmlns="http://www.w3.org/2000/svg"> <line stroke="#E4FFEF" x1="-4.37114e-08" x2="31.49" y1="0.5" y2="0.499997"></line> </svg>
```

**SVG-4**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 17 17" xmlns="http://www.w3.org/2000/svg"> <line stroke="#E4FFEF" transform="matrix(0.707107 -0.707107 -0.707107 -0.707107 0 16.1543)" x2="22.8456" y1="-0.5" y2="-0.5"></line> </svg>
```

**SVG-5**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 23 1" xmlns="http://www.w3.org/2000/svg"> <line stroke="#E4FFEF" transform="matrix(1 2.52881e-07 2.52881e-07 -1 0 0)" x2="22.8456" y1="-0.5" y2="-0.5"></line> </svg>
```

**SVG-6**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 1 23" xmlns="http://www.w3.org/2000/svg"> <line stroke="#E4FFEF" transform="matrix(1.68587e-07 -1 -1 -1.68587e-07 0 22.8457)" x2="22.8456" y1="-0.5" y2="-0.5"></line> </svg>
```

**SVG-7**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 15 15" xmlns="http://www.w3.org/2000/svg"> <line stroke="#E4FFEF" stroke-width="0.864017" transform="matrix(0.707107 -0.707107 -0.707107 -0.707107 0 13.9575)" x2="19.739" y1="-0.432008" y2="-0.432008"></line> </svg>
```

**SVG-8**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 20 1" xmlns="http://www.w3.org/2000/svg"> <line stroke="#E4FFEF" stroke-width="0.864017" transform="matrix(1 2.52881e-07 2.52881e-07 -1 0 0)" x2="19.739" y1="-0.432008" y2="-0.432008"></line> </svg>
```

**SVG-9**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 1 20" xmlns="http://www.w3.org/2000/svg"> <line stroke="#E4FFEF" stroke-width="0.864017" transform="matrix(1.68587e-07 -1 -1 -1.68587e-07 0 19.739)" x2="19.739" y1="-0.432008" y2="-0.432008"></line> </svg>
```

## 15. Acceptance criteria
- Compared with https://secret-snows.vercel.app, the result matches at 1440×810, 1280×720 and 390×844 at the key moments: end of the intro, every pause and the final state.
- Positions, sizes, colors, fonts, timings and curves match this specification.
- All texts are real, editable HTML; all links are clickable `<a href="#">` with visible focus; invisible things cannot be clicked.
- Images, video and fonts load from the URLs in section 13 and there are no console errors.
- The layout adapts when the window is resized and follows section 11 on mobile.
- With `prefers-reduced-motion` the final state is shown directly.
