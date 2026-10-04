# Replication prompt — Primal

> Complete instructions for an AI coding agent. It contains everything needed to rebuild the animated hero «Primal» identically: structure, styles, texts, animation timings and curves, mobile view and links to the resources (images, video and fonts).

## Role and goal
Act as a senior front-end developer specialized in web animation. Build a single-screen page (hero) that exactly reproduces the animated design «Primal» described here, without adding or removing anything.
Published reference version: https://primal-snows.vercel.app — use it to compare your result visually.

## 1. Overview
Jewelry hero «PRIMAL — Unleash Yourself», designed at 1440×810. Behind everything, full-bleed, a video of a white big cat with a blue porcelain pattern whose playback is driven by scrolling. The automatic intro (0 → 2.45 s) shows the giant letters P-R-I-M-A-L over the cat, the round «Explore» button and the top bar (logo, search and bag icons, «Buy Now» button). Scrolling advances the timeline up to 13.559 s: the letters split apart and leave, «Unleash your inner beast.» appears with its description, two necklace images, two round jewelry buttons and the music button; in the final part the title «Unleash yourself» and three jewelry cards (Ocean Heart, Sapphire Tide, Crystal Drop) appear.

## 2. Deliverable and technical rules
- A single `index.html` file with the CSS inside `<style>` and the JavaScript inside `<script>`. Plain (vanilla) JavaScript, no frameworks and no build step.
- The only external dependency: Lenis 1.3.26 for smooth scrolling: `<script src="https://cdn.jsdelivr.net/npm/lenis@1.3.26/dist/lenis.min.js"></script>` before your script (if it fails to load, everything must work with native scrolling).
- Images, video and fonts are ALWAYS loaded from the absolute URLs in section 13 «Resources». Do not download them, do not inline them as base64 and do not use any other resources.
- Every visible text is real, selectable and editable HTML text (never text turned into an image or into SVG paths). The vector shapes in the Appendix are only icons and decoration.
- Lists use `<ul>`/`<li>` (inside `<nav aria-label="…">` where indicated) and links use clickable `<a href="#">`, with `aria-label` where indicated.
- Head: `<html lang="en">`, `<meta charset="utf-8">`, `<meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover">`, `<title>Primal — Unleash Yourself</title>`, `<meta name="description" content="PRIMAL: discover elegance and craftsmanship with an exquisite jewelry collection, designed to inspire and shine.">`, `<meta name="theme-color" content="#b9c3cf">`.
- Respect `prefers-reduced-motion: reduce`: the loader works the same, but there is no intro, no smooth scrolling and no snap; t stays fixed on the final state (t = 13.559 s) even when scrolling.
- Do not add elements, texts, effects or sections that are not in this specification.

## 3. Base structure (common HTML + CSS)
```html
<body>
<div class="loader" id="loader" aria-hidden="true">
  <div class="loader-in"><span>PRIMAL</span><div class="loader-bar"><i id="loaderBar"></i></div><span id="loaderPct">000</span></div>
</div>
<main class="hero" id="hero" aria-label="Primal — Unleash Yourself">
  <div class="stage" id="stage">
    <!-- one layer (div.blk) per «Layer» in section 9, in the same order: later ones are on top -->
  </div>
</main>
<script>/* engine from section 6 */</script>
</body>
```
```css
:root{--bg:#b9c3cf;--ink:#1b2f4c;color-scheme:light}
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
- **Aloevera Display** (weight 200): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/primal/assets/fonts/AloeveraDisplay-ExtraLight.woff2
- **Aloevera Display** (weight 300): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/primal/assets/fonts/AloeveraDisplay-Light.woff2
- **Aloevera Display** (weight 500): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/primal/assets/fonts/AloeveraDisplay-Medium.woff2
- **Outfit** (weight 500): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/primal/assets/fonts/Outfit-500.woff2
- **TOP LUXURY** (weight 400): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/primal/assets/fonts/TOPLUXURY.woff2

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
  - `E1` = cubic-bezier(0, 1.0712, 1, 1)
  - `E2` = cubic-bezier(0, 1.07, 1, 1)
  - `E3` = cubic-bezier(0, 0, 1, -0.03)
  - `E4` = cubic-bezier(0.5, 0, 0.5, 1)
  - `E5` = cubic-bezier(0, 0, 0, 1)
  - `E6` = cubic-bezier(0, 1.05, 0.5, 1)
  - `E7` = cubic-bezier(0, 1.0521, 0.5, 1)
- `cubic-bezier(x1, y1, x2, y2)` curves work as in CSS: given the time progress p, solve the parameter u such that X(u) = p and return Y(u).

## 7. Playback (intro and scrolling)
- Total timeline duration: 13.559 s. Automatic intro: from t = 0 to t = 2.45 s. The rest (2.45 → 13.559 s) is driven by scrolling.
- `.hero` height = 100vh + track. track = round((13.559 − 2.45) × 45) = **500vh** on desktop and round((13.559 − 2.45) × 40) = **444vh** on mobile (use exactly these values; they are set through the CSS variable `--track`). `.stage` is sticky, so the stage stays fixed while the track is scrolled.
- While loading: `html.is-locked` (overflow hidden), `history.scrollRestoration = 'manual'`, `scrollTo(0,0)`.
- When the loader gets the `is-done` class, play the intro: t advances in real time (linearly) from 0 to 2.45 s in 2.45 s. During the intro scrolling is blocked (Lenis stopped; if Lenis is not available, keep `is-locked` until the intro ends) and scroll events do not change t. When it ends, t = 2.45 and scrolling is enabled.
- Smooth scrolling with Lenis: `new Lenis({lerp:0.085, wheelMultiplier:0.9, smoothWheel:true})`, `lenis.stop()` until the intro ends, then `lenis.start()`; call `lenis.raf(now)` in the requestAnimationFrame loop. Without Lenis, use native scrolling.
- Scroll → time mapping: p = clamp((scrollY − hero.offsetTop) / (hero.offsetHeight − stage.clientHeight), 0, 1); t = 2.45 + p × (13.559 − 2.45).
- Pauses (holds), in seconds: [6.3 – 7.8], [13.259 – 13.559]. Snap (Lenis only; no snap without Lenis): the direction is the sign of the last scroll change; 170 ms after the last scroll event (every event restarts the timer), if t is not inside a pause (with a ±0.02 s margin), scroll with `lenis.scrollTo` to the start of the next pause when scrolling down, or to the end of the previous pause when scrolling up. Duration = clamp(|Δt| × 0.45, 0.6, 1.8) s; easing easeInOutCubic (x < 0.5 ? 4x³ : 1 − (−2x+2)³/2). No snap with `prefers-reduced-motion` or during another snap (the «snapping» state is released in `onComplete` or, as a safety net, after 2.2 s).
- On every resize: recompute layers, track and t from the current scroll position.
- Video («scrub» mode, driven by the timeline): `<video muted playsinline preload="auto">` with its poster; it never plays by itself. For reliable seeking, download the MP4 with `fetch` as a Blob (reporting progress to the loader) and set `video.src = URL.createObjectURL(blob)` (if that fails, use the direct URL). On every frame, if `readyState ≥ 2` and it is not `seeking`: target = min(max(0, t), duration − 0.04); if |currentTime − target| > 0.012, `currentTime = target`.

## 8. Loader and preloading
- The loader covers the screen with the `--bg` color and shows «PRIMAL», a 160×1 px bar and a 3-digit percentage (`000` → `100`) in the `--ink` color.
- Weighted progress: fonts (`document.fonts.ready`) weight 1; each `<img>` element on the stage weight 0.4, even if it repeats a file (it counts once it has loaded —or failed— and `img.decode()` has finished; the video poster does not count); the video weight 6 (its fetch download progress). Percentage = Math.round(progress × 100) with 3 digits. The bar uses `transform: scaleX(progress)` and never goes backwards.
- Wait until everything finishes (14 s maximum; if it runs out, continue without cancelling anything); then set the video `src` (the Blob URL if already downloaded, otherwise the direct URL) and wait for its `loadeddata` or `error` (3 s maximum). Then remove `is-locked` from `<html>` (except in the no-Lenis case described in section 7), add `is-done` to the loader (it fades out in 0.9 s) and start the intro.

## 9. Layers and elements
Notation of each line: `[eN]` = suggested identifier; `box` = div; `text` = div with text (the content in quotes, keeping capitalization, line breaks and spaces); `group` = boxless container; `vector` = box containing an inline SVG; `<a href="#">`, `<ul>`, `<li>`, `<nav>` = semantic tag to use; `«name»` = layer name in the design (for reference only, also handy as `data-name`); `pos(x,y)` = left/top; `size A×B` = width×height; after `|` come the literal CSS styles. Every `text` element gets `class="t"` (used by the pointer-events rule in section 3); boxes, groups and vectors may use `class="b"`, `"g"` and `"v"`. Decorative layers (border, glass, inner shadow, background blur, fill layer) are absolute elements (for example `<i>`) covering their container (given `inset`) with inline `pointer-events:none` and `border-radius:inherit` unless stated otherwise. Images go inside an absolute container `inset:0; overflow:hidden; border-radius:inherit`. The order of the lines is the stacking order (later is on top).

#### Layer bk0 «Fondo» — BLEED LAYER (cover), design box x=0 y=0 1440×810
- [e1] box «Animation» pos(0,0) size 1440×810 | background-color:#000000

#### Layer bk1 «Background Image» — BLEED LAYER (cover), design box x=0 y=0 1440×810
- [e2] box «Background Image» pos(0,0) size 1440×810
  - video: `<video muted playsinline preload="auto" poster="https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/primal/assets/video/a5af3797c8.jpg" data-src="https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/primal/assets/video/a5af3797c8.mp4">` with position:absolute; inset:0; width:100%; height:100%; object-fit:cover; display:block (the `src` is assigned from JavaScript once preloading finishes, see sections 7 and 8)

#### Layer bk2 «Footer Background» — CONTAINED LAYER (contain, anchor x=0, y=1), design box x=0 y=650.05 1440×159.95
- [e3] box «Footer Background» transform translate(0,810) rotate(0rad) scale(1,-1) size 1440×159.95 | background-color:rgba(255,255,255,0.01)
  - background blur (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(20px); mask:linear-gradient(180deg, rgba(0,0,0,1), rgba(0,0,0,0))

#### Layer bk3 «Music Button» — CONTAINED LAYER (contain, anchor x=1, y=1), design box x=1382.21 y=750.09 53.93×52.19
- [e4] box <a href="#"> aria-label="Music" «Music Button» pos(1382.21,750.09) size 53.93×52.19 | background-color:rgba(74,92,115,0.01)
  - background blur (backdrop-filter): border-radius:inherit; backdrop-filter:blur(50px); inset:-5px; mask:linear-gradient(90deg,transparent,#000 10px,#000 calc(100% - 10px),transparent),linear-gradient(180deg,transparent,#000 10px,#000 calc(100% - 10px),transparent); mask-composite:intersect

#### Layer bk4 «PRIMAL» — CONTAINED LAYER (contain, anchor x=0, y=0), design box x=121 y=197.95 1199×223
- [e5] group «PRIMAL»
  - [e6] box «PRI CONTAINER» pos(121,197.95) size 494.5×223
      ↳ animation: offsetX: 2.478→4.239s 0→-121 (E1); 4.452→6.239s -121→-271 (E2); 7.854→9.794s -271→-596 (E3)
    - [e7] group «PRI»
      - [e8] text pos(417.5,0) size 77×223 | mix-blend-mode:difference; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; filter:blur(0px); font-family:'TOP LUXURY'; font-weight:400; font-size:320px; line-height:0.84; letter-spacing:-0.02em; text-transform:uppercase; color:#c58878 | TEXT: "I"
          ↳ animation: opacity: 0.76→1.394s 0→1 (E4); offsetY: 0.76→5.957s 245.05→0 (E5)
      - [e9] text pos(200.5,0) size 224×223 | mix-blend-mode:difference; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; filter:blur(0px); font-family:'TOP LUXURY'; font-weight:400; font-size:320px; line-height:0.84; letter-spacing:-0.02em; text-transform:uppercase; color:#c58878 | TEXT: "R"
          ↳ animation: opacity: 0.632→1.265s 0→1 (E4); offsetY: 0.638→5.824s 245.05→0 (E5)
      - [e10] text pos(0,0) size 207×223 | mix-blend-mode:difference; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; filter:blur(0px); font-family:'TOP LUXURY'; font-weight:400; font-size:320px; line-height:0.84; letter-spacing:-0.02em; text-transform:uppercase; color:#c58878 | TEXT: "P"
          ↳ animation: opacity: 0.486→1.119s 0→1 (E4); offsetY: 0.486→5.677s 245.05→0 (E5)
  - [e11] box «MAL CONTAINER» pos(615.5,197.95) size 704.5×223
      ↳ animation: offsetX: 2.475→4.21s 0→129.5 (E2); 4.44→6.239s 129.5→319.5 (E2); 7.87→9.794s 319.5→824.5 (E3)
    - [e12] group «MAL»
      - [e13] text pos(497.5,0) size 207×223 | mix-blend-mode:difference; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; filter:blur(0px); font-family:'TOP LUXURY'; font-weight:400; font-size:320px; line-height:0.84; letter-spacing:-0.02em; text-transform:uppercase; color:#c58878 | TEXT: "L"
          ↳ animation: opacity: 1.119→1.747s 0→1 (E4); offsetY: 1.119→6.272s 245.05→0 (E5)
      - [e14] text pos(293.5,0) size 211×223 | mix-blend-mode:difference; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; filter:blur(0px); font-family:'TOP LUXURY'; font-weight:400; font-size:320px; line-height:0.84; letter-spacing:-0.02em; text-transform:uppercase; color:#c58878 | TEXT: "A"
          ↳ animation: opacity: 0.997→1.624s 0→1 (E4); offsetY: 0.997→6.136s 245.05→0 (E5)
      - [e15] text pos(0,0) size 300×223 | mix-blend-mode:difference; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; filter:blur(0px); font-family:'TOP LUXURY'; font-weight:400; font-size:320px; line-height:0.84; letter-spacing:-0.02em; text-transform:uppercase; color:#c58878 | TEXT: "M"
          ↳ animation: opacity: 0.878→1.505s 0→1 (E4); offsetY: 0.878→6.012s 245.05→0 (E5)

#### Layer bk5 «Product Description Container» — CONTAINED LAYER (contain, anchor x=-1, y=1), design box x=33 y=625 381×105
- [e16] group «Product Description Container»
    ↳ animation: opacity: 7.856→8.066s 1→0 (E4)
  - [e17] text pos(33,625) size 183×105 | mix-blend-mode:difference; white-space:pre-wrap; text-align:left; font-family:'Aloevera Display'; font-weight:200; font-size:38px; line-height:0.93; letter-spacing:-0.01em; color:#c58878 | TEXT: "Unleash your inner beast."
      ↳ animation: opacity: 2.496→3.005s 0→1 (E4); offsetY: 2.496→3.647s 62→0 (E1)
  - [e18] text pos(285,634) size 129×96 | mix-blend-mode:difference; white-space:pre-wrap; text-align:left; font-family:'Aloevera Display'; font-weight:300; font-size:14px; line-height:1.17; color:#c58878 | TEXT: "Discover elegance and craftsmanship with our exquisite jewelry collection, designed to inspire and shine."
      ↳ animation: opacity: 2.675→3.177s 0→1 (E4); offsetY: 2.675→3.784s 62→0 (E1)

#### Layer bk6 «Woman Necklace Image 2» — CONTAINED LAYER (contain, anchor x=1, y=1), design box x=1249 y=432.95 161×201
- [e19] box «Woman Necklace Image 2» pos(1249,432.95) size 161×201 | border-radius:40px
    ↳ animation: opacity: 2.514→2.824s 0→1 (E4); 7.885→8.328s 1→0 (E4); offsetY: 2.514→4.209s 92.05→0 (E6); 7.89→9.235s 0→-344.95 (linear); 9.235→9.241s -344.95→-123.95 (E1)
  - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/primal/assets/img/1c8b46370c.webp: absolutely positioned `<img alt="">` at (0,0), 1122×1402 px, max-width:none, transform-origin 0 0 and transform matrix(0.1921,0,0,0.1921,-27.274,-34.17); the container clips it (overflow hidden, same border-radius)

#### Layer bk7 «Woman Necklace Image 1» — CONTAINED LAYER (contain, anchor x=1, y=0), design box x=1061 y=362.95 161×201
- [e20] box «Woman Necklace Image 1» pos(1061,362.95) size 161×201 | border-radius:40px
    ↳ animation: opacity: 2.675→2.981s 0→1 (E4); 7.894→8.31s 1→0 (E4); offsetY: 2.675→4.208s 114.05→0 (E7); 7.873→9.234s 0→-137.95 (E1)
  - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/primal/assets/img/47d81e9d82.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)

#### Layer bk8 «Jewelry Buttons» — CONTAINED LAYER (contain, anchor x=1, y=1), design box x=1315 y=673.95 80×80
- [e21] box <a href="#"> aria-label="Jewel 2" «Jewelry Buttons» pos(1315,673.95) size 80×80 | border-radius:66px; overflow:hidden; background-color:#ffffff
    ↳ animation: offsetY: 2.905→3.879s 164.05→0 (E1)
  - [e22] box «Jewelry Image 2» transform translate(17.33,61.33) rotate(-1.57rad) size 42.67×44.67
    - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/primal/assets/img/29a1fd1750-7cb199.webp: absolutely positioned `<img alt="">` at (0,0), 1968×2464 px, max-width:none, transform-origin 0 0 and transform matrix(0.0513,0,0,0.0513,-31.182,-43.631); the container clips it (overflow hidden, same border-radius)

#### Layer bk9 «Jewelry Buttons» — CONTAINED LAYER (contain, anchor x=1, y=1), design box x=1218 y=673.95 80×80
- [e23] box <a href="#"> aria-label="Jewel 1" «Jewelry Buttons» pos(1218,673.95) size 80×80 | border-radius:66px; overflow:hidden; background-color:#ffffff
    ↳ animation: offsetY: 3.096→4.07s 164.05→0 (E1)
  - [e24] box «Jewelry Image 1» pos(18,17) size 44×46
    - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/primal/assets/img/168e446c4d-83538e.webp: absolutely positioned `<img alt="">` at (0,0), 1968×2464 px, max-width:none, transform-origin 0 0 and transform matrix(0.0223,0,0,0.0284,0,-12.057); the container clips it (overflow hidden, same border-radius)

#### Layer bk10 «Explore Button» — CONTAINED LAYER (contain, anchor x=0, y=1), design box x=626 y=610 188.38×188.38
- [e25] group <a href="#"> «Explore Button»
    ↳ animation: offsetY: 9.234→10.114s 0→210 (E1)
  - [e26] box «Ellipse 2» pos(626,610) size 188.38×188.38 | opacity:0.5; border-radius:50%
    - border (stroke): inset:-1px; border-radius:50%; border:1px solid rgba(255,255,255,0.8)
  - [e27] box «Ellipse 1» pos(659.47,643.47) size 121.44×121.44 | border-radius:50%; background-color:#ffffff
  - [e28] text pos(686.69,694.69) size 67×17 | white-space:pre; text-align:center; font-family:'Outfit'; font-weight:500; font-size:15px; line-height:1.14; text-transform:uppercase; color:#34414b | TEXT: "Explore"

#### Layer bk11 «Top Navigation Bar» — CONTAINED LAYER (contain, anchor x=0, y=-1), design box x=19.51 y=14 1384.49×48
- [e29] group «Top Navigation Bar»
    ↳ animation: offsetY: 0→1.854s -102→0 (E1)
  - [e30] group <a href="#"> aria-label="Primal — Home" «Logo Container»
    - [e31] box «Logo Image» pos(19.51,21) size 26.66×32.93 | mix-blend-mode:multiply
      - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/primal/assets/img/a73bf73aad-a.webp: absolutely positioned `<img alt="">` at (0,0), 2048×2048 px, max-width:none, transform-origin 0 0 and transform matrix(0.0296,0,0,0.0296,-17.06,-13.924); the container clips it (overflow hidden, same border-radius)
  - [e32] box «Navigation Actions» pos(1161,14) size 243×48
    - [-] list <nav> aria-label="Shop actions"
      - [-] list <ul>
        - [e33] box <li> «MagnifyingGlass» pos(0,16) size 16×16 | mix-blend-mode:difference
          - [-] link <a href="#"> aria-label="Search" that covers its container's whole box (inset:0)
            - [e35] vector «Vector» pos(1.33,1.33) size 11.34×11.34
              - vector drawing SVG-1 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
            - [e36] vector «Vector» pos(9.86,9.86) size 4.81×4.81
              - vector drawing SVG-2 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
        - [e37] box <li> «HandbagSimple» pos(55,16) size 16×16
          - [-] link <a href="#"> aria-label="Bag" that covers its container's whole box (inset:0)
            - [e39] vector «Vector» pos(0.83,3.83) size 14.34×9.84
              - vector drawing SVG-3 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
            - [e40] vector «Vector» pos(4.83,0.83) size 6.34×4.34
              - vector drawing SVG-4 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
        - [e41] box <li> «Buy Now Container» pos(110,0) size 133×48 | border-radius:999px; background-color:#ffffff
          - [-] link <a href="#"> that covers its container's whole box (inset:0)
            - [e42] text pos(22,19.5) size 63×9 | white-space:pre; text-align:right; text-box:trim-both cap alphabetic; font-family:'Aloevera Display'; font-weight:500; font-size:14px; line-height:1.14; text-transform:uppercase; color:#1b2f4c | TEXT: "Buy Now"
            - [e43] box «HandbagSimple» pos(95,16) size 16×16
              - [e45] vector «Vector» pos(0.83,3.83) size 14.34×9.84
                - vector drawing SVG-3 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
              - [e46] vector «Vector» pos(4.83,0.83) size 6.34×4.34
                - vector drawing SVG-4 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box

#### Layer bk12 «Unleash yourself» — CONTAINED LAYER (contain, anchor x=0, y=-1), design box x=200 y=102 1040×70
- [e47] text pos(200,102) size 1040×70 | mix-blend-mode:difference; white-space:pre; text-align:center; text-box:trim-both cap alphabetic; font-family:'TOP LUXURY'; font-weight:400; font-size:100px; line-height:0.84; letter-spacing:-0.02em; text-transform:uppercase; color:#c58878 | TEXT: "Unleash yourself"
    ↳ animation: opacity: 9.794→10.209s 0→1 (E4); offsetY: 9.794→11.788s 244→0 (E1)

#### Layer bk13 «cards» — CONTAINED LAYER (contain, anchor x=0, y=1), design box x=366 y=457 684×321
- [e48] group «cards»
  - [-] list <ul>
    - [e49] group <li> «card1»
        ↳ animation: rotation(°): 10.789→11.187s -7→0 (E1); offsetX: 10.789→12.95s -5.24→0 (E1); offsetY: 10.789→12.95s 395.66→0 (E1) [rotation/scale around the group origin [466,617.5] (transform-origin in px)]
      - [-] link <a href="#"> that covers its container's whole box (inset:0)
        - [e50] vector «Subtract» pos(366,457) size 200×321
          - vector drawing SVG-5 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
        - [e51] text pos(386.49,633) size 90×52 | mix-blend-mode:difference; white-space:pre; text-align:left; font-family:'Aloevera Display'; font-weight:200; font-size:28px; line-height:0.93; letter-spacing:-0.01em; font-feature-settings:'zero' 1; color:#c58878 | TEXT: "Ocean
Heart"
        - [e52] text pos(386,698) size 160×48 | mix-blend-mode:difference; white-space:pre-wrap; text-align:left; font-family:'Aloevera Display'; font-weight:300; font-size:14px; line-height:1.17; font-feature-settings:'zero' 1; color:#c58878 | TEXT: "A stunning jewel ring that captures the essence of the sea."
        - [e53] box «Frame 42» pos(366,457) size 200×162 | border-radius:40px 40px 0px 40px; overflow:hidden
          - [e54] box «a8be356d-2bfc-4da7-a692-993957fb52c3-2026-06-30 5» pos(-25,-159) size 371×321 | border-radius:40px 0px 0px 0px
            - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/primal/assets/img/eebd383d3e-5ccc07.webp: absolutely positioned `<img alt="">` at (0,0), 2880×2880 px, max-width:none, transform-origin 0 0 and transform matrix(0.2852,0,0,0.2645,-428.57,-397); the container clips it (overflow hidden, same border-radius)
        - [e55] group «Group 44»
          - [e56] box «Ellipse 13» pos(494,464) size 44.78×44.78 | border-radius:50%; background-color:#ffffff
          - [e57] group «Group 45»
            - [e58] box «Ellipse 15» transform translate(512.66,481.33) rotate(0.78rad) size 2.71×2.71 | border-radius:50%; background-color:#0056a1
            - [e59] box «Ellipse 17» transform translate(519.33,487.99) rotate(0.78rad) size 2.71×2.71 | border-radius:50%; background-color:#0056a1
            - [e60] box «Ellipse 16» transform translate(519.33,481.33) rotate(0.78rad) size 2.71×2.71 | border-radius:50%; background-color:#0056a1
    - [e61] group <li> «card2»
        ↳ animation: rotation(°): 10.962→11.36s -7→0 (E1); offsetX: 10.962→13.123s -5.24→0 (E1); offsetY: 10.962→13.123s 395.66→0 (E1) [rotation/scale around the group origin [708,617.5] (transform-origin in px)]
      - [-] link <a href="#"> that covers its container's whole box (inset:0)
        - [e62] vector «Subtract» pos(608,457) size 200×321
          - vector drawing SVG-5 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
        - [e63] text pos(628.49,633) size 120×52 | mix-blend-mode:difference; white-space:pre; text-align:left; font-family:'Aloevera Display'; font-weight:200; font-size:28px; line-height:0.93; letter-spacing:-0.01em; font-feature-settings:'zero' 1; color:#c58878 | TEXT: "Sapphire 
Tide"
        - [e64] text pos(628,698) size 160×48 | mix-blend-mode:difference; white-space:pre-wrap; text-align:left; font-family:'Aloevera Display'; font-weight:300; font-size:14px; line-height:1.17; font-feature-settings:'zero' 1; color:#c58878 | TEXT: "A radiant necklace inspired by the rhythm of blue waves."
        - [e65] box «Frame 43» pos(608,457) size 200×162 | border-radius:40px 40px 0px 40px; overflow:hidden
          - [e66] box «a8be356d-2bfc-4da7-a692-993957fb52c3-2026-06-30 5» pos(-25,-159) size 371×321
            - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/primal/assets/img/eebd383d3e-5ccc07.webp: absolutely positioned `<img alt="">` at (0,0), 2880×2880 px, max-width:none, transform-origin 0 0 and transform matrix(0.2555,0,0,0.2459,-364.892,0); the container clips it (overflow hidden, same border-radius)
        - [e67] group «Group 44»
          - [e68] box «Ellipse 13» pos(736,464) size 44.78×44.78 | border-radius:50%; background-color:#ffffff
          - [e69] group «Group 45»
            - [e70] box «Ellipse 15» transform translate(754.66,481.33) rotate(0.78rad) size 2.71×2.71 | border-radius:50%; background-color:#0056a1
            - [e71] box «Ellipse 17» transform translate(761.33,487.99) rotate(0.78rad) size 2.71×2.71 | border-radius:50%; background-color:#0056a1
            - [e72] box «Ellipse 16» transform translate(761.33,481.33) rotate(0.78rad) size 2.71×2.71 | border-radius:50%; background-color:#0056a1
    - [e73] group <li> «card3»
        ↳ animation: rotation(°): 11.098→11.496s -7→0 (E1); offsetX: 11.098→13.259s -5.24→0 (E1); offsetY: 11.098→13.259s 395.66→0 (E1) [rotation/scale around the group origin [950,617.5] (transform-origin in px)]
      - [-] link <a href="#"> that covers its container's whole box (inset:0)
        - [e74] vector «Subtract» pos(850,457) size 200×321
          - vector drawing SVG-5 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
        - [e75] text pos(870.49,633) size 87×52 | mix-blend-mode:difference; white-space:pre; text-align:left; font-family:'Aloevera Display'; font-weight:200; font-size:28px; line-height:0.93; letter-spacing:-0.01em; font-feature-settings:'zero' 1; color:#c58878 | TEXT: "Crystal
Drop"
        - [e76] text pos(870,698) size 160×48 | mix-blend-mode:difference; white-space:pre-wrap; text-align:left; font-family:'Aloevera Display'; font-weight:300; font-size:14px; line-height:1.17; font-feature-settings:'zero' 1; color:#c58878 | TEXT: "A delicate jewel piece with drops of ocean-blue light."
        - [e77] box «Frame 43» pos(850,457) size 200×162 | border-radius:40px 40px 0px 40px; overflow:hidden
          - [e78] box «a8be356d-2bfc-4da7-a692-993957fb52c3-2026-06-30 5» pos(-85,-203) size 449×389
            - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/primal/assets/img/eebd383d3e-5ccc07.webp: absolutely positioned `<img alt="">` at (0,0), 2880×2880 px, max-width:none, transform-origin 0 0 and transform matrix(0.3191,0,0,0.2701,0,-389); the container clips it (overflow hidden, same border-radius)
        - [e79] group «Group 44»
          - [e80] box «Ellipse 13» pos(978,464) size 44.78×44.78 | border-radius:50%; background-color:#ffffff
          - [e81] group «Group 45»
            - [e82] box «Ellipse 15» transform translate(996.66,481.33) rotate(0.78rad) size 2.71×2.71 | border-radius:50%; background-color:#0056a1
            - [e83] box «Ellipse 17» transform translate(1003.33,487.99) rotate(0.78rad) size 2.71×2.71 | border-radius:50%; background-color:#0056a1
            - [e84] box «Ellipse 16» transform translate(1003.33,481.33) rotate(0.78rad) size 2.71×2.71 | border-radius:50%; background-color:#0056a1

## 10. Final adjustments
- [e30] «Logo Container»: enlarge ×1.65 keeping the point (19.5, 21) of its container fixed (equivalent to `transform: scale(1.65)` with `transform-origin: 19.5px 21px` in container coordinates). Its content keeps its animation.
- [e33] «MagnifyingGlass»: enlarge ×1.4 keeping the point (8, 24) of its container fixed (equivalent to `transform: scale(1.4)` with `transform-origin: 8px 24px` in container coordinates). Its content keeps its animation.
- [e37] «HandbagSimple»: enlarge ×1.4 keeping the point (63, 24) of its container fixed (equivalent to `transform: scale(1.4)` with `transform-origin: 63px 24px` in container coordinates). Its content keeps its animation.
- Icons [e33] «MagnifyingGlass» and [e37] «HandbagSimple»: no mix-blend-mode (`normal`) and their SVGs in pure black: every stroke `stroke:#000` and every fill other than `none` `fill:#000`.

## 11. Mobile view
- It activates when the stage width is < 768 px or the width/height ratio is < 0.82 (add the class `is-m` to `<html>`). Mobile reference design: 390×844 px.
- sm = min(W/390, H/844); ox = (W − 390·sm)/2; oy = (H − 844·sm)/2.
- Listed contained layer with {x, y, z, ax, ay}: its scale is sm·z (z = 1 if not given) and the top-left corner of its «design box» is placed on screen at (ox·(1+ax) + x·sm, oy·(1+ay) + y·sm) (ax, ay = 0 if not given); the rest of its content keeps its position relative to that box.
- Bleed layer: z = max(W/1440, H/810)·(given z or 1); screen = ((W − 1440·z)·fx + X·z, (H − 810·z)·fy + Y·z), with fx, fy = 0.5 unless another value is given.
- Contained layers that are not in the list are hidden on mobile.
- Per-element adjustments: «hide» = `display:none`; «shift dx, dy» = move the element by that distance in px of its container (if there is also «scale ×z», it is scaled from its top-left corner); «multiply its animated offsets by [kx, ky]» = multiply the total value of offsetX by kx and of offsetY by ky (the mobile «shift dx, dy» is not multiplied); «fade out between t1 and t2» = multiply its opacity by clamp((t2 − t)/(t2 − t1), 0, 1) from t1 on; «place the element point (rx, ry) at (mx, my) of the mobile design at scale e×sm» = draw the element at scale Se = e·sm with its top-left corner (animated x, y) at (ox + mx·sm + (x − rx)·Se, oy + my·sm + (y − ry)·Se); «width» = fixed width in px; «transition … between t1 and t2» = go from its layer's normal placement (the mobile one) to that placement with smoothstep interpolation (u²·(3 − 2u)) of scale and origin between those times.
- On mobile the track uses 40 vh per second (see section 7).
Mobile layer placement:
- layer «Top Navigation Bar»: x=16, y=14, z=0.9
- layer «Unleash yourself»: x=15.6, y=104, z=0.34
- layer «PRIMAL»: x=9.16, y=150, z=0.31
- layer «Woman Necklace Image 1»: x=196, y=330, z=0.6
- layer «Woman Necklace Image 2»: x=290, y=372, z=0.6
- layer «Product Description Container»: x=16, y=578, z=0.85
- layer «Explore Button»: x=16, y=704, z=0.56
- layer bk9 «Jewelry Buttons»: x=252, y=740, z=0.7
- layer bk8 «Jewelry Buttons»: x=318, y=740, z=0.7
- layer «cards»: x=17.16, y=560, z=0.52
- layer «Footer Background»: hide
- layer «Music Button»: hide
Unlisted non-bleed layers are hidden on mobile.
Per-element mobile adjustments:
- [e32] «Navigation Actions»: shift dx=-986.5, dy=0 (design units of its layer)
- [e49] «card1»: multiply its animated offsets by [1,2.3]
- [e61] «card2»: multiply its animated offsets by [1,2.3]
- [e73] «card3»: multiply its animated offsets by [1,2.3]
- [e25] «Explore Button»: multiply its animated offsets by [1,1.7]

## 12. Semantics and accessibility
- `<main class="hero" aria-label="Primal — Unleash Yourself">`; the loader has `aria-hidden="true"`.
- Lists with `<ul>` and `<li>`; menus inside `<nav aria-label="…">`. Each `<a href="#">` uses the given `aria-label` when its content is not text. If a link covers a whole box, it goes inside it as an absolute `<a class="lk">` covering it (`inset:0`).
- Decorative images with `alt=""`; decorative SVGs with `aria-hidden="true"`.
- Only texts and links receive pointer events (CSS in section 3) and invisible elements get `visibility:hidden`, so nothing that cannot be seen can ever be clicked.
- Visible focus: `outline: 2px solid currentColor; outline-offset: 3px`.

## 13. Resources (absolute URLs)
All resources are hosted in the public repository `danielsnows/F4M-Bluebird` and served by jsDelivr (CDN with CORS enabled):
- `assets/fonts/AloeveraDisplay-ExtraLight.woff2` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/primal/assets/fonts/AloeveraDisplay-ExtraLight.woff2
- `assets/fonts/AloeveraDisplay-Light.woff2` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/primal/assets/fonts/AloeveraDisplay-Light.woff2
- `assets/fonts/AloeveraDisplay-Medium.woff2` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/primal/assets/fonts/AloeveraDisplay-Medium.woff2
- `assets/fonts/Outfit-500.woff2` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/primal/assets/fonts/Outfit-500.woff2
- `assets/fonts/TOPLUXURY.woff2` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/primal/assets/fonts/TOPLUXURY.woff2
- `assets/img/168e446c4d-83538e.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/primal/assets/img/168e446c4d-83538e.webp
- `assets/img/1c8b46370c.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/primal/assets/img/1c8b46370c.webp
- `assets/img/29a1fd1750-7cb199.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/primal/assets/img/29a1fd1750-7cb199.webp
- `assets/img/47d81e9d82.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/primal/assets/img/47d81e9d82.webp
- `assets/img/a73bf73aad-a.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/primal/assets/img/a73bf73aad-a.webp
- `assets/img/eebd383d3e-5ccc07.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/primal/assets/img/eebd383d3e-5ccc07.webp
- `assets/video/a5af3797c8.jpg` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/primal/assets/video/a5af3797c8.jpg
- `assets/video/a5af3797c8.mp4` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/primal/assets/video/a5af3797c8.mp4

## 14. Appendix: vector shapes
Each SVG fills 100% of its element's box (`position:absolute; inset:0; width:100%; height:100%; overflow:visible`, `preserveAspectRatio="none"`, `aria-hidden="true"`). If an element says «with fill #xxxxxx (instead of #yyyyyy)», use the same SVG changing that fill color. If an SVG with internal `id`s (masks, gradients) is used more than once, give each copy unique ids and update its `url(#…)` references.

**SVG-1**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 12 12" xmlns="http://www.w3.org/2000/svg"> <path d="M5.67078 10.6708C8.4322 10.6708 10.6708 8.4322 10.6708 5.67078C10.6708 2.90935 8.4322 0.670776 5.67078 0.670776C2.90935 0.670776 0.670776 2.90935 0.670776 5.67078C0.670776 8.4322 2.90935 10.6708 5.67078 10.6708Z" stroke="#777777" stroke-linecap="round" stroke-linejoin="round" stroke-width="1.34167"></path> </svg>
```

**SVG-2**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 5 5" xmlns="http://www.w3.org/2000/svg"> <path d="M0.670776 0.670776L4.13515 4.13515" stroke="#777777" stroke-linecap="round" stroke-linejoin="round" stroke-width="1.34167"></path> </svg>
```

**SVG-3**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 15 10" xmlns="http://www.w3.org/2000/svg"> <path d="M12.2735 0.670776H2.06479C1.94189 0.670761 1.82323 0.715673 1.73114 0.797057C1.63905 0.878442 1.57989 0.990684 1.56479 1.11265L0.674168 8.61265C0.665929 8.68302 0.672742 8.75434 0.694153 8.82188C0.715564 8.88942 0.751086 8.95164 0.798363 9.00442C0.84564 9.05719 0.903594 9.09931 0.968383 9.12799C1.03317 9.15667 1.10332 9.17126 1.17417 9.17078H13.1642C13.235 9.17126 13.3052 9.15667 13.37 9.12799C13.4347 9.09931 13.4927 9.05719 13.54 9.00442C13.5872 8.95164 13.6228 8.88942 13.6442 8.82188C13.6656 8.75434 13.6724 8.68302 13.6642 8.61265L12.7735 1.11265C12.7584 0.990684 12.6993 0.878442 12.6072 0.797057C12.5151 0.715673 12.3964 0.670761 12.2735 0.670776Z" stroke="#1B2F4C" stroke-linecap="round" stroke-linejoin="round" stroke-width="1.34167"></path> </svg>
```

**SVG-4**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 7 5" xmlns="http://www.w3.org/2000/svg"> <path d="M0.670776 3.67078V3.17078C0.670776 2.50774 0.934168 1.87185 1.40301 1.40301C1.87185 0.934168 2.50774 0.670776 3.17078 0.670776C3.83382 0.670776 4.4697 0.934168 4.93854 1.40301C5.40738 1.87185 5.67078 2.50774 5.67078 3.17078V3.67078" stroke="#1B2F4C" stroke-linecap="round" stroke-linejoin="round" stroke-width="1.34167"></path> </svg>
```

**SVG-5**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 200 321" xmlns="http://www.w3.org/2000/svg"> <path d="M116 31.5C116 48.897 130.103 63 147.5 63H160C182.091 63 200 80.9086 200 103V281C200 303.091 182.091 321 160 321H40C17.9086 321 0 303.091 0 281V40C1.90082e-06 17.9086 17.9086 3.2213e-07 40 0H84.5C101.897 0 116 14.103 116 31.5Z" fill="white"></path> </svg>
```

## 15. Acceptance criteria
- Compared with https://primal-snows.vercel.app, the result matches at 1440×810, 1280×720 and 390×844 at the key moments: end of the intro, every pause and the final state.
- Positions, sizes, colors, fonts, timings and curves match this specification.
- All texts are real, editable HTML; all links are clickable `<a href="#">` with visible focus; invisible things cannot be clicked.
- Images, video and fonts load from the URLs in section 13 and there are no console errors.
- The layout adapts when the window is resized and follows section 11 on mobile.
- With `prefers-reduced-motion` the final state is shown directly.
