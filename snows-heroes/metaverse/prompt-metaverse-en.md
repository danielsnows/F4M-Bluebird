# Replication prompt — Runshift (Metaverse)

> Complete instructions for an AI coding agent. It contains everything needed to rebuild the animated hero «Runshift (Metaverse)» identically: structure, styles, texts, animation timings and curves, mobile view and links to the resources (images, video and fonts).

## Role and goal
Act as a senior front-end developer specialized in web animation. Build a single-screen page (hero) that exactly reproduces the animated design «Runshift (Metaverse)» described here, without adding or removing anything.
Published reference version: https://metaverse-snows.vercel.app — use it to compare your result visually.

## 1. Overview
Video game hero «Runshift — Vibrant Metaverse», designed at 1440×810. Behind everything, full-bleed, a scroll-driven video of an animated runner. The automatic intro (0 → 1.75 s) brings in the navigation bar (Runshift logo, Home, Worlds, Exploration, Characters, Journey, Connect, «Enter» button and menu button), the first slide «Endless Worlds» («Open worlds» tag, text and «Explore Now» button), the four round thumbnails at the bottom and the «3.2M» counter. Scrolling goes through four slides that alternate left/right: Endless Worlds → Hero Gear → Daily Runs → Squad Mode; the thumbnail of the active slide grows.

## 2. Deliverable and technical rules
- A single `index.html` file with the CSS inside `<style>` and the JavaScript inside `<script>`. Plain (vanilla) JavaScript, no frameworks and no build step.
- The only external dependency: Lenis 1.3.26 for smooth scrolling: `<script src="https://cdn.jsdelivr.net/npm/lenis@1.3.26/dist/lenis.min.js"></script>` before your script (if it fails to load, everything must work with native scrolling).
- Images, video and fonts are ALWAYS loaded from the absolute URLs in section 13 «Resources». Do not download them, do not inline them as base64 and do not use any other resources.
- Every visible text is real, selectable and editable HTML text (never text turned into an image or into SVG paths). The vector shapes in the Appendix are only icons and decoration.
- Lists use `<ul>`/`<li>` (inside `<nav aria-label="…">` where indicated) and links use clickable `<a href="#">`, with `aria-label` where indicated.
- Head: `<html lang="en">`, `<meta charset="utf-8">`, `<meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover">`, `<title>Runshift — Vibrant Metaverse</title>`, `<meta name="description" content="Runshift: race through ever-changing landscapes. Endless worlds, hero gear, daily runs and squad mode.">`, `<meta name="theme-color" content="#292929">`.
- Respect `prefers-reduced-motion: reduce`: the loader works the same, but there is no intro, no smooth scrolling and no snap; t stays fixed on the final state (t = 12.08 s) even when scrolling.
- Do not add elements, texts, effects or sections that are not in this specification.

## 3. Base structure (common HTML + CSS)
```html
<body>
<div class="loader" id="loader" aria-hidden="true">
  <div class="loader-in"><span>RUNSHIFT</span><div class="loader-bar"><i id="loaderBar"></i></div><span id="loaderPct">000</span></div>
</div>
<main class="hero" id="hero" aria-label="Runshift — Vibrant Metaverse">
  <div class="stage" id="stage">
    <!-- one layer (div.blk) per «Layer» in section 9, in the same order: later ones are on top -->
  </div>
</main>
<script>/* engine from section 6 */</script>
</body>
```
```css
:root{--bg:#292929;--ink:#ffffff;color-scheme:dark}
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
- **Epilogue** (variable font, weight range 100–900 (font-weight:100 900)): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/metaverse/assets/fonts/Epilogue.woff2
- **Gantari** (variable font, weight range 100–900 (font-weight:100 900)): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/metaverse/assets/fonts/Gantari.woff2
- **Lexend Mega** (variable font, weight range 100–900 (font-weight:100 900)): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/metaverse/assets/fonts/LexendMega.woff2
- **Special Gothic Expanded One** (weight 400): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/metaverse/assets/fonts/SpecialGothicExpandedOne.woff2

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
  - `E0` = cubic-bezier(0.5, 0, 0.5, 1)
  - `E1` = cubic-bezier(0.25, 0.1, 0.25, 1)
  - `E3` = cubic-bezier(0, 0, 0.58, 1)
  - `E4` = cubic-bezier(0.16, 1, 0.3, 1)
  - `E5` = cubic-bezier(0, 1.0712, 1, 1)
  - `E6` = cubic-bezier(1, 0, 0, 1)
  - `E7` = cubic-bezier(1, -0.0088, 1, 1)
  - `E8` = cubic-bezier(1, 0.0168, 1, 1)
  - `E9` = cubic-bezier(0.22, 1, 0.36, 1)
- `cubic-bezier(x1, y1, x2, y2)` curves work as in CSS: given the time progress p, solve the parameter u such that X(u) = p and return Y(u).

## 7. Playback (intro and scrolling)
- Total timeline duration: 12.08 s. Automatic intro: from t = 0 to t = 1.75 s. The rest (1.75 → 12.08 s) is driven by scrolling.
- `.hero` height = 100vh + track. track = round((12.08 − 1.75) × 34) = **351vh** on desktop and round((12.08 − 1.75) × 30) = **310vh** on mobile (use exactly these values; they are set through the CSS variable `--track`). `.stage` is sticky, so the stage stays fixed while the track is scrolled.
- While loading: `html.is-locked` (overflow hidden), `history.scrollRestoration = 'manual'`, `scrollTo(0,0)`.
- When the loader gets the `is-done` class, play the intro: t advances in real time (linearly) from 0 to 1.75 s in 1.75 s. During the intro scrolling is blocked (Lenis stopped; if Lenis is not available, keep `is-locked` until the intro ends) and scroll events do not change t. When it ends, t = 1.75 and scrolling is enabled.
- Smooth scrolling with Lenis: `new Lenis({lerp:0.085, wheelMultiplier:0.9, smoothWheel:true})`, `lenis.stop()` until the intro ends, then `lenis.start()`; call `lenis.raf(now)` in the requestAnimationFrame loop. Without Lenis, use native scrolling.
- Scroll → time mapping: p = clamp((scrollY − hero.offsetTop) / (hero.offsetHeight − stage.clientHeight), 0, 1); t = 1.75 + p × (12.08 − 1.75).
- Pauses (holds), in seconds: [1.75 – 1.83], [3.87 – 5.75], [7.61 – 9.65], [11.68 – 12.08]. Snap (Lenis only; no snap without Lenis): the direction is the sign of the last scroll change; 170 ms after the last scroll event (every event restarts the timer), if t is not inside a pause (with a ±0.02 s margin), scroll with `lenis.scrollTo` to the start of the next pause when scrolling down, or to the end of the previous pause when scrolling up. Duration = clamp(|Δt| × 0.45, 0.6, 1.8) s; easing easeInOutCubic (x < 0.5 ? 4x³ : 1 − (−2x+2)³/2). No snap with `prefers-reduced-motion` or during another snap (the «snapping» state is released in `onComplete` or, as a safety net, after 2.2 s).
- On every resize: recompute layers, track and t from the current scroll position.
- Video («scrub» mode, driven by the timeline): `<video muted playsinline preload="auto">` with its poster; it never plays by itself. For reliable seeking, download the MP4 with `fetch` as a Blob (reporting progress to the loader) and set `video.src = URL.createObjectURL(blob)` (if that fails, use the direct URL). On every frame, if `readyState ≥ 2` and it is not `seeking`: target = min(max(0, t), duration − 0.04); if |currentTime − target| > 0.012, `currentTime = target`.

## 8. Loader and preloading
- The loader covers the screen with the `--bg` color and shows «RUNSHIFT», a 160×1 px bar and a 3-digit percentage (`000` → `100`) in the `--ink` color.
- Weighted progress: fonts (`document.fonts.ready`) weight 1; each `<img>` element on the stage weight 0.4, even if it repeats a file (it counts once it has loaded —or failed— and `img.decode()` has finished; the video poster does not count); the video weight 6 (its fetch download progress). Percentage = Math.round(progress × 100) with 3 digits. The bar uses `transform: scaleX(progress)` and never goes backwards.
- Wait until everything finishes (14 s maximum; if it runs out, continue without cancelling anything); then set the video `src` (the Blob URL if already downloaded, otherwise the direct URL) and wait for its `loadeddata` or `error` (3 s maximum). Then remove `is-locked` from `<html>` (except in the no-Lenis case described in section 7), add `is-done` to the loader (it fades out in 0.9 s) and start the intro.

## 9. Layers and elements
Notation of each line: `[eN]` = suggested identifier; `box` = div; `text` = div with text (the content in quotes, keeping capitalization, line breaks and spaces); `group` = boxless container; `vector` = box containing an inline SVG; `<a href="#">`, `<ul>`, `<li>`, `<nav>` = semantic tag to use; `«name»` = layer name in the design (for reference only, also handy as `data-name`); `pos(x,y)` = left/top; `size A×B` = width×height; after `|` come the literal CSS styles. Every `text` element gets `class="t"` (used by the pointer-events rule in section 3); boxes, groups and vectors may use `class="b"`, `"g"` and `"v"`. Decorative layers (border, glass, inner shadow, background blur, fill layer) are absolute elements (for example `<i>`) covering their container (given `inset`) with inline `pointer-events:none` and `border-radius:inherit` unless stated otherwise. Images go inside an absolute container `inset:0; overflow:hidden; border-radius:inherit`. The order of the lines is the stacking order (later is on top).

#### Layer bk0 «Fondo» — BLEED LAYER (cover), design box x=0 y=0 1440×810
- [e1] box «home» pos(0,0) size 1440×810 | background-color:#292929

#### Layer bk1 «ChatGPT Image 5 de ago. de 2026, 02_06_35 1» — BLEED LAYER (cover), design box x=0 y=0 1440×810.43
- [e2] box «ChatGPT Image 5 de ago. de 2026, 02_06_35 1» pos(0.38,0) size 1440×810.43
  - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/metaverse/assets/img/be4b7af277.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)

#### Layer bk2 «01 1» — BLEED LAYER (cover), design box x=0 y=0 1440×826.67
- [e3] box «01 1» pos(0,-8.33) size 1440×826.67
  - video: `<video muted playsinline preload="auto" poster="https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/metaverse/assets/video/618ec1861d.jpg" data-src="https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/metaverse/assets/video/618ec1861d.mp4">` with position:absolute; inset:0; width:100%; height:100%; object-fit:cover; display:block (the `src` is assigned from JavaScript once preloading finishes, see sections 7 and 8)

#### Layer bk3 «Rectangle 23» — BLEED LAYER (cover), design box x=0 y=0 1440×810
- [e4] box «Rectangle 23» pos(0,0) size 1440×810 | opacity:0.5; background:radial-gradient(813.99px 813.99px at 682.00px 505.42px, rgba(0,0,0,0.08) 34.2%, #000000 99.45%)

#### Layer bk4 «Frame 37» — CONTAINED LAYER (contain, anchor x=0, y=1), design box x=575 y=714.67 290×80
- [e5] box «Frame 37» pos(575,714.67) size 290×80
  - [-] list <ul>
    - [e6] box <li> «Ellipse 1» transform translate(0,0) rotate(0rad) size 80×80 | border-radius:50%
        ↳ animation: opacity: 0.2→0.6s 0→1 (E1); offsetY: 0.2→0.6s 30→0 (E1); scaleX: 2→2.4s 1→0.62 (E1); scaleY: 2→2.4s 1→0.62 (E1) [rotation/scale around the box center]
      - [-] link <a href="#"> aria-label="World 1" that covers its container's whole box (inset:0)
        - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/metaverse/assets/img/be4b7af277.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)
        - border (stroke): inset:0px; border-radius:50%; border:2px solid #ffffff
    - [e7] box <li> «Ellipse 2» transform translate(100,15) rotate(0rad) size 50×50 | border-radius:50%
        ↳ animation: opacity: 0.3→0.7s 0→1 (E1); offsetY: 0.3→0.7s 30→0 (E1); scaleX: 2→2.4s 1→1.6 (E1); 5.9→6.3s 1.6→1 (E1); scaleY: 2→2.4s 1→1.6 (E1); 5.9→6.3s 1.6→1 (E1) [rotation/scale around the box center]
      - [-] link <a href="#"> aria-label="World 2" that covers its container's whole box (inset:0)
        - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/metaverse/assets/img/0ce79d7996.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)
        - border (stroke): inset:0px; border-radius:50%; border:2px solid #ffffff
    - [e8] box <li> «Ellipse 3» transform translate(170,15) rotate(0rad) size 50×50 | border-radius:50%
        ↳ animation: opacity: 0.4→0.8s 0→1 (E1); offsetY: 0.4→0.8s 30→0 (E1); scaleX: 5.9→6.3s 1→1.6 (E1); 9.88→10.28s 1.6→1 (E1); scaleY: 5.9→6.3s 1→1.6 (E1); 9.88→10.28s 1.6→1 (E1) [rotation/scale around the box center]
      - [-] link <a href="#"> aria-label="World 3" that covers its container's whole box (inset:0)
        - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/metaverse/assets/img/628bb1563d.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)
        - border (stroke): inset:0px; border-radius:50%; border:2px solid #ffffff
    - [e9] box <li> «Ellipse 4» transform translate(240,15) rotate(0rad) size 50×50 | border-radius:50%
        ↳ animation: opacity: 0.5→0.9s 0→1 (E1); offsetY: 0.5→0.9s 30→0 (E1); scaleX: 9.88→10.28s 1→1.6 (E1); scaleY: 9.88→10.28s 1→1.6 (E1) [rotation/scale around the box center]
      - [-] link <a href="#"> aria-label="World 4" that covers its container's whole box (inset:0)
        - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/metaverse/assets/img/875ecd2bf9.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)
        - border (stroke): inset:0px; border-radius:50%; border:2px solid #ffffff

#### Layer bk5 «Group 15» — CONTAINED LAYER (contain, anchor x=1, y=1), design box x=1260 y=623 156×162.39
- [e10] group «Group 15»
  - [e11] group «Group 13»
    - [e12] group «Repeat group 1»
      - [e13] vector «Line 8» transform translate(1301.26,636.95) rotate(0rad) size 3.44×5.52 | opacity:0.4
          ↳ animation: opacity: 0.45→0.6s 0→1 (E3); scaleX: 0.45→0.7s 0.3→1 (E4); scaleY: 0.45→0.7s 0.3→1 (E4) [rotation/scale around the box center]
        - vector drawing SVG-1 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e14] vector «Line 8» transform translate(1291.38,644.29) rotate(0rad) size 2.89×3.47 | opacity:0.4
          ↳ animation: opacity: 0.47→0.62s 0→1 (E3); scaleX: 0.47→0.72s 0.3→1 (E4); scaleY: 0.47→0.72s 0.3→1 (E4) [rotation/scale around the box center]
        - vector drawing SVG-2 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e15] vector «Line 8» transform translate(1282.17,652.23) rotate(0rad) size 3.22×3.22 | opacity:0.4
          ↳ animation: opacity: 0.49→0.64s 0→1 (E3); scaleX: 0.49→0.74s 0.3→1 (E4); scaleY: 0.49→0.74s 0.3→1 (E4) [rotation/scale around the box center]
        - vector drawing SVG-3 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e16] vector «Line 8» transform translate(1274.33,661.54) rotate(0rad) size 3.47×2.89 | opacity:0.4
          ↳ animation: opacity: 0.51→0.66s 0→1 (E3); scaleX: 0.51→0.76s 0.3→1 (E4); scaleY: 0.51→0.76s 0.3→1 (E4) [rotation/scale around the box center]
        - vector drawing SVG-4 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e17] vector «Line 8» transform translate(1268.07,671.98) rotate(0rad) size 3.63×2.48 | opacity:0.4
          ↳ animation: opacity: 0.53→0.68s 0→1 (E3); scaleX: 0.53→0.78s 0.3→1 (E4); scaleY: 0.53→0.78s 0.3→1 (E4) [rotation/scale around the box center]
        - vector drawing SVG-5 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e18] vector «Line 8» transform translate(1263.52,683.29) rotate(0rad) size 3.71×2.02 | opacity:0.4
          ↳ animation: opacity: 0.55→0.7s 0→1 (E3); scaleX: 0.55→0.8s 0.3→1 (E4); scaleY: 0.55→0.8s 0.3→1 (E4) [rotation/scale around the box center]
        - vector drawing SVG-6 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e19] vector «Line 8» transform translate(1260.81,695.19) rotate(0rad) size 3.7×1.51 | opacity:0.4
          ↳ animation: opacity: 0.57→0.72s 0→1 (E3); scaleX: 0.57→0.82s 0.3→1 (E4); scaleY: 0.57→0.82s 0.3→1 (E4) [rotation/scale around the box center]
        - vector drawing SVG-7 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e20] vector «Line 8» transform translate(1260,707.39) rotate(0rad) size 3.59×0.96 | opacity:0.4
          ↳ animation: opacity: 0.59→0.74s 0→1 (E3); scaleX: 0.59→0.84s 0.3→1 (E4); scaleY: 0.59→0.84s 0.3→1 (E4) [rotation/scale around the box center]
        - vector drawing SVG-8 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e21] vector «Line 8» transform translate(1260.96,719.03) rotate(0rad) size 3.7×1.51 | opacity:0.4
          ↳ animation: opacity: 0.61→0.76s 0→1 (E3); scaleX: 0.61→0.86s 0.3→1 (E4); scaleY: 0.61→0.86s 0.3→1 (E4) [rotation/scale around the box center]
        - vector drawing SVG-9 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e22] vector «Line 8» transform translate(1263.82,730.38) rotate(0rad) size 3.71×2.02 | opacity:0.4
          ↳ animation: opacity: 0.63→0.78s 0→1 (E3); scaleX: 0.63→0.88s 0.3→1 (E4); scaleY: 0.63→0.88s 0.3→1 (E4) [rotation/scale around the box center]
        - vector drawing SVG-10 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e23] vector «Line 8» transform translate(1268.5,741.17) rotate(0rad) size 3.63×2.48 | opacity:0.4
          ↳ animation: opacity: 0.65→0.8s 0→1 (E3); scaleX: 0.65→0.9s 0.3→1 (E4); scaleY: 0.65→0.9s 0.3→1 (E4) [rotation/scale around the box center]
        - vector drawing SVG-11 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e24] vector «Line 8» transform translate(1274.9,751.13) rotate(0rad) size 3.47×2.89 | opacity:0.4
          ↳ animation: opacity: 0.67→0.82s 0→1 (E3); scaleX: 0.67→0.92s 0.3→1 (E4); scaleY: 0.67→0.92s 0.3→1 (E4) [rotation/scale around the box center]
        - vector drawing SVG-12 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e25] vector «Line 8» transform translate(1282.85,760) rotate(0rad) size 3.22×3.22 | opacity:0.4
          ↳ animation: opacity: 0.69→0.84s 0→1 (E3); scaleX: 0.69→0.94s 0.3→1 (E4); scaleY: 0.69→0.94s 0.3→1 (E4) [rotation/scale around the box center]
        - vector drawing SVG-13 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e26] vector «Line 8» transform translate(1292.15,767.59) rotate(0rad) size 2.89×3.47 | opacity:0.4
          ↳ animation: opacity: 0.71→0.86s 0→1 (E3); scaleX: 0.71→0.96s 0.3→1 (E4); scaleY: 0.71→0.96s 0.3→1 (E4) [rotation/scale around the box center]
        - vector drawing SVG-14 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e27] vector «Line 8» transform translate(1302.59,773.69) rotate(0rad) size 2.48×3.63 | opacity:0.4
          ↳ animation: opacity: 0.73→0.88s 0→1 (E3); scaleX: 0.73→0.98s 0.3→1 (E4); scaleY: 0.73→0.98s 0.3→1 (E4) [rotation/scale around the box center]
        - vector drawing SVG-15 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e28] vector «Line 8» transform translate(1313.9,778.16) rotate(0rad) size 2.02×3.71 | opacity:0.4
          ↳ animation: opacity: 0.75→0.9s 0→1 (E3); scaleX: 0.75→1s 0.3→1 (E4); scaleY: 0.75→1s 0.3→1 (E4) [rotation/scale around the box center]
        - vector drawing SVG-16 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e29] vector «Line 8» transform translate(1325.8,780.88) rotate(0rad) size 1.51×3.7 | opacity:0.4
          ↳ animation: opacity: 0.77→0.92s 0→1 (E3); scaleX: 0.77→1.02s 0.3→1 (E4); scaleY: 0.77→1.02s 0.3→1 (E4) [rotation/scale around the box center]
        - vector drawing SVG-17 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e30] vector «Line 8» transform translate(1338,781.8) rotate(0rad) size 0.96×3.59 | opacity:0.4
          ↳ animation: opacity: 0.79→0.94s 0→1 (E3); scaleX: 0.79→1.04s 0.3→1 (E4); scaleY: 0.79→1.04s 0.3→1 (E4) [rotation/scale around the box center]
        - vector drawing SVG-18 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e31] vector «Line 8» transform translate(1349.64,780.73) rotate(0rad) size 1.51×3.7 | opacity:0.4
          ↳ animation: opacity: 0.81→0.96s 0→1 (E3); scaleX: 0.81→1.06s 0.3→1 (E4); scaleY: 0.81→1.06s 0.3→1 (E4) [rotation/scale around the box center]
        - vector drawing SVG-19 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e32] vector «Line 8» transform translate(1360.99,777.86) rotate(0rad) size 2.02×3.71 | opacity:0.4
          ↳ animation: opacity: 0.83→0.98s 0→1 (E3); scaleX: 0.83→1.08s 0.3→1 (E4); scaleY: 0.83→1.08s 0.3→1 (E4) [rotation/scale around the box center]
        - vector drawing SVG-20 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e33] vector «Line 8» transform translate(1371.78,773.25) rotate(0rad) size 2.48×3.63 | opacity:0.4
          ↳ animation: opacity: 0.85→1s 0→1 (E3); scaleX: 0.85→1.1s 0.3→1 (E4); scaleY: 0.85→1.1s 0.3→1 (E4) [rotation/scale around the box center]
        - vector drawing SVG-21 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e34] vector «Line 8» transform translate(1381.74,767.02) rotate(0rad) size 2.89×3.47 | opacity:0.4
          ↳ animation: opacity: 0.87→1.02s 0→1 (E3); scaleX: 0.87→1.12s 0.3→1 (E4); scaleY: 0.87→1.12s 0.3→1 (E4) [rotation/scale around the box center]
        - vector drawing SVG-22 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e35] vector «Line 8» transform translate(1390.62,759.33) rotate(0rad) size 3.22×3.22 | opacity:0.4
          ↳ animation: opacity: 0.89→1.04s 0→1 (E3); scaleX: 0.89→1.14s 0.3→1 (E4); scaleY: 0.89→1.14s 0.3→1 (E4) [rotation/scale around the box center]
        - vector drawing SVG-23 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e36] vector «Line 8» transform translate(1398.2,750.35) rotate(0rad) size 3.47×2.89 | opacity:0.4
          ↳ animation: opacity: 0.91→1.06s 0→1 (E3); scaleX: 0.91→1.16s 0.3→1 (E4); scaleY: 0.91→1.16s 0.3→1 (E4) [rotation/scale around the box center]
        - vector drawing SVG-24 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e37] vector «Line 8» transform translate(1404.3,740.32) rotate(0rad) size 3.63×2.48 | opacity:0.4
          ↳ animation: opacity: 0.93→1.08s 0→1 (E3); scaleX: 0.93→1.18s 0.3→1 (E4); scaleY: 0.93→1.18s 0.3→1 (E4) [rotation/scale around the box center]
        - vector drawing SVG-25 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e38] vector «Line 8» transform translate(1408.77,729.47) rotate(0rad) size 3.71×2.02 | opacity:0.4
          ↳ animation: opacity: 0.95→1.1s 0→1 (E3); scaleX: 0.95→1.2s 0.3→1 (E4); scaleY: 0.95→1.2s 0.3→1 (E4) [rotation/scale around the box center]
        - vector drawing SVG-26 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e39] vector «Line 8» transform translate(1411.49,718.08) rotate(0rad) size 3.7×1.51 | opacity:0.4
          ↳ animation: opacity: 0.97→1.12s 0→1 (E3); scaleX: 0.97→1.22s 0.3→1 (E4); scaleY: 0.97→1.22s 0.3→1 (E4) [rotation/scale around the box center]
        - vector drawing SVG-27 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e40] vector «Line 8» transform translate(1412.41,706.43) rotate(0rad) size 3.59×0.96 | opacity:0.4
          ↳ animation: opacity: 0.99→1.14s 0→1 (E3); scaleX: 0.99→1.24s 0.3→1 (E4); scaleY: 0.99→1.24s 0.3→1 (E4) [rotation/scale around the box center]
        - vector drawing SVG-28 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e41] vector «Line 8» transform translate(1411.34,694.24) rotate(0rad) size 3.7×1.51 | opacity:0.4
          ↳ animation: opacity: 1.01→1.16s 0→1 (E3); scaleX: 1.01→1.26s 0.3→1 (E4); scaleY: 1.01→1.26s 0.3→1 (E4) [rotation/scale around the box center]
        - vector drawing SVG-29 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e42] vector «Line 8» transform translate(1408.47,682.37) rotate(0rad) size 3.71×2.02 | opacity:0.4
          ↳ animation: opacity: 1.03→1.18s 0→1 (E3); scaleX: 1.03→1.28s 0.3→1 (E4); scaleY: 1.03→1.28s 0.3→1 (E4) [rotation/scale around the box center]
        - vector drawing SVG-30 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e43] vector «Line 8» transform translate(1403.86,671.12) rotate(0rad) size 3.63×2.48 | opacity:0.4
          ↳ animation: opacity: 1.05→1.2s 0→1 (E3); scaleX: 1.05→1.3s 0.3→1 (E4); scaleY: 1.05→1.3s 0.3→1 (E4) [rotation/scale around the box center]
        - vector drawing SVG-31 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e44] vector «Line 8» transform translate(1397.64,660.77) rotate(0rad) size 3.47×2.89 | opacity:0.4
          ↳ animation: opacity: 1.07→1.22s 0→1 (E3); scaleX: 1.07→1.32s 0.3→1 (E4); scaleY: 1.07→1.32s 0.3→1 (E4) [rotation/scale around the box center]
        - vector drawing SVG-32 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e45] vector «Line 8» transform translate(1389.94,651.56) rotate(0rad) size 3.22×3.22 | opacity:0.4
          ↳ animation: opacity: 1.09→1.24s 0→1 (E3); scaleX: 1.09→1.34s 0.3→1 (E4); scaleY: 1.09→1.34s 0.3→1 (E4) [rotation/scale around the box center]
        - vector drawing SVG-33 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e46] vector «Line 8» transform translate(1380.96,643.72) rotate(0rad) size 2.89×3.47 | opacity:0.4
          ↳ animation: opacity: 1.11→1.26s 0→1 (E3); scaleX: 1.11→1.36s 0.3→1 (E4); scaleY: 1.11→1.36s 0.3→1 (E4) [rotation/scale around the box center]
        - vector drawing SVG-34 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e47] vector «Line 8» transform translate(1370.46,636.53) rotate(0rad) size 3.43×5.48 | opacity:0.4
          ↳ animation: opacity: 1.13→1.28s 0→1 (E3); scaleX: 1.13→1.38s 0.3→1 (E4); scaleY: 1.13→1.38s 0.3→1 (E4) [rotation/scale around the box center]
        - vector drawing SVG-35 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
    - [e48] group «Group 12»
        ↳ animation: opacity: 1→1.4s 0→1 (E3)
      - [e49] vector «Line 8» pos(1337.04,623) size 0.96×16.37
        - vector drawing SVG-36 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e50] vector «Line 8» pos(1324.18,626.1) size 2.85×12.2
        - vector drawing SVG-37 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e51] vector «Line 8» pos(1312.3,631.09) size 3.4×7.95
        - vector drawing SVG-38 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e52] vector «Line 8» pos(1359.4,630.8) size 3.39×7.93
        - vector drawing SVG-39 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e53] vector «Line 8» pos(1348.02,625.95) size 2.85×12.2
        - vector drawing SVG-40 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
  - [e54] text transform translate(1308.43,697.81) rotate(0rad) size 59×14 | white-space:pre; text-align:center; text-box:trim-both cap alphabetic; font-family:'Special Gothic Expanded One'; font-weight:400; font-size:19.17px; line-height:normal; letter-spacing:-0.01em; text-transform:uppercase; color:#ffffff | TEXT: "3.2M"
      ↳ animation: opacity: 0.8→1.2s 0→1 (E3); scaleX: 0.8→1.3s 0.5→1 (E4); scaleY: 0.8→1.3s 0.5→1 (E4) [rotation/scale around the box center]

#### Layer bk6 «Container» — CONTAINED LAYER (contain, anchor x=-1, y=0), design box x=54 y=173 506.94×410.77
- [e55] group «Container»
    ↳ animation: opacity: 0.2→0.59s 0→1 (E0); 2.204→2.306s 1→0 (E0); offsetX: 0.2→1.745s 359.61→20 (E5); 1.831→2.455s 20→-1666.59 (E6); offsetY: 0.2→1.745s 33.28→0 (E5); 1.831→2.455s 0→-117.36 (E6); scaleX: 0.2→1.745s 0.31→1 (E5); 1.831→2.455s 1→6.23 (E6); scaleY: 0.2→1.745s 0.31→1 (E5); 1.831→2.455s 1→6.23 (E6) [rotation/scale around the group origin [307.47,378.38] (transform-origin in px)]
  - [e56] text pos(54,399) size 313×75 | white-space:pre-wrap; text-align:left; font-family:'Epilogue'; font-weight:400; font-size:18px; line-height:1.4; color:#ffffff | TEXT: "Race through ever-changing landscapes that shift with every step you take."
  - [e57] box <a href="#"> «Button Container» pos(54,514) size 231.13×69.77
    - [e58] box «Text Button Container» pos(0,0) size 169.67×69.77 | border-radius:999px; background-color:#ffffff
      - [e59] text pos(26.83,27.38) size 116×15 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Epilogue'; font-weight:500; font-size:20px; line-height:normal; letter-spacing:-0.03em; color:#121212 | TEXT: "Explore Now"
    - [e60] box «Icon Button Container» pos(169.67,4.15) size 61.47×61.47 | border-radius:999px; background-color:rgba(0,0,0,0.01)
      - glass effect (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(2px) saturate(1.25)
      - inner shadow: inset:0; border-radius:inherit; box-shadow:inset 1.2px 0px 0 0 rgba(255,255,255,0.22),inset -1.2px 0px 0 0 rgba(255,255,255,0.07),inset 0 0 19.2px 0 rgba(255,255,255,0.07)
      - [e61] box «ArrowUpRight» pos(20,20) size 21.47×21.47
        - [e62] vector «Vector» pos(4.69,4.7) size 12.08×12.08
          - vector drawing SVG-41 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
  - [e63] text pos(54,217) size 506.94×142 | white-space:pre-wrap; text-align:left; font-family:'Special Gothic Expanded One'; font-weight:400; font-size:84px; line-height:0.84; letter-spacing:-0.01em; text-transform:uppercase; color:#ffffff | TEXT: "Endless Worlds"
  - [e64] box «Container» pos(54,173) size 107×24 | border-radius:999px
    - [e65] text pos(10,8) size 87×8 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Gantari'; font-weight:400; font-size:12px; line-height:1.4; text-transform:uppercase; color:#ffffff | TEXT: "Open worlds"
    - border (stroke): inset:0px; border-radius:999px; border:1px solid rgba(255,255,255,0.49)

#### Layer bk7 «Container» — CONTAINED LAYER (contain, anchor x=1, y=0), design box x=934.33 y=184 506.94×410.77
- [e66] group «Container»
    ↳ animation: opacity: 2.326→2.363s 0→1 (E0); offsetX: 2.326→3.871s -310.08→0 (E5); 5.754→6.652s 0→939.06 (E7); offsetY: 2.326→3.871s 32.59→0 (E5); 5.754→6.652s 0→-62.16 (E7); scaleX: 2.326→3.871s 0.18→1 (E5); 5.754→6.652s 1→2.55 (E8); scaleY: 2.326→3.871s 0.18→1 (E5); 5.754→6.652s 1→2.55 (E8) [rotation/scale around the group origin [1187.8,389.38] (transform-origin in px)]
  - [e67] text pos(934.33,410) size 313×75 | white-space:pre-wrap; text-align:left; font-family:'Epilogue'; font-weight:400; font-size:18px; line-height:1.4; color:#ffffff | TEXT: "Unlock powerful outfits and accessories to boost your runner's abilities."
  - [e68] box <a href="#"> «Button Container» pos(934.33,525) size 209.13×69.77
    - [e69] box «Text Button Container» pos(0,0) size 147.67×69.77 | border-radius:999px; background-color:#ffffff
      - [e70] text pos(26.83,27.38) size 94×15 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Epilogue'; font-weight:500; font-size:20px; line-height:normal; letter-spacing:-0.03em; color:#121212 | TEXT: "View Gear"
    - [e71] box «Icon Button Container» pos(147.67,4.15) size 61.47×61.47 | border-radius:999px; background-color:rgba(0,0,0,0.01)
      - glass effect (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(2px) saturate(1.25)
      - inner shadow: inset:0; border-radius:inherit; box-shadow:inset 1.2px 0px 0 0 rgba(255,255,255,0.22),inset -1.2px 0px 0 0 rgba(255,255,255,0.07),inset 0 0 19.2px 0 rgba(255,255,255,0.07)
      - [e72] box «ArrowUpRight» pos(20,20) size 21.47×21.47
        - [e73] vector «Vector» pos(4.7,4.7) size 12.08×12.08
          - vector drawing SVG-41 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
  - [e74] text pos(934.33,228) size 506.94×142 | white-space:pre-wrap; text-align:left; font-family:'Special Gothic Expanded One'; font-weight:400; font-size:84px; line-height:0.84; letter-spacing:-0.01em; text-transform:uppercase; color:#ffffff | TEXT: "Hero Gear"
  - [e75] box «Container» pos(934.33,184) size 98×24 | border-radius:999px
    - [e76] text pos(10,8) size 78×8 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Gantari'; font-weight:400; font-size:12px; line-height:1.4; text-transform:uppercase; color:#ffffff | TEXT: "Suit up now"
    - border (stroke): inset:0px; border-radius:999px; border:1px solid rgba(255,255,255,0.49)

#### Layer bk8 «Container» — CONTAINED LAYER (contain, anchor x=-1, y=0), design box x=54 y=173 506.94×410.77
- [e77] group «Container»
    ↳ animation: opacity: 6.063→6.453s 0→1 (E0); 10.026→10.128s 1→0 (E0); offsetX: 6.063→7.608s 359.61→20 (E5); 9.654→10.278s 20→-1666.59 (E6); offsetY: 6.063→7.608s 33.28→0 (E5); 9.654→10.278s 0→-117.36 (E6); scaleX: 6.063→7.608s 0.31→1 (E5); 9.654→10.278s 1→6.23 (E6); scaleY: 6.063→7.608s 0.31→1 (E5); 9.654→10.278s 1→6.23 (E6) [rotation/scale around the group origin [307.47,378.38] (transform-origin in px)]
  - [e78] text pos(54,399) size 313×75 | white-space:pre-wrap; text-align:left; font-family:'Epilogue'; font-weight:400; font-size:18px; line-height:1.4; color:#ffffff | TEXT: "Jump into new challenges every day and climb the global leaderboard."
  - [e79] box <a href="#"> «Button Container» pos(54,514) size 209.13×69.77
    - [e80] box «Text Button Container» pos(0,0) size 147.67×69.77 | border-radius:999px; background-color:#ffffff
      - [e81] text pos(26.83,27.38) size 94×15 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Epilogue'; font-weight:500; font-size:20px; line-height:normal; letter-spacing:-0.03em; color:#121212 | TEXT: "View Gear"
    - [e82] box «Icon Button Container» pos(147.67,4.15) size 61.47×61.47 | border-radius:999px; background-color:rgba(0,0,0,0.01)
      - glass effect (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(2px) saturate(1.25)
      - inner shadow: inset:0; border-radius:inherit; box-shadow:inset 1.2px 0px 0 0 rgba(255,255,255,0.22),inset -1.2px 0px 0 0 rgba(255,255,255,0.07),inset 0 0 19.2px 0 rgba(255,255,255,0.07)
      - [e83] box «ArrowUpRight» pos(20,20) size 21.47×21.47
        - [e84] vector «Vector» pos(4.69,4.7) size 12.08×12.08
          - vector drawing SVG-41 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
  - [e85] text pos(54,217) size 506.94×142 | white-space:pre-wrap; text-align:left; font-family:'Special Gothic Expanded One'; font-weight:400; font-size:84px; line-height:0.84; letter-spacing:-0.01em; text-transform:uppercase; color:#ffffff | TEXT: "Daily Runs"
  - [e86] box «Container» pos(54,173) size 93×24 | border-radius:999px
    - [e87] text pos(10,8) size 73×8 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Gantari'; font-weight:400; font-size:12px; line-height:1.4; text-transform:uppercase; color:#ffffff | TEXT: "Daily quest"
    - border (stroke): inset:0px; border-radius:999px; border:1px solid rgba(255,255,255,0.49)

#### Layer bk9 «Container» — CONTAINED LAYER (contain, anchor x=1, y=0), design box x=934.33 y=184 506.94×410.77
- [e88] group «Container»
    ↳ animation: opacity: 10.134→10.171s 0→1 (E0); offsetX: 10.134→11.679s -310.08→0 (E5); offsetY: 10.134→11.679s 32.59→0 (E5); scaleX: 10.134→11.679s 0.18→1 (E5); scaleY: 10.134→11.679s 0.18→1 (E5) [rotation/scale around the group origin [1187.8,389.38] (transform-origin in px)]
  - [e89] text pos(934.33,410) size 313×50 | white-space:pre-wrap; text-align:left; font-family:'Epilogue'; font-weight:400; font-size:18px; line-height:1.4; color:#ffffff | TEXT: "Compete in limited-time races for exclusive rewards and rare items."
  - [e90] box <a href="#"> «Button Container» pos(934.33,525) size 219.13×69.77
    - [e91] box «Text Button Container» pos(0,0) size 157.67×69.77 | border-radius:999px; background-color:#ffffff
      - [e92] text pos(26.83,27.38) size 104×15 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Epilogue'; font-weight:500; font-size:20px; line-height:normal; letter-spacing:-0.03em; color:#121212 | TEXT: "Enter Race"
    - [e93] box «Icon Button Container» pos(157.67,4.15) size 61.47×61.47 | border-radius:999px; background-color:rgba(0,0,0,0.01)
      - glass effect (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(2px) saturate(1.25)
      - inner shadow: inset:0; border-radius:inherit; box-shadow:inset 1.2px 0px 0 0 rgba(255,255,255,0.22),inset -1.2px 0px 0 0 rgba(255,255,255,0.07),inset 0 0 19.2px 0 rgba(255,255,255,0.07)
      - [e94] box «ArrowUpRight» pos(20,20) size 21.47×21.47
        - [e95] vector «Vector» pos(4.7,4.7) size 12.08×12.08
          - vector drawing SVG-41 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
  - [e96] text pos(934.33,228) size 506.94×142 | white-space:pre-wrap; text-align:left; font-family:'Special Gothic Expanded One'; font-weight:400; font-size:84px; line-height:0.84; letter-spacing:-0.01em; text-transform:uppercase; color:#ffffff | TEXT: "Squad Mode"
  - [e97] box «Container» pos(934.33,184) size 106×24 | border-radius:999px
    - [e98] text pos(10,8) size 86×8 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Gantari'; font-weight:400; font-size:12px; line-height:1.4; text-transform:uppercase; color:#ffffff | TEXT: "Team up now"
    - border (stroke): inset:0px; border-radius:999px; border:1px solid rgba(255,255,255,0.49)

#### Layer bk10 «navbar» — CONTAINED LAYER (contain, anchor x=0, y=-1), design box x=24 y=16 1392×52
- [e99] group «navbar»
  - [e100] box «Frame 1597882325» pos(379.5,18) size 681×50 | border-radius:999px; overflow:hidden; background-color:rgba(255,255,255,0.01)
      ↳ animation: opacity: 0.14→0.44s 0→1 (E3)
    - glass effect (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(2px) saturate(1.25)
    - inner shadow: inset:0; border-radius:inherit; box-shadow:inset 1.2px 0px 0 0 rgba(255,255,255,0.22),inset -1.2px 0px 0 0 rgba(255,255,255,0.07),inset 0 0 19.2px 0 rgba(255,255,255,0.07)
    - [-] list <nav> aria-label="Main navigation"
      - [-] list <ul>
        - [e101] box <li> «Frame 1597882289» pos(8,8) size 66×34 | border-radius:99px; background:linear-gradient(53.58deg, rgba(255,255,255,0.33) -16.16%, rgba(255,255,255,0) 107.79%)
            ↳ animation: opacity: 0.08→0.48s 0→1 (E9); offsetY: 0.08→0.48s -30→0 (E9)
          - [-] link <a href="#"> that covers its container's whole box (inset:0)
            - [e102] text pos(12,12) size 42×10 | white-space:pre; text-align:right; text-box:trim-both cap alphabetic; font-family:'Epilogue'; font-weight:700; font-size:14px; line-height:1.4; color:#ffffff | TEXT: "Home"
            - border (stroke): inset:0px; border-radius:99px; padding:1px; background:linear-gradient(-131.88deg, rgba(255,255,255,0.6) 8.02%, rgba(255,255,255,0) 102.95%); 1px gradient border (gradient background clipped with mask: linear-gradient content-box exclude)
        - [e103] box <li> «Frame 1597882290» pos(106,8) size 71×34 | border-radius:99px
            ↳ animation: opacity: 0.16→0.56s 0→1 (E9); offsetY: 0.16→0.56s -30→0 (E9)
          - [-] link <a href="#"> that covers its container's whole box (inset:0)
            - [e104] text pos(12,12) size 47×10 | white-space:pre; text-align:right; text-box:trim-both cap alphabetic; font-family:'Epilogue'; font-weight:400; font-size:14px; line-height:1.4; color:#ffffff | TEXT: "Worlds"
        - [e105] box <li> «Frame 1597882292» pos(209,8) size 102×34 | border-radius:99px
            ↳ animation: opacity: 0.24→0.64s 0→1 (E9); offsetY: 0.24→0.64s -30→0 (E9)
          - [-] link <a href="#"> that covers its container's whole box (inset:0)
            - [e106] text pos(12,12) size 78×10 | white-space:pre; text-align:right; text-box:trim-both cap alphabetic; font-family:'Epilogue'; font-weight:400; font-size:14px; line-height:1.4; color:#ffffff | TEXT: "Exploration"
        - [e107] box <li> «Frame 1597882291» pos(343,8) size 102×34 | border-radius:99px
            ↳ animation: opacity: 0.32→0.72s 0→1 (E9); offsetY: 0.32→0.72s -30→0 (E9)
          - [-] link <a href="#"> that covers its container's whole box (inset:0)
            - [e108] text pos(12,12) size 78×10 | white-space:pre; text-align:right; text-box:trim-both cap alphabetic; font-family:'Epilogue'; font-weight:400; font-size:14px; line-height:1.4; color:#ffffff | TEXT: "Characters"
        - [e109] box <li> «Frame 1597882293» pos(477,8) size 80×34 | border-radius:99px
            ↳ animation: opacity: 0.4→0.8s 0→1 (E9); offsetY: 0.4→0.8s -30→0 (E9)
          - [-] link <a href="#"> that covers its container's whole box (inset:0)
            - [e110] text pos(12,12) size 56×10 | white-space:pre; text-align:right; text-box:trim-both cap alphabetic; font-family:'Epilogue'; font-weight:400; font-size:14px; line-height:1.4; color:#ffffff | TEXT: "Journey"
        - [e111] box <li> «Frame 1597882294» pos(589,8) size 84×34 | border-radius:99px
            ↳ animation: opacity: 0.48→0.88s 0→1 (E9); offsetY: 0.48→0.88s -30→0 (E9)
          - [-] link <a href="#"> that covers its container's whole box (inset:0)
            - [e112] text pos(12,12) size 60×10 | white-space:pre; text-align:right; text-box:trim-both cap alphabetic; font-family:'Epilogue'; font-weight:400; font-size:14px; line-height:1.4; color:#ffffff | TEXT: "Connect"
  - [e113] box <a href="#"> aria-label="Menu" «Frame 1597882296» pos(1364,16) size 52×52 | border-radius:32px; background-color:rgba(255,255,255,0.00)
      ↳ animation: opacity: 0.56→0.96s 0→1 (E9); offsetY: 0.56→0.96s -30→0 (E9)
    - glass effect (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(0px) saturate(1.25)
    - inner shadow: inset:0; border-radius:inherit; box-shadow:inset 1.2px 0px 0 0 rgba(255,255,255,0.44),inset -1.2px 0px 0 0 rgba(255,255,255,0.14),inset 0 0 24px 0 rgba(255,255,255,0.14)
    - [e114] group «Group 8»
      - [e115] vector «Line 9» pos(14.51,19.66) size 22.98×2
        - vector drawing SVG-42 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e116] vector «Line 11» pos(20.13,26) size 11.73×2
        - vector drawing SVG-43 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e117] vector «Line 10» pos(14.51,32.34) size 22.98×2
        - vector drawing SVG-42 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
  - [e118] box <a href="#"> «Frame 1597882324» pos(1278,26) size 63×34 | border-radius:99px; background:linear-gradient(52.29deg, rgba(255,255,255,0.33) -17.27%, rgba(255,255,255,0) 107.59%)
      ↳ animation: opacity: 0.64→1.04s 0→1 (E9); offsetY: 0.64→1.04s -30→0 (E9)
    - background blur (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(20px)
    - [e119] text pos(12,12) size 39×10 | white-space:pre; text-align:right; text-box:trim-both cap alphabetic; font-family:'Epilogue'; font-weight:700; font-size:14px; line-height:1.4; color:#ffffff | TEXT: "Enter"
    - border (stroke): inset:0px; border-radius:99px; padding:1px; background:linear-gradient(-133.21deg, rgba(255,255,255,0.6) 7.78%, rgba(255,255,255,0) 103.88%); 1px gradient border (gradient background clipped with mask: linear-gradient content-box exclude)
  - [e120] group <a href="#"> «Group 34»
      ↳ animation: opacity: 0→0.4s 0→1 (E9); offsetY: 0→0.4s -30→0 (E9)
    - [e121] box «image 9» pos(24,34) size 17.14×17.15
      - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/metaverse/assets/img/323d20ac73.webp: absolutely positioned `<img alt="">` at (0,0), 2048×2048 px, max-width:none, transform-origin 0 0 and transform matrix(0.0109,0,0,0.0109,-2.657,-2.654); the container clips it (overflow hidden, same border-radius)
    - [e122] text pos(48,38.29) size 59×8 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:400; font-size:12.00px; line-height:1.4; letter-spacing:-0.13em; color:#ffffff | TEXT: "Runshift"

## 10. Final adjustments
- [e120] «Group 34»: enlarge ×2 keeping the point (24, 42.5) of its container fixed (equivalent to `transform: scale(2)` with `transform-origin: 24px 42.5px` in container coordinates). Its content keeps its animation.
- Mobile only: as the last child of layer bk3 «Rectangle 23» (above its content and below the following layers) add an absolute overlay at (0,0), 1440×810 frame px, with no pointer events, with `background: linear-gradient(180deg, rgba(8,6,28,.42) 0%, rgba(8,6,28,0) 16%, rgba(8,6,28,0) 38%, rgba(8,6,28,.5) 62%, rgba(8,6,28,.72) 100%)` to improve text readability.

## 11. Mobile view
- It activates when the stage width is < 768 px or the width/height ratio is < 0.82 (add the class `is-m` to `<html>`). Mobile reference design: 390×844 px.
- sm = min(W/390, H/844); ox = (W − 390·sm)/2; oy = (H − 844·sm)/2.
- Listed contained layer with {x, y, z, ax, ay}: its scale is sm·z (z = 1 if not given) and the top-left corner of its «design box» is placed on screen at (ox·(1+ax) + x·sm, oy·(1+ay) + y·sm) (ax, ay = 0 if not given); the rest of its content keeps its position relative to that box.
- Bleed layer: z = max(W/1440, H/810)·(given z or 1); screen = ((W − 1440·z)·fx + X·z, (H − 810·z)·fy + Y·z), with fx, fy = 0.5 unless another value is given.
- Contained layers that are not in the list are hidden on mobile.
- Per-element adjustments: «hide» = `display:none`; «shift dx, dy» = move the element by that distance in px of its container (if there is also «scale ×z», it is scaled from its top-left corner); «multiply its animated offsets by [kx, ky]» = multiply the total value of offsetX by kx and of offsetY by ky (the mobile «shift dx, dy» is not multiplied); «fade out between t1 and t2» = multiply its opacity by clamp((t2 − t)/(t2 − t1), 0, 1) from t1 on; «place the element point (rx, ry) at (mx, my) of the mobile design at scale e×sm» = draw the element at scale Se = e·sm with its top-left corner (animated x, y) at (ox + mx·sm + (x − rx)·Se, oy + my·sm + (y − ry)·Se); «width» = fixed width in px; «transition … between t1 and t2» = go from its layer's normal placement (the mobile one) to that placement with smoothstep interpolation (u²·(3 − 2u)) of scale and origin between those times.
- On mobile the track uses 30 vh per second (see section 7).
Mobile layer placement:
- layer «navbar»: x=16, y=14, z=0.9
- layer bk6 «Container»: x=20, y=372, z=0.68
- layer bk7 «Container»: x=20, y=364.52, z=0.68
- layer bk8 «Container»: x=20, y=372, z=0.68
- layer bk9 «Container»: x=20, y=364.52, z=0.68
- layer «Frame 37»: x=64.5, y=744, z=0.9, ay=1
- layer «Group 15»: hide
Unlisted non-bleed layers are hidden on mobile.
Per-element mobile adjustments:
- [e100] «Frame 1597882325»: hide
- [e113] «Frame 1597882296»: shift dx=-994, dy=0 (design units of its layer)
- [e118] «Frame 1597882324»: shift dx=-983, dy=0 (design units of its layer)
- [e66] «Container»: multiply its animated offsets by [2.4,1]; fade out between t=5.75s and 6.1s
- [e88] «Container»: multiply its animated offsets by [2.4,1]
- [e55] «Container»: fade out between t=1.83s and 2.3s
- [e77] «Container»: fade out between t=9.65s and 10.1s

## 12. Semantics and accessibility
- `<main class="hero" aria-label="Runshift — Vibrant Metaverse">`; the loader has `aria-hidden="true"`.
- Lists with `<ul>` and `<li>`; menus inside `<nav aria-label="…">`. Each `<a href="#">` uses the given `aria-label` when its content is not text. If a link covers a whole box, it goes inside it as an absolute `<a class="lk">` covering it (`inset:0`).
- Decorative images with `alt=""`; decorative SVGs with `aria-hidden="true"`.
- Only texts and links receive pointer events (CSS in section 3) and invisible elements get `visibility:hidden`, so nothing that cannot be seen can ever be clicked.
- Visible focus: `outline: 2px solid currentColor; outline-offset: 3px`.

## 13. Resources (absolute URLs)
All resources are hosted in the public repository `danielsnows/F4M-Bluebird` and served by jsDelivr (CDN with CORS enabled):
- `assets/fonts/Epilogue.woff2` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/metaverse/assets/fonts/Epilogue.woff2
- `assets/fonts/Gantari.woff2` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/metaverse/assets/fonts/Gantari.woff2
- `assets/fonts/LexendMega.woff2` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/metaverse/assets/fonts/LexendMega.woff2
- `assets/fonts/SpecialGothicExpandedOne.woff2` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/metaverse/assets/fonts/SpecialGothicExpandedOne.woff2
- `assets/img/0ce79d7996.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/metaverse/assets/img/0ce79d7996.webp
- `assets/img/323d20ac73.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/metaverse/assets/img/323d20ac73.webp
- `assets/img/628bb1563d.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/metaverse/assets/img/628bb1563d.webp
- `assets/img/875ecd2bf9.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/metaverse/assets/img/875ecd2bf9.webp
- `assets/img/be4b7af277.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/metaverse/assets/img/be4b7af277.webp
- `assets/video/618ec1861d.jpg` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/metaverse/assets/video/618ec1861d.jpg
- `assets/video/618ec1861d.mp4` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/metaverse/assets/video/618ec1861d.mp4

## 14. Appendix: vector shapes
Each SVG fills 100% of its element's box (`position:absolute; inset:0; width:100%; height:100%; overflow:visible`, `preserveAspectRatio="none"`, `aria-hidden="true"`). If an element says «with fill #xxxxxx (instead of #yyyyyy)», use the same SVG changing that fill color. If an SVG with internal `id`s (masks, gradients) is used more than once, give each copy unique ids and update its `url(#…)` references.

**SVG-1**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 4 6" xmlns="http://www.w3.org/2000/svg"> <line opacity="0.4" stroke="white" stroke-width="0.958553" transform="matrix(0.45399 0.891007 0.891007 -0.45399 0.854126 0)" x2="5.70204" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-2**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 3 4" xmlns="http://www.w3.org/2000/svg"> <line opacity="0.4" stroke="white" stroke-width="0.958553" transform="matrix(0.587785 0.809017 0.809017 -0.587785 0.775513 0)" x2="3.58995" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-3**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 4 4" xmlns="http://www.w3.org/2000/svg"> <line opacity="0.4" stroke="white" stroke-width="0.958553" transform="matrix(0.707107 0.707107 0.707107 -0.707107 0.677856 0)" x2="3.58995" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-4**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 4 3" xmlns="http://www.w3.org/2000/svg"> <line opacity="0.4" stroke="white" stroke-width="0.958553" transform="matrix(0.809017 0.587785 0.587785 -0.809017 0.563477 0)" x2="3.58995" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-5**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 4 3" xmlns="http://www.w3.org/2000/svg"> <line opacity="0.4" stroke="white" stroke-width="0.958553" transform="matrix(0.891006 0.453991 0.453991 -0.891006 0.435181 0)" x2="3.58995" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-6**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 4 3" xmlns="http://www.w3.org/2000/svg"> <line opacity="0.4" stroke="white" stroke-width="0.958554" transform="matrix(0.951057 0.309017 0.309017 -0.951057 0.296265 0)" x2="3.58995" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-7**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 4 2" xmlns="http://www.w3.org/2000/svg"> <line opacity="0.4" stroke="white" stroke-width="0.958553" transform="matrix(0.987688 0.156434 0.156434 -0.987688 0.149902 0)" x2="3.58995" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-8**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 4 1" xmlns="http://www.w3.org/2000/svg"> <line opacity="0.4" stroke="white" stroke-width="0.958553" transform="matrix(1 0 0 -1 0 0)" x2="3.58995" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-9**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 4 2" xmlns="http://www.w3.org/2000/svg"> <line opacity="0.4" stroke="white" stroke-width="0.958553" transform="matrix(0.987688 -0.156434 -0.156434 -0.987688 0 0.561523)" x2="3.58995" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-10**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 4 3" xmlns="http://www.w3.org/2000/svg"> <line opacity="0.4" stroke="white" stroke-width="0.958553" transform="matrix(0.951056 -0.309017 -0.309017 -0.951056 0 1.10938)" x2="3.58995" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-11**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 4 3" xmlns="http://www.w3.org/2000/svg"> <line opacity="0.4" stroke="white" stroke-width="0.958553" transform="matrix(0.891007 -0.45399 -0.45399 -0.891007 0 1.62988)" x2="3.58995" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-12**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 4 3" xmlns="http://www.w3.org/2000/svg"> <line opacity="0.4" stroke="white" stroke-width="0.958553" transform="matrix(0.809017 -0.587785 -0.587785 -0.809017 0 2.11011)" x2="3.58995" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-13**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 4 4" xmlns="http://www.w3.org/2000/svg"> <line opacity="0.4" stroke="white" stroke-width="0.958553" transform="matrix(0.707107 -0.707107 -0.707107 -0.707107 0 2.53857)" x2="3.58995" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-14**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 3 4" xmlns="http://www.w3.org/2000/svg"> <line opacity="0.4" stroke="white" stroke-width="0.958553" transform="matrix(0.587785 -0.809017 -0.809017 -0.587785 0 2.9043)" x2="3.58995" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-15**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 3 4" xmlns="http://www.w3.org/2000/svg"> <line opacity="0.4" stroke="white" stroke-width="0.958553" transform="matrix(0.453991 -0.891007 -0.891007 -0.453991 0 3.19873)" x2="3.58995" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-16**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 3 4" xmlns="http://www.w3.org/2000/svg"> <line opacity="0.4" stroke="white" stroke-width="0.958554" transform="matrix(0.309017 -0.951057 -0.951057 -0.309017 0 3.41431)" x2="3.58995" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-17**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 2 4" xmlns="http://www.w3.org/2000/svg"> <line opacity="0.4" stroke="white" stroke-width="0.958553" transform="matrix(0.156434 -0.987688 -0.987688 -0.156434 0 3.54565)" x2="3.58995" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-18**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 1 4" xmlns="http://www.w3.org/2000/svg"> <line opacity="0.4" stroke="white" stroke-width="0.958553" transform="matrix(-4.37114e-08 -1 -1 4.37114e-08 0 3.58984)" x2="3.58995" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-19**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 2 4" xmlns="http://www.w3.org/2000/svg"> <line opacity="0.4" stroke="white" stroke-width="0.958553" transform="matrix(-0.156435 -0.987688 -0.987688 0.156435 0.561646 3.6958)" x2="3.58995" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-20**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 3 4" xmlns="http://www.w3.org/2000/svg"> <line opacity="0.4" stroke="white" stroke-width="0.958553" transform="matrix(-0.309017 -0.951056 -0.951056 0.309017 1.10938 3.71045)" x2="3.58995" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-21**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 3 4" xmlns="http://www.w3.org/2000/svg"> <line opacity="0.4" stroke="white" stroke-width="0.958553" transform="matrix(-0.45399 -0.891007 -0.891007 0.45399 1.62976 3.63379)" x2="3.58995" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-22**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 3 4" xmlns="http://www.w3.org/2000/svg"> <line opacity="0.4" stroke="white" stroke-width="0.958553" transform="matrix(-0.587785 -0.809017 -0.809017 0.587785 2.11011 3.46777)" x2="3.58995" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-23**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 4 4" xmlns="http://www.w3.org/2000/svg"> <line opacity="0.4" stroke="white" stroke-width="0.958553" transform="matrix(-0.707107 -0.707107 -0.707107 0.707107 2.53845 3.21631)" x2="3.58995" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-24**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 4 3" xmlns="http://www.w3.org/2000/svg"> <line opacity="0.4" stroke="white" stroke-width="0.958553" transform="matrix(-0.809017 -0.587785 -0.587785 0.809017 2.9043 2.8855)" x2="3.58995" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-25**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 4 3" xmlns="http://www.w3.org/2000/svg"> <line opacity="0.4" stroke="white" stroke-width="0.958553" transform="matrix(-0.891007 -0.45399 -0.45399 0.891007 3.19873 2.48389)" x2="3.58995" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-26**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 4 3" xmlns="http://www.w3.org/2000/svg"> <line opacity="0.4" stroke="white" stroke-width="0.958554" transform="matrix(-0.951056 -0.309017 -0.309017 0.951056 3.41431 2.021)" x2="3.58995" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-27**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 4 2" xmlns="http://www.w3.org/2000/svg"> <line opacity="0.4" stroke="white" stroke-width="0.958553" transform="matrix(-0.987688 -0.156435 -0.156435 0.987688 3.54578 1.5083)" x2="3.58995" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-28**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 4 1" xmlns="http://www.w3.org/2000/svg"> <line opacity="0.4" stroke="white" stroke-width="0.958553" transform="matrix(-1 -3.17865e-08 -3.17865e-08 1 3.58997 0.958496)" x2="3.58995" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-29**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 4 2" xmlns="http://www.w3.org/2000/svg"> <line opacity="0.4" stroke="white" stroke-width="0.958553" transform="matrix(-0.987688 0.156434 0.156434 0.987688 3.69568 0.946777)" x2="3.58995" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-30**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 4 3" xmlns="http://www.w3.org/2000/svg"> <line opacity="0.4" stroke="white" stroke-width="0.958553" transform="matrix(-0.951056 0.309017 0.309017 0.951056 3.71045 0.911621)" x2="3.58995" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-31**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 4 3" xmlns="http://www.w3.org/2000/svg"> <line opacity="0.4" stroke="white" stroke-width="0.958553" transform="matrix(-0.891006 0.45399 0.45399 0.891006 3.63379 0.854004)" x2="3.58995" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-32**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 4 3" xmlns="http://www.w3.org/2000/svg"> <line opacity="0.4" stroke="white" stroke-width="0.958553" transform="matrix(-0.809017 0.587785 0.587785 0.809017 3.46777 0.775391)" x2="3.58995" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-33**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 4 4" xmlns="http://www.w3.org/2000/svg"> <line opacity="0.4" stroke="white" stroke-width="0.958553" transform="matrix(-0.707107 0.707107 0.707107 0.707107 3.21631 0.677734)" x2="3.58995" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-34**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 3 4" xmlns="http://www.w3.org/2000/svg"> <line opacity="0.4" stroke="white" stroke-width="0.958553" transform="matrix(-0.587785 0.809017 0.809017 0.587785 2.88562 0.563477)" x2="3.58995" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-35**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 4 6" xmlns="http://www.w3.org/2000/svg"> <line opacity="0.4" stroke="white" stroke-width="0.958553" transform="matrix(-0.453991 0.891006 0.891006 0.453991 3.42664 0.435059)" x2="5.66653" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-36**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 1 17" xmlns="http://www.w3.org/2000/svg"> <line stroke="white" stroke-width="0.958553" transform="matrix(-1.31134e-07 1 1 1.31134e-07 0.958496 0)" x2="16.368" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-37**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 3 13" xmlns="http://www.w3.org/2000/svg"> <line stroke="white" stroke-width="0.958553" transform="matrix(0.156434 0.987688 0.987688 -0.156434 0.946777 0)" x2="12.196" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-38**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 4 8" xmlns="http://www.w3.org/2000/svg"> <line stroke="white" stroke-width="0.958553" transform="matrix(0.309017 0.951057 0.951057 -0.309017 0.911621 0)" x2="8.04701" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-39**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 4 8" xmlns="http://www.w3.org/2000/svg"> <line stroke="white" stroke-width="0.958554" transform="matrix(-0.309017 0.951057 0.951057 0.309017 3.39258 0.296143)" x2="8.02834" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-40**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 3 13" xmlns="http://www.w3.org/2000/svg"> <line stroke="white" stroke-width="0.958553" transform="matrix(-0.156435 0.987688 0.987688 0.156435 2.85461 0.149902)" x2="12.196" y1="-0.479277" y2="-0.479277"></line> </svg>
```

**SVG-41**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 13 13" xmlns="http://www.w3.org/2000/svg"> <path d="M12.0754 0.670833V9.39167C12.0754 9.56958 12.0047 9.74021 11.8789 9.86602C11.7531 9.99182 11.5825 10.0625 11.4045 10.0625C11.2266 10.0625 11.056 9.99182 10.9302 9.86602C10.8044 9.74021 10.7337 9.56958 10.7337 9.39167V2.29006L1.14582 11.8788C1.01995 12.0047 0.849221 12.0754 0.671206 12.0754C0.493191 12.0754 0.322467 12.0047 0.196592 11.8788C0.0707161 11.7529 0 11.5822 0 11.4042C0 11.2262 0.0707161 11.0554 0.196592 10.9296L9.78532 1.34167H2.68371C2.50579 1.34167 2.33516 1.27099 2.20936 1.14518C2.08355 1.01938 2.01287 0.848749 2.01287 0.670833C2.01287 0.492917 2.08355 0.322288 2.20936 0.196483C2.33516 0.070677 2.50579 0 2.68371 0H11.4045C11.5825 0 11.7531 0.070677 11.8789 0.196483C12.0047 0.322288 12.0754 0.492917 12.0754 0.670833Z" fill="white"></path> </svg>
```

**SVG-42**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 23 2" xmlns="http://www.w3.org/2000/svg"> <line stroke="white" stroke-width="2" x1="22.9839" x2="-6.26544e-08" y1="1" y2="0.999997"></line> </svg>
```

**SVG-43**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 12 2" xmlns="http://www.w3.org/2000/svg"> <line stroke="white" stroke-width="2" x1="11.7305" x2="-8.74228e-08" y1="1" y2="0.999999"></line> </svg>
```

## 15. Acceptance criteria
- Compared with https://metaverse-snows.vercel.app, the result matches at 1440×810, 1280×720 and 390×844 at the key moments: end of the intro, every pause and the final state.
- Positions, sizes, colors, fonts, timings and curves match this specification.
- All texts are real, editable HTML; all links are clickable `<a href="#">` with visible focus; invisible things cannot be clicked.
- Images, video and fonts load from the URLs in section 13 and there are no console errors.
- The layout adapts when the window is resized and follows section 11 on mobile.
- With `prefers-reduced-motion` the final state is shown directly.
