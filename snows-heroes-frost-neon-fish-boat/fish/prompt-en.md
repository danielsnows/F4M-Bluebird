# Replication prompt — Flow — Floating Through Aqua Dreams

> Complete instructions for an AI coding agent. It contains everything needed to rebuild the animated hero «Flow — Floating Through Aqua Dreams» identically: structure, styles, texts, animation timings and curves, mobile view and links to the resources (images, video and fonts).

## Role and goal
Act as a senior front-end developer specialized in web animation. Build a single-screen page (hero) that exactly reproduces the animated design «Flow — Floating Through Aqua Dreams» described here, without adding or removing anything.

## 1. Overview
Hero «Flow — Floating Through Aqua Dreams», designed at 1440×810 with a soft-blue aquatic look. Full-bleed in the background, a video of a translucent betta fish swimming above the water, driven by the scroll. The automatic intro (0 → 1.65 s) brings in, letter by letter, the title «Floating / Through / Aqua Dreams», the «Shop Now» button, the «24k Motion Loop» ring of dots (which turns into place), the title «Silence Moves Beneath Light», the right column with two fish pills and its text, and the top bar (logo, Flow · Current · Depth · Motion and search, user and menu icons). On scroll (7.5 → 10 s) all that content leaves upwards and the second scene arrives: the title «Drift Beyond Still Waters», the text «Silence Moves Beneath Light» and four bottom cards (Ethereal Motion, Ambient Depth, Surface Reflection, Silent Current) that rise one after another until 11.85 s. On hover, buttons take a soft aqua tint and menu links change colour.

## 2. Deliverable and technical rules
- A single `index.html` file with the CSS inside `<style>` and the JavaScript inside `<script>`. Plain (vanilla) JavaScript, no frameworks and no build step.
- The only external dependency: the Lenis library, exact version 1.3.26 (npm package `lenis`, file `dist/lenis.min.js`, which exposes the global class `Lenis`), for smooth scrolling. Load it with a `<script>` tag from a public npm CDN before your script (if it fails to load, everything must work with native scrolling).
- Images, video and fonts are ALWAYS loaded from the absolute URLs in section 13 «Resources». Do not download them, do not inline them as base64 and do not use any other resources.
- Every visible text is real, selectable and editable HTML text (never text turned into an image or into SVG paths). The vector shapes in the Appendix are only icons and decoration.
- Lists use `<ul>`/`<li>` (inside `<nav aria-label="…">` where indicated) and links use clickable `<a href="#">`, with `aria-label` where indicated.
- Head: `<html lang="en">`, `<meta charset="utf-8">`, `<meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover">`, `<title>Flow — Motion Loop</title>`, `<meta name="description" content="Flow: an iridescent fish gliding over calm water.">`, `<meta name="theme-color" content="#9fbad3">`.
- Respect `prefers-reduced-motion: reduce`: the loader works the same, but there is no intro, no smooth scrolling and no snap; t stays fixed on the final state (t = 11.85 s) even when scrolling.
- Do not add elements, texts, effects or sections that are not in this specification.

## 3. Base structure (common HTML + CSS)
```html
<body>
<div class="loader" id="loader" aria-hidden="true">
  <div class="loader-in"><span>FLOW</span><div class="loader-bar"><i id="loaderBar"></i></div><span id="loaderPct">000</span></div>
</div>
<main class="hero" id="hero" aria-label="Flow — Motion Loop">
  <div class="stage" id="stage">
    <!-- one layer (div.blk) per «Layer» in section 9, in the same order: later ones are on top -->
  </div>
</main>
<script>/* engine from section 6 */</script>
</body>
```
```css
:root{--bg:#9fbad3;--ink:#ffffff;color-scheme:light}
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
- **Kurdis** (variable font, weight range 1–1000 (font-weight:1 1000)): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/fish/assets/fonts/Kurdis-VariableVF.woff2
- **Noto Sans** (variable font, weight range 100–900 (font-weight:100 900)): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/fish/assets/fonts/noto-sans-VF-normal.woff2

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
  - `E2` = cubic-bezier(0.45, 0, 0.55, 1)
  - `E3` = spring with mass=1, stiffness=80, damping=20, normalized to 1.25 s (see formula)
  - `E4` = cubic-bezier(0, 0, 0.58, 1)
  - `E5` = spring with mass=1, stiffness=100, damping=15, normalized to 1.278 s (see formula)
- `cubic-bezier(x1, y1, x2, y2)` curves work as in CSS: given the time progress p, solve the parameter u such that X(u) = p and return Y(u).

Spring formula with mass m, stiffness k, damping c and normalized duration D:
- ω0 = √(k/m), ζ = c / (2·√(k·m)).
- If ζ < 1: ωd = ω0·√(1−ζ²); x(τ) = 1 − e^(−ζ·ω0·τ)·(cos(ωd·τ) + (ζ·ω0/ωd)·sin(ωd·τ)).
- If ζ = 1: x(τ) = 1 − e^(−ω0·τ)·(1 + ω0·τ).
- If ζ > 1: q = √(ζ²−1), r1 = −ω0·(ζ−q), r2 = −ω0·(ζ+q); x(τ) = 1 + (r2·e^(r1·τ) − r1·e^(r2·τ)) / (r1 − r2).
- easing(p) = x(p·D) / x(D) for 0 < p < 1; 0 if p ≤ 0; 1 if p ≥ 1.

## 7. Playback (intro and scrolling)
- Total timeline duration: 11.85 s. Automatic intro: from t = 0 to t = 1.65 s. The rest (1.65 → 11.85 s) is driven by scrolling.
- `.hero` height = 100vh + track. track = round((11.85 − 1.65) × 40) = **408vh** on desktop and round((11.85 − 1.65) × 34) = **347vh** on mobile (use exactly these values; they are set through the CSS variable `--track`). `.stage` is sticky, so the stage stays fixed while the track is scrolled.
- While loading: `html.is-locked` (overflow hidden), `history.scrollRestoration = 'manual'`, `scrollTo(0,0)`.
- When the loader gets the `is-done` class, play the intro: t advances in real time (linearly) from 0 to 1.65 s in 1.65 s. During the intro scrolling is blocked (Lenis stopped; if Lenis is not available, keep `is-locked` until the intro ends) and scroll events do not change t. When it ends, t = 1.65 and scrolling is enabled.
- Smooth scrolling with Lenis: `new Lenis({lerp:0.085, wheelMultiplier:0.9, smoothWheel:true})`, `lenis.stop()` until the intro ends, then `lenis.start()`; call `lenis.raf(now)` in the requestAnimationFrame loop. Without Lenis, use native scrolling.
- Scroll → time mapping: p = clamp((scrollY − hero.offsetTop) / (hero.offsetHeight − stage.clientHeight), 0, 1); t = 1.65 + p × (11.85 − 1.65).
- Pauses (holds), in seconds: [1.65 – 2], [2 – 7.5], [10 – 10.4], [11.85 – 11.85]. Snap (Lenis only; no snap without Lenis): the direction is the sign of the last scroll change; 170 ms after the last scroll event (every event restarts the timer), if t is not inside a pause (with a ±0.02 s margin), scroll with `lenis.scrollTo` to the start of the next pause when scrolling down, or to the end of the previous pause when scrolling up. Duration = clamp(|Δt| × 0.45, 0.6, 1.8) s; easing easeInOutCubic (x < 0.5 ? 4x³ : 1 − (−2x+2)³/2). No snap with `prefers-reduced-motion` or during another snap (the «snapping» state is released in `onComplete` or, as a safety net, after 2.2 s).
- On every resize: recompute layers, track and t from the current scroll position.
- Video («scrub» mode, driven by the timeline): `<video muted playsinline preload="auto">` with its poster; it never plays by itself. For reliable seeking, download the MP4 with `fetch` as a Blob (reporting progress to the loader) and set `video.src = URL.createObjectURL(blob)` (if that fails, use the direct URL). On every frame, if `readyState ≥ 2` and it is not `seeking`: target = min(max(0, t), duration − 0.04); if |currentTime − target| > 0.012, `currentTime = target`.

## 8. Loader and preloading
- The loader covers the screen with the `--bg` color and shows «FLOW», a 160×1 px bar and a 3-digit percentage (`000` → `100`) in the `--ink` color.
- Weighted progress: fonts (`document.fonts.ready`) weight 1; each `<img>` element on the stage weight 0.4, even if it repeats a file (it counts once it has loaded —or failed— and `img.decode()` has finished; the video poster does not count); the video weight 6 (its fetch download progress). Percentage = Math.round(progress × 100) with 3 digits. The bar uses `transform: scaleX(progress)` and never goes backwards.
- Wait until everything finishes (14 s maximum; if it runs out, continue without cancelling anything); then set the video `src` (the Blob URL if already downloaded, otherwise the direct URL) and wait for its `loadeddata` or `error` (3 s maximum). Then remove `is-locked` from `<html>` (except in the no-Lenis case described in section 7), add `is-done` to the loader (it fades out in 0.9 s) and start the intro.

## 9. Layers and elements
Notation of each line: `[eN]` = suggested identifier; `box` = div; `text` = div with text (the content in quotes, keeping capitalization, line breaks and spaces); `group` = boxless container; `vector` = box containing an inline SVG; `<a href="#">`, `<ul>`, `<li>`, `<nav>` = semantic tag to use; `«name»` = layer name in the design (for reference only, also handy as `data-name`); `pos(x,y)` = left/top; `size A×B` = width×height; after `|` come the literal CSS styles. Every `text` element gets `class="t"` (used by the pointer-events rule in section 3); boxes, groups and vectors may use `class="b"`, `"g"` and `"v"`. Decorative layers (border, glass, inner shadow, background blur, fill layer) are absolute elements (for example `<i>`) covering their container (given `inset`) with inline `pointer-events:none` and `border-radius:inherit` unless stated otherwise. Images go inside an absolute container `inset:0; overflow:hidden; border-radius:inherit`. The order of the lines is the stacking order (later is on top).

#### Layer bk0 «Fondo» — BLEED LAYER (cover), design box x=0 y=0 1440×810
- [e1] box «home» pos(0,0) size 1440×810 | background-color:#4c6f8a

#### Layer bk1 «Group 71» — BLEED LAYER (cover), design box x=0 y=0 1464.94×824.03
- [e2] group «Group 71»
  - [e3] box «Fish_moving_close_to_water_202605180208 1» transform translate(1452.47,-8.02) rotate(3.14rad) scale(1,-1) size 1464.94×824.03
    - video: `<video muted playsinline preload="auto" poster="https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/fish/assets/video/60573c67c5.jpg" data-src="https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/fish/assets/video/60573c67c5.mp4">` with position:absolute; inset:0; width:100%; height:100%; object-fit:cover; display:block (the `src` is assigned from JavaScript once preloading finishes, see sections 7 and 8)
  - [e4] box «Rectangle 7» pos(0,722) size 1440×88 | background-color:rgba(0,0,0,0.01)
    - background blur (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(25px); mask:linear-gradient(180deg, rgba(0,0,0,0), rgba(0,0,0,1))

#### Layer bk2 «Star 1» — CONTAINED LAYER (contain, anchor x=-1, y=-1), design box x=-505.28 y=-477.28 1200.28×1200.28
- [e5] vector «Star 1» pos(0,0) size 1262.86×810 | filter:blur(288.49px)
    ↳ animation: opacity: 7.5→10s 1→0 (E2)
  - vector drawing SVG-1 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box

#### Layer bk3 «stats» — CONTAINED LAYER (contain, anchor x=-1, y=1), design box x=332 y=569 216.44×216.44
- [e11] group «stats»
    ↳ animation: opacity: jump from 1 to 0 at 2s
  - [e6] box «stats» pos(332,569) size 216.44×216.44
    - [e7] group «Group 66» | opacity:0
        ↳ animation: opacity: 0.2→1.45s 0→1 (E3)
      - [e8] text pos(71.94,190.42) size 73×33 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:400; font-size:41.38px; line-height:1.04 | TEXT in runs: "24" {font-family:'Kurdis'; font-weight:400; font-size:41.38px; line-height:1.04; color:#ffffff} + "k" {font-family:'Kurdis'; font-weight:400; font-size:41.38px; line-height:1.04; color:#ffffff}
          ↳ animation: y: 0.2→1.45s 190.42→79.71 (E3)
      - [e9] text pos(67.72,249.43) size 81×18 | white-space:pre; text-align:center; font-family:'Noto Sans'; font-weight:400; font-size:14px; line-height:normal; letter-spacing:-0.02em; color:#ffffff | TEXT: "Motion Loop"
          ↳ animation: y: 0.2→1.45s 249.43→118.72 (E3)
      - [e10] vector «Repeat group 1» transform matrix(0,-1,1,0,-6.73,317.141) size 216.44×223.17
          ↳ animation: y: 0.2→1.45s 0→-100.7 (E3); rotation(°): 0.2→1.45s 0→90 (E3) [rotation around point [108.22,114.95] of the box]
        - vector drawing SVG-2 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box

#### Layer bk4 «Group 69» — CONTAINED LAYER (contain, anchor x=-1, y=-1), design box x=24 y=186 701×209
- [e12] group «Group 69»
    ↳ animation: opacity: jump from 1 to 0 at 2s
  - [e13] box «Floating - Animation ▶» pos(24,186) size 372×53
      ↳ animation: opacity: jump from 1 to 0 at 2s
    - [e16] group «F»
        ↳ animation: opacity: jump from 1 to 0 at 2s
      - [e14] box «F» pos(0,0) size 372×53
        - [e15] text pos(0,33) size 372×53 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "F" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "loating" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
            ↳ animation: y: 0→0.15s 33→0 (E4); opacity: 0→0.15s 0→1 (E4)
    - [e19] group «l»
        ↳ animation: opacity: jump from 1 to 0 at 2s
      - [e17] box «l» pos(0,0) size 372×53
        - [e18] text pos(0,33) size 372×53 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "F" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "l" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "oating" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
            ↳ animation: y: 0.02→0.17s 33→0 (E4); opacity: 0.02→0.17s 0→1 (E4)
    - [e22] group «o»
        ↳ animation: opacity: jump from 1 to 0 at 2s
      - [e20] box «o» pos(0,0) size 372×53
        - [e21] text pos(0,33) size 372×53 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Fl" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "o" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "ating" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
            ↳ animation: y: 0.04→0.19s 33→0 (E4); opacity: 0.04→0.19s 0→1 (E4)
    - [e25] group «a»
        ↳ animation: opacity: jump from 1 to 0 at 2s
      - [e23] box «a» pos(0,0) size 372×53
        - [e24] text pos(0,33) size 372×53 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Flo" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "a" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "ting" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
            ↳ animation: y: 0.06→0.21s 33→0 (E4); opacity: 0.06→0.21s 0→1 (E4)
    - [e28] group «t»
        ↳ animation: opacity: jump from 1 to 0 at 2s
      - [e26] box «t» pos(0,0) size 372×53
        - [e27] text pos(0,33) size 372×53 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Floa" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "t" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "ing" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
            ↳ animation: y: 0.08→0.23s 33→0 (E4); opacity: 0.08→0.23s 0→1 (E4)
    - [e31] group «i»
        ↳ animation: opacity: jump from 1 to 0 at 2s
      - [e29] box «i» pos(0,0) size 372×53
        - [e30] text pos(0,33) size 372×53 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Float" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "i" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "ng" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
            ↳ animation: y: 0.1→0.25s 33→0 (E4); opacity: 0.1→0.25s 0→1 (E4)
    - [e34] group «n»
        ↳ animation: opacity: jump from 1 to 0 at 2s
      - [e32] box «n» pos(0,0) size 372×53
        - [e33] text pos(0,33) size 372×53 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Floati" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "n" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "g" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
            ↳ animation: y: 0.12→0.27s 33→0 (E4); opacity: 0.12→0.27s 0→1 (E4)
    - [e37] group «g»
        ↳ animation: opacity: jump from 1 to 0 at 2s
      - [e35] box «g» pos(0,0) size 372×53
        - [e36] text pos(0,33) size 372×53 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Floatin" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "g" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff}
            ↳ animation: y: 0.14→0.29s 33→0 (E4); opacity: 0.14→0.29s 0→1 (E4)
  - [e38] box «Through - Animation ▶» pos(141,264) size 389×53
      ↳ animation: opacity: jump from 1 to 0 at 2s
    - [e41] group «T»
        ↳ animation: opacity: jump from 1 to 0 at 2s
      - [e39] box «T» pos(0,0) size 389×53
        - [e40] text pos(0,33) size 389×53 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "T" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "hrough" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
            ↳ animation: y: 0.2→0.35s 33→0 (E4); opacity: 0.2→0.35s 0→1 (E4)
    - [e44] group «h»
        ↳ animation: opacity: jump from 1 to 0 at 2s
      - [e42] box «h» pos(0,0) size 389×53
        - [e43] text pos(0,33) size 389×53 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "T" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "h" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "rough" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
            ↳ animation: y: 0.22→0.37s 33→0 (E4); opacity: 0.22→0.37s 0→1 (E4)
    - [e47] group «r»
        ↳ animation: opacity: jump from 1 to 0 at 2s
      - [e45] box «r» pos(0,0) size 389×53
        - [e46] text pos(0,33) size 389×53 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Th" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "r" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "ough" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
            ↳ animation: y: 0.24→0.39s 33→0 (E4); opacity: 0.24→0.39s 0→1 (E4)
    - [e50] group «o»
        ↳ animation: opacity: jump from 1 to 0 at 2s
      - [e48] box «o» pos(0,0) size 389×53
        - [e49] text pos(0,33) size 389×53 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Thr" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "o" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "ugh" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
            ↳ animation: y: 0.26→0.41s 33→0 (E4); opacity: 0.26→0.41s 0→1 (E4)
    - [e53] group «u»
        ↳ animation: opacity: jump from 1 to 0 at 2s
      - [e51] box «u» pos(0,0) size 389×53
        - [e52] text pos(0,33) size 389×53 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Thro" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "u" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "gh" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
            ↳ animation: y: 0.28→0.43s 33→0 (E4); opacity: 0.28→0.43s 0→1 (E4)
    - [e56] group «g»
        ↳ animation: opacity: jump from 1 to 0 at 2s
      - [e54] box «g» pos(0,0) size 389×53
        - [e55] text pos(0,33) size 389×53 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Throu" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "g" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "h" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
            ↳ animation: y: 0.3→0.45s 33→0 (E4); opacity: 0.3→0.45s 0→1 (E4)
    - [e59] group «h»
        ↳ animation: opacity: jump from 1 to 0 at 2s
      - [e57] box «h» pos(0,0) size 389×53
        - [e58] text pos(0,33) size 389×53 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Throug" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "h" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff}
            ↳ animation: y: 0.32→0.47s 33→0 (E4); opacity: 0.32→0.47s 0→1 (E4)
  - [e60] box «Aqua Dreams - Animation ▶» pos(141,342) size 584×53
      ↳ animation: opacity: jump from 1 to 0 at 2s
    - [e63] group «A»
        ↳ animation: opacity: jump from 1 to 0 at 2s
      - [e61] box «A» pos(0,0) size 584×53
        - [e62] text pos(0,33) size 584×53 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "A" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "qua Dreams" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
            ↳ animation: y: 0.4→0.55s 33→0 (E4); opacity: 0.4→0.55s 0→1 (E4)
    - [e66] group «q»
        ↳ animation: opacity: jump from 1 to 0 at 2s
      - [e64] box «q» pos(0,0) size 584×53
        - [e65] text pos(0,33) size 584×53 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "A" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "q" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "ua Dreams" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
            ↳ animation: y: 0.42→0.57s 33→0 (E4); opacity: 0.42→0.57s 0→1 (E4)
    - [e69] group «u»
        ↳ animation: opacity: jump from 1 to 0 at 2s
      - [e67] box «u» pos(0,0) size 584×53
        - [e68] text pos(0,33) size 584×53 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Aq" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "u" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "a Dreams" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
            ↳ animation: y: 0.44→0.59s 33→0 (E4); opacity: 0.44→0.59s 0→1 (E4)
    - [e72] group «a»
        ↳ animation: opacity: jump from 1 to 0 at 2s
      - [e70] box «a» pos(0,0) size 584×53
        - [e71] text pos(0,33) size 584×53 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Aqu" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "a" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + " Dreams" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
            ↳ animation: y: 0.46→0.61s 33→0 (E4); opacity: 0.46→0.61s 0→1 (E4)
    - [e75] group « »
        ↳ animation: opacity: jump from 1 to 0 at 2s
      - [e73] box « » pos(0,0) size 584×53
        - [e74] text pos(0,33) size 584×53 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Aqua" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + " " {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "Dreams" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
            ↳ animation: y: 0.48→0.63s 33→0 (E4)
    - [e78] group «D»
        ↳ animation: opacity: jump from 1 to 0 at 2s
      - [e76] box «D» pos(0,0) size 584×53
        - [e77] text pos(0,33) size 584×53 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Aqua " {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "D" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "reams" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
            ↳ animation: y: 0.5→0.65s 33→0 (E4); opacity: 0.5→0.65s 0→1 (E4)
    - [e81] group «r»
        ↳ animation: opacity: jump from 1 to 0 at 2s
      - [e79] box «r» pos(0,0) size 584×53
        - [e80] text pos(0,33) size 584×53 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Aqua D" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "r" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "eams" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
            ↳ animation: y: 0.52→0.67s 33→0 (E4); opacity: 0.52→0.67s 0→1 (E4)
    - [e84] group «e»
        ↳ animation: opacity: jump from 1 to 0 at 2s
      - [e82] box «e» pos(0,0) size 584×53
        - [e83] text pos(0,33) size 584×53 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Aqua Dr" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "e" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "ams" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
            ↳ animation: y: 0.54→0.69s 33→0 (E4); opacity: 0.54→0.69s 0→1 (E4)
    - [e87] group «a»
        ↳ animation: opacity: jump from 1 to 0 at 2s
      - [e85] box «a» pos(0,0) size 584×53
        - [e86] text pos(0,33) size 584×53 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Aqua Dre" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "a" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "ms" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
            ↳ animation: y: 0.56→0.71s 33→0 (E4); opacity: 0.56→0.71s 0→1 (E4)
    - [e90] group «m»
        ↳ animation: opacity: jump from 1 to 0 at 2s
      - [e88] box «m» pos(0,0) size 584×53
        - [e89] text pos(0,33) size 584×53 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Aqua Drea" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "m" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "s" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
            ↳ animation: y: 0.58→0.73s 33→0 (E4); opacity: 0.58→0.73s 0→1 (E4)
    - [e93] group «s»
        ↳ animation: opacity: jump from 1 to 0 at 2s
      - [e91] box «s» pos(0,0) size 584×53
        - [e92] text pos(0,33) size 584×53 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Aqua Dream" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "s" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff}
            ↳ animation: y: 0.6→0.75s 33→0 (E4); opacity: 0.6→0.75s 0→1 (E4)

#### Layer bk5 «main menu» — CONTAINED LAYER (contain, anchor x=0, y=-1), design box x=24 y=13.63 1392×50.37
- [e94] box «main menu» pos(24,13.63) size 1392×50.37
  - [e95] box <a href="#"> aria-label="Flow home" «image 24» pos(0,-100) size 50×50
      ↳ animation: y: 0.001→1.279s -100→0 (E5)
    - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/fish/assets/img/ed31cf5284.webp: absolutely positioned `<img alt="">` at (0,0), 1024×1024 px, max-width:none, transform-origin 0 0 and transform matrix(0.0889,0,0,0.0889,-20.528,-20.528); the container clips it (overflow hidden, same border-radius)
  - [e96] box <a href="#"> aria-label="Open menu" «DotsNine» pos(1352,-359.63) size 40×40
      ↳ animation: y: 0.001→1.279s -359.63→10.37 (E5)
    - [e98] vector «Vector» pos(7.5,7.5) size 3.75×3.75
      - vector drawing SVG-3 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
    - [e99] vector «Vector» pos(18.12,7.5) size 3.75×3.75
      - vector drawing SVG-3 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
    - [e100] vector «Vector» pos(28.75,7.5) size 3.75×3.75
      - vector drawing SVG-3 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
    - [e101] vector «Vector» pos(7.5,18.12) size 3.75×3.75
      - vector drawing SVG-3 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
    - [e102] vector «Vector» pos(18.12,18.12) size 3.75×3.75
      - vector drawing SVG-3 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
    - [e103] vector «Vector» pos(28.75,18.12) size 3.75×3.75
      - vector drawing SVG-3 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
    - [e104] vector «Vector» pos(7.5,28.75) size 3.75×3.75
      - vector drawing SVG-3 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
    - [e105] vector «Vector» pos(18.12,28.75) size 3.75×3.75
      - vector drawing SVG-3 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
    - [e106] vector «Vector» pos(28.75,28.75) size 3.75×3.75
      - vector drawing SVG-3 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
  - [e107] box <a href="#"> aria-label="Account" «User» pos(1293,-299.63) size 20×20
      ↳ animation: y: 0.001→1.279s -299.63→20.37 (E5)
    - [e109] vector «Vector» pos(4.33,1.83) size 11.34×11.34
      - vector drawing SVG-4 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
    - [e110] vector «Vector» pos(1.83,11.83) size 16.34×5.72
      - vector drawing SVG-5 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
  - [e111] box <a href="#"> aria-label="Search" «MagnifyingGlass» pos(1234,-249.63) size 20×20
      ↳ animation: y: 0.001→1.279s -249.63→20.37 (E5)
    - [e113] vector «Vector» pos(1.83,1.83) size 13.84×13.84
      - vector drawing SVG-6 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
    - [e114] vector «Vector» pos(12.5,12.5) size 5.67×5.67
      - vector drawing SVG-7 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
  - [e115] group «Group 73»
    - [-] list <nav> aria-label="Main navigation"
      - [-] list <ul>
        - [e116] text <li> pos(479,-134.82) size 35×20 | white-space:pre; text-align:left; font-family:'Noto Sans'; font-weight:400; font-size:16px; line-height:normal; color:#ffffff | TEXT: "" | inline link: the text is the content of an <a href="#"> inside the element
            ↳ animation: y: 0.001→1.279s -134.82→15.18 (E5)
        - [e117] text <li> pos(594,-184.82) size 58×20 | opacity:0.5; white-space:pre; text-align:left; font-family:'Noto Sans'; font-weight:400; font-size:16px; line-height:normal; color:#ffffff | TEXT: "" | inline link: the text is the content of an <a href="#"> inside the element
            ↳ animation: y: 0.001→1.279s -184.82→15.18 (E5)
        - [e118] text <li> pos(732,-244.82) size 47×20 | opacity:0.5; white-space:pre; text-align:left; font-family:'Noto Sans'; font-weight:400; font-size:16px; line-height:normal; color:#ffffff | TEXT: "" | inline link: the text is the content of an <a href="#"> inside the element
            ↳ animation: y: 0.001→1.279s -244.82→15.18 (E5)
        - [e119] text <li> pos(859,-304.82) size 54×20 | opacity:0.5; white-space:pre; text-align:left; font-family:'Noto Sans'; font-weight:400; font-size:16px; line-height:normal; color:#ffffff | TEXT: "" | inline link: the text is the content of an <a href="#"> inside the element
            ↳ animation: y: 0.001→1.279s -304.82→15.18 (E5)

#### Layer bk6 «button» — CONTAINED LAYER (contain, anchor x=-1, y=0), design box x=131 y=449 197×88
- [e128] group «button»
    ↳ animation: opacity: jump from 1 to 0 at 2s
  - [e120] box «button» pos(131,449) size 197×88 | opacity:0; border-radius:999px; overflow:hidden
      ↳ animation: width: 0.2→1.45s 197→217 (E3); opacity: 0.2→1.45s 0→1 (E3)
    - [e121] box <a href="#"> «Frame 1171275726» pos(-14,10) size 65×68 | border-radius:999px; background-color:#ffffff
        ↳ animation: x: 0.2→1.45s -14→10 (E3); width: 0.2→1.45s 65→197 (E3)
      - [e122] text transform translate(22.76,30.72) rotate(0rad) size 25×7 | opacity:0; white-space:pre; text-align:left; font-family:'Noto Sans'; font-weight:400; font-size:5.23px; line-height:normal; color:#000000 | TEXT: "Shop Now"
          ↳ animation: x: 0.2→1.45s 22.77→20 (E3); y: 0.2→1.45s 30.73→24 (E3); width: 0.2→1.45s 25→25.21 (E3); height: 0.2→1.45s 7→6.55 (E3); opacity: 0.2→1.45s 0→1 (E3); content-scale: 0.2→1.45s 1→3.05 (E3)
      - [e123] box «Frame 19» transform translate(6.5,60) rotate(-1.57rad) size 52×52 | border-radius:30px; background-color:#dbedf9
          ↳ animation: x: 0.2→1.45s 6.5→137 (E3); y: 0.2→1.45s 60→8 (E3); rotation(rad): 0.2→1.45s -1.57→0 (E3)
        - [e124] box «ArrowRight» pos(19,19) size 14×14
          - [e126] vector «Vector» pos(1.56,6.38) size 10.88×1.25
            - vector drawing SVG-8 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
          - [e127] vector «Vector» pos(7.25,2.44) size 5.19×9.12
            - vector drawing SVG-9 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
    - border (stroke): inset:0px; border-radius:999px; border:1px solid rgba(255,255,255,0.24)

#### Layer bk7 «right title» — CONTAINED LAYER (contain, anchor x=1, y=1), design box x=842.11 y=625.86 223.89×123.92
- [e133] group «right title»
    ↳ animation: opacity: jump from 1 to 0 at 2s
  - [e129] box «right title» pos(842.11,625.86) size 223.89×123.92
    - [e130] group «Group 70»
      - [e131] text pos(-84.75,0) size 172×59 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:32px; line-height:1.04 | TEXT in runs: "Silence 
" {font-family:'Kurdis'; font-weight:600; font-size:32px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "Moves" {font-family:'Kurdis'; font-weight:600; font-size:32px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:rgba(255,255,255,0.4)}
          ↳ animation: x: 0.2→1.45s -84.75→0 (E3); opacity: 0.2→1.45s 0→1 (E3)
      - [e132] text pos(-103.11,64.92) size 187×59 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:32px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff | TEXT: "Beneath 
Light"
          ↳ animation: x: 0.2→1.45s -103.11→36.89 (E3); opacity: 0.2→1.45s 0→1 (E3)

#### Layer bk8 «right section» — CONTAINED LAYER (contain, anchor x=1, y=1), design box x=1137.5 y=462.77 273×337.55
- [e143] group «right section»
    ↳ animation: opacity: jump from 1 to 0 at 2s
  - [e134] box «right section» pos(1137.5,462.77) size 273×337.55
    - [e135] text pos(189,171.05) size 134×108 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Noto Sans'; font-weight:400; font-size:14px; line-height:normal; color:#ffffff | TEXT: "Designed to evoke calmness through fluid interactions, atmospheric depth, and abstract aquatic movement."
        ↳ animation: x: 0.4→1.65s 189→139 (E3); opacity: 0.4→1.65s 0→1 (E3)
    - [e136] group «Group 64» | opacity:0
        ↳ animation: opacity: 0.4→1.65s 0→1 (E3)
      - [e137] box «Rectangle 8» transform translate(103.5,256.04) rotate(3.14rad) scale(1,-1) size 53.5×97.5 | border-radius:99px; background-color:#ffffff
          ↳ animation: y: 0.4→1.65s 256.05→112.55 (E3); height: 0.4→1.65s 97.5→221 (E3)
      - [e138] box «image 21» transform translate(103.5,256.40) rotate(3.14rad) scale(1,-1) size 47.93×97.14
          ↳ animation: y: 0.4→1.65s 256.41→127.77 (E3); content-scale: 0.4→1.65s 1→2.16 (E3)
        - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/fish/assets/img/95f782d606-5ccc07.webp: absolutely positioned `<img alt="">` at (0,0), 1856×2464 px, max-width:none, transform-origin 0 0 and transform matrix(0.0394,0,0,0.0394,-25.243,0); the container clips it (overflow hidden, same border-radius)
    - [e139] group «Group 65» | opacity:0
        ↳ animation: opacity: 0.4→1.65s 0→1 (E3)
      - [e140] box «Rectangle 8» pos(200.01,-69.58) size 53.5×123.13 | border-radius:99px; background-color:#ffffff
          ↳ animation: y: 0.4→1.65s -69.58→0 (E3)
      - [e141] box «image 22» pos(200.01,-69.58) size 53.5×123.13 | border-radius:99px
          ↳ animation: y: 0.4→1.65s -69.58→0 (E3)
        - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/fish/assets/img/34fc44b9cc-187f7f.webp: absolutely positioned `<img alt="">` at (0,0), 1856×2464 px, max-width:none, transform-origin 0 0 and transform matrix(0.0669,0,0,0.0669,-48.173,-8.925); the container clips it (overflow hidden, same border-radius)
      - [e142] box «image 23» pos(239.01,2.42) size 31.99×58.23
          ↳ animation: y: 0.4→1.65s 2.42→72 (E3)
        - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/fish/assets/img/34fc44b9cc-187f7f.webp: absolutely positioned `<img alt="">` at (0,0), 1856×2464 px, max-width:none, transform-origin 0 0 and transform matrix(0.0669,0,0,0.0669,-87.172,-80.925); the container clips it (overflow hidden, same border-radius)

#### Layer bk9 «Group 74» — CONTAINED LAYER (contain, anchor x=0, y=0), design box x=24 y=186 1386.5×614.32
- [e144] group «Group 74» | opacity:0
    ↳ animation: opacity: jump from 0 to 1 at 2s
  - [e145] box «stats» pos(332,569) size 216.44×216.44 | opacity:0
      ↳ animation: y: 7.5→10s 569→-254.27 (E2); opacity: jump from 0 to 1 at 2s
    - [e146] group «Group 66» | opacity:0
        ↳ animation: opacity: jump from 0 to 1 at 2s
      - [e147] text pos(71.94,79.72) size 73×33 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:400; font-size:41.38px; line-height:1.04 | TEXT in runs: "24" {font-family:'Kurdis'; font-weight:400; font-size:41.38px; line-height:1.04; color:#ffffff} + "k" {font-family:'Kurdis'; font-weight:400; font-size:41.38px; line-height:1.04; color:#ffffff}
          ↳ animation: opacity: jump from 0 to 1 at 2s
      - [e148] text pos(67.72,118.72) size 81×18 | opacity:0; white-space:pre; text-align:center; font-family:'Noto Sans'; font-weight:400; font-size:14px; line-height:normal; letter-spacing:-0.02em; color:#ffffff | TEXT: "Motion Loop"
          ↳ animation: opacity: jump from 0 to 1 at 2s
      - [e149] vector «Repeat group 1» pos(0,-6.73) size 216.44×223.17 | opacity:0
          ↳ animation: opacity: jump from 0 to 1 at 2s
        - vector drawing SVG-10 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
  - [e150] group «Group 72» | opacity:0
      ↳ animation: opacity: jump from 0 to 1 at 2s
    - [e151] text pos(24,186) size 372×53 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff | TEXT: "Floating"
        ↳ animation: y: 7.5→10s 186→-747.27 (E2); opacity: jump from 0 to 1 at 2s
    - [e152] text pos(141,264) size 389×53 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff | TEXT: "Through"
        ↳ animation: y: 7.5→10s 264→-629.27 (E2); opacity: jump from 0 to 1 at 2s
    - [e153] text pos(141,342) size 584×53 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff | TEXT: "Aqua Dreams"
        ↳ animation: y: 7.5→10s 342→-521.27 (E2); opacity: jump from 0 to 1 at 2s
  - [e154] box «button» pos(131,449) size 217×88 | opacity:0; border-radius:999px; overflow:hidden
      ↳ animation: y: 7.5→10s 449→-394.27 (E2); opacity: jump from 0 to 1 at 2s
    - [e155] box <a href="#"> «Frame 1171275726» pos(10,10) size 197×68 | opacity:0; border-radius:999px; background-color:#ffffff
        ↳ animation: opacity: jump from 0 to 1 at 2s
      - [e156] text pos(20,24) size 77×20 | opacity:0; white-space:pre; text-align:left; font-family:'Noto Sans'; font-weight:400; font-size:16px; line-height:normal; color:#000000 | TEXT: "Shop Now"
          ↳ animation: opacity: jump from 0 to 1 at 2s
      - [e157] box «Frame 19» pos(137,8) size 52×52 | opacity:0; border-radius:30px; background-color:#dbedf9
          ↳ animation: opacity: jump from 0 to 1 at 2s
        - [e158] box «ArrowRight» pos(19,19) size 14×14 | opacity:0
            ↳ animation: opacity: jump from 0 to 1 at 2s
          - [e160] vector «Vector» pos(1.56,6.38) size 10.88×1.25 | opacity:0
              ↳ animation: opacity: jump from 0 to 1 at 2s
            - vector drawing SVG-8 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
          - [e161] vector «Vector» pos(7.25,2.44) size 5.19×9.12 | opacity:0
              ↳ animation: opacity: jump from 0 to 1 at 2s
            - vector drawing SVG-9 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
    - border (stroke): inset:0px; border-radius:999px; border:1px solid rgba(255,255,255,0.24)
  - [e162] box «right title» pos(842.11,625.86) size 223.89×123.92 | opacity:0
      ↳ animation: y: 7.5→10s 625.86→-237.42 (E2); opacity: jump from 0 to 1 at 2s
    - [e163] group «Group 70» | opacity:0
        ↳ animation: opacity: jump from 0 to 1 at 2s
      - [e164] text pos(0,0) size 172×59 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:32px; line-height:1.04 | TEXT in runs: "Silence 
" {font-family:'Kurdis'; font-weight:600; font-size:32px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "Moves" {font-family:'Kurdis'; font-weight:600; font-size:32px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:rgba(255,255,255,0.4)}
          ↳ animation: opacity: jump from 0 to 1 at 2s
      - [e165] text pos(36.89,64.92) size 187×59 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:32px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff | TEXT: "Beneath 
Light"
          ↳ animation: opacity: jump from 0 to 1 at 2s
  - [e166] box «right section» pos(1137.5,462.77) size 273×337.55 | opacity:0
      ↳ animation: y: 7.5→10s 462.77→-470.5 (E2); opacity: jump from 0 to 1 at 2s
    - [e167] text pos(139,171.05) size 134×108 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Noto Sans'; font-weight:400; font-size:14px; line-height:normal; color:#ffffff | TEXT: "Designed to evoke calmness through fluid interactions, atmospheric depth, and abstract aquatic movement."
        ↳ animation: opacity: jump from 0 to 1 at 2s
    - [e168] group «Group 64» | opacity:0
        ↳ animation: opacity: jump from 0 to 1 at 2s
      - [e169] box «Rectangle 8» transform translate(103.5,112.54) rotate(3.14rad) scale(1,-1) size 53.5×221 | opacity:0; border-radius:99px; background-color:#ffffff
          ↳ animation: opacity: jump from 0 to 1 at 2s
      - [e170] box «image 21» transform translate(103.5,127.77) rotate(3.14rad) scale(1,-1) size 103.5×209.77 | opacity:0
          ↳ animation: opacity: jump from 0 to 1 at 2s
        - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/fish/assets/img/95f782d606-5ccc07.webp: absolutely positioned `<img alt="">` at (0,0), 1856×2464 px, max-width:none, transform-origin 0 0 and transform matrix(0.0851,0,0,0.0851,-54.513,0); the container clips it (overflow hidden, same border-radius)
    - [e171] group «Group 65» | opacity:0
        ↳ animation: opacity: jump from 0 to 1 at 2s
      - [e172] box «Rectangle 8» pos(200.01,0) size 53.5×123.13 | opacity:0; border-radius:99px; background-color:#ffffff
          ↳ animation: opacity: jump from 0 to 1 at 2s
      - [e173] box «image 22» pos(200.01,0) size 53.5×123.13 | opacity:0; border-radius:99px
          ↳ animation: opacity: jump from 0 to 1 at 2s
        - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/fish/assets/img/34fc44b9cc-187f7f.webp: absolutely positioned `<img alt="">` at (0,0), 1856×2464 px, max-width:none, transform-origin 0 0 and transform matrix(0.0669,0,0,0.0669,-48.173,-8.925); the container clips it (overflow hidden, same border-radius)
      - [e174] box «image 23» pos(239.01,72) size 31.99×58.23 | opacity:0
          ↳ animation: opacity: jump from 0 to 1 at 2s
        - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/fish/assets/img/34fc44b9cc-187f7f.webp: absolutely positioned `<img alt="">` at (0,0), 1856×2464 px, max-width:none, transform-origin 0 0 and transform matrix(0.0669,0,0,0.0669,-87.172,-80.925); the container clips it (overflow hidden, same border-radius)

#### Layer bk10 «Drift Beyond Still Waters - Animation ▶» — CONTAINED LAYER (contain, anchor x=-1, y=-1), design box x=24 y=151.88 358×260
- [e175] box «Drift Beyond Still Waters - Animation ▶» pos(24,151.88) size 358×260 | opacity:0
    ↳ animation: opacity: 7.5→10s 0→1 (E2)
  - [e176] box «D» pos(0,0) size 358×260 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e177] text pos(0,33) size 358×260 | opacity:0; white-space:pre-wrap; text-align:right; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "D" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "rift Beyond Still Waters" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
        ↳ animation: y: 10.4→10.55s 33→0 (E4); opacity: 10.4→10.55s 0→1 (E4)
  - [e178] box «r» pos(0,0) size 358×260 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e179] text pos(0,33) size 358×260 | opacity:0; white-space:pre-wrap; text-align:right; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "D" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "r" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "ift Beyond Still Waters" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
        ↳ animation: y: 10.42→10.57s 33→0 (E4); opacity: 10.42→10.57s 0→1 (E4)
  - [e180] box «i» pos(0,0) size 358×260 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e181] text pos(0,33) size 358×260 | opacity:0; white-space:pre-wrap; text-align:right; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Dr" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "i" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "ft Beyond Still Waters" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
        ↳ animation: y: 10.44→10.59s 33→0 (E4); opacity: 10.44→10.59s 0→1 (E4)
  - [e182] box «f» pos(0,0) size 358×260 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e183] text pos(0,33) size 358×260 | opacity:0; white-space:pre-wrap; text-align:right; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Dri" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "f" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "t Beyond Still Waters" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
        ↳ animation: y: 10.46→10.61s 33→0 (E4); opacity: 10.46→10.61s 0→1 (E4)
  - [e184] box «t» pos(0,0) size 358×260 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e185] text pos(0,33) size 358×260 | opacity:0; white-space:pre-wrap; text-align:right; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Drif" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "t" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + " Beyond Still Waters" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
        ↳ animation: y: 10.48→10.63s 33→0 (E4); opacity: 10.48→10.63s 0→1 (E4)
  - [e186] box « » pos(0,0) size 358×260 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e187] text pos(0,33) size 358×260 | opacity:0; white-space:pre-wrap; text-align:right; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Drift" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + " " {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "Beyond Still Waters" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
        ↳ animation: y: 10.5→10.65s 33→0 (E4)
  - [e188] box «B» pos(0,0) size 358×260 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e189] text pos(0,33) size 358×260 | opacity:0; white-space:pre-wrap; text-align:right; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Drift " {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "B" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "eyond Still Waters" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
        ↳ animation: y: 10.52→10.67s 33→0 (E4); opacity: 10.52→10.67s 0→1 (E4)
  - [e190] box «e» pos(0,0) size 358×260 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e191] text pos(0,33) size 358×260 | opacity:0; white-space:pre-wrap; text-align:right; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Drift B" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "e" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "yond Still Waters" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
        ↳ animation: y: 10.54→10.69s 33→0 (E4); opacity: 10.54→10.69s 0→1 (E4)
  - [e192] box «y» pos(0,0) size 358×260 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e193] text pos(0,33) size 358×260 | opacity:0; white-space:pre-wrap; text-align:right; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Drift Be" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "y" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "ond Still Waters" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
        ↳ animation: y: 10.56→10.71s 33→0 (E4); opacity: 10.56→10.71s 0→1 (E4)
  - [e194] box «o» pos(0,0) size 358×260 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e195] text pos(0,33) size 358×260 | opacity:0; white-space:pre-wrap; text-align:right; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Drift Bey" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "o" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "nd Still Waters" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
        ↳ animation: y: 10.58→10.73s 33→0 (E4); opacity: 10.58→10.73s 0→1 (E4)
  - [e196] box «n» pos(0,0) size 358×260 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e197] text pos(0,33) size 358×260 | opacity:0; white-space:pre-wrap; text-align:right; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Drift Beyo" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "n" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "d Still Waters" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
        ↳ animation: y: 10.6→10.75s 33→0 (E4); opacity: 10.6→10.75s 0→1 (E4)
  - [e198] box «d» pos(0,0) size 358×260 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e199] text pos(0,33) size 358×260 | opacity:0; white-space:pre-wrap; text-align:right; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Drift Beyon" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "d" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + " Still Waters" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
        ↳ animation: y: 10.62→10.77s 33→0 (E4); opacity: 10.62→10.77s 0→1 (E4)
  - [e200] box « » pos(0,0) size 358×260 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e201] text pos(0,33) size 358×260 | opacity:0; white-space:pre-wrap; text-align:right; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Drift Beyond" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + " " {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "Still Waters" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
        ↳ animation: y: 10.64→10.79s 33→0 (E4)
  - [e202] box «S» pos(0,0) size 358×260 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e203] text pos(0,33) size 358×260 | opacity:0; white-space:pre-wrap; text-align:right; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Drift Beyond " {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "S" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "till Waters" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
        ↳ animation: y: 10.66→10.81s 33→0 (E4); opacity: 10.66→10.81s 0→1 (E4)
  - [e204] box «t» pos(0,0) size 358×260 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e205] text pos(0,33) size 358×260 | opacity:0; white-space:pre-wrap; text-align:right; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Drift Beyond S" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "t" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "ill Waters" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
        ↳ animation: y: 10.68→10.83s 33→0 (E4); opacity: 10.68→10.83s 0→1 (E4)
  - [e206] box «i» pos(0,0) size 358×260 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e207] text pos(0,33) size 358×260 | opacity:0; white-space:pre-wrap; text-align:right; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Drift Beyond St" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "i" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "ll Waters" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
        ↳ animation: y: 10.7→10.85s 33→0 (E4); opacity: 10.7→10.85s 0→1 (E4)
  - [e208] box «l» pos(0,0) size 358×260 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e209] text pos(0,33) size 358×260 | opacity:0; white-space:pre-wrap; text-align:right; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Drift Beyond Sti" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "l" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "l Waters" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
        ↳ animation: y: 10.72→10.87s 33→0 (E4); opacity: 10.72→10.87s 0→1 (E4)
  - [e210] box «l» pos(0,0) size 358×260 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e211] text pos(0,33) size 358×260 | opacity:0; white-space:pre-wrap; text-align:right; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Drift Beyond Stil" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "l" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + " Waters" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
        ↳ animation: y: 10.74→10.89s 33→0 (E4); opacity: 10.74→10.89s 0→1 (E4)
  - [e212] box « » pos(0,0) size 358×260 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e213] text pos(0,33) size 358×260 | opacity:0; white-space:pre-wrap; text-align:right; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Drift Beyond Still" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + " " {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "Waters" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
        ↳ animation: y: 10.76→10.91s 33→0 (E4)
  - [e214] box «W» pos(0,0) size 358×260 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e215] text pos(0,33) size 358×260 | opacity:0; white-space:pre-wrap; text-align:right; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Drift Beyond Still " {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "W" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "aters" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
        ↳ animation: y: 10.78→10.93s 33→0 (E4); opacity: 10.78→10.93s 0→1 (E4)
  - [e216] box «a» pos(0,0) size 358×260 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e217] text pos(0,33) size 358×260 | opacity:0; white-space:pre-wrap; text-align:right; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Drift Beyond Still W" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "a" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "ters" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
        ↳ animation: y: 10.8→10.95s 33→0 (E4); opacity: 10.8→10.95s 0→1 (E4)
  - [e218] box «t» pos(0,0) size 358×260 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e219] text pos(0,33) size 358×260 | opacity:0; white-space:pre-wrap; text-align:right; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Drift Beyond Still Wa" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "t" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "ers" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
        ↳ animation: y: 10.82→10.97s 33→0 (E4); opacity: 10.82→10.97s 0→1 (E4)
  - [e220] box «e» pos(0,0) size 358×260 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e221] text pos(0,33) size 358×260 | opacity:0; white-space:pre-wrap; text-align:right; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Drift Beyond Still Wat" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "e" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "rs" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
        ↳ animation: y: 10.84→10.99s 33→0 (E4); opacity: 10.84→10.99s 0→1 (E4)
  - [e222] box «r» pos(0,0) size 358×260 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e223] text pos(0,33) size 358×260 | opacity:0; white-space:pre-wrap; text-align:right; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Drift Beyond Still Wate" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "r" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff} + "s" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent}
        ↳ animation: y: 10.86→11.01s 33→0 (E4); opacity: 10.86→11.01s 0→1 (E4)
  - [e224] box «s» pos(0,0) size 358×260 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e225] text pos(0,33) size 358×260 | opacity:0; white-space:pre-wrap; text-align:right; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04 | TEXT in runs: "Drift Beyond Still Water" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:transparent} + "s" {font-family:'Kurdis'; font-weight:600; font-size:65.99px; line-height:1.04; letter-spacing:-0.05em; text-transform:uppercase; color:#ffffff}
        ↳ animation: y: 10.88→11.03s 33→0 (E4); opacity: 10.88→11.03s 0→1 (E4)

#### Layer bk11 «Silence Moves Beneath Light - Animation ▶» — CONTAINED LAYER (contain, anchor x=0, y=-1), design box x=505.08 y=145 141.92×120
- [e226] box «Silence Moves Beneath Light - Animation ▶» pos(505.08,145) size 141.92×120 | opacity:0
    ↳ animation: opacity: 7.5→10s 0→1 (E2)
  - [e227] box «S» pos(0,0) size 141.92×120 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e228] text pos(0,12) size 141.92×120 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal | TEXT in runs: "S" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:#ffffff} + "ilence Moves Beneath Light" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent}
        ↳ animation: y: 10.4→10.55s 12→0 (E4); opacity: 10.4→10.55s 0→1 (E4)
  - [e229] box «i» pos(0,0) size 141.92×120 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e230] text pos(0,12) size 141.92×120 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal | TEXT in runs: "S" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent} + "i" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:#ffffff} + "lence Moves Beneath Light" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent}
        ↳ animation: y: 10.42→10.57s 12→0 (E4); opacity: 10.42→10.57s 0→1 (E4)
  - [e231] box «l» pos(0,0) size 141.92×120 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e232] text pos(0,12) size 141.92×120 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal | TEXT in runs: "Si" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent} + "l" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:#ffffff} + "ence Moves Beneath Light" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent}
        ↳ animation: y: 10.44→10.59s 12→0 (E4); opacity: 10.44→10.59s 0→1 (E4)
  - [e233] box «e» pos(0,0) size 141.92×120 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e234] text pos(0,12) size 141.92×120 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal | TEXT in runs: "Sil" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent} + "e" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:#ffffff} + "nce Moves Beneath Light" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent}
        ↳ animation: y: 10.46→10.61s 12→0 (E4); opacity: 10.46→10.61s 0→1 (E4)
  - [e235] box «n» pos(0,0) size 141.92×120 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e236] text pos(0,12) size 141.92×120 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal | TEXT in runs: "Sile" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent} + "n" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:#ffffff} + "ce Moves Beneath Light" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent}
        ↳ animation: y: 10.48→10.63s 12→0 (E4); opacity: 10.48→10.63s 0→1 (E4)
  - [e237] box «c» pos(0,0) size 141.92×120 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e238] text pos(0,12) size 141.92×120 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal | TEXT in runs: "Silen" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent} + "c" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:#ffffff} + "e Moves Beneath Light" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent}
        ↳ animation: y: 10.5→10.65s 12→0 (E4); opacity: 10.5→10.65s 0→1 (E4)
  - [e239] box «e» pos(0,0) size 141.92×120 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e240] text pos(0,12) size 141.92×120 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal | TEXT in runs: "Silenc" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent} + "e" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:#ffffff} + " Moves Beneath Light" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent}
        ↳ animation: y: 10.52→10.67s 12→0 (E4); opacity: 10.52→10.67s 0→1 (E4)
  - [e241] box « » pos(0,0) size 141.92×120 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e242] text pos(0,12) size 141.92×120 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal | TEXT in runs: "Silence" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent} + " " {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:#ffffff} + "Moves Beneath Light" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent}
        ↳ animation: y: 10.54→10.69s 12→0 (E4)
  - [e243] box «M» pos(0,0) size 141.92×120 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e244] text pos(0,12) size 141.92×120 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal | TEXT in runs: "Silence " {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent} + "M" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:#ffffff} + "oves Beneath Light" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent}
        ↳ animation: y: 10.56→10.71s 12→0 (E4); opacity: 10.56→10.71s 0→1 (E4)
  - [e245] box «o» pos(0,0) size 141.92×120 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e246] text pos(0,12) size 141.92×120 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal | TEXT in runs: "Silence M" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent} + "o" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:#ffffff} + "ves Beneath Light" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent}
        ↳ animation: y: 10.58→10.73s 12→0 (E4); opacity: 10.58→10.73s 0→1 (E4)
  - [e247] box «v» pos(0,0) size 141.92×120 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e248] text pos(0,12) size 141.92×120 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal | TEXT in runs: "Silence Mo" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent} + "v" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:#ffffff} + "es Beneath Light" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent}
        ↳ animation: y: 10.6→10.75s 12→0 (E4); opacity: 10.6→10.75s 0→1 (E4)
  - [e249] box «e» pos(0,0) size 141.92×120 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e250] text pos(0,12) size 141.92×120 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal | TEXT in runs: "Silence Mov" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent} + "e" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:#ffffff} + "s Beneath Light" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent}
        ↳ animation: y: 10.62→10.77s 12→0 (E4); opacity: 10.62→10.77s 0→1 (E4)
  - [e251] box «s» pos(0,0) size 141.92×120 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e252] text pos(0,12) size 141.92×120 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal | TEXT in runs: "Silence Move" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent} + "s" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:#ffffff} + " Beneath Light" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent}
        ↳ animation: y: 10.64→10.79s 12→0 (E4); opacity: 10.64→10.79s 0→1 (E4)
  - [e253] box « » pos(0,0) size 141.92×120 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e254] text pos(0,12) size 141.92×120 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal | TEXT in runs: "Silence Moves" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent} + " " {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:#ffffff} + "Beneath Light" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent}
        ↳ animation: y: 10.66→10.81s 12→0 (E4)
  - [e255] box «B» pos(0,0) size 141.92×120 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e256] text pos(0,12) size 141.92×120 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal | TEXT in runs: "Silence Moves " {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent} + "B" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:#ffffff} + "eneath Light" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent}
        ↳ animation: y: 10.68→10.83s 12→0 (E4); opacity: 10.68→10.83s 0→1 (E4)
  - [e257] box «e» pos(0,0) size 141.92×120 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e258] text pos(0,12) size 141.92×120 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal | TEXT in runs: "Silence Moves B" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent} + "e" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:#ffffff} + "neath Light" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent}
        ↳ animation: y: 10.7→10.85s 12→0 (E4); opacity: 10.7→10.85s 0→1 (E4)
  - [e259] box «n» pos(0,0) size 141.92×120 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e260] text pos(0,12) size 141.92×120 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal | TEXT in runs: "Silence Moves Be" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent} + "n" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:#ffffff} + "eath Light" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent}
        ↳ animation: y: 10.72→10.87s 12→0 (E4); opacity: 10.72→10.87s 0→1 (E4)
  - [e261] box «e» pos(0,0) size 141.92×120 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e262] text pos(0,12) size 141.92×120 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal | TEXT in runs: "Silence Moves Ben" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent} + "e" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:#ffffff} + "ath Light" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent}
        ↳ animation: y: 10.74→10.89s 12→0 (E4); opacity: 10.74→10.89s 0→1 (E4)
  - [e263] box «a» pos(0,0) size 141.92×120 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e264] text pos(0,12) size 141.92×120 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal | TEXT in runs: "Silence Moves Bene" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent} + "a" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:#ffffff} + "th Light" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent}
        ↳ animation: y: 10.76→10.91s 12→0 (E4); opacity: 10.76→10.91s 0→1 (E4)
  - [e265] box «t» pos(0,0) size 141.92×120 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e266] text pos(0,12) size 141.92×120 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal | TEXT in runs: "Silence Moves Benea" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent} + "t" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:#ffffff} + "h Light" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent}
        ↳ animation: y: 10.78→10.93s 12→0 (E4); opacity: 10.78→10.93s 0→1 (E4)
  - [e267] box «h» pos(0,0) size 141.92×120 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e268] text pos(0,12) size 141.92×120 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal | TEXT in runs: "Silence Moves Beneat" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent} + "h" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:#ffffff} + " Light" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent}
        ↳ animation: y: 10.8→10.95s 12→0 (E4); opacity: 10.8→10.95s 0→1 (E4)
  - [e269] box « » pos(0,0) size 141.92×120 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e270] text pos(0,12) size 141.92×120 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal | TEXT in runs: "Silence Moves Beneath" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent} + " " {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:#ffffff} + "Light" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent}
        ↳ animation: y: 10.82→10.97s 12→0 (E4)
  - [e271] box «L» pos(0,0) size 141.92×120 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e272] text pos(0,12) size 141.92×120 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal | TEXT in runs: "Silence Moves Beneath " {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent} + "L" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:#ffffff} + "ight" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent}
        ↳ animation: y: 10.84→10.99s 12→0 (E4); opacity: 10.84→10.99s 0→1 (E4)
  - [e273] box «i» pos(0,0) size 141.92×120 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e274] text pos(0,12) size 141.92×120 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal | TEXT in runs: "Silence Moves Beneath L" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent} + "i" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:#ffffff} + "ght" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent}
        ↳ animation: y: 10.86→11.01s 12→0 (E4); opacity: 10.86→11.01s 0→1 (E4)
  - [e275] box «g» pos(0,0) size 141.92×120 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e276] text pos(0,12) size 141.92×120 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal | TEXT in runs: "Silence Moves Beneath Li" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent} + "g" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:#ffffff} + "ht" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent}
        ↳ animation: y: 10.88→11.03s 12→0 (E4); opacity: 10.88→11.03s 0→1 (E4)
  - [e277] box «h» pos(0,0) size 141.92×120 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e278] text pos(0,12) size 141.92×120 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal | TEXT in runs: "Silence Moves Beneath Lig" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent} + "h" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:#ffffff} + "t" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent}
        ↳ animation: y: 10.9→11.05s 12→0 (E4); opacity: 10.9→11.05s 0→1 (E4)
  - [e279] box «t» pos(0,0) size 141.92×120 | opacity:0
      ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e280] text pos(0,12) size 141.92×120 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal | TEXT in runs: "Silence Moves Beneath Ligh" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:transparent} + "t" {font-family:'Noto Sans'; font-weight:400; font-size:24px; line-height:normal; color:#ffffff}
        ↳ animation: y: 10.92→11.07s 12→0 (E4); opacity: 10.92→11.07s 0→1 (E4)

#### Layer bk12 «Component 1» — CONTAINED LAYER (contain, anchor x=0, y=1), design box x=0 y=505 1440×305
- [e281] box «Component 1» pos(0,505) size 1440×305 | opacity:0
    ↳ animation: opacity: 7.5→10s 0→1 (E2)
  - [e282] box «Frame 1597882324» pos(0,311) size 360×241 | opacity:0; overflow:hidden; background-color:rgba(255,255,255,0.08)
      ↳ animation: y: 10.6→11.85s 311→64 (E3); opacity: 7.5→10s 0→1 (E2)
    - background blur (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(20px)
    - [e283] text pos(23.83,68) size 211.92×33 | opacity:0; white-space:pre-wrap; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:18px; line-height:1.04; text-transform:uppercase; color:#ffffff | TEXT: "Ethereal 
Motion"
        ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e284] box <a href="#"> «Frame 1171275726» pos(23.83,172) size 145.73×41.42 | opacity:0; border-radius:608.48px
        ↳ animation: opacity: 7.5→10s 0→1 (E2)
      - [e285] text pos(12.18,13.21) size 85×15 | opacity:0; white-space:pre; text-align:left; font-family:'Noto Sans'; font-weight:400; font-size:12px; line-height:normal; color:#ffffff | TEXT: "View Sequence"
          ↳ animation: opacity: 7.5→10s 0→1 (E2)
      - [e286] box «Frame 19» pos(109.18,4.87) size 31.67×31.67 | opacity:0; border-radius:18.27px; background-color:rgba(219,237,249,0.1)
          ↳ animation: opacity: 7.5→10s 0→1 (E2)
        - [e287] box «ArrowRight» pos(11.57,11.57) size 8.53×8.53 | opacity:0
            ↳ animation: opacity: 7.5→10s 0→1 (E2)
          - [e289] vector «Vector» pos(0.95,3.88) size 6.62×0.76 | opacity:0
              ↳ animation: opacity: 7.5→10s 0→1 (E2)
            - vector drawing SVG-11 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
          - [e290] vector «Vector» pos(4.42,1.48) size 3.16×5.56 | opacity:0
              ↳ animation: opacity: 7.5→10s 0→1 (E2)
            - vector drawing SVG-12 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - border (stroke): inset:0px; border-radius:608.48px; border:1px solid rgba(255,255,255,0.3)
    - [e291] box «image 21» transform translate(690.76,-213.54) rotate(3.14rad) scale(1,-1) size 557.76×771.1 | opacity:0
        ↳ animation: x: 10.6→11.85s 690.77→562.8 (E3); y: 10.6→11.85s -213.55→-245.39 (E3); opacity: 7.5→10s 0→1 (E2); content-scale: 10.6→11.85s 1→0.79 (E3)
      - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/fish/assets/img/95f782d606-5ccc07.webp: absolutely positioned `<img alt="">` at (0,0), 1856×2464 px, max-width:none, transform-origin 0 0 and transform matrix(0.3129,0,0,0.3129,-23.064,0); the container clips it (overflow hidden, same border-radius)
  - [e292] box «Frame 1597882329» pos(720,904.5) size 360×305 | opacity:0; overflow:hidden; background-color:#081425
      ↳ animation: y: 10.6→11.85s 904.5→0 (E3); opacity: 7.5→10s 0→1 (E2)
    - [e293] box «image 23» transform translate(860.79,-463.72) rotate(-2.99rad) scale(1,-1) size 1228.67×1631.16 | opacity:0
        ↳ animation: opacity: 7.5→10s 0→0.6 (E2)
      - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/fish/assets/img/34fc44b9cc-187f7f.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)
    - [e294] text pos(23.83,134.34) size 211.92×44 | opacity:0; white-space:pre-wrap; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:24px; line-height:1.04; text-transform:uppercase; color:#ffffff | TEXT: "Surface
Reflection"
        ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e295] text pos(149.38,211.85) size 176.73×72 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Noto Sans'; font-weight:400; font-size:14px; line-height:normal; color:#ffffff | TEXT: "Smooth aquatic movement designed to feel cinematic, weightless, and endlessly calming."
        ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e296] group «Group 72» | opacity:0
        ↳ animation: opacity: 7.5→10s 0→1 (E2)
      - [e297] box <a href="#"> aria-label="Surface Reflection" «Frame 19» pos(296.1,22.9) size 41×41 | opacity:0; border-radius:999px; background-color:#ffffff
          ↳ animation: opacity: 7.5→10s 0→1 (E2)
        - [e298] box «ArrowRight» transform translate(9.18,20.5) rotate(-0.78rad) size 16×16 | opacity:0
            ↳ animation: opacity: 7.5→10s 0→1 (E2)
          - [e300] vector «Vector» transform matrix(0.707,0.707,-0.707,0.707,8,1.962) size 8.54×8.54 | opacity:0
              ↳ animation: opacity: 7.5→10s 0→1 (E2)
            - vector drawing SVG-13 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
          - [e301] vector «Vector» transform matrix(0.707,0.707,-0.707,0.707,9,2.962) size 7.12×7.12 | opacity:0
              ↳ animation: opacity: 7.5→10s 0→1 (E2)
            - vector drawing SVG-14 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e302] box «Ellipse 25» pos(273.19,0) size 86.81×86.81 | opacity:0; border-radius:50%
          ↳ animation: opacity: 7.5→10s 0→0.3 (E2)
        - border (stroke): inset:0px; border-radius:50%; border:1px solid #ffffff
      - [e303] box «Ellipse 26» pos(241.65,-31.54) size 149.9×149.9 | opacity:0; border-radius:50%
          ↳ animation: opacity: 7.5→10s 0→0.2 (E2)
        - border (stroke): inset:0px; border-radius:50%; border:1px solid #ffffff
      - [e304] box «Ellipse 27» pos(209.92,-63.27) size 213.35×213.35 | opacity:0; border-radius:50%
          ↳ animation: opacity: 7.5→10s 0→0.1 (E2)
        - border (stroke): inset:0px; border-radius:50%; border:1px solid #ffffff
  - [e305] box «Frame 1597882328» pos(1080,1288) size 360×241 | opacity:0; overflow:hidden; background-color:rgba(255,255,255,0.08)
      ↳ animation: y: 10.6→11.85s 1288→64 (E3); opacity: 7.5→10s 0→1 (E2)
    - background blur (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(20px)
    - [e306] text pos(23.83,68) size 211.92×33 | opacity:0; white-space:pre-wrap; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:18px; line-height:1.04; text-transform:uppercase; color:#ffffff | TEXT: "Silent 
Current"
        ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e307] box <a href="#"> «Frame 1171275726» pos(23.83,172) size 146.73×41.42 | opacity:0; border-radius:608.48px
        ↳ animation: opacity: 7.5→10s 0→1 (E2)
      - [e308] text pos(12.18,13.21) size 86×15 | opacity:0; white-space:pre; text-align:left; font-family:'Noto Sans'; font-weight:400; font-size:12px; line-height:normal; color:#ffffff | TEXT: "Discover Vision"
          ↳ animation: opacity: 7.5→10s 0→1 (E2)
      - [e309] box «Frame 19» pos(110.18,4.87) size 31.67×31.67 | opacity:0; border-radius:18.27px; background-color:rgba(219,237,249,0.1)
          ↳ animation: opacity: 7.5→10s 0→1 (E2)
        - [e310] box «ArrowRight» pos(11.57,11.57) size 8.53×8.53 | opacity:0
            ↳ animation: opacity: 7.5→10s 0→1 (E2)
          - [e312] vector «Vector» pos(0.95,3.88) size 6.62×0.76 | opacity:0
              ↳ animation: opacity: 7.5→10s 0→1 (E2)
            - vector drawing SVG-15 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
          - [e313] vector «Vector» pos(4.42,1.48) size 3.16×5.56 | opacity:0
              ↳ animation: opacity: 7.5→10s 0→1 (E2)
            - vector drawing SVG-16 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - border (stroke): inset:0px; border-radius:608.48px; border:1px solid rgba(255,255,255,0.3)
    - [e314] box «image 25» transform translate(1088.56,-354.15) rotate(-2.77rad) scale(1,-1) size 698.37×1246.11 | opacity:0
        ↳ animation: x: 10.6→11.85s 1088.57→819.36 (E3); y: 10.6→11.85s -354.16→-237.78 (E3); width: 10.6→11.85s 698.37→519.8 (E3); height: 10.6→11.85s 1246.11→927.49 (E3); opacity: 7.5→10s 0→1 (E2)
      - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/fish/assets/img/fcfb8065e0.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)
  - [e315] box «Frame 1597882330» pos(360,596) size 360×241 | opacity:0; overflow:hidden; background-color:rgba(255,255,255,0.08)
      ↳ animation: y: 10.6→11.85s 596→64 (E3); opacity: 7.5→10s 0→1 (E2)
    - background blur (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(20px)
    - [e316] text pos(23.83,68) size 211.92×33 | opacity:0; white-space:pre-wrap; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kurdis'; font-weight:600; font-size:18px; line-height:1.04; text-transform:uppercase; color:#ffffff | TEXT: "Ambient 
Depth"
        ↳ animation: opacity: 7.5→10s 0→1 (E2)
    - [e317] box <a href="#"> «Frame 1171275726» pos(23.83,172) size 156.73×41.42 | opacity:0; border-radius:608.48px
        ↳ animation: opacity: 7.5→10s 0→1 (E2)
      - [e318] text pos(12.18,13.21) size 96×15 | opacity:0; white-space:pre; text-align:left; font-family:'Noto Sans'; font-weight:400; font-size:12px; line-height:normal; color:#ffffff | TEXT: "Enter Experience"
          ↳ animation: opacity: 7.5→10s 0→1 (E2)
      - [e319] box «Frame 19» pos(120.18,4.87) size 31.67×31.67 | opacity:0; border-radius:18.27px; background-color:rgba(219,237,249,0.1)
          ↳ animation: opacity: 7.5→10s 0→1 (E2)
        - [e320] box «ArrowRight» pos(11.57,11.57) size 8.53×8.53 | opacity:0
            ↳ animation: opacity: 7.5→10s 0→1 (E2)
          - [e322] vector «Vector» pos(0.95,3.88) size 6.62×0.76 | opacity:0
              ↳ animation: opacity: 7.5→10s 0→1 (E2)
            - vector drawing SVG-15 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
          - [e323] vector «Vector» pos(4.42,1.48) size 3.16×5.56 | opacity:0
              ↳ animation: opacity: 7.5→10s 0→1 (E2)
            - vector drawing SVG-16 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - border (stroke): inset:0px; border-radius:608.48px; border:1px solid rgba(255,255,255,0.3)
    - [e324] box «image 26» pos(175.68,-252.42) size 507.57×673.85 | opacity:0
        ↳ animation: x: 10.6→11.85s 175.68→153.11 (E3); y: 10.6→11.85s -252.42→-129.47 (E3); width: 10.6→11.85s 507.57→322.35 (E3); height: 10.6→11.85s 673.85→427.95 (E3); opacity: 7.5→10s 0→1 (E2)
      - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/fish/assets/img/bc4feda1c4.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)

## 10. Final adjustments
### Hover states (only when `matchMedia("(hover: hover) and (pointer: fine)")` matches; no hover on touch)
- Menu and text links (subtle colour change): [e116] «Flow», [e117] «Current», [e118] «Depth», [e119] «Motion». On pointer enter or focus, for the link and each of its descendants read its computed colour c (skip it if its alpha is 0, e.g. gradient text) and store the variable `--hvc` = rgb(c + (#dbedf9 − c) × 1) per channel (alpha 1); add the class `hv-tr` and, on the next frame, `is-hv`. On leave (or blur) remove `is-hv` and, 420 ms later, `hv-tr`.
- Buttons (subtle background change): [e121] «Shop Now» → layer in the link itself with background rgba(219,237,249,.8); [e155] «Shop Now» → layer in the link itself with background rgba(219,237,249,.8); [e284] «View Sequence» → layer in the link itself with background rgba(255,255,255,.16); [e297] «Surface Reflection» → layer in the link itself with background rgba(219,237,249,.8); [e307] «Discover Vision» → layer in the link itself with background rgba(255,255,255,.16); [e317] «Enter Experience» → layer in the link itself with background rgba(255,255,255,.16). In each one insert `<i class="hvo" aria-hidden="true">` inside the given box, right after its fill/effect layers (`i.pl`, `i.bb`, `i.gl`, `i.is`) and before its content, with that background colour; on pointer enter or focus the link adds `is-hv` to that layer and removes it on leave or blur.
- Icon or logo links without a surface of their own (drop to 0.72 opacity on hover): [e95] «Flow home», [e96] «Open menu», [e107] «Account», [e111] «Search». They get the class `hv-ic`.
- Literal CSS for these states:
```css
.blk .hvo{position:absolute;inset:0;border-radius:inherit;pointer-events:none;opacity:0;transition:opacity .35s cubic-bezier(.22,1,.36,1)}
.blk .hvo.is-hv{opacity:1}
.blk a.hv-tr,.blk a.hv-tr *{transition:color .35s cubic-bezier(.22,1,.36,1)}
.blk a.is-hv,.blk a.is-hv *{color:var(--hvc)!important}
.blk a.hv-ic{transition:opacity .3s ease}
.blk a.hv-ic:hover,.blk a.hv-ic:focus-visible{opacity:.72!important}
.blk .hz{pointer-events:auto}
.blk .hz i.pl{transition:scale .8s cubic-bezier(.22,1,.36,1)}
.blk .hz:hover i.pl{scale:var(--hz,1.06)}
```

## 11. Mobile view
- It activates when the stage width is < 768 px or the width/height ratio is < 0.82 (add the class `is-m` to `<html>`). Mobile reference design: 390×844 px.
- sm = min(W/390, H/844); ox = (W − 390·sm)/2; oy = (H − 844·sm)/2.
- Listed contained layer with {x, y, z, ax, ay}: its scale is sm·z (z = 1 if not given) and the top-left corner of its «design box» is placed on screen at (ox·(1+ax) + x·sm, oy·(1+ay) + y·sm) (ax, ay = 0 if not given); the rest of its content keeps its position relative to that box.
- Bleed layer: z = max(W/1440, H/810)·(given z or 1); screen = ((W − 1440·z)·fx + X·z, (H − 810·z)·fy + Y·z), with fx, fy = 0.5 unless another value is given.
- Contained layers that are not in the list are hidden on mobile.
- Per-element adjustments: «hide» = `display:none`; «shift dx, dy» = move the element by that distance in px of its container (if there is also «scale ×z», it is scaled from its top-left corner); «multiply its animated offsets by [kx, ky]» = multiply the total value of offsetX by kx and of offsetY by ky (the mobile «shift dx, dy» is not multiplied); «fade out between t1 and t2» = multiply its opacity by clamp((t2 − t)/(t2 − t1), 0, 1) from t1 on; «place the element point (rx, ry) at (mx, my) of the mobile design at scale e×sm» = draw the element at scale Se = e·sm with its top-left corner (animated x, y) at (ox + mx·sm + (x − rx)·Se, oy + my·sm + (y − ry)·Se); «width» = fixed width in px; «transition … between t1 and t2» = go from its layer's normal placement (the mobile one) to that placement with smoothstep interpolation (u²·(3 − 2u)) of scale and origin between those times.
- On mobile the track uses 34 vh per second (see section 7).
Mobile layer placement:
- layer «Star 1»: x=-250, y=-240, z=0.5
- layer «main menu»: x=16, y=14, z=0.8
- layer «stats»: x=0, y=0, z=0.75
- layer «Group 69»: x=0, y=0, z=0.5
- layer «button»: x=0, y=0, z=0.75
- layer «right title»: x=0, y=0, z=0.75
- layer «right section»: x=0, y=0, z=0.9
- layer «Group 74»: x=0, y=0, z=0.75
- layer «Drift Beyond Still Waters - Animation ▶»: x=20, y=96, z=0.6
- layer «Silence Moves Beneath Light - Animation ▶»: x=250, y=100, z=0.9
- layer «Component 1»: x=0, y=515, z=0.54
Unlisted non-bleed layers are hidden on mobile.
Per-element mobile adjustments:
- [e115] «Group 73»: hide
- [e96] «DotsNine»: shift dx=-944.5, dy=0 (design units of its layer)
- [e107] «User»: shift dx=-944.5, dy=0 (design units of its layer)
- [e111] «MagnifyingGlass»: shift dx=-944.5, dy=0 (design units of its layer)
- [e292] «Frame 1597882329»: shift dx=-720, dy=305 (design units of its layer)
- [e305] «Frame 1597882328»: shift dx=-720, dy=305 (design units of its layer)
- [e12] «Group 69»: place the element point (24,186) at (20,118) of the mobile design at scale 0.5×sm
- [e150] «Group 72»: place the element point (24,186) at (20,118) of the mobile design at scale 0.5×sm
- [e120] «button»: place the element point (131,449) at (10,236) of the mobile design at scale 0.75×sm
- [e154] «button»: place the element point (131,449) at (10,236) of the mobile design at scale 0.75×sm
- [e6] «stats»: place the element point (332,569) at (16,326) of the mobile design at scale 0.75×sm
- [e145] «stats»: place the element point (332,569) at (16,326) of the mobile design at scale 0.75×sm
- [e129] «right title»: place the element point (842,626) at (205,380) of the mobile design at scale 0.75×sm
- [e162] «right title»: place the element point (842,626) at (205,380) of the mobile design at scale 0.75×sm
- [e134] «right section»: place the element point (1138,463) at (128,520) of the mobile design at scale 0.9×sm
- [e166] «right section»: place the element point (1138,463) at (128,520) of the mobile design at scale 0.9×sm

## 12. Semantics and accessibility
- `<main class="hero" aria-label="Flow — Motion Loop">`; the loader has `aria-hidden="true"`.
- Lists with `<ul>` and `<li>`; menus inside `<nav aria-label="…">`. Each `<a href="#">` uses the given `aria-label` when its content is not text. If a link covers a whole box, it goes inside it as an absolute `<a class="lk">` covering it (`inset:0`).
- Decorative images with `alt=""`; decorative SVGs with `aria-hidden="true"`.
- Only texts and links receive pointer events (CSS in section 3) and invisible elements get `visibility:hidden`, so nothing that cannot be seen can ever be clicked.
- Visible focus: `outline: 2px solid currentColor; outline-offset: 3px`.

## 13. Resources (absolute URLs)
All resources are served from a CDN with CORS enabled; use these URLs exactly as given:
- `assets/fonts/Kurdis-VariableVF.woff2` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/fish/assets/fonts/Kurdis-VariableVF.woff2
- `assets/fonts/noto-sans-VF-normal.woff2` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/fish/assets/fonts/noto-sans-VF-normal.woff2
- `assets/img/34fc44b9cc-187f7f.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/fish/assets/img/34fc44b9cc-187f7f.webp
- `assets/img/95f782d606-5ccc07.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/fish/assets/img/95f782d606-5ccc07.webp
- `assets/img/bc4feda1c4.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/fish/assets/img/bc4feda1c4.webp
- `assets/img/ed31cf5284.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/fish/assets/img/ed31cf5284.webp
- `assets/img/fcfb8065e0.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/fish/assets/img/fcfb8065e0.webp
- `assets/video/60573c67c5.jpg` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/fish/assets/video/60573c67c5.jpg
- `assets/video/60573c67c5.mp4` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/fish/assets/video/60573c67c5.mp4

## 14. Appendix: vector shapes
Each SVG fills 100% of its element's box (`position:absolute; inset:0; width:100%; height:100%; overflow:visible`, `preserveAspectRatio="none"`, `aria-hidden="true"`). If an element says «with fill #xxxxxx (instead of #yyyyyy)», use the same SVG changing that fill color. If an SVG with internal `id`s (masks, gradients) is used more than once, give each copy unique ids and update its `url(#…)` references.

**SVG-1**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 1263 810"> <g filter="url(#i0)"> <path d="M94.858 -477.284L173.261 -92.5515L480.622 -336.878L293.38 8.24091L685.882 18.6443L320.609 162.664L614.596 422.929L242.207 298.461L300.119 686.807L94.858 352.092L-110.403 686.807L-52.4907 298.461L-424.88 422.929L-130.893 162.664L-496.167 18.6443L-103.664 8.24091L-290.906 -336.878L16.4554 -92.5515L94.858 -477.284Z" fill="#7DA3C1"></path> </g> <defs> <filter color-interpolation-filters="sRGB" filterUnits="userSpaceOnUse" height="2318.05" id="i0" width="2336.01" x="-1073.15" y="-1054.26"> <feFlood flood-opacity="0" result="BackgroundImageFix"></feFlood> <feBlend in="SourceGraphic" in2="BackgroundImageFix" mode="normal" result="shape"></feBlend> <feGaussianBlur result="effect1_foregroundBlur_13051_1284" stdDeviation="288.49"></feGaussianBlur> </filter> </defs> </svg>
```

**SVG-2**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 217 224"> <g data-figma-trr="r40u2.5-0f" id="i0"> <circle cx="108.218" cy="0.814034" fill="white" opacity="0.3" r="0.814034"></circle> <circle cx="108.218" cy="8.84922" fill="white" opacity="0.6" r="1.22105"></circle> <circle cx="108.218" cy="17.931" fill="white" opacity="0.7" r="1.86065"></circle> <circle cx="108.218" cy="27.6522" fill="white" r="1.86066"></circle> <circle cx="108.218" cy="37.3733" fill="white" opacity="0.8" r="1.86065"></circle> <circle cx="108.218" cy="46.4552" fill="white" opacity="0.7" r="1.22105"></circle> <circle cx="108.218" cy="54.4903" fill="white" opacity="0.6" r="0.814034"></circle> <circle cx="108.218" cy="61.7694" fill="white" opacity="0.5" r="0.465154"></circle> <circle cx="108.218" cy="68.5217" fill="white" opacity="0.4" r="0.287125"></circle> <circle cx="108.218" cy="75.096" fill="white" opacity="0.3" r="0.287125"></circle> </g> <use transform="translate(19.3143 -15.5139) rotate(9)" xlink:href="#e10_i0"></use> <use transform="translate(40.8176 -27.8154) rotate(18)" xlink:href="#e10_i0"></use> <use transform="translate(63.9806 -36.6015) rotate(27)" xlink:href="#e10_i0"></use> <use transform="translate(88.2329 -41.656) rotate(36)" xlink:href="#e10_i0"></use> <use transform="translate(112.977 -42.8544) rotate(45)" xlink:href="#e10_i0"></use> <use transform="translate(137.604 -40.1671) rotate(54)" xlink:href="#e10_i0"></use> <use transform="translate(161.508 -33.6604) rotate(63)" xlink:href="#e10_i0"></use> <use transform="translate(184.1 -23.4944) rotate(72)" xlink:href="#e10_i0"></use> <use transform="translate(204.823 -9.9195) rotate(81)" xlink:href="#e10_i0"></use> <use transform="translate(223.167 6.73005) rotate(90)" xlink:href="#e10_i0"></use> <use transform="translate(238.681 26.0443) rotate(99)" xlink:href="#e10_i0"></use> <use transform="translate(250.982 47.5477) rotate(108)" xlink:href="#e10_i0"></use> <use transform="translate(259.768 70.7107) rotate(117)" xlink:href="#e10_i0"></use> <use transform="translate(264.823 94.9629) rotate(126)" xlink:href="#e10_i0"></use> <use transform="translate(266.021 119.707) rotate(135)" xlink:href="#e10_i0"></use> <use transform="translate(263.334 144.335) rotate(144)" xlink:href="#e10_i0"></use> <use transform="translate(256.827 168.238) rotate(153)" xlink:href="#e10_i0"></use> <use transform="translate(246.661 190.83) rotate(162)" xlink:href="#e10_i0"></use> <use transform="translate(233.086 211.553) rotate(171)" xlink:href="#e10_i0"></use> <use transform="translate(216.437 229.897) rotate(-180)" xlink:href="#e10_i0"></use> <use transform="translate(197.123 245.411) rotate(-171)" xlink:href="#e10_i0"></use> <use transform="translate(175.619 257.712) rotate(-162)" xlink:href="#e10_i0"></use> <use transform="translate(152.456 266.498) rotate(-153)" xlink:href="#e10_i0"></use> <use transform="translate(128.204 271.553) rotate(-144)" xlink:href="#e10_i0"></use> <use transform="translate(103.46 272.751) rotate(-135)" xlink:href="#e10_i0"></use> <use transform="translate(78.8323 270.064) rotate(-126)" xlink:href="#e10_i0"></use> <use transform="translate(54.9287 263.557) rotate(-117)" xlink:href="#e10_i0"></use> <use transform="translate(32.3373 253.391) rotate(-108)" xlink:href="#e10_i0"></use> <use transform="translate(11.6143 239.816) rotate(-99)" xlink:href="#e10_i0"></use> <use transform="translate(-6.73004 223.167) rotate(-90)" xlink:href="#e10_i0"></use> <use transform="translate(-22.2439 203.853) rotate(-81)" xlink:href="#e10_i0"></use> <use transform="translate(-34.5454 182.349) rotate(-72)" xlink:href="#e10_i0"></use> <use transform="translate(-43.3316 159.186) rotate(-63)" xlink:href="#e10_i0"></use> <use transform="translate(-48.386 134.934) rotate(-54)" xlink:href="#e10_i0"></use> <use transform="translate(-49.5844 110.19) rotate(-45)" xlink:href="#e10_i0"></use> <use transform="translate(-46.8971 85.5623) rotate(-36)" xlink:href="#e10_i0"></use> <use transform="translate(-40.3904 61.6588) rotate(-27)" xlink:href="#e10_i0"></use> <use transform="translate(-30.2244 39.0673) rotate(-18)" xlink:href="#e10_i0"></use> <use transform="translate(-16.6495 18.3443) rotate(-8.99999)" xlink:href="#e10_i0"></use> </svg>
```

**SVG-3**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 4 4"> <path d="M1.875 3.75C2.91053 3.75 3.75 2.91053 3.75 1.875C3.75 0.839466 2.91053 0 1.875 0C0.839466 0 0 0.839466 0 1.875C0 2.91053 0.839466 3.75 1.875 3.75Z" fill="#F3EEE7"></path> </svg>
```

**SVG-4**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 12 12"> <path d="M5.6709 10.6709C8.43232 10.6709 10.6709 8.43232 10.6709 5.6709C10.6709 2.90947 8.43232 0.670898 5.6709 0.670898C2.90947 0.670898 0.670898 2.90947 0.670898 5.6709C0.670898 8.43232 2.90947 10.6709 5.6709 10.6709Z" stroke="#F3EEE7" stroke-linecap="round" stroke-linejoin="round" stroke-width="1.34167"></path> </svg>
```

**SVG-5**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 17 6"> <path d="M0.670898 5.0459C2.18418 2.43105 4.93262 0.670898 8.1709 0.670898C11.4092 0.670898 14.1576 2.43105 15.6709 5.0459" stroke="#F3EEE7" stroke-linecap="round" stroke-linejoin="round" stroke-width="1.34167"></path> </svg>
```

**SVG-6**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 14 14"> <path d="M6.9209 13.1709C10.3727 13.1709 13.1709 10.3727 13.1709 6.9209C13.1709 3.46912 10.3727 0.670898 6.9209 0.670898C3.46912 0.670898 0.670898 3.46912 0.670898 6.9209C0.670898 10.3727 3.46912 13.1709 6.9209 13.1709Z" stroke="#F3EEE7" stroke-linecap="round" stroke-linejoin="round" stroke-width="1.34167"></path> </svg>
```

**SVG-7**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 6 6"> <path d="M0.670898 0.670898L5.00137 5.00137" stroke="#F3EEE7" stroke-linecap="round" stroke-linejoin="round" stroke-width="1.34167"></path> </svg>
```

**SVG-8**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 11 2"> <path d="M0.625 0.625H10.25" stroke="black" stroke-linecap="round" stroke-linejoin="round" stroke-width="1.25"></path> </svg>
```

**SVG-9**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 6 10"> <path d="M0.625 0.625L4.5625 4.5625L0.625 8.5" stroke="black" stroke-linecap="round" stroke-linejoin="round" stroke-width="1.25"></path> </svg>
```

**SVG-10**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 217 224"> <g data-figma-trr="r40u2.5-0f" id="i0"> <circle cx="108.218" cy="0.814034" fill="white" opacity="0.3" r="0.814034"></circle> <circle cx="108.218" cy="8.84922" fill="white" opacity="0.6" r="1.22105"></circle> <circle cx="108.218" cy="17.931" fill="white" opacity="0.7" r="1.86065"></circle> <circle cx="108.218" cy="27.6522" fill="white" r="1.86066"></circle> <circle cx="108.218" cy="37.3733" fill="white" opacity="0.8" r="1.86065"></circle> <circle cx="108.218" cy="46.4552" fill="white" opacity="0.7" r="1.22105"></circle> <circle cx="108.218" cy="54.4903" fill="white" opacity="0.6" r="0.814034"></circle> <circle cx="108.218" cy="61.7694" fill="white" opacity="0.5" r="0.465154"></circle> <circle cx="108.218" cy="68.5217" fill="white" opacity="0.4" r="0.287125"></circle> <circle cx="108.218" cy="75.096" fill="white" opacity="0.3" r="0.287125"></circle> </g> <use transform="translate(19.3143 -15.5139) rotate(9)" xlink:href="#e149_i0"></use> <use transform="translate(40.8176 -27.8154) rotate(18)" xlink:href="#e149_i0"></use> <use transform="translate(63.9806 -36.6015) rotate(27)" xlink:href="#e149_i0"></use> <use transform="translate(88.2329 -41.656) rotate(36)" xlink:href="#e149_i0"></use> <use transform="translate(112.977 -42.8544) rotate(45)" xlink:href="#e149_i0"></use> <use transform="translate(137.604 -40.1671) rotate(54)" xlink:href="#e149_i0"></use> <use transform="translate(161.508 -33.6604) rotate(63)" xlink:href="#e149_i0"></use> <use transform="translate(184.1 -23.4944) rotate(72)" xlink:href="#e149_i0"></use> <use transform="translate(204.823 -9.9195) rotate(81)" xlink:href="#e149_i0"></use> <use transform="translate(223.167 6.73005) rotate(90)" xlink:href="#e149_i0"></use> <use transform="translate(238.681 26.0443) rotate(99)" xlink:href="#e149_i0"></use> <use transform="translate(250.982 47.5477) rotate(108)" xlink:href="#e149_i0"></use> <use transform="translate(259.768 70.7107) rotate(117)" xlink:href="#e149_i0"></use> <use transform="translate(264.823 94.9629) rotate(126)" xlink:href="#e149_i0"></use> <use transform="translate(266.021 119.707) rotate(135)" xlink:href="#e149_i0"></use> <use transform="translate(263.334 144.335) rotate(144)" xlink:href="#e149_i0"></use> <use transform="translate(256.827 168.238) rotate(153)" xlink:href="#e149_i0"></use> <use transform="translate(246.661 190.83) rotate(162)" xlink:href="#e149_i0"></use> <use transform="translate(233.086 211.553) rotate(171)" xlink:href="#e149_i0"></use> <use transform="translate(216.437 229.897) rotate(-180)" xlink:href="#e149_i0"></use> <use transform="translate(197.123 245.411) rotate(-171)" xlink:href="#e149_i0"></use> <use transform="translate(175.619 257.712) rotate(-162)" xlink:href="#e149_i0"></use> <use transform="translate(152.456 266.498) rotate(-153)" xlink:href="#e149_i0"></use> <use transform="translate(128.204 271.553) rotate(-144)" xlink:href="#e149_i0"></use> <use transform="translate(103.46 272.751) rotate(-135)" xlink:href="#e149_i0"></use> <use transform="translate(78.8323 270.064) rotate(-126)" xlink:href="#e149_i0"></use> <use transform="translate(54.9287 263.557) rotate(-117)" xlink:href="#e149_i0"></use> <use transform="translate(32.3373 253.391) rotate(-108)" xlink:href="#e149_i0"></use> <use transform="translate(11.6143 239.816) rotate(-99)" xlink:href="#e149_i0"></use> <use transform="translate(-6.73004 223.167) rotate(-90)" xlink:href="#e149_i0"></use> <use transform="translate(-22.2439 203.853) rotate(-81)" xlink:href="#e149_i0"></use> <use transform="translate(-34.5454 182.349) rotate(-72)" xlink:href="#e149_i0"></use> <use transform="translate(-43.3316 159.186) rotate(-63)" xlink:href="#e149_i0"></use> <use transform="translate(-48.386 134.934) rotate(-54)" xlink:href="#e149_i0"></use> <use transform="translate(-49.5844 110.19) rotate(-45)" xlink:href="#e149_i0"></use> <use transform="translate(-46.8971 85.5623) rotate(-36)" xlink:href="#e149_i0"></use> <use transform="translate(-40.3904 61.6588) rotate(-27)" xlink:href="#e149_i0"></use> <use transform="translate(-30.2244 39.0673) rotate(-18)" xlink:href="#e149_i0"></use> <use transform="translate(-16.6495 18.3443) rotate(-8.99999)" xlink:href="#e149_i0"></use> </svg>
```

**SVG-11**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 7 1"> <path d="M0.380737 0.380859H6.24329" stroke="white" stroke-linecap="round" stroke-linejoin="round" stroke-width="0.76137"></path> </svg>
```

**SVG-12**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 4 6"> <path d="M0.380737 0.380859L2.77905 2.77918L0.380737 5.17749" stroke="white" stroke-linecap="round" stroke-linejoin="round" stroke-width="0.76137"></path> </svg>
```

**SVG-13**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 9 9"> <path d="M0.380615 8.15869L8.15879 0.380517" stroke="black" stroke-linecap="round" stroke-linejoin="round" stroke-width="0.76137"></path> </svg>
```

**SVG-14**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 8 8"> <path d="M0.380615 0.380859H6.74458V6.74482" stroke="black" stroke-linecap="round" stroke-linejoin="round" stroke-width="0.76137"></path> </svg>
```

**SVG-15**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 7 1"> <path d="M0.380615 0.380859H6.24317" stroke="white" stroke-linecap="round" stroke-linejoin="round" stroke-width="0.76137"></path> </svg>
```

**SVG-16**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 4 6"> <path d="M0.380615 0.380859L2.77893 2.77918L0.380615 5.17749" stroke="white" stroke-linecap="round" stroke-linejoin="round" stroke-width="0.76137"></path> </svg>
```

## 15. Acceptance criteria
- At 1440×810, 1280×720 and 390×844 the composition is identical at the key moments: end of the intro, every pause and the final state.
- Positions, sizes, colors, fonts, timings and curves match this specification.
- All texts are real, editable HTML; all links are clickable `<a href="#">` with visible focus; invisible things cannot be clicked.
- Images, video and fonts load from the URLs in section 13 and there are no console errors.
- The layout adapts when the window is resized and follows section 11 on mobile.
- With `prefers-reduced-motion` the final state is shown directly.
