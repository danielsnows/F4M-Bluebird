# Replication prompt — S5motors — The Perfect Balance of Industrial Speed

> Complete instructions for an AI coding agent. It contains everything needed to rebuild the animated hero «S5motors — The Perfect Balance of Industrial Speed» identically: structure, styles, texts, animation timings and curves, mobile view and links to the resources (images, video and fonts).

## Role and goal
Act as a senior front-end developer specialized in web animation. Build a single-screen page (hero) that exactly reproduces the animated design «S5motors — The Perfect Balance of Industrial Speed» described here, without adding or removing anything.

## 1. Overview
Automotive hero «S5motors — The Perfect Balance of Industrial Speed», designed at 1440×810 with a night look in violets and blues. Full-bleed in the background, a video of a white sports car driving through a glowing forest of mushrooms and flowers, with a dark gradient on the left that gives the text contrast. The automatic intro (0 → 2.05 s) brings in all the content: the letters of the title «The perfect / Balance / of industrial / Speed» appear one by one, and the wireframe globe, the text «Experience elegance, power, and thrilling drives…», the «View More» button with its arrow, the top bar (S5motors logo, pill menu Home · Models · Performance · Technology · Gallery · Contact, «Login» and a menu button), the four glass cards at the bottom (32 km Range, a photo of the car, 150 km Top Speed, 75 kWh Battery Capacity) and the «Experience Luxury on Wheels» card come in. After that, the scroll drives the video (2.05 → 7.6 s): with the interface in place, the car comes forward through the forest. Between 4.35 and 6.6 s the content moves on to the second section as a vertical scroll: section 1 (title, text, button and cards) rises and leaves through the top while the five numbered blocks —(001) Performance, (002) Innovation, (003) Comfort, (004) Power and (005) Elegance—, each with its text, come in from below; meanwhile the video camera rises to the top view of the car, the left gradient gives way to a radial one that darkens the edges and the menu stays in place. On hover, buttons lighten their background and menu links turn lavender.

## 2. Deliverable and technical rules
- A single `index.html` file with the CSS inside `<style>` and the JavaScript inside `<script>`. Plain (vanilla) JavaScript, no frameworks and no build step.
- The only external dependency: the Lenis library, exact version 1.3.26 (npm package `lenis`, file `dist/lenis.min.js`, which exposes the global class `Lenis`), for smooth scrolling. Load it with a `<script>` tag from a public npm CDN before your script (if it fails to load, everything must work with native scrolling).
- Images, video and fonts are ALWAYS loaded from the absolute URLs in section 13 «Resources». Do not download them, do not inline them as base64 and do not use any other resources.
- Every visible text is real, selectable and editable HTML text (never text turned into an image or into SVG paths). The vector shapes in the Appendix are only icons and decoration.
- Lists use `<ul>`/`<li>` (inside `<nav aria-label="…">` where indicated) and links use clickable `<a href="#">`, with `aria-label` where indicated.
- Head: `<html lang="en">`, `<meta charset="utf-8">`, `<meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover">`, `<title>The Perfect Balance of Industrial Speed</title>`, `<meta name="description" content="Experience elegance, power, and thrilling drives with a luxury car that redefines performance.">`, `<meta name="theme-color" content="#2a0f4f">`.
- Respect `prefers-reduced-motion: reduce`: the loader works the same, but there is no intro, no smooth scrolling and no snap; t stays fixed on the final state (t = 7.6 s) even when scrolling.
- Do not add elements, texts, effects or sections that are not in this specification.

## 3. Base structure (common HTML + CSS)
```html
<body>
<div class="loader" id="loader" aria-hidden="true">
  <div class="loader-in"><span>THE PERFECT BALANCE</span><div class="loader-bar"><i id="loaderBar"></i></div><span id="loaderPct">000</span></div>
</div>
<main class="hero" id="hero" aria-label="The Perfect Balance of Industrial Speed">
  <div class="stage" id="stage">
    <!-- one layer (div.blk) per «Layer» in section 9, in the same order: later ones are on top -->
  </div>
</main>
<script>/* engine from section 6 */</script>
</body>
```
```css
:root{--bg:#2a0f4f;--ink:#ffffff;color-scheme:dark}
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
- Recompute everything on every `resize` and also whenever the real size of `.stage` changes without a `resize` (a `ResizeObserver` on `.stage`; e.g. when a scrollbar appears or goes away), so the layers anchored to the edges always stay attached to the frame. On every recompute also set the CSS variable `--s` on `.stage` to the current scale (s on desktop; sm in the mobile view of section 11): `stage.style.setProperty('--s', scale)`; the styles that must follow the design use it (section 10).

## 5. Fonts
Declare these fonts with `@font-face{font-family:'…';src:url(URL) format('woff2');font-weight:…;font-style:normal}` and use them with the stack `'Font', system-ui, sans-serif`:
- **Gantari** (variable font, weight range 100–900 (font-weight:100 900)): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/neon/assets/fonts/gantari-VF-normal.woff2
- **Gantari** (variable font, weight range 100–900 (font-weight:100 900)): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/neon/assets/fonts/gantari-VF-italic.woff2
- **Lexend Exa** (variable font, weight range 100–900 (font-weight:100 900)): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/neon/assets/fonts/lexend-exa-VF-normal.woff2
- **Lexend Mega** (variable font, weight range 100–900 (font-weight:100 900)): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/neon/assets/fonts/lexend-mega-VF-normal.woff2

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
  - `E0` = cubic-bezier(0, 0, 0.58, 1)
  - `E1` = spring with mass=1, stiffness=80, damping=20, normalized to 1.25 s (see formula)
- `cubic-bezier(x1, y1, x2, y2)` curves work as in CSS: given the time progress p, solve the parameter u such that X(u) = p and return Y(u).

Spring formula with mass m, stiffness k, damping c and normalized duration D:
- ω0 = √(k/m), ζ = c / (2·√(k·m)).
- If ζ < 1: ωd = ω0·√(1−ζ²); x(τ) = 1 − e^(−ζ·ω0·τ)·(cos(ωd·τ) + (ζ·ω0/ωd)·sin(ωd·τ)).
- If ζ = 1: x(τ) = 1 − e^(−ω0·τ)·(1 + ω0·τ).
- If ζ > 1: q = √(ζ²−1), r1 = −ω0·(ζ−q), r2 = −ω0·(ζ+q); x(τ) = 1 + (r2·e^(r1·τ) − r1·e^(r2·τ)) / (r1 − r2).
- easing(p) = x(p·D) / x(D) for 0 < p < 1; 0 if p ≤ 0; 1 if p ≥ 1.

## 7. Playback (intro and scrolling)
- Total timeline duration: 7.6 s. Automatic intro: from t = 0 to t = 2.05 s. The rest (2.05 → 7.6 s) is driven by scrolling.
- `.hero` height = 100vh + track. track = round((7.6 − 2.05) × 40) = **222vh** on desktop and round((7.6 − 2.05) × 34) = **189vh** on mobile (use exactly these values; they are set through the CSS variable `--track`). `.stage` is sticky, so the stage stays fixed while the track is scrolled.
- While loading: `html.is-locked` (overflow hidden; add it before the first measurement of the stage, so a classic scrollbar does not narrow the measured width), `history.scrollRestoration = 'manual'`, `scrollTo(0,0)`.
- When the loader gets the `is-done` class, play the intro: t advances in real time (linearly) from 0 to 2.05 s in 2.05 s. During the intro scrolling is blocked (Lenis stopped; if Lenis is not available, keep `is-locked` until the intro ends) and scroll events do not change t. When it ends, t = 2.05 and scrolling is enabled.
- Smooth scrolling with Lenis: `new Lenis({lerp:0.085, wheelMultiplier:0.9, smoothWheel:true})`, `lenis.stop()` until the intro ends, then `lenis.start()`; call `lenis.raf(now)` in the requestAnimationFrame loop. Without Lenis, use native scrolling.
- Scroll → time mapping: p = clamp((scrollY − hero.offsetTop) / (hero.offsetHeight − stage.clientHeight), 0, 1); t = 2.05 + p × (7.6 − 2.05).
- Pauses (holds), in seconds: [2.05 – 4.35], [6.6 – 7.6]. Snap (Lenis only; no snap without Lenis): the direction is the sign of the last scroll change; 170 ms after the last scroll event (every event restarts the timer), if t is not inside a pause (with a ±0.02 s margin), scroll with `lenis.scrollTo` to the start of the next pause when scrolling down, or to the end of the previous pause when scrolling up. Duration = clamp(|Δt| × 0.45, 0.6, 1.8) s; easing easeInOutCubic (x < 0.5 ? 4x³ : 1 − (−2x+2)³/2). No snap with `prefers-reduced-motion` or during another snap (the «snapping» state is released in `onComplete` or, as a safety net, after 2.2 s).
- On every resize: recompute layers, track and t from the current scroll position.
- Video («scrub» mode, driven by the timeline): `<video muted playsinline preload="auto">` with its poster; it never plays by itself. For reliable seeking, download the MP4 with `fetch` as a Blob (reporting progress to the loader) and set `video.src = URL.createObjectURL(blob)` (if that fails, use the direct URL). On every frame, if `readyState ≥ 2` and it is not `seeking`: target = min(max(0, t), duration − 0.04); if |currentTime − target| > 0.012, `currentTime = target`.

## 8. Loader and preloading
- The loader covers the screen with the `--bg` color and shows «THE PERFECT BALANCE», a 160×1 px bar and a 3-digit percentage (`000` → `100`) in the `--ink` color.
- Weighted progress: fonts (`document.fonts.ready`) weight 1; each `<img>` element on the stage weight 0.4, even if it repeats a file (it counts once it has loaded —or failed— and `img.decode()` has finished; the video poster does not count); the video weight 6 (its fetch download progress). Percentage = Math.round(progress × 100) with 3 digits. The bar uses `transform: scaleX(progress)` and never goes backwards.
- Wait until everything finishes (14 s maximum; if it runs out, continue without cancelling anything); then set the video `src` (the Blob URL if already downloaded, otherwise the direct URL) and wait for its `loadeddata` or `error` (3 s maximum). Then remove `is-locked` from `<html>` (except in the no-Lenis case described in section 7), add `is-done` to the loader (it fades out in 0.9 s) and start the intro.

## 9. Layers and elements
Notation of each line: `[eN]` = suggested identifier; `box` = div; `text` = div with text (the content in quotes, keeping capitalization, line breaks and spaces); `group` = boxless container; `vector` = box containing an inline SVG; `<a href="#">`, `<ul>`, `<li>`, `<nav>` = semantic tag to use; `«name»` = layer name in the design (for reference only, also handy as `data-name`); `pos(x,y)` = left/top; `size A×B` = width×height; after `|` come the literal CSS styles. Every `text` element gets `class="t"` (used by the pointer-events rule in section 3); boxes, groups and vectors may use `class="b"`, `"g"` and `"v"`. Decorative layers (border, glass, inner shadow, background blur, fill layer) are absolute elements (for example `<i>`) covering their container (given `inset`) with inline `pointer-events:none` and `border-radius:inherit` unless stated otherwise. Images go inside an absolute container `inset:0; overflow:hidden; border-radius:inherit`. The order of the lines is the stacking order (later is on top).

#### Layer bk0 «Fondo» — BLEED LAYER (cover), design box x=0 y=0 1440×810
- [e1] box «home» pos(0,0) size 1440×810 | background-color:#909b9f

#### Layer bk1 «capcut-edit 1» — BLEED LAYER (cover), design box x=0 y=0 1440×827.23
- [e2] box «capcut-edit 1» pos(0,-8.62) size 1440×827.23
  - video: `<video muted playsinline preload="auto" poster="https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/neon/assets/video/b65f61a5ac.jpg" data-src="https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/neon/assets/video/b65f61a5ac.mp4">` with position:absolute; inset:0; width:100%; height:100%; object-fit:cover; display:block (the `src` is assigned from JavaScript once preloading finishes, see sections 7 and 8)

#### Layer bk2 «Rectangle 22» — CONTAINED LAYER (contain, anchor x=-1, y=0), design box x=0 y=0 588.9×810
- [e3] box «Rectangle 22» pos(0,0) size 588.9×810 | opacity:0.6; background:linear-gradient(90deg, #0f0424 2.01%, rgba(65,13,126,0) 92.71%)

#### Layer bk3 «Rectangle 22 s2» — BLEED LAYER (cover), design box x=0 y=0 1440×810
- [e4] box «Rectangle 22 s2» pos(0,0) size 1440×810 | opacity:0.9; background:radial-gradient(813.99px 813.98px at 681.99px 505.42px, rgba(65,13,126,0.08) 20%, #0f0424 99.45%)

#### Layer bk5 «Experience elegance, power, an... - Animation ▶» — CONTAINED LAYER (contain, anchor x=-1, y=0), design box x=53 y=393 401×50
- [e5] box «Experience elegance, power, an... - Animation ▶» pos(53,393) size 401×50 | overflow:hidden
  - [e6] box «Experience elegance, power, and thrilling drives» pos(0,0) size 401×50
    - [e7] text pos(0,-9) size 401×50 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Gantari'; font-weight:400; font-size:18px; line-height:1.4 | TEXT in runs: "Experience elegance, power, and thrilling drives
" {font-family:'Gantari'; font-weight:400; font-size:18px; line-height:1.4; color:#ffffff} + "with a luxury car that redefines performance." {font-family:'Gantari'; font-weight:400; font-size:18px; line-height:1.4; color:transparent}
        ↳ animation: y: 0.4→0.7s -9→0 (E0); opacity: 0.4→0.7s 0→1 (E0)
  - [e8] box «with a luxury car that redefines performance.» pos(0,0) size 401×50
    - [e9] text pos(0,-9) size 401×50 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Gantari'; font-weight:400; font-size:18px; line-height:1.4 | TEXT in runs: "Experience elegance, power, and thrilling drives
" {font-family:'Gantari'; font-weight:400; font-size:18px; line-height:1.4; color:transparent} + "with a luxury car that redefines performance." {font-family:'Gantari'; font-weight:400; font-size:18px; line-height:1.4; color:#ffffff}
        ↳ animation: y: 0.475→0.775s -9→0 (E0); opacity: 0.475→0.775s 0→1 (E0)

#### Layer bk6 «Component 3» — CONTAINED LAYER (contain, anchor x=-1, y=0), design box x=53 y=471 209.13×69.77
- [e10] box «Component 3» pos(53,471) size 209.13×69.77 | border-radius:999px
  - [e11] box «Frame 1597882288» pos(0,0) size 209.13×69.77
    - [e12] box <a href="#"> «Frame 1171275727» pos(41,0) size 66×70 | opacity:0; border-radius:999px; background-color:#ffffff
        ↳ animation: x: 0.4→1.65s 41→0 (E1); width: 0.4→1.65s 66→147.67 (E1); height: 0.4→1.65s 70→69.77 (E1); opacity: 0.4→1.65s 0→1 (E1)
      - [e13] text transform translate(4,31) rotate(0rad) size 58×8 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Gantari'; font-weight:500; font-size:12.34px; line-height:normal; letter-spacing:-0.03em; color:#121212 | TEXT: "View More"
          ↳ animation: x: 0.4→1.65s 4→26.83 (E1); y: 0.4→1.65s 31→28.38 (E1); height: 0.4→1.65s 8→8.02 (E1); opacity: 0.4→1.65s 0→1 (E1); content-scale: 0.4→1.65s 1→1.62 (E1)
    - [e14] box <a href="#"> aria-label="View more" «Frame 1171275728» pos(107,4) size 61.47×61.47 | opacity:0; border-radius:999px; background-color:rgba(0,0,0,0.01)
        ↳ animation: x: 0.4→1.65s 107→147.67 (E1); y: 0.4→1.65s 4→4.15 (E1); opacity: 0.4→1.65s 0→1 (E1)
      - glass effect (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(2px) saturate(1.25)
      - inner shadow: inset:0; border-radius:inherit; box-shadow:inset 1.2px 0px 0 0 rgba(255,255,255,0.22),inset -1.2px 0px 0 0 rgba(255,255,255,0.07),inset 0 0 19.2px 0 rgba(255,255,255,0.07)
      - [e15] box «ArrowUpRight» transform translate(20,41.46) rotate(-1.57rad) size 21.47×21.47
          ↳ animation: y: 0.4→1.65s 41.47→20 (E1); rotation(rad): 0.4→1.65s -1.57→0 (E1)
        - [e16] vector «Vector» pos(4.7,4.7) size 12.07×12.07
          - vector drawing SVG-1 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box

#### Layer bk7 «Group 9» — CONTAINED LAYER (contain, anchor x=-1, y=-1), design box x=53 y=152 494×213
- [e17] group «Group 9»
  - [e18] group «Group 10»
    - [e19] box «The perfect - Animation ▶» pos(53,152) size 386×42 | overflow:hidden
      - [e20] box «T» pos(0,0) size 386×42
        - [e21] text pos(0,-30) size 386×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXT in runs: "T" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "he perfect" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animation: y: 0→0.3s -30→0 (E0); opacity: 0→0.3s 0→1 (E0)
      - [e22] box «h» pos(0,0) size 386×42
        - [e23] text pos(0,-30) size 386×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXT in runs: "T" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "h" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "e perfect" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animation: y: 0.075→0.375s -30→0 (E0); opacity: 0.075→0.375s 0→1 (E0)
      - [e24] box «e» pos(0,0) size 386×42
        - [e25] text pos(0,-30) size 386×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXT in runs: "Th" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "e" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + " perfect" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animation: y: 0.15→0.45s -30→0 (E0); opacity: 0.15→0.45s 0→1 (E0)
      - [e26] box « » pos(0,0) size 386×42
        - [e27] text pos(0,-30) size 386×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXT in runs: "The" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + " " {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "perfect" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animation: y: 0.225→0.525s -30→0 (E0)
      - [e28] box «p» pos(0,0) size 386×42
        - [e29] text pos(0,-30) size 386×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXT in runs: "The " {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "p" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "erfect" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animation: y: 0.3→0.6s -30→0 (E0); opacity: 0.3→0.6s 0→1 (E0)
      - [e30] box «e» pos(0,0) size 386×42
        - [e31] text pos(0,-30) size 386×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXT in runs: "The p" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "e" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "rfect" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animation: y: 0.375→0.675s -30→0 (E0); opacity: 0.375→0.675s 0→1 (E0)
      - [e32] box «r» pos(0,0) size 386×42
        - [e33] text pos(0,-30) size 386×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXT in runs: "The pe" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "r" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "fect" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animation: y: 0.45→0.75s -30→0 (E0); opacity: 0.45→0.75s 0→1 (E0)
      - [e34] box «f» pos(0,0) size 386×42
        - [e35] text pos(0,-30) size 386×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXT in runs: "The per" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "f" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "ect" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animation: y: 0.525→0.825s -30→0 (E0); opacity: 0.525→0.825s 0→1 (E0)
      - [e36] box «e» pos(0,0) size 386×42
        - [e37] text pos(0,-30) size 386×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXT in runs: "The perf" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "e" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "ct" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animation: y: 0.6→0.9s -30→0 (E0); opacity: 0.6→0.9s 0→1 (E0)
      - [e38] box «c» pos(0,0) size 386×42
        - [e39] text pos(0,-30) size 386×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXT in runs: "The perfe" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "c" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "t" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animation: y: 0.675→0.975s -30→0 (E0); opacity: 0.675→0.975s 0→1 (E0)
      - [e40] box «t» pos(0,0) size 386×42
        - [e41] text pos(0,-30) size 386×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXT in runs: "The perfec" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "t" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff}
            ↳ animation: y: 0.75→1.05s -30→0 (E0); opacity: 0.75→1.05s 0→1 (E0)
    - [e42] box «of industrial - Animation ▶» pos(104,266) size 443×42 | overflow:hidden
      - [e43] box «o» pos(0,0) size 443×42
        - [e44] text pos(0,-30) size 443×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXT in runs: "o" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "f industrial" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animation: y: 0.2→0.5s -30→0 (E0); opacity: 0.2→0.5s 0→1 (E0)
      - [e45] box «f» pos(0,0) size 443×42
        - [e46] text pos(0,-30) size 443×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXT in runs: "o" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "f" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + " industrial" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animation: y: 0.275→0.575s -30→0 (E0); opacity: 0.275→0.575s 0→1 (E0)
      - [e47] box « » pos(0,0) size 443×42
        - [e48] text pos(0,-30) size 443×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXT in runs: "of" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + " " {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "industrial" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animation: y: 0.35→0.65s -30→0 (E0)
      - [e49] box «i» pos(0,0) size 443×42
        - [e50] text pos(0,-30) size 443×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXT in runs: "of " {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "i" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "ndustrial" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animation: y: 0.425→0.725s -30→0 (E0); opacity: 0.425→0.725s 0→1 (E0)
      - [e51] box «n» pos(0,0) size 443×42
        - [e52] text pos(0,-30) size 443×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXT in runs: "of i" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "n" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "dustrial" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animation: y: 0.5→0.8s -30→0 (E0); opacity: 0.5→0.8s 0→1 (E0)
      - [e53] box «d» pos(0,0) size 443×42
        - [e54] text pos(0,-30) size 443×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXT in runs: "of in" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "d" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "ustrial" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animation: y: 0.575→0.875s -30→0 (E0); opacity: 0.575→0.875s 0→1 (E0)
      - [e55] box «u» pos(0,0) size 443×42
        - [e56] text pos(0,-30) size 443×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXT in runs: "of ind" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "u" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "strial" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animation: y: 0.65→0.95s -30→0 (E0); opacity: 0.65→0.95s 0→1 (E0)
      - [e57] box «s» pos(0,0) size 443×42
        - [e58] text pos(0,-30) size 443×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXT in runs: "of indu" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "s" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "trial" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animation: y: 0.725→1.025s -30→0 (E0); opacity: 0.725→1.025s 0→1 (E0)
      - [e59] box «t» pos(0,0) size 443×42
        - [e60] text pos(0,-30) size 443×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXT in runs: "of indus" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "t" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "rial" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animation: y: 0.8→1.1s -30→0 (E0); opacity: 0.8→1.1s 0→1 (E0)
      - [e61] box «r» pos(0,0) size 443×42
        - [e62] text pos(0,-30) size 443×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXT in runs: "of indust" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "r" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "ial" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animation: y: 0.875→1.175s -30→0 (E0); opacity: 0.875→1.175s 0→1 (E0)
      - [e63] box «i» pos(0,0) size 443×42
        - [e64] text pos(0,-30) size 443×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXT in runs: "of industr" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "i" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "al" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animation: y: 0.95→1.25s -30→0 (E0); opacity: 0.95→1.25s 0→1 (E0)
      - [e65] box «a» pos(0,0) size 443×42
        - [e66] text pos(0,-30) size 443×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXT in runs: "of industri" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "a" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "l" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animation: y: 1.025→1.325s -30→0 (E0); opacity: 1.025→1.325s 0→1 (E0)
      - [e67] box «l» pos(0,0) size 443×42
        - [e68] text pos(0,-30) size 443×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXT in runs: "of industria" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "l" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff}
            ↳ animation: y: 1.1→1.4s -30→0 (E0); opacity: 1.1→1.4s 0→1 (E0)
    - [e69] box «speed - Animation ▶» pos(201.57,323) size 200×42 | overflow:hidden
      - [e70] box «s» pos(0,0) size 200×42
        - [e71] text pos(0,-30) size 200×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXT in runs: "s" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "peed" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animation: y: 0.3→0.6s -30→0 (E0); opacity: 0.3→0.6s 0→1 (E0)
      - [e72] box «p» pos(0,0) size 200×42
        - [e73] text pos(0,-30) size 200×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXT in runs: "s" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "p" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "eed" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animation: y: 0.375→0.675s -30→0 (E0); opacity: 0.375→0.675s 0→1 (E0)
      - [e74] box «e» pos(0,0) size 200×42
        - [e75] text pos(0,-30) size 200×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXT in runs: "sp" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "e" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "ed" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animation: y: 0.45→0.75s -30→0 (E0); opacity: 0.45→0.75s 0→1 (E0)
      - [e76] box «e» pos(0,0) size 200×42
        - [e77] text pos(0,-30) size 200×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXT in runs: "spe" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "e" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "d" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animation: y: 0.525→0.825s -30→0 (E0); opacity: 0.525→0.825s 0→1 (E0)
      - [e78] box «d» pos(0,0) size 200×42
        - [e79] text pos(0,-30) size 200×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXT in runs: "spee" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "d" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff}
            ↳ animation: y: 0.6→0.9s -30→0 (E0); opacity: 0.6→0.9s 0→1 (E0)
    - [e80] box «Balance - Animation ▶» pos(196,209) size 285×42 | overflow:hidden
      - [e81] box «B» pos(0,0) size 285×42
        - [e82] text pos(0,-30) size 285×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXT in runs: "B" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "alance" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animation: y: 0.1→0.4s -30→0 (E0); opacity: 0.1→0.4s 0→1 (E0)
      - [e83] box «a» pos(0,0) size 285×42
        - [e84] text pos(0,-30) size 285×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXT in runs: "B" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "a" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "lance" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animation: y: 0.175→0.475s -30→0 (E0); opacity: 0.175→0.475s 0→1 (E0)
      - [e85] box «l» pos(0,0) size 285×42
        - [e86] text pos(0,-30) size 285×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXT in runs: "Ba" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "l" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "ance" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animation: y: 0.25→0.55s -30→0 (E0); opacity: 0.25→0.55s 0→1 (E0)
      - [e87] box «a» pos(0,0) size 285×42
        - [e88] text pos(0,-30) size 285×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXT in runs: "Bal" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "a" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "nce" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animation: y: 0.325→0.625s -30→0 (E0); opacity: 0.325→0.625s 0→1 (E0)
      - [e89] box «n» pos(0,0) size 285×42
        - [e90] text pos(0,-30) size 285×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXT in runs: "Bala" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "n" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "ce" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animation: y: 0.4→0.7s -30→0 (E0); opacity: 0.4→0.7s 0→1 (E0)
      - [e91] box «c» pos(0,0) size 285×42
        - [e92] text pos(0,-30) size 285×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXT in runs: "Balan" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "c" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "e" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animation: y: 0.475→0.775s -30→0 (E0); opacity: 0.475→0.775s 0→1 (E0)
      - [e93] box «e» pos(0,0) size 285×42
        - [e94] text pos(0,-30) size 285×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXT in runs: "Balanc" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "e" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff}
            ↳ animation: y: 0.55→0.85s -30→0 (E0); opacity: 0.55→0.85s 0→1 (E0)
  - [e95] box «Component 2» pos(53,323) size 138×42
    - [e96] group «Group 2» | opacity:0
        ↳ animation: opacity: 0.3→1.55s 0→1 (E1)
      - [e97] box «Ellipse 1» pos(-24,0) size 138×42 | border-radius:50%
          ↳ animation: x: 0.3→1.55s -24→0 (E1)
        - border (stroke): inset:-0.5px; border-radius:50%; padding:1px; background:linear-gradient(90deg, #ffffff 0%, rgba(255,255,255,0.26) 30.77%); 1px gradient border (gradient background clipped with mask: linear-gradient content-box exclude)
      - [e98] box «Ellipse 4» pos(-24,7) size 138×28 | border-radius:50%
          ↳ animation: x: 0.3→1.55s -24→0 (E1)
        - border (stroke): inset:-0.5px; border-radius:50%; padding:1px; background:linear-gradient(90deg, #ffffff 0%, rgba(255,255,255,0.26) 30.77%); 1px gradient border (gradient background clipped with mask: linear-gradient content-box exclude)
      - [e99] box «Ellipse 5» pos(-24,15) size 138×12 | border-radius:50%
          ↳ animation: x: 0.3→1.55s -24→0 (E1)
        - border (stroke): inset:-0.5px; border-radius:50%; padding:1px; background:linear-gradient(90deg, #ffffff 0%, rgba(255,255,255,0.26) 30.77%); 1px gradient border (gradient background clipped with mask: linear-gradient content-box exclude)
      - [e100] box «Ellipse 2» pos(16.02,0) size 57.97×42 | border-radius:50%
          ↳ animation: x: 0.3→1.55s 16.02→40.02 (E1)
        - border (stroke): inset:-0.5px; border-radius:50%; padding:1px; background:linear-gradient(90deg, #ffffff 0%, rgba(255,255,255,0.26) 30.77%); 1px gradient border (gradient background clipped with mask: linear-gradient content-box exclude)
      - [e101] box «Ellipse 7» pos(5,0) size 80×42 | border-radius:50%
          ↳ animation: x: 0.3→1.55s 5→29 (E1)
        - border (stroke): inset:-0.5px; border-radius:50%; padding:1px; background:linear-gradient(90deg, #ffffff 0%, rgba(255,255,255,0.26) 30.77%); 1px gradient border (gradient background clipped with mask: linear-gradient content-box exclude)
      - [e102] box «Ellipse 8» pos(-5.96,0) size 101.91×42 | border-radius:50%
          ↳ animation: x: 0.3→1.55s -5.96→18.04 (E1)
        - border (stroke): inset:-0.5px; border-radius:50%; padding:1px; background:linear-gradient(90deg, #ffffff 0%, rgba(255,255,255,0.26) 30.77%); 1px gradient border (gradient background clipped with mask: linear-gradient content-box exclude)
      - [e103] box «Ellipse 9» pos(-16.58,0) size 123.15×42 | border-radius:50%
          ↳ animation: x: 0.3→1.55s -16.58→7.42 (E1)
        - border (stroke): inset:-0.5px; border-radius:50%; padding:1px; background:linear-gradient(90deg, #ffffff 0%, rgba(255,255,255,0.26) 30.77%); 1px gradient border (gradient background clipped with mask: linear-gradient content-box exclude)
      - [e104] box «Ellipse 3» pos(27,0) size 36×42 | border-radius:50%
          ↳ animation: x: 0.3→1.55s 27→51 (E1)
        - border (stroke): inset:-0.5px; border-radius:50%; padding:1px; background:linear-gradient(90deg, #ffffff 0%, rgba(255,255,255,0.26) 30.77%); 1px gradient border (gradient background clipped with mask: linear-gradient content-box exclude)
      - [e105] box «Ellipse 6» pos(38.59,0) size 12.82×42 | border-radius:50%
          ↳ animation: x: 0.3→1.55s 38.59→62.59 (E1)
        - border (stroke): inset:-0.5px; border-radius:50%; padding:1px; background:linear-gradient(90deg, #ffffff 0%, rgba(255,255,255,0.26) 30.77%); 1px gradient border (gradient background clipped with mask: linear-gradient content-box exclude)

#### Layer bk8 «image 4» — CONTAINED LAYER (contain, anchor x=0, y=1), design box x=842.26 y=798.33 1×0.36
- [e106] box «image 4» pos(842.26,798.33) size 1×0.36
  - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/neon/assets/img/ddbf958085.webp: absolutely positioned `<img alt="">` at (0,0), 1024×574 px, max-width:none, transform-origin 0 0 and transform matrix(0.0043,0,0,0.0043,-1.755,-1.078); the container clips it (overflow hidden, same border-radius)

#### Layer bk9 «Component 4» — CONTAINED LAYER (contain, anchor x=-1, y=1), design box x=53 y=637 684×149
- [e107] box «Component 4» pos(53,637) size 684×149
  - [e108] box «Frame 1171275741» pos(0,180) size 156×149 | border-radius:32px; background-color:rgba(255,255,255,0.00)
      ↳ animation: y: 0.5→1.75s 180→0 (E1)
    - glass effect (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(16px) saturate(1.25)
    - inner shadow: inset:0; border-radius:inherit; box-shadow:inset 1.2px 0px 0 0 rgba(255,255,255,0.44),inset -1.2px 0px 0 0 rgba(255,255,255,0.14),inset 0 0 24px 0 rgba(255,255,255,0.14)
    - [e109] text pos(25,160) size 42×9 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Gantari'; font-weight:400; font-size:14px; line-height:1.4; font-feature-settings:'liga' 0,'salt' 1; color:#ffffff | TEXT: "Range"
        ↳ animation: y: 0.5→1.75s 160→90 (E1)
    - [e110] box «Frame 20» pos(23.39,78.5) size 75×25
        ↳ animation: y: 0.5→1.75s 78.5→48.5 (E1)
      - [e111] text pos(0,0) size 43×25 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Exa'; font-weight:600; font-size:35px; line-height:1.2; letter-spacing:-0.09em; text-transform:uppercase; font-feature-settings:'liga' 0,'salt' 1; color:#ffffff | TEXT: "32"
      - [e112] text pos(42,0) size 33×13 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Exa'; font-weight:300; font-size:19px; line-height:1.2; letter-spacing:-0.09em; text-transform:uppercase; font-feature-settings:'liga' 0,'salt' 1; color:#ffffff | TEXT: "km"
  - [e113] box «Frame 1171275742» pos(176,210) size 156×149 | border-radius:32px; overflow:hidden; background-color:#d8bdff
      ↳ animation: y: 0.5→1.75s 210→0 (E1)
    - [e114] box «image 8» transform translate(363.08,-5) rotate(3.14rad) scale(1,-1) size 469.08×264
        ↳ animation: x: 0.5→1.75s 363.08→269 (E1); y: 0.5→1.75s -5→-15 (E1); width: 0.5→1.75s 469.08→281 (E1); height: 0.5→1.75s 264→158 (E1)
      - fill layer (covers the whole box, stacked in this order): mix-blend-mode:multiply
      - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/neon/assets/img/1ab5d7ea92.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)
      - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/neon/assets/img/50c30bd7df.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)
  - [e115] box «Frame 1171275743» pos(352,260) size 156×149 | border-radius:32px; background-color:rgba(255,255,255,0.00)
      ↳ animation: y: 0.5→1.75s 260→0 (E1)
    - glass effect (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(16px) saturate(1.25)
    - inner shadow: inset:0; border-radius:inherit; box-shadow:inset 1.2px 0px 0 0 rgba(255,255,255,0.44),inset -1.2px 0px 0 0 rgba(255,255,255,0.14),inset 0 0 24px 0 rgba(255,255,255,0.14)
    - [e116] text pos(25,160) size 67×9 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Gantari'; font-weight:400; font-size:14px; line-height:1.4; font-feature-settings:'liga' 0,'salt' 1; color:#ffffff | TEXT: "Top Speed"
        ↳ animation: y: 0.5→1.75s 160→90 (E1)
    - [e117] box «Frame 21» pos(23.39,78.5) size 97×25
        ↳ animation: y: 0.5→1.75s 78.5→48.5 (E1)
      - [e118] text pos(0,0) size 65×25 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Exa'; font-weight:600; font-size:35px; line-height:1.2; letter-spacing:-0.09em; text-transform:uppercase; font-feature-settings:'liga' 0,'salt' 1; color:#ffffff | TEXT: "150"
      - [e119] text pos(64,0) size 33×13 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Exa'; font-weight:300; font-size:19px; line-height:1.2; letter-spacing:-0.09em; text-transform:uppercase; font-feature-settings:'liga' 0,'salt' 1; color:#ffffff | TEXT: "km"
  - [e120] box «Frame 1171275744» pos(528,300) size 156×149 | border-radius:32px; background-color:rgba(255,255,255,0.00)
      ↳ animation: y: 0.5→1.75s 300→0 (E1)
    - glass effect (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(16px) saturate(1.25)
    - inner shadow: inset:0; border-radius:inherit; box-shadow:inset 1.2px 0px 0 0 rgba(255,255,255,0.44),inset -1.2px 0px 0 0 rgba(255,255,255,0.14),inset 0 0 24px 0 rgba(255,255,255,0.14)
    - [e121] text pos(25,160) size 109×9 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Gantari'; font-weight:400; font-size:14px; line-height:1.4; font-feature-settings:'liga' 0,'salt' 1; color:#ffffff | TEXT: "Battery Capacity"
        ↳ animation: y: 0.5→1.75s 160→90 (E1)
    - [e122] box «Frame 20» pos(23.39,78.5) size 91×25
        ↳ animation: y: 0.5→1.75s 78.5→48.5 (E1)
      - [e123] text pos(0,0) size 42×25 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Exa'; font-weight:600; font-size:35px; line-height:1.2; letter-spacing:-0.09em; text-transform:uppercase; font-feature-settings:'liga' 0,'salt' 1; color:#ffffff | TEXT: "75"
      - [e124] text pos(41,0) size 50×13 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Exa'; font-weight:300; font-size:19px; line-height:1.2; letter-spacing:-0.09em; text-transform:uppercase; font-feature-settings:'liga' 0,'salt' 1; color:#ffffff | TEXT: "kWh"

#### Layer bk10 «Component 5» — CONTAINED LAYER (contain, anchor x=1, y=1), design box x=1179 y=385 237×297
- [e125] box «Component 5» pos(1179,385) size 237×297 | border-radius:32px; overflow:hidden
  - [e126] group «Group 33»
    - [e127] box «Rectangle 27» pos(0,420) size 237×193 | border-radius:32px; background-color:rgba(255,255,255,0.00)
        ↳ animation: y: 0.5→1.75s 420→0 (E1)
      - glass effect (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(0px) saturate(1.25)
      - inner shadow: inset:0; border-radius:inherit; box-shadow:inset 0px -1.2px 0 0 rgba(255,255,255,0.44),inset 0px 1.2px 0 0 rgba(255,255,255,0.14),inset 0 0 24px 0 rgba(255,255,255,0.14)
    - [e128] box «Rectangle 26» pos(0,367) size 237×193 | border-radius:32px; background:linear-gradient(180deg, #d44eb5 0%, #6009e3 26.17%)
        ↳ animation: y: 0.5→1.75s 367→17 (E1)
      - background blur (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(4.95px)
    - [e129] box «Frame 1597882325» pos(0,323) size 237×254 | border-radius:32px; overflow:hidden; background:linear-gradient(136.32deg, #ffffff 62.8%, #d1abff 98.02%)
        ↳ animation: y: 0.5→1.75s 323→43 (E1)
      - background blur (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(10px)
      - [e130] box «Rectangle 25» transform translate(-6,13) rotate(0rad) size 249×153 | border-radius:22px; filter:brightness(1.14)
          ↳ animation: x: 0.5→1.75s -6→10 (E1); y: 0.5→1.75s 13→10 (E1); width: 0.5→1.75s 249→249.32 (E1); height: 0.5→1.75s 153→152.81 (E1); radius: 0.5→1.75s 22→25.28 (E1); content-scale: 0.5→1.75s 1→0.87 (E1)
        - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/neon/assets/img/7b0aed9020-612186.webp: absolutely positioned `<img alt="">` at (0,0), 1672×941 px, max-width:none, transform-origin 0 0 and transform matrix(0.3112,0,0,0.3120,-146.558,-23.766); the container clips it (overflow hidden, same border-radius)
      - [e131] text pos(29,205) size 156×64 | white-space:pre-wrap; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:400; font-size:20px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'liga' 0,'salt' 1; color:#3e336b | TEXT: "Experience Luxury on Wheels"
          ↳ animation: y: 0.5→1.75s 205→165 (E1)
      - [e132] box <a href="#"> aria-label="Experience luxury on wheels" «Frame 1171275728» pos(169,236) size 61.47×61.47 | border-radius:999px; background-color:rgba(255,255,255,0.38)
          ↳ animation: y: 0.5→1.75s 236→186 (E1)
        - glass effect (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(2px) saturate(1.25)
        - inner shadow: inset:0; border-radius:inherit; box-shadow:inset 1.2px 0px 0 0 rgba(255,255,255,0.22),inset -1.2px 0px 0 0 rgba(255,255,255,0.07),inset 0 0 19.2px 0 rgba(255,255,255,0.07)
        - [e133] box «ArrowUpRight» pos(20,20) size 21.47×21.47
          - [e134] vector «Vector» pos(4.7,4.7) size 12.07×12.07
            - vector drawing SVG-2 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - border (stroke): inset:0px; border-radius:32px; padding:2px; background:linear-gradient(-39.03deg, rgba(255,255,255,0.25) 8.63%, rgba(255,255,255,0.11) 95%); 1px gradient border (gradient background clipped with mask: linear-gradient content-box exclude)

#### Layer bk11 «Group 42» — CONTAINED LAYER (contain, anchor x=0, y=-1), design box x=49 y=147 1340×203
- [e135] group «Group 42»
  - [e136] group «Group 37»
    - [e137] text pos(86,154) size 304×28 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:40px; line-height:0.97; letter-spacing:-0.21em; text-transform:uppercase; color:#ffffff | TEXT: "Performance"
    - [e138] text pos(49,147) size 34×20 | opacity:0.7; white-space:pre; text-align:left; font-family:'Gantari'; font-weight:500; font-size:14px; line-height:1.4; color:#ffffff | TEXT: "(001)"
    - [e139] text pos(91,210) size 251×140 | white-space:pre-wrap; text-align:left; font-family:'Gantari'; font-weight:400; font-size:14px; line-height:1.4; color:#ffffff | TEXT: "Unleash breathtaking acceleration and razor-sharp handling with our precision-engineered powertrains. Every component is tuned for maximum output, delivering an exhilarating drive that commands the road with authority."
  - [e140] group «Group 41»
    - [e141] text pos(1133,154) size 214×28 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:40px; line-height:0.97; letter-spacing:-0.21em; text-transform:uppercase; color:#ffffff | TEXT: "Elegance"
    - [e142] text pos(1096,147) size 36×20 | opacity:0.7; white-space:pre; text-align:left; font-family:'Gantari'; font-weight:500; font-size:14px; line-height:1.4; color:#ffffff | TEXT: "(005)"
    - [e143] text pos(1138,210) size 251×120 | white-space:pre-wrap; text-align:left; font-family:'Gantari'; font-weight:400; font-size:14px; line-height:1.4; color:#ffffff | TEXT: "Sculpted lines and refined aesthetics define every curve of our luxury vehicles. Handcrafted details and premium materials create a presence that turns heads and embodies timeless sophistication."

#### Layer bk12 «Group 40» — CONTAINED LAYER (contain, anchor x=0, y=1), design box x=123 y=460 1191×279
- [e144] group «Group 40»
  - [e145] group «Group 38»
    - [e146] text pos(160,467) size 249×28 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:40px; line-height:0.97; letter-spacing:-0.21em; text-transform:uppercase; color:#ffffff | TEXT: "Innovation"
    - [e147] text pos(123,460) size 35×20 | opacity:0.7; white-space:pre; text-align:left; font-family:'Gantari'; font-weight:500; font-size:14px; line-height:1.4; color:#ffffff | TEXT: "(002)"
    - [e148] text pos(165,523) size 251×120 | white-space:pre-wrap; text-align:left; font-family:'Gantari'; font-weight:400; font-size:14px; line-height:1.4; color:#ffffff | TEXT: "Cutting-edge technology seamlessly integrates into every system onboard. From adaptive AI-assisted driving to next-generation infotainment, our vehicles set the standard for automotive advancement."
  - [e149] group «Group 40»
    - [e150] text pos(609,563) size 200×28 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:40px; line-height:0.97; letter-spacing:-0.21em; text-transform:uppercase; color:#ffffff | TEXT: "Comfort"
    - [e151] text pos(572,556) size 35×20 | opacity:0.7; white-space:pre; text-align:left; font-family:'Gantari'; font-weight:500; font-size:14px; line-height:1.4; color:#ffffff | TEXT: "(003)"
    - [e152] text pos(614,619) size 251×120 | white-space:pre-wrap; text-align:left; font-family:'Gantari'; font-weight:400; font-size:14px; line-height:1.4; color:#ffffff | TEXT: "Step into a sanctuary of refinement with hand-stitched leather, climate-controlled seating, and near-silent cabins. Every journey becomes an indulgent escape from the demands of the outside world."
  - [e153] group «Group 39»
    - [e154] text pos(1058,467) size 151×28 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:40px; line-height:0.97; letter-spacing:-0.21em; text-transform:uppercase; color:#ffffff | TEXT: "Power"
    - [e155] text pos(1021,460) size 37×20 | opacity:0.7; white-space:pre; text-align:left; font-family:'Gantari'; font-weight:500; font-size:14px; line-height:1.4; color:#ffffff | TEXT: "(004)"
    - [e156] text pos(1063,523) size 251×120 | white-space:pre-wrap; text-align:left; font-family:'Gantari'; font-weight:400; font-size:14px; line-height:1.4; color:#ffffff | TEXT: "Massive horsepower figures and torque curves that redefine what a road car can achieve. Our engines are engineered to perform relentlessly, delivering power on demand at every speed."

#### Layer bk13 «Frame 26» — CONTAINED LAYER (contain, anchor x=1, y=1), design box x=1344 y=716 69.77×69.77
- [e157] box <a href="#"> aria-label="Scroll down" «Frame 26» pos(1344,716) size 69.77×69.77 | border-radius:32px; background-color:rgba(255,255,255,0.00)
  - glass effect (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(0px) saturate(1.25)
  - inner shadow: inset:0; border-radius:inherit; box-shadow:inset 1.2px 0px 0 0 rgba(255,255,255,0.44),inset -1.2px 0px 0 0 rgba(255,255,255,0.14),inset 0 0 24px 0 rgba(255,255,255,0.14)
  - [e158] box «ArrowDown» transform translate(50.88,18.88) rotate(3.14rad) scale(1,-1) size 32×32
    - [e160] vector «Vector» transform matrix(-1,0,0,1,17,4) size 2×24
      - vector drawing SVG-3 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
    - [e161] vector «Vector» transform matrix(-1,0,0,1,26,17) size 20×11
      - vector drawing SVG-4 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box

#### Layer bk14 «menu» — CONTAINED LAYER (contain, anchor x=0, y=-1), design box x=24 y=16 1392×52
- [e162] box «menu» pos(24,16) size 1392×52
  - [e163] box «Frame 1597882325» pos(648,2) size 96×49 | opacity:0; border-radius:999px; overflow:hidden; background-color:rgba(255,255,255,0.01)
      ↳ animation: x: 0.001→1.251s 648→366.5 (E1); width: 0.001→1.251s 96→659 (E1); opacity: 0.001→1.251s 0→1 (E1)
    - glass effect (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(2px) saturate(1.25)
    - inner shadow: inset:0; border-radius:inherit; box-shadow:inset 1.2px 0px 0 0 rgba(255,255,255,0.22),inset -1.2px 0px 0 0 rgba(255,255,255,0.07),inset 0 0 19.2px 0 rgba(255,255,255,0.07)
    - [-] list <nav> aria-label="Main navigation"
      - [-] list <ul>
        - [e164] box <li> «Frame 1597882289» pos(17,8) size 63×33 | border-radius:99px; background:linear-gradient(53.12deg, rgba(255,255,255,0.33) -16.55%, rgba(255,255,255,0) 107.72%)
            ↳ animation: x: 0.001→1.251s 17→8 (E1)
          - [-] link <a href="#"> that covers its container's whole box (inset:0)
            - [e165] text pos(12,12) size 39×9 | white-space:pre; text-align:right; text-box:trim-both cap alphabetic; font-family:'Gantari'; font-weight:700; font-size:14px; line-height:1.4; color:#ffffff | TEXT: "Home"
            - border (stroke): inset:0px; border-radius:99px; padding:1px; background:linear-gradient(-132.35deg, rgba(255,255,255,0.6) 7.93%, rgba(255,255,255,0) 103.28%); 1px gradient border (gradient background clipped with mask: linear-gradient content-box exclude)
        - [e166] box <li> «Frame 1597882290» pos(12,8) size 72×33 | border-radius:99px
            ↳ animation: x: 0.001→1.251s 12→103 (E1)
          - [-] link <a href="#"> that covers its container's whole box (inset:0)
            - [e167] text pos(12,12) size 48×9 | white-space:pre; text-align:right; text-box:trim-both cap alphabetic; font-family:'Gantari'; font-weight:400; font-size:14px; line-height:1.4; color:#ffffff | TEXT: "Models"
        - [e168] box <li> «Frame 1597882292» pos(-5,8) size 107×33 | border-radius:99px
            ↳ animation: x: 0.001→1.251s -5→207 (E1)
          - [-] link <a href="#"> that covers its container's whole box (inset:0)
            - [e169] text pos(12,12) size 83×9 | white-space:pre; text-align:right; text-box:trim-both cap alphabetic; font-family:'Gantari'; font-weight:400; font-size:14px; line-height:1.4; color:#ffffff | TEXT: "Performance"
        - [e170] box <li> «Frame 1597882291» pos(0,8) size 97×33 | border-radius:99px
            ↳ animation: x: 0.001→1.251s 0→346 (E1)
          - [-] link <a href="#"> that covers its container's whole box (inset:0)
            - [e171] text pos(12,12) size 73×9 | white-space:pre; text-align:right; text-box:trim-both cap alphabetic; font-family:'Gantari'; font-weight:400; font-size:14px; line-height:1.4; color:#ffffff | TEXT: "Technology"
        - [e172] box <li> «Frame 1597882293» pos(14,8) size 69×33 | border-radius:99px
            ↳ animation: x: 0.001→1.251s 14→475 (E1)
          - [-] link <a href="#"> that covers its container's whole box (inset:0)
            - [e173] text pos(12,12) size 45×9 | white-space:pre; text-align:right; text-box:trim-both cap alphabetic; font-family:'Gantari'; font-weight:400; font-size:14px; line-height:1.4; color:#ffffff | TEXT: "Gallery"
        - [e174] box <li> «Frame 1597882294» pos(11,8) size 75×33 | border-radius:99px
            ↳ animation: x: 0.001→1.251s 11→576 (E1)
          - [-] link <a href="#"> that covers its container's whole box (inset:0)
            - [e175] text pos(12,12) size 51×9 | white-space:pre; text-align:right; text-box:trim-both cap alphabetic; font-family:'Gantari'; font-weight:400; font-size:14px; line-height:1.4; color:#ffffff | TEXT: "Contact"
  - [e176] box <a href="#"> aria-label="Open menu" «Frame 1597882296» pos(1250,0) size 52×52 | opacity:0; border-radius:32px; background-color:rgba(255,255,255,0.00)
      ↳ animation: x: 0.001→1.251s 1250→1340 (E1); opacity: 0.001→1.251s 0→1 (E1)
    - glass effect (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(0px) saturate(1.25)
    - inner shadow: inset:0; border-radius:inherit; box-shadow:inset 1.2px 0px 0 0 rgba(255,255,255,0.44),inset -1.2px 0px 0 0 rgba(255,255,255,0.14),inset 0 0 24px 0 rgba(255,255,255,0.14)
    - [e177] group «Group 8»
      - [e178] box «Line 9» transform translate(37.49,20.66) rotate(3.14rad) size 22.98×2 | background-color:#ffffff
      - [e179] box «Line 11» transform translate(31.86,27) rotate(3.14rad) size 11.73×2 | background-color:#ffffff
      - [e180] box «Line 10» transform translate(37.49,33.33) rotate(3.14rad) size 22.98×2 | background-color:#ffffff
  - [e181] box <a href="#"> «Frame 1597882324» pos(1044,9) size 60×33 | opacity:0; border-radius:99px; background:linear-gradient(51.77deg, rgba(255,255,255,0.33) -17.73%, rgba(255,255,255,0) 107.5%)
      ↳ animation: x: 0.001→1.251s 1044→1254 (E1); y: 0.001→1.251s 9→10 (E1); opacity: 0.001→1.251s 0→1 (E1)
    - background blur (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(20px)
    - [e182] text pos(12,12) size 36×9 | white-space:pre; text-align:right; text-box:trim-both cap alphabetic; font-family:'Gantari'; font-weight:700; font-size:14px; line-height:1.4; color:#ffffff | TEXT: "Login"
    - border (stroke): inset:0px; border-radius:99px; padding:1px; background:linear-gradient(-133.75deg, rgba(255,255,255,0.6) 7.68%, rgba(255,255,255,0) 104.26%); 1px gradient border (gradient background clipped with mask: linear-gradient content-box exclude)
  - [e183] group <a href="#"> «Group 34» | opacity:0
      ↳ animation: opacity: 0.001→1.251s 0→1 (E1)
    - [e184] box «image 9» pos(90,17) size 17.15×17.15
        ↳ animation: x: 0.001→1.251s 90→0 (E1); y: 0.001→1.251s 17→18 (E1); width: 0.001→1.251s 17.15→17.14 (E1)
      - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/neon/assets/img/799dcf4932.webp: absolutely positioned `<img alt="">` at (0,0), 2048×2048 px, max-width:none, transform-origin 0 0 and transform matrix(0.0331,0,0,0.0331,-25.328,-25.328); the container clips it (overflow hidden, same border-radius)
    - [e185] text pos(114,21.29) size 66×8 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:400; font-size:12.00px; line-height:1.4; letter-spacing:-0.13em; color:#ffffff | TEXT: "S5motors"
        ↳ animation: x: 0.001→1.251s 114→24 (E1); y: 0.001→1.251s 21.29→22.29 (E1)

## 10. Final adjustments
- [e4] «Rectangle 22 s2»: on both desktop and mobile, its opacity is also multiplied by clamp((t − 4.9)/1.1, 0, 1).
- [e3] «Rectangle 22»: on desktop and mobile its opacity is also multiplied by clamp((6 − t)/1.1, 0, 1) when t > 4.9 (if the element has no animation of its own, that is its opacity directly, with `visibility:hidden` at 0).
- Time-linked vertical offset of [e17] «Group 9», [e5] «Experience elegance, power, an... - Animation ▶», [e10] «Component 3», [e107] «Component 4», [e125] «Component 5»: CSS property `translate: 0px Ypx` (added to its animated position), with Y in stage heights: Y px = value × stage.clientHeight / accumulated zoom of the element (product of the `zoom` of the element and its containers). Keyframes (t, value): (4.9 s, 0) → (6.3 s, -1.12) [cubic-bezier(0.4, 0, 0.75, 1)]; between two keyframes interpolate with the curve given on the second; before the first and after the last keep the end value. Applies on desktop and mobile; if several entries affect the same element their values add up.
- Time-linked vertical offset of [e135] «Group 42», [e144] «Group 40»: CSS property `translate: 0px Ypx` (added to its animated position), with Y in stage heights: Y px = value × stage.clientHeight / accumulated zoom of the element (product of the `zoom` of the element and its containers). Keyframes (t, value): (4.35 s, 1.05) → (4.75 s, 0.74) [cubic-bezier(0.2, 0, 0.4, 1)] → (5.25 s, 0.66) [linear] → (6.6 s, 0) [cubic-bezier(0.35, 0, 0.2, 1)]; between two keyframes interpolate with the curve given on the second; before the first and after the last keep the end value. Applies on desktop and mobile; if several entries affect the same element their values add up.
### Hover states (only when `matchMedia("(hover: hover) and (pointer: fine)")` matches; no hover on touch)
- Menu and text links (subtle colour change): [e164] «Home», [e166] «Models», [e168] «Performance», [e170] «Technology», [e172] «Gallery», [e174] «Contact». On pointer enter or focus, for the link and each of its descendants read its computed colour c (skip it if its alpha is 0, e.g. gradient text) and store the variable `--hvc` = rgb(c + (#d8bdff − c) × 1) per channel (alpha 1); add the class `hv-tr` and, on the next frame, `is-hv`. On leave (or blur) remove `is-hv` and, 420 ms later, `hv-tr`.
- Buttons (subtle background change): [e12] «View More» → layer in the link itself with background rgba(216,189,255,.45); [e14] «View more» → layer in the link itself with background rgba(255,255,255,.16); [e132] «Experience luxury on wheels» → layer in the link itself with background rgba(255,255,255,.16); [e157] «Scroll down» → layer in the link itself with background rgba(255,255,255,.16); [e176] «Open menu» → layer in the link itself with background rgba(255,255,255,.16); [e181] «Login» → layer in the link itself with background rgba(255,255,255,.16). In each one insert `<i class="hvo" aria-hidden="true">` inside the given box, right after its fill/effect layers (`i.pl`, `i.bb`, `i.gl`, `i.is`) and before its content, with that background colour; on pointer enter or focus the link adds `is-hv` to that layer and removes it on leave or blur.
- Icon or logo links without a surface of their own (drop to 0.72 opacity on hover): [e183] «S5motors». They get the class `hv-ic`.
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
- layer «Rectangle 22»: x=0, y=0, z=1.05
- layer «menu»: x=-8, y=14, z=1
- layer «Group 9»: x=12, y=104, z=0.68
- layer «Experience elegance, power, an... - Animation ▶»: x=16, y=262, z=0.85
- layer «Component 3»: x=16, y=318, z=0.85
- layer «Component 5»: x=244, y=448, z=0.55
- layer «Component 4»: x=16, y=682, z=0.52
- layer «Frame 26»: x=170.5, y=776, z=0.7
- layer «Group 42»: x=24, y=92, z=0.7
- layer «Group 40»: x=24, y=243.9, z=0.7
Unlisted non-bleed layers are hidden on mobile.
Per-element mobile adjustments:
- [e163] «Frame 1597882325»: hide
- [e181] «Frame 1597882324»: shift dx=-994, dy=0 (design units of its layer)
- [e176] «Frame 1597882296»: shift dx=-1010, dy=0 (design units of its layer)
- [e140] «Group 41»: shift dx=-1047, dy=808 (design units of its layer)
- [e149] «Group 40»: shift dx=-449, dy=101 (design units of its layer)
- [e153] «Group 39»: shift dx=-898, dy=394 (design units of its layer)
- [e157] «Frame 26»: fade out between t=7.85s and 8.45s

## 12. Semantics and accessibility
- `<main class="hero" aria-label="The Perfect Balance of Industrial Speed">`; the loader has `aria-hidden="true"`.
- Lists with `<ul>` and `<li>`; menus inside `<nav aria-label="…">`. Each `<a href="#">` uses the given `aria-label` when its content is not text. If a link covers a whole box, it goes inside it as an absolute `<a class="lk">` covering it (`inset:0`).
- Decorative images with `alt=""`; decorative SVGs with `aria-hidden="true"`.
- Only texts and links receive pointer events (CSS in section 3) and invisible elements get `visibility:hidden`, so nothing that cannot be seen can ever be clicked.
- Visible focus: `outline: 2px solid currentColor; outline-offset: 3px`.

## 13. Resources (absolute URLs)
All resources are served from a CDN with CORS enabled; use these URLs exactly as given:
- `assets/fonts/gantari-VF-italic.woff2` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/neon/assets/fonts/gantari-VF-italic.woff2
- `assets/fonts/gantari-VF-normal.woff2` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/neon/assets/fonts/gantari-VF-normal.woff2
- `assets/fonts/lexend-exa-VF-normal.woff2` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/neon/assets/fonts/lexend-exa-VF-normal.woff2
- `assets/fonts/lexend-mega-VF-normal.woff2` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/neon/assets/fonts/lexend-mega-VF-normal.woff2
- `assets/img/1ab5d7ea92.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/neon/assets/img/1ab5d7ea92.webp
- `assets/img/50c30bd7df.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/neon/assets/img/50c30bd7df.webp
- `assets/img/799dcf4932.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/neon/assets/img/799dcf4932.webp
- `assets/img/7b0aed9020-612186.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/neon/assets/img/7b0aed9020-612186.webp
- `assets/img/ddbf958085.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/neon/assets/img/ddbf958085.webp
- `assets/video/b65f61a5ac.jpg` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/neon/assets/video/b65f61a5ac.jpg
- `assets/video/b65f61a5ac.mp4` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/neon/assets/video/b65f61a5ac.mp4

## 14. Appendix: vector shapes
Each SVG fills 100% of its element's box (`position:absolute; inset:0; width:100%; height:100%; overflow:visible`, `preserveAspectRatio="none"`, `aria-hidden="true"`). If an element says «with fill #xxxxxx (instead of #yyyyyy)», use the same SVG changing that fill color. If an SVG with internal `id`s (masks, gradients) is used more than once, give each copy unique ids and update its `url(#…)` references.

**SVG-1**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 13 13"> <path d="M12.0754 0.670833V9.39167C12.0754 9.56958 12.0047 9.74021 11.8789 9.86602C11.7531 9.99182 11.5825 10.0625 11.4045 10.0625C11.2266 10.0625 11.056 9.99182 10.9302 9.86602C10.8044 9.74021 10.7337 9.56958 10.7337 9.39167V2.29006L1.14582 11.8788C1.01995 12.0047 0.849221 12.0754 0.671206 12.0754C0.493191 12.0754 0.322467 12.0047 0.196592 11.8788C0.0707161 11.7529 0 11.5822 0 11.4042C0 11.2262 0.0707161 11.0554 0.196592 10.9296L9.78532 1.34167H2.68371C2.50579 1.34167 2.33516 1.27099 2.20936 1.14518C2.08355 1.01938 2.01287 0.848749 2.01287 0.670833C2.01287 0.492917 2.08355 0.322288 2.20936 0.196483C2.33516 0.070677 2.50579 0 2.68371 0H11.4045C11.5825 0 11.7531 0.070677 11.8789 0.196483C12.0047 0.322288 12.0754 0.492917 12.0754 0.670833Z" fill="white"></path> </svg>
```

**SVG-2**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 13 13"> <path d="M12.0754 0.670833V9.39167C12.0754 9.56958 12.0047 9.74021 11.8789 9.86602C11.7531 9.99182 11.5825 10.0625 11.4045 10.0625C11.2266 10.0625 11.056 9.99182 10.9302 9.86602C10.8044 9.74021 10.7337 9.56958 10.7337 9.39167V2.29006L1.14582 11.8788C1.01995 12.0047 0.849221 12.0754 0.671206 12.0754C0.493191 12.0754 0.322467 12.0047 0.196592 11.8788C0.0707161 11.7529 0 11.5822 0 11.4042C0 11.2262 0.0707161 11.0554 0.196592 10.9296L9.78532 1.34167H2.68371C2.50579 1.34167 2.33516 1.27099 2.20936 1.14518C2.08355 1.01938 2.01287 0.848749 2.01287 0.670833C2.01287 0.492917 2.08355 0.322288 2.20936 0.196483C2.33516 0.070677 2.50579 0 2.68371 0H11.4045C11.5825 0 11.7531 0.070677 11.8789 0.196483C12.0047 0.322288 12.0754 0.492917 12.0754 0.670833Z" fill="#892EFF"></path> </svg>
```

**SVG-3**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 2 24"> <path d="M1 1V23" stroke="white" stroke-linecap="round" stroke-linejoin="round" stroke-width="2"></path> </svg>
```

**SVG-4**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 20 11"> <path d="M19 1L10 10L1 1" stroke="white" stroke-linecap="round" stroke-linejoin="round" stroke-width="2"></path> </svg>
```

## 15. Acceptance criteria
- At 1440×810, 1280×720 and 390×844 the composition is identical at the key moments: end of the intro, every pause and the final state.
- Positions, sizes, colors, fonts, timings and curves match this specification.
- All texts are real, editable HTML; all links are clickable `<a href="#">` with visible focus; invisible things cannot be clicked.
- Images, video and fonts load from the URLs in section 13 and there are no console errors.
- The layout adapts when the window is resized and follows section 11 on mobile.
- With `prefers-reduced-motion` the final state is shown directly.
