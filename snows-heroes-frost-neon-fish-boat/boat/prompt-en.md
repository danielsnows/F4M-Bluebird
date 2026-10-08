# Replication prompt — Born to Glide — Electric Speedboat

> Complete instructions for an AI coding agent. It contains everything needed to rebuild the animated hero «Born to Glide — Electric Speedboat» identically: structure, styles, texts, animation timings and curves, mobile view and links to the resources (images, video and fonts).

## Role and goal
Act as a senior front-end developer specialized in web animation. Build a single-screen page (hero) that exactly reproduces the animated design «Born to Glide — Electric Speedboat» described here, without adding or removing anything.

## 1. Overview
Electric speedboat hero («Redefine Ocean Speed / Born to Glide»), designed at 1440×810 with a marine look in deep blues and red. Full-bleed in the background, an aerial video of a red speedboat cutting a wake through the sea, driven by the scroll. The automatic intro (0 → 2.28 s) brings in the title «Redefine Ocean Speed», the text «A sculpted electric speedboat concept…», the «Reserve Now» and «Watch demo» buttons, the «Operation Efficiency 85%» glass card with the boat, the section numbers 01–05, the «Scroll down» circle and the top bar (logo, Home, Design, Performance, Technology, «Login» and menu). On scroll (8 → 14 s) that content moves up and four «Silent Power» cards with photos of the boat come out around the title «Born to Glide» and its text, while the active number changes to 02. In the last stretch (15.5 → 20 s) the «Dive into an extraordinary realm of marine» scene appears: the white line drawing of the boat, which grows, and the paragraph «Explore a remarkable world of marine design…». On hover, buttons and menu links subtly change tone and the «Silent Power» cards gently zoom their image.

## 2. Deliverable and technical rules
- A single `index.html` file with the CSS inside `<style>` and the JavaScript inside `<script>`. Plain (vanilla) JavaScript, no frameworks and no build step.
- The only external dependency: the Lenis library, exact version 1.3.26 (npm package `lenis`, file `dist/lenis.min.js`, which exposes the global class `Lenis`), for smooth scrolling. Load it with a `<script>` tag from a public npm CDN before your script (if it fails to load, everything must work with native scrolling).
- Images, video and fonts are ALWAYS loaded from the absolute URLs in section 13 «Resources». Do not download them, do not inline them as base64 and do not use any other resources.
- Every visible text is real, selectable and editable HTML text (never text turned into an image or into SVG paths). The vector shapes in the Appendix are only icons and decoration.
- Lists use `<ul>`/`<li>` (inside `<nav aria-label="…">` where indicated) and links use clickable `<a href="#">`, with `aria-label` where indicated.
- Head: `<html lang="en">`, `<meta charset="utf-8">`, `<meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover">`, `<title>Born to Glide — Electric Speedboat</title>`, `<meta name="description" content="A sculpted electric speedboat concept: silent power, smooth acceleration and a cinematic connection between driver and sea.">`, `<meta name="theme-color" content="#0d2236">`.
- Respect `prefers-reduced-motion: reduce`: the loader works the same, but there is no intro, no smooth scrolling and no snap; t stays fixed on the final state (t = 20 s) even when scrolling.
- Do not add elements, texts, effects or sections that are not in this specification.

## 3. Base structure (common HTML + CSS)
```html
<body>
<div class="loader" id="loader" aria-hidden="true">
  <div class="loader-in"><span>BORN TO GLIDE</span><div class="loader-bar"><i id="loaderBar"></i></div><span id="loaderPct">000</span></div>
</div>
<main class="hero" id="hero" aria-label="Born to Glide — Electric Speedboat">
  <div class="stage" id="stage">
    <!-- one layer (div.blk) per «Layer» in section 9, in the same order: later ones are on top -->
  </div>
</main>
<script>/* engine from section 6 */</script>
</body>
```
```css
:root{--bg:#0d2236;--ink:#ffffff;color-scheme:dark}
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
- **Monument Extended** (weight 400): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/boat/assets/fonts/MonumentExtended-Regular.woff2
- **PP Monument Extended** (weight 200): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/boat/assets/fonts/PPMonumentExtended-Light.woff2
- **PP Monument Extended** (weight 400): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/boat/assets/fonts/PPMonumentExtended-Regular.woff2
- **PP Monument Extended** (weight 800): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/boat/assets/fonts/PPMonumentExtended-Black.woff2
- **Quatro** (weight 400): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/boat/assets/fonts/Quatro-Regular.woff2
- **Quatro** (weight 500): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/boat/assets/fonts/Quatro-Medium.woff2

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
  - `E1` = cubic-bezier(1, -0.0005, 0, 1.0138)
  - `E2` = spring with mass=1, stiffness=80, damping=20, normalized to 1.25 s (see formula)
  - `E3` = cubic-bezier(0, 0, 0.58, 1)
  - `E4` = spring with mass=1, stiffness=80, damping=20, normalized to 2.083 s (see formula)
- `cubic-bezier(x1, y1, x2, y2)` curves work as in CSS: given the time progress p, solve the parameter u such that X(u) = p and return Y(u).

Spring formula with mass m, stiffness k, damping c and normalized duration D:
- ω0 = √(k/m), ζ = c / (2·√(k·m)).
- If ζ < 1: ωd = ω0·√(1−ζ²); x(τ) = 1 − e^(−ζ·ω0·τ)·(cos(ωd·τ) + (ζ·ω0/ωd)·sin(ωd·τ)).
- If ζ = 1: x(τ) = 1 − e^(−ω0·τ)·(1 + ω0·τ).
- If ζ > 1: q = √(ζ²−1), r1 = −ω0·(ζ−q), r2 = −ω0·(ζ+q); x(τ) = 1 + (r2·e^(r1·τ) − r1·e^(r2·τ)) / (r1 − r2).
- easing(p) = x(p·D) / x(D) for 0 < p < 1; 0 if p ≤ 0; 1 if p ≥ 1.

## 7. Playback (intro and scrolling)
- Total timeline duration: 20 s. Automatic intro: from t = 0 to t = 2.284 s. The rest (2.284 → 20 s) is driven by scrolling.
- `.hero` height = 100vh + track. track = round((20 − 2.284) × 40) = **709vh** on desktop and round((20 − 2.284) × 34) = **602vh** on mobile (use exactly these values; they are set through the CSS variable `--track`). `.stage` is sticky, so the stage stays fixed while the track is scrolled.
- While loading: `html.is-locked` (overflow hidden), `history.scrollRestoration = 'manual'`, `scrollTo(0,0)`.
- When the loader gets the `is-done` class, play the intro: t advances in real time (linearly) from 0 to 2.284 s in 2.284 s. During the intro scrolling is blocked (Lenis stopped; if Lenis is not available, keep `is-locked` until the intro ends) and scroll events do not change t. When it ends, t = 2.284 and scrolling is enabled.
- Smooth scrolling with Lenis: `new Lenis({lerp:0.085, wheelMultiplier:0.9, smoothWheel:true})`, `lenis.stop()` until the intro ends, then `lenis.start()`; call `lenis.raf(now)` in the requestAnimationFrame loop. Without Lenis, use native scrolling.
- Scroll → time mapping: p = clamp((scrollY − hero.offsetTop) / (hero.offsetHeight − stage.clientHeight), 0, 1); t = 2.284 + p × (20 − 2.284).
- Pauses (holds), in seconds: [2.284 – 3], [3 – 8], [14 – 15.5], [20 – 20]. Snap (Lenis only; no snap without Lenis): the direction is the sign of the last scroll change; 170 ms after the last scroll event (every event restarts the timer), if t is not inside a pause (with a ±0.02 s margin), scroll with `lenis.scrollTo` to the start of the next pause when scrolling down, or to the end of the previous pause when scrolling up. Duration = clamp(|Δt| × 0.45, 0.6, 1.8) s; easing easeInOutCubic (x < 0.5 ? 4x³ : 1 − (−2x+2)³/2). No snap with `prefers-reduced-motion` or during another snap (the «snapping» state is released in `onComplete` or, as a safety net, after 2.2 s).
- On every resize: recompute layers, track and t from the current scroll position.
- Video («scrub» mode, driven by the timeline): `<video muted playsinline preload="auto">` with its poster; it never plays by itself. For reliable seeking, download the MP4 with `fetch` as a Blob (reporting progress to the loader) and set `video.src = URL.createObjectURL(blob)` (if that fails, use the direct URL). On every frame, if `readyState ≥ 2` and it is not `seeking`: target = min(max(0, t), duration − 0.04); if |currentTime − target| > 0.012, `currentTime = target`.

## 8. Loader and preloading
- The loader covers the screen with the `--bg` color and shows «BORN TO GLIDE», a 160×1 px bar and a 3-digit percentage (`000` → `100`) in the `--ink` color.
- Weighted progress: fonts (`document.fonts.ready`) weight 1; each `<img>` element on the stage weight 0.4, even if it repeats a file (it counts once it has loaded —or failed— and `img.decode()` has finished; the video poster does not count); the video weight 6 (its fetch download progress). Percentage = Math.round(progress × 100) with 3 digits. The bar uses `transform: scaleX(progress)` and never goes backwards.
- Wait until everything finishes (14 s maximum; if it runs out, continue without cancelling anything); then set the video `src` (the Blob URL if already downloaded, otherwise the direct URL) and wait for its `loadeddata` or `error` (3 s maximum). Then remove `is-locked` from `<html>` (except in the no-Lenis case described in section 7), add `is-done` to the loader (it fades out in 0.9 s) and start the intro.

## 9. Layers and elements
Notation of each line: `[eN]` = suggested identifier; `box` = div; `text` = div with text (the content in quotes, keeping capitalization, line breaks and spaces); `group` = boxless container; `vector` = box containing an inline SVG; `<a href="#">`, `<ul>`, `<li>`, `<nav>` = semantic tag to use; `«name»` = layer name in the design (for reference only, also handy as `data-name`); `pos(x,y)` = left/top; `size A×B` = width×height; after `|` come the literal CSS styles. Every `text` element gets `class="t"` (used by the pointer-events rule in section 3); boxes, groups and vectors may use `class="b"`, `"g"` and `"v"`. Decorative layers (border, glass, inner shadow, background blur, fill layer) are absolute elements (for example `<i>`) covering their container (given `inset`) with inline `pointer-events:none` and `border-radius:inherit` unless stated otherwise. Images go inside an absolute container `inset:0; overflow:hidden; border-radius:inherit`. The order of the lines is the stacking order (later is on top).

#### Layer bk0 «Fondo» — BLEED LAYER (cover), design box x=0 y=0 1440×810
- [e1] box «home» pos(0,0) size 1440×810 | background-color:#000000

#### Layer bk1 «Cena_inicial_-_2026-07-07_202607070207 1» — BLEED LAYER (cover), design box x=0 y=0 1440×810
- [e2] box «Cena_inicial_-_2026-07-07_202607070207 1» transform translate(1440,0) rotate(3.14rad) scale(1,-1) size 1440×810
  - video: `<video muted playsinline preload="auto" poster="https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/boat/assets/video/7f91265fa8.jpg" data-src="https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/boat/assets/video/7f91265fa8.mp4">` with position:absolute; inset:0; width:100%; height:100%; object-fit:cover; display:block (the `src` is assigned from JavaScript once preloading finishes, see sections 7 and 8)

#### Layer bk2 «Music Button» — CONTAINED LAYER (contain, anchor x=-1, y=1), design box x=5.36 y=757.81 53.93×52.19
- [e3] box <a href="#"> aria-label="Music" «Music Button» pos(5.36,757.81) size 53.93×52.19 | background-color:rgba(74,92,115,0.01)
  - background blur (backdrop-filter): border-radius:inherit; backdrop-filter:blur(50px); inset:-5px; mask:linear-gradient(90deg,transparent,#000 10px,#000 calc(100% - 10px),transparent),linear-gradient(180deg,transparent,#000 10px,#000 calc(100% - 10px),transparent); mask-composite:intersect

#### Layer bk3 «Rectangle 23» — BLEED LAYER (cover), design box x=0 y=0.02 1440×144
- [e4] box «Rectangle 23» pos(0,0.01) size 1440×144 | background:linear-gradient(180deg, rgba(3,36,51,0.4) 0%, rgba(3,36,51,0) 100%)
  - background blur (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(10px); mask:linear-gradient(180deg, rgba(0,0,0,1), rgba(0,0,0,0))

#### Layer bk4 «Component 1» — CONTAINED LAYER (contain, anchor x=0, y=-1), design box x=28.99 y=27.27 1387.01×24.01
- [e5] box «Component 1» pos(28.99,27.27) size 1387.01×24.01
  - [e6] box <a href="#"> aria-label="Home" «image 75» pos(-102.99,-2.27) size 30×26
      ↳ animation: x: 0.001→1.251s -102.99→-2.99 (E2)
    - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/boat/assets/img/bad46cb175.webp: absolutely positioned `<img alt="">` at (0,0), 2048×2048 px, max-width:none, transform-origin 0 0 and transform matrix(0.0222,0,0,0.0219,-7.742,-9.89); the container clips it (overflow hidden, same border-radius)
  - [e7] box «Frame 5» pos(341.01,7) size 704×10
    - [-] list <nav> aria-label="Main navigation"
      - [-] list <ul>
        - (list of 4 <li> elements with the SAME inner structure; the first one is fully detailed as the template and the others only state what changes)
        - [e8] text <li> pos(330,0) size 45×10 | opacity:0; white-space:pre; text-align:left; font-family:'PP Monument Extended'; font-weight:400; font-size:11px; line-height:0.93; letter-spacing:-0.03em; text-transform:uppercase; color:#ffffff | TEXT: "" | inline link: the text is the content of an <a href="#"> inside the element
            ↳ animation: x: 0.001→1.251s 330→0 (E2); opacity: 0.001→1.251s 0→1 (E2)
        - [e9] <li> card same as the template | position/rotation: see table; size 55×10 | texts: "Design"
        - [e10] <li> card same as the template | position/rotation: see table; size 114×10 | texts: "Performance"
        - [e11] <li> card same as the template | position/rotation: see table; size 103×10 | texts: "Technology"
          (The <li> cards above: each card box animates according to the following table.)

Card animation table (x). Shared segments: T1 = 0.001→1.251s (E2). Each cell is the value at the END of that segment (before the first segment it equals «start»; between segments it holds; if a property does not change in a segment, the value is repeated):

| card | property | start | T1 |
|---|---|---|---|
| e8 | x | 330 | 330 |
| e9 | x | 325 | 325 |
| e10 | x | 295 | 295 |
| e11 | x | 301 | 301 |

The other animated card properties (opacity) animate EXACTLY like the template.
  - [e12] group <a href="#"> aria-label="Open menu" «Group 8»
    - [e13] box «Line 9» transform translate(1605.00,8.71) rotate(3.14rad) size 32.07×1 | background-color:#ffffff
        ↳ animation: x: 0.001→1.251s 1605.01→1387.01 (E2)
    - [e14] box «Line 10» transform translate(1605.00,16.29) rotate(3.14rad) size 32.07×1 | background-color:#ffffff
        ↳ animation: x: 0.001→1.251s 1605.01→1387.01 (E2)
  - [e15] text <a href="#"> pos(1446.18,7) size 45×10 | white-space:pre; text-align:left; font-family:'PP Monument Extended'; font-weight:400; font-size:11px; line-height:0.93; letter-spacing:-0.03em; text-transform:uppercase; color:#ffffff | TEXT: "Login"
      ↳ animation: x: 0.001→1.251s 1446.18→1278.18 (E2)

#### Layer bk5 «Group 53» — CONTAINED LAYER (contain, anchor x=0, y=0), design box x=24 y=186.02 1392×592
- [e16] group «Group 53»
    ↳ animation: opacity: 15.5→20s 1→0 (E1)
  - [e17] box «Redefine  Ocean  Speed - Animation ▶» pos(24,186.01) size 506×219
      ↳ animation: opacity: jump from 1 to 0 at 3s
    - [e20] group «Redefine »
        ↳ animation: opacity: jump from 1 to 0 at 3s
      - [e18] box «Redefine » pos(0,0) size 506×219
        - [e19] text pos(0,39) size 506×219 | opacity:0; white-space:pre; text-align:left; font-family:'Monument Extended'; font-weight:400; font-size:78px; line-height:0.93 | TEXT in runs: "Redefine 
" {font-family:'Monument Extended'; font-weight:400; font-size:78px; line-height:0.93; letter-spacing:-0.03em; text-transform:uppercase; color:#ffffff} + "Ocean 
Speed" {font-family:'Monument Extended'; font-weight:400; font-size:78px; line-height:0.93; letter-spacing:-0.03em; text-transform:uppercase; color:transparent}
            ↳ animation: y: 0.8→1.1s 39→0 (E3); opacity: 0.8→1.1s 0→1 (E3)
    - [e23] group «Ocean »
        ↳ animation: opacity: jump from 1 to 0 at 3s
      - [e21] box «Ocean » pos(0,0) size 506×219
        - [e22] text pos(0,39) size 506×219 | opacity:0; white-space:pre; text-align:left; font-family:'Monument Extended'; font-weight:400; font-size:78px; line-height:0.93 | TEXT in runs: "Redefine 
" {font-family:'Monument Extended'; font-weight:400; font-size:78px; line-height:0.93; letter-spacing:-0.03em; text-transform:uppercase; color:transparent} + "Ocean 
" {font-family:'Monument Extended'; font-weight:400; font-size:78px; line-height:0.93; letter-spacing:-0.03em; text-transform:uppercase; color:#ffffff} + "Speed" {font-family:'Monument Extended'; font-weight:400; font-size:78px; line-height:0.93; letter-spacing:-0.03em; text-transform:uppercase; color:transparent}
            ↳ animation: y: 0.075→0.375s 39→0 (E3); opacity: 0.075→0.375s 0→1 (E3)
    - [e26] group «Speed»
        ↳ animation: opacity: jump from 1 to 0 at 3s
      - [e24] box «Speed» pos(0,0) size 506×219
        - [e25] text pos(0,39) size 506×219 | opacity:0; white-space:pre; text-align:left; font-family:'Monument Extended'; font-weight:400; font-size:78px; line-height:0.93 | TEXT in runs: "Redefine 
Ocean 
" {font-family:'Monument Extended'; font-weight:400; font-size:78px; line-height:0.93; letter-spacing:-0.03em; text-transform:uppercase; color:transparent} + "Speed" {font-family:'Monument Extended'; font-weight:400; font-size:78px; line-height:0.93; letter-spacing:-0.03em; text-transform:uppercase; color:#ffffff}
            ↳ animation: y: 0.15→0.45s 39→0 (E3); opacity: 0.15→0.45s 0→1 (E3)
  - [e27] box «A sculpted electric speedboat ... - Animation ▶» pos(24,433) size 382.4×66
      ↳ animation: opacity: jump from 1 to 0 at 3s
    - [e30] group «A sculpted electric speedboat concept »
        ↳ animation: opacity: jump from 1 to 0 at 3s
      - [e28] box «A sculpted electric speedboat concept » pos(0,0) size 382.4×66
        - [e29] text pos(0,9) size 382.4×66 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Quatro'; font-weight:400; font-size:18px; line-height:1.2 | TEXT in runs: "A sculpted electric speedboat concept 
" {font-family:'Quatro'; font-weight:400; font-size:18px; line-height:1.2; color:#ffffff} + "designed for precision, elegance, and 
effortless high-performance ocean travel." {font-family:'Quatro'; font-weight:400; font-size:18px; line-height:1.2; color:transparent}
            ↳ animation: y: 0.2→0.5s 9→0 (E3); opacity: 0.2→0.5s 0→1 (E3)
    - [e33] group «designed for precision, elegance, and »
        ↳ animation: opacity: jump from 1 to 0 at 3s
      - [e31] box «designed for precision, elegance, and » pos(0,0) size 382.4×66
        - [e32] text pos(0,9) size 382.4×66 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Quatro'; font-weight:400; font-size:18px; line-height:1.2 | TEXT in runs: "A sculpted electric speedboat concept 
" {font-family:'Quatro'; font-weight:400; font-size:18px; line-height:1.2; color:transparent} + "designed for precision, elegance, and 
" {font-family:'Quatro'; font-weight:400; font-size:18px; line-height:1.2; color:#ffffff} + "effortless high-performance ocean travel." {font-family:'Quatro'; font-weight:400; font-size:18px; line-height:1.2; color:transparent}
            ↳ animation: y: 0.275→0.575s 9→0 (E3); opacity: 0.275→0.575s 0→1 (E3)
    - [e36] group «effortless high-performance ocean travel.»
        ↳ animation: opacity: jump from 1 to 0 at 3s
      - [e34] box «effortless high-performance ocean travel.» pos(0,0) size 382.4×66
        - [e35] text pos(0,9) size 382.4×66 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Quatro'; font-weight:400; font-size:18px; line-height:1.2 | TEXT in runs: "A sculpted electric speedboat concept 
designed for precision, elegance, and 
" {font-family:'Quatro'; font-weight:400; font-size:18px; line-height:1.2; color:transparent} + "effortless high-performance ocean travel." {font-family:'Quatro'; font-weight:400; font-size:18px; line-height:1.2; color:#ffffff}
            ↳ animation: y: 0.35→0.65s 9→0 (E3); opacity: 0.35→0.65s 0→1 (E3)
  - [e45] group «Component 2»
      ↳ animation: offsetY: 8→14s 0→-880 (E1); opacity: 15.5→20s 1→0 (E1)
    - [e37] box «Component 2» pos(24,561.01) size 404×66
      - [e38] group «Group 52» | opacity:0
          ↳ animation: opacity: 0.3→1.55s 0→1 (E2)
        - [e39] box <a href="#"> «Frame 1171275731» pos(0,20) size 179×66 | border-radius:99px; background-color:#ffffff
            ↳ animation: y: 0.3→1.55s 20→0 (E2)
          - [e40] text pos(32,38) size 115×10 | white-space:pre; text-align:left; font-family:'PP Monument Extended'; font-weight:800; font-size:11px; line-height:0.93; letter-spacing:-0.02em; text-transform:uppercase; color:#000000 | TEXT: "Reserve Now"
              ↳ animation: y: 0.3→1.55s 38→28 (E2)
        - [e41] box <a href="#"> «Frame 1597882326» pos(199,70) size 205×66 | border-radius:99px
            ↳ animation: y: 0.3→1.55s 70→0 (E2)
          - [e42] text pos(20,38) size 108×10 | white-space:pre; text-align:left; font-family:'PP Monument Extended'; font-weight:800; font-size:11px; line-height:0.93; letter-spacing:-0.02em; text-transform:uppercase; color:#ffffff | TEXT: "Watch demo"
              ↳ animation: y: 0.3→1.55s 38→28 (E2)
          - [e43] box «Frame 1597882323» pos(148,50) size 46×46 | border-radius:99px; background-color:rgba(255,255,255,0.14)
              ↳ animation: y: 0.3→1.55s 50→10 (E2)
            - [e44] vector «Polygon 1» pos(18.2,16.15) size 12.4×13.71
              - vector drawing SVG-1 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
          - border (stroke): inset:0px; border-radius:99px; border:1px solid #ffffff
  - [e57] group «Component 4»
      ↳ animation: offsetY: 8→14s 0→-910 (E1); opacity: 15.5→20s 1→0 (E1)
    - [e46] box «Component 4» pos(957,489.01) size 459×289 | border-radius:60px; overflow:hidden
      - [e47] box «Frame 1597882326» pos(160,0) size 459×289 | opacity:0; border-radius:60px; overflow:hidden; background-color:rgba(255,255,255,0.13)
          ↳ animation: x: 0.2→2.284s 160→0 (E4); opacity: 0.2→2.284s 0→1 (E4)
        - background blur (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(20px)
        - [e48] box «image 71» pos(250.33,34.35) size 478.33×360.5
            ↳ animation: x: 0.2→2.284s 250.33→-9.67 (E4)
          - fill layer (covers the whole box, stacked in this order): mix-blend-mode:multiply
          - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/boat/assets/img/4db459584e-a.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)
          - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/boat/assets/img/b6c45d7061.webp: absolutely positioned `<img alt="">` at (0,0), 1448×1086 px, max-width:none, transform-origin 0 0 and transform matrix(0.3303,0,0,0.3303,0,1.751); the container clips it (overflow hidden, same border-radius)
        - [e49] text pos(162.63,43.51) size 113×52 | white-space:pre; text-align:left; font-family:'Quatro'; font-weight:400; font-size:20px; line-height:1.3; letter-spacing:-0.02em; text-transform:uppercase; color:#ffffff | TEXT: "Operation 
Efficiency"
            ↳ animation: x: 0.2→2.284s 162.63→32.63 (E4)
        - [e50] group «Group 4»
          - [e51] text pos(427.85,48.67) size 68×52 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'PP Monument Extended'; font-weight:200; font-size:73.48px; line-height:1.2; letter-spacing:-0.03em; color:#ffffff | TEXT: "8"
              ↳ animation: x: 0.2→2.284s 427.85→267.85 (E4)
          - [e52] box «Frame 6» pos(490.45,43.51) size 52.25×31 | overflow:hidden
              ↳ animation: x: 0.2→2.284s 490.46→330.46 (E4)
            - [e53] text pos(-6.15,5.17) size 66×52 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'PP Monument Extended'; font-weight:200; font-size:73.48px; line-height:1.2; letter-spacing:-0.03em; color:#ffffff | TEXT: "5"
          - [e54] text pos(542.42,48.38) size 48×26 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'PP Monument Extended'; font-weight:200; font-size:36.74px; line-height:1.2; letter-spacing:-0.03em; color:#ffffff | TEXT: "%"
              ↳ animation: x: 0.2→2.284s 542.42→382.42 (E4)
        - [e55] box <a href="#"> aria-label="Play video" «Frame 1597882323» pos(527,208) size 66×66 | border-radius:99px; background-color:rgba(255,255,255,0.14)
            ↳ animation: x: 0.2→2.284s 527→377 (E4)
          - background blur (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(20px)
          - [e56] vector «Polygon 1» pos(28.2,26.15) size 12.4×13.71
            - vector drawing SVG-2 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
  - [e58] text pos(24,186.01) size 506×219 | opacity:0; white-space:pre; text-align:left; font-family:'Monument Extended'; font-weight:400; font-size:78px; line-height:0.93; letter-spacing:-0.03em; text-transform:uppercase; color:#ffffff | TEXT: "Redefine 
Ocean 
Speed"
      ↳ animation: y: 8→14s 186.02→-773.98 (E1); opacity: jump from 0 to 1 at 3s; 15.5→20s 1→0 (E1)
  - [e59] text pos(24,433) size 382.4×66 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Quatro'; font-weight:400; font-size:18px; line-height:1.2; color:#ffffff | TEXT: "A sculpted electric speedboat concept designed for precision, elegance, and effortless high-performance ocean travel."
      ↳ animation: y: 8→14s 433→-467 (E1); opacity: jump from 0 to 1 at 3s; 15.5→20s 1→0 (E1)

#### Layer bk6 «Component 5» — CONTAINED LAYER (contain, anchor x=1, y=0), design box x=1363 y=180.52 53×272
- [e66] group «Component 5»
    ↳ animation: offsetX: 15.5→20s 0→90 (E1); offsetY: 8→14s 0→270 (E1)
  - [e60] box «Component 5» pos(1363,180.52) size 53×272
    - [e61] text transform translate(75,0) rotate(0rad) size 38×16 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'PP Monument Extended'; font-weight:400; font-size:22px; line-height:1.4; color:#ffffff | TEXT: "01"
        ↳ animation: x: 0.2→2.284s 75→15 (E4); width: 8→14s 38→37.4 (E1); 15.5→20s 37.4→38 (E1); height: 8→14s 16→15.4 (E1); 15.5→20s 15.4→16 (E1); opacity: 8→14s 1→0.8 (E1); 15.5→20s 0.8→1 (E1); content-scale: 8→14s 1→0.91 (E1); 15.5→20s 0.91→1 (E1)
    - [e62] text transform translate(97,66) rotate(0rad) size 36×14 | opacity:0.8; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'PP Monument Extended'; font-weight:400; font-size:20px; line-height:1.4; color:#ffffff | TEXT: "02"
        ↳ animation: x: 0.2→2.284s 97→17 (E4); width: 8→14s 36→35.45 (E1); 15.5→20s 35.45→36 (E1); height: 8→14s 14→14.55 (E1); 15.5→20s 14.55→14 (E1); opacity: 8→14s 0.8→1 (E1); 15.5→20s 1→0.8 (E1); content-scale: 8→14s 1→1.1 (E1); 15.5→20s 1.1→1 (E1)
    - [e63] text pos(127,130) size 36×14 | opacity:0.6; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'PP Monument Extended'; font-weight:400; font-size:20px; line-height:1.4; color:#ffffff | TEXT: "03"
        ↳ animation: x: 0.2→2.284s 127→17 (E4)
    - [e64] text pos(155,194) size 38×14 | opacity:0.4; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'PP Monument Extended'; font-weight:400; font-size:20px; line-height:1.4; color:#ffffff | TEXT: "04"
        ↳ animation: x: 0.2→2.284s 155→15 (E4)
    - [e65] text pos(197,258) size 36×14 | opacity:0.2; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'PP Monument Extended'; font-weight:400; font-size:20px; line-height:1.4; color:#ffffff | TEXT: "05"
        ↳ animation: x: 0.2→2.284s 197→17 (E4)

#### Layer bk7 «Component 3» — CONTAINED LAYER (contain, anchor x=0, y=1), design box x=582.19 y=663.74 275.63×275.63
- [e77] group «Component 3»
    ↳ animation: offsetY: 8→14s 0→150 (E1)
  - [e67] box «Component 3» pos(582.19,663.74) size 275.63×275.63
    - [e68] group «scroll» | opacity:0
        ↳ animation: opacity: 0.4→1.65s 0→1 (E2)
      - [e69] box «Ellipse 17» pos(96.81,97) size 82×82 | opacity:0.1; border-radius:50%
          ↳ animation: x: 0.4→1.65s 96.81→0 (E2); y: 0.4→1.65s 97→0 (E2); width: 0.4→1.65s 82→275.63 (E2); height: 0.4→1.65s 82→275.63 (E2)
        - border (stroke): inset:0px; border-radius:50%; border:1px solid #ffffff
      - [e70] box «Ellipse 19» pos(96.81,95.81) size 82×83 | opacity:0.2; border-radius:50%
          ↳ animation: x: 0.4→1.65s 96.81→28.24 (E2); y: 0.4→1.65s 95.81→28.24 (E2); width: 0.4→1.65s 82→219.15 (E2); height: 0.4→1.65s 83→219.15 (E2)
        - border (stroke): inset:0px; border-radius:50%; border:1px solid #ffffff
      - [e71] box «Ellipse 18» pos(96.81,97) size 82×82 | opacity:0.2; border-radius:50%
          ↳ animation: x: 0.4→1.65s 96.81→53.72 (E2); y: 0.4→1.65s 97→53.72 (E2); width: 0.4→1.65s 82→168.19 (E2); height: 0.4→1.65s 82→168.19 (E2)
        - border (stroke): inset:0px; border-radius:50%; border:1px solid #ffffff
      - [e72] vector «SCROLL DOWN» transform matrix(-1,0,0,-1,207.31,207.391) size 138.94×66.4 | opacity:0.4
          ↳ animation: rotation(°): 0.4→1.65s 0→180 (E2) [rotation around point [69.5,69.58] of the box]
        - vector drawing SVG-3 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e73] box «ArrowDown» transform translate(148.31,171) rotate(3.14rad) size 20×20
          ↳ animation: x: 0.4→1.65s 148.31→128.31 (E2); y: 0.4→1.65s 171→110.27 (E2); rotation(rad): 0.4→1.65s 3.14→0 (E2)
        - [e75] vector «Vector» pos(9,2.12) size 2×15.75
          - vector drawing SVG-4 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
        - [e76] vector «Vector» pos(3.38,10.25) size 13.25×7.62
          - vector drawing SVG-5 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box

#### Layer bk8 «Group 55» — BLEED LAYER (cover), design box x=0 y=0 2266×1334
- [e78] group «Group 55» | opacity:0
    ↳ animation: opacity: 8→14s 0→1 (E1)
  - [e79] vector «Star 1» pos(0,454) size 1151×356 | opacity:0; filter:blur(200px)
      ↳ animation: y: 15.5→20s 454→-256 (E1); opacity: 8→14s 0→1 (E1)
    - vector drawing SVG-6 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
  - [e80] box «Rectangle 24» transform translate(0,1520) rotate(0rad) scale(1,-1) size 1440×345 | opacity:0; background:linear-gradient(180deg, rgba(3,36,51,0.4) 0%, rgba(3,36,51,0) 100%)
      ↳ animation: y: 15.5→20s 1520→810 (E1); opacity: 8→14s 0→1 (E1)
    - background blur (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(35.1px); mask:linear-gradient(180deg, rgba(0,0,0,1), rgba(0,0,0,0))
  - [e81] box «Music Button» pos(5.36,1467.79) size 53.93×52.19 | opacity:0; background-color:rgba(74,92,115,0.01)
      ↳ animation: y: 15.5→20s 1467.79→757.79 (E1); opacity: 8→14s 0→1 (E1)
    - background blur (backdrop-filter): border-radius:inherit; backdrop-filter:blur(50px); inset:-5px; mask:linear-gradient(90deg,transparent,#000 10px,#000 calc(100% - 10px),transparent),linear-gradient(180deg,transparent,#000 10px,#000 calc(100% - 10px),transparent); mask-composite:intersect
  - [e82] text pos(838,1180) size 379×148 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'PP Monument Extended'; font-weight:800; font-size:40px; line-height:0.93; letter-spacing:-0.03em; text-transform:uppercase; color:#ffffff | TEXT: "Dive into an extraordinary realm of marine "
      ↳ animation: x: 15.5→20s 838→608 (E1); y: 15.5→20s 1180→470 (E1); opacity: 8→14s 0→1 (E1)
  - [e83] text pos(1404,1186) size 393×242 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Quatro'; font-weight:400; font-size:18px; line-height:1.2; color:#ffffff | TEXT: "Explore a remarkable world of marine design that combines stunning aesthetics with top-notch coastal performance. Picture a vessel that not only impresses with its sleek lines and innovative structure but also excels in tough waters. This is where art meets engineering, setting a new standard for marine experiences. Whether you're cruising the coast or facing rough seas, our designs promise to enhance your journey, prioritizing both style and functionality."
      ↳ animation: x: 15.5→20s 1404→1023 (E1); y: 15.5→20s 1186→476 (E1); opacity: 8→14s 0→1 (E1)
  - [e84] box «image 74» transform translate(104.33,740) rotate(3.14rad) scale(1,-1) size 529.33×397 | opacity:0; mix-blend-mode:screen
      ↳ animation: x: 15.5→20s 104.33→643.33 (E1); y: 15.5→20s 740→273 (E1); width: 15.5→20s 529.33→777.33 (E1); height: 15.5→20s 397→583 (E1); opacity: 8→14s 0→1 (E1)
    - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/boat/assets/img/8b8f2b5aee-f28e0c-k.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)

#### Layer bk9 «Group 54» — BLEED LAYER (cover), design box x=0 y=0 1388.34×708.5
- [e85] group «Group 54» | opacity:0
    ↳ animation: opacity: jump from 0 to 1 at 3s
  - [e86] text pos(395,1690.5) size 650×68 | opacity:0; white-space:pre-wrap; text-align:center; font-family:'Quatro'; font-weight:400; font-size:28px; line-height:1.2; color:#ffffff | TEXT: "Built for bold escapes, smooth acceleration, and a cinematic connection between driver and sea."
      ↳ animation: y: 8→14s 1690.5→670.5 (E1); 15.5→20s 670.5→-139.5 (E1); opacity: jump from 0 to 1 at 3s
  - [e87] text pos(431,1387) size 578×146 | opacity:0; white-space:pre-wrap; text-align:center; font-family:'PP Monument Extended'; font-weight:800; font-size:78px; line-height:0.93; letter-spacing:-0.03em; text-transform:uppercase; color:#ffffff | TEXT: "Born To Glide"
      ↳ animation: y: 8→14s 1387→507 (E1); 15.5→20s 507→-413 (E1); opacity: jump from 0 to 1 at 3s
  - [e88] box «Frame 1597882325» pos(25.83,1160) size 255×215 | opacity:0; border-radius:60px; overflow:hidden; background-color:rgba(24,64,79,0.17)
      ↳ animation: y: 8→14s 1160→105.48 (E1); 15.5→20s 105.48→-1114.52 (E1); opacity: jump from 0 to 1 at 3s
    - background blur (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(20px)
    - [e89] box «image 70» transform translate(6.60,4.89) rotate(0.11rad) size 304.25×228.19 | opacity:0
        ↳ animation: opacity: jump from 0 to 1 at 3s
      - fill layer (covers the whole box, stacked in this order): mix-blend-mode:multiply
      - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/boat/assets/img/0dd95ba758-a.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)
      - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/boat/assets/img/6dd84d67ee.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)
    - [e90] text pos(22.63,36.41) size 141.37×52 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Quatro'; font-weight:400; font-size:20px; line-height:1.3; letter-spacing:-0.02em; text-transform:uppercase; color:#ffffff | TEXT: "Silent 
Power"
        ↳ animation: opacity: jump from 0 to 1 at 3s
    - [e91] box <a href="#"> aria-label="Play video" «Frame 1597882323» pos(174,16.12) size 66×66 | opacity:0; border-radius:99px; background-color:rgba(255,255,255,0.14)
        ↳ animation: opacity: jump from 0 to 1 at 3s
      - background blur (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(20px)
      - [e92] vector «Polygon 1» pos(28.2,26.15) size 12.4×13.71 | opacity:0
          ↳ animation: opacity: jump from 0 to 1 at 3s
        - vector drawing SVG-7 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
  - [e93] box «Frame 1597882330» pos(1159.17,1050) size 255×215 | opacity:0; border-radius:60px; overflow:hidden; background-color:rgba(24,64,79,0.17)
      ↳ animation: y: 8→14s 1050→105.48 (E1); 15.5→20s 105.48→-1114.52 (E1); opacity: jump from 0 to 1 at 3s
    - background blur (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(20px)
    - [e94] box «image 72» pos(-129.72,30) size 446.07×290.49 | opacity:0
        ↳ animation: opacity: jump from 0 to 1 at 3s
      - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/boat/assets/img/4aea2d8a92.webp: absolutely positioned `<img alt="">` at (0,0), 2016×1313 px, max-width:none, transform-origin 0 0 and transform matrix(0.2212,0,0,0.2212,0.085,0.023); the container clips it (overflow hidden, same border-radius)
    - [e95] text pos(22.63,36.41) size 141.37×52 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Quatro'; font-weight:400; font-size:20px; line-height:1.3; letter-spacing:-0.02em; text-transform:uppercase; color:#ffffff | TEXT: "Silent 
Power"
        ↳ animation: opacity: jump from 0 to 1 at 3s
    - [e96] box <a href="#"> aria-label="Play video" «Frame 1597882323» pos(174,16.12) size 66×66 | opacity:0; border-radius:99px; background-color:rgba(255,255,255,0.14)
        ↳ animation: opacity: jump from 0 to 1 at 3s
      - background blur (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(20px)
      - [e97] vector «Polygon 1» pos(28.2,26.15) size 12.4×13.71 | opacity:0
          ↳ animation: opacity: jump from 0 to 1 at 3s
        - vector drawing SVG-8 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
  - [e98] box «Frame 1597882325» pos(141.81,1480) size 255×215 | opacity:0; border-radius:60px; overflow:hidden; background-color:rgba(24,64,79,0.17)
      ↳ animation: y: 8→14s 1480→370 (E1); 15.5→20s 370→-660 (E1); opacity: jump from 0 to 1 at 3s
    - background blur (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(20px)
    - [e99] text pos(22.63,36.41) size 141.37×52 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Quatro'; font-weight:400; font-size:20px; line-height:1.3; letter-spacing:-0.02em; text-transform:uppercase; color:#ffffff | TEXT: "Silent 
Power"
        ↳ animation: opacity: jump from 0 to 1 at 3s
    - [e100] box <a href="#"> aria-label="Play video" «Frame 1597882323» pos(174,16.12) size 66×66 | opacity:0; border-radius:99px; background-color:rgba(255,255,255,0.14)
        ↳ animation: opacity: jump from 0 to 1 at 3s
      - background blur (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(20px)
      - [e101] vector «Polygon 1» pos(28.2,26.15) size 12.4×13.71 | opacity:0
          ↳ animation: opacity: jump from 0 to 1 at 3s
        - vector drawing SVG-1 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
    - [e102] box «image 70» transform translate(364.87,35.69) rotate(1.52rad) size 242.04×506.58 | opacity:0
        ↳ animation: opacity: jump from 0 to 1 at 3s
      - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/boat/assets/img/7bb2853745.webp: absolutely positioned `<img alt="">` at (0,0), 2880×1768 px, max-width:none, transform-origin 0 0 and transform matrix(0.5184,0,0,0.5185,-625.512,-205.107); the container clips it (overflow hidden, same border-radius)
  - [e103] box «Frame 1597882330» pos(1043.18,1370) size 255×215 | opacity:0; border-radius:60px; overflow:hidden; background-color:rgba(24,64,79,0.17)
      ↳ animation: y: 8→14s 1370→370 (E1); 15.5→20s 370→-660 (E1); opacity: jump from 0 to 1 at 3s
    - background blur (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(20px)
    - [e104] box «image 73» pos(0,79.6) size 254.93×191.2 | opacity:0
        ↳ animation: opacity: jump from 0 to 1 at 3s
      - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/boat/assets/img/f41bd0e718.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)
    - [e105] text pos(22.63,36.41) size 141.37×52 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Quatro'; font-weight:400; font-size:20px; line-height:1.3; letter-spacing:-0.02em; text-transform:uppercase; color:#ffffff | TEXT: "Silent 
Power"
        ↳ animation: opacity: jump from 0 to 1 at 3s
    - [e106] box <a href="#"> aria-label="Play video" «Frame 1597882323» pos(174,16.12) size 66×66 | opacity:0; border-radius:99px; background-color:rgba(255,255,255,0.14)
        ↳ animation: opacity: jump from 0 to 1 at 3s
      - background blur (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(20px)
      - [e107] vector «Polygon 1» pos(28.2,26.15) size 12.4×13.71 | opacity:0
          ↳ animation: opacity: jump from 0 to 1 at 3s
        - vector drawing SVG-9 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box

## 10. Final adjustments
### Hover states (only when `matchMedia("(hover: hover) and (pointer: fine)")` matches; no hover on touch)
- Menu and text links (subtle colour change): [e8] «Home», [e9] «Design», [e10] «Performance», [e11] «Technology», [e15] «Login». On pointer enter or focus, for the link and each of its descendants read its computed colour c (skip it if its alpha is 0, e.g. gradient text) and store the variable `--hvc` = rgb(c + (#ff5a4f − c) × 0.45) per channel (alpha 1); add the class `hv-tr` and, on the next frame, `is-hv`. On leave (or blur) remove `is-hv` and, 420 ms later, `hv-tr`.
- Buttons (subtle background change): [e3] «Music» → layer in the link itself with background rgba(255,255,255,.16); [e12] «Open menu» → layer in its child box with a visible surface that is largest on screen with background rgba(255,90,79,.12); [e39] «Reserve Now» → layer in the link itself with background rgba(255,90,79,.12); [e41] «Watch demo» → layer in the link itself with background rgba(255,255,255,.16); [e55] «Play video» → layer in the link itself with background rgba(255,255,255,.16); [e91] «Play video» → layer in the link itself with background rgba(255,255,255,.16); [e96] «Play video» → layer in the link itself with background rgba(255,255,255,.16); [e100] «Play video» → layer in the link itself with background rgba(255,255,255,.16); [e106] «Play video» → layer in the link itself with background rgba(255,255,255,.16). In each one insert `<i class="hvo" aria-hidden="true">` inside the given box, right after its fill/effect layers (`i.pl`, `i.bb`, `i.gl`, `i.is`) and before its content, with that background colour; on pointer enter or focus the link adds `is-hv` to that layer and removes it on leave or blur.
- Icon or logo links without a surface of their own (drop to 0.72 opacity on hover): [e6] «Home». They get the class `hv-ic`.
- Cards whose images zoom in smoothly on hover: [e88] «Frame 1597882325» ×1.07, [e93] «Frame 1597882330» ×1.07, [e98] «Frame 1597882325» ×1.07, [e103] «Frame 1597882330» ×1.07. They get the class `hz` and the variable `--hz` with that scale; their `i.pl` layers (images) scale inside the card's clip.
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
- layer «Rectangle 23»: x=-525, y=0, z=1
- layer «Component 1»: x=19, y=22, z=1
- layer «Group 53»: x=16, y=120, z=0.68
- layer «Component 3»: x=133, y=770, z=0.45
- layer «Music Button»: x=8, y=790, z=0.8
Unlisted non-bleed layers are hidden on mobile.
Per-element mobile adjustments:
- [e7] «Frame 5»: hide
- [e15] «Login»: shift dx=-1012, dy=0 (design units of its layer)
- [e12] «Group 8»: shift dx=-1032, dy=0 (design units of its layer)
- [e46] «Component 4»: shift dx=-899.2, dy=344 (design units of its layer)
- [e80] «Rectangle 24»: hide
- [e87] «Born To Glide»: place the element point (431,507) at (21.6,530) of the mobile design at scale 0.6×sm; fade out between t=17s and 19s
- [e86] «Built for bold escapes, smooth accelerat»: place the element point (395,670) at (16.25,632) of the mobile design at scale 0.55×sm; fade out between t=16.5s and 18.5s
- [e88] «Frame 1597882325»: place the element point (26,105) at (12,180) of the mobile design at scale 0.7×sm
- [e93] «Frame 1597882330»: place the element point (1159,105) at (200,180) of the mobile design at scale 0.7×sm
- [e98] «Frame 1597882325»: place the element point (142,370) at (12,345) of the mobile design at scale 0.7×sm
- [e103] «Frame 1597882330»: place the element point (1043,370) at (200,345) of the mobile design at scale 0.7×sm
- [e84] «image 74»: place the element point (-134,273) at (1,110) of the mobile design at scale 0.5×sm
- [e82] «Dive into an extraordinary realm of mari»: place the element point (608,470) at (16,430) of the mobile design at scale 0.75×sm
- [e83] «Explore a remarkable world of marine des»: place the element point (1023,476) at (16,560) of the mobile design at scale 0.8×sm
- [e79] «Star 1»: place the element point (-469,144) at (-150,60) of the mobile design at scale 0.5×sm
- [e81] «Music Button»: place the element point (5,758) at (8,790) of the mobile design at scale 0.8×sm
- [e47] «Frame 1597882326»: fade out between t=9.5s and 11.5s

## 12. Semantics and accessibility
- `<main class="hero" aria-label="Born to Glide — Electric Speedboat">`; the loader has `aria-hidden="true"`.
- Lists with `<ul>` and `<li>`; menus inside `<nav aria-label="…">`. Each `<a href="#">` uses the given `aria-label` when its content is not text. If a link covers a whole box, it goes inside it as an absolute `<a class="lk">` covering it (`inset:0`).
- Decorative images with `alt=""`; decorative SVGs with `aria-hidden="true"`.
- Only texts and links receive pointer events (CSS in section 3) and invisible elements get `visibility:hidden`, so nothing that cannot be seen can ever be clicked.
- Visible focus: `outline: 2px solid currentColor; outline-offset: 3px`.

## 13. Resources (absolute URLs)
All resources are served from a CDN with CORS enabled; use these URLs exactly as given:
- `assets/fonts/MonumentExtended-Regular.woff2` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/boat/assets/fonts/MonumentExtended-Regular.woff2
- `assets/fonts/PPMonumentExtended-Black.woff2` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/boat/assets/fonts/PPMonumentExtended-Black.woff2
- `assets/fonts/PPMonumentExtended-Light.woff2` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/boat/assets/fonts/PPMonumentExtended-Light.woff2
- `assets/fonts/PPMonumentExtended-Regular.woff2` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/boat/assets/fonts/PPMonumentExtended-Regular.woff2
- `assets/fonts/Quatro-Medium.woff2` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/boat/assets/fonts/Quatro-Medium.woff2
- `assets/fonts/Quatro-Regular.woff2` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/boat/assets/fonts/Quatro-Regular.woff2
- `assets/img/0dd95ba758-a.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/boat/assets/img/0dd95ba758-a.webp
- `assets/img/4aea2d8a92.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/boat/assets/img/4aea2d8a92.webp
- `assets/img/4db459584e-a.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/boat/assets/img/4db459584e-a.webp
- `assets/img/6dd84d67ee.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/boat/assets/img/6dd84d67ee.webp
- `assets/img/7bb2853745.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/boat/assets/img/7bb2853745.webp
- `assets/img/8b8f2b5aee-f28e0c-k.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/boat/assets/img/8b8f2b5aee-f28e0c-k.webp
- `assets/img/b6c45d7061.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/boat/assets/img/b6c45d7061.webp
- `assets/img/bad46cb175.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/boat/assets/img/bad46cb175.webp
- `assets/img/f41bd0e718.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/boat/assets/img/f41bd0e718.webp
- `assets/video/7f91265fa8.jpg` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/boat/assets/video/7f91265fa8.jpg
- `assets/video/7f91265fa8.mp4` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@80f420e/snows-heroes-frost-neon-fish-boat/boat/assets/video/7f91265fa8.mp4

## 14. Appendix: vector shapes
Each SVG fills 100% of its element's box (`position:absolute; inset:0; width:100%; height:100%; overflow:visible`, `preserveAspectRatio="none"`, `aria-hidden="true"`). If an element says «with fill #xxxxxx (instead of #yyyyyy)», use the same SVG changing that fill color. If an SVG with internal `id`s (masks, gradients) is used more than once, give each copy unique ids and update its `url(#…)` references.

**SVG-1**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 13 14"> <path d="M11.4016 5.12171C12.7349 5.89151 12.7349 7.81601 11.4016 8.58581L3 13.4365C1.66667 14.2063 -6.99004e-07 13.244 -6.31706e-07 11.7044L-2.07647e-07 2.00309C-1.40349e-07 0.463485 1.66667 -0.498765 3 0.271036L11.4016 5.12171Z" fill="white"></path> </svg>
```

**SVG-2**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 13 14"> <path d="M11.4016 5.12147C12.7349 5.89127 12.7349 7.81577 11.4016 8.58557L3 13.4362C1.66667 14.206 -6.99004e-07 13.2438 -6.31706e-07 11.7042L-2.07647e-07 2.00284C-1.40349e-07 0.463241 1.66667 -0.499009 3 0.270792L11.4016 5.12147Z" fill="white"></path> </svg>
```

**SVG-3**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 139 67"> <foreignObject height="106.402" width="178.941" x="-20" y="-20"><div style="backdrop-filter:blur(10px);clip-path:url(#i0);height:100%;width:100%"></div></foreignObject><path d="M7.28975 64.1871C7.48876 63.094 7.18392 62.4348 6.36144 62.285C5.45238 62.1195 5.19736 62.9676 4.91083 63.8658C4.57902 64.8899 4.00419 65.9592 2.2943 65.6479C0.411252 65.3051 0.275858 63.7151 0.545794 62.2325C0.675836 61.5182 0.882603 60.8738 1.06182 60.258L2.27389 60.4787C2.09962 61.006 1.85347 61.7438 1.72934 62.4256C1.53231 63.5078 1.76928 64.11 2.516 64.246C3.32766 64.3937 3.57872 63.6903 3.85833 62.8915C4.24919 61.7887 4.66763 60.5343 6.5615 60.8791C8.28221 61.1924 8.80826 62.5404 8.48907 64.2936C8.33144 65.1594 8.09415 65.91 7.84212 66.5574L6.61922 66.3347C6.91851 65.5506 7.16956 64.8472 7.28975 64.1871ZM7.62905 47.802C5.38994 46.6722 4.5505 44.844 5.63077 42.7031C5.92809 42.1139 6.29424 41.5347 6.81789 40.9365L7.94726 41.5064C7.59173 41.8691 7.08773 42.4773 6.81519 43.0174C6.12144 44.3923 6.38272 45.5837 8.23882 46.5203C10.2128 47.5163 11.3614 46.8515 11.9461 45.6926C12.2236 45.1427 12.423 44.4301 12.4845 43.7711L13.5942 44.3311C13.4981 45.0588 13.2148 45.8399 12.9423 46.3801C11.8273 48.5897 9.92707 48.9616 7.62905 47.802ZM13.8096 28.0811L15.5952 26.1967C16.8588 24.8633 18.2441 24.5849 19.5855 25.856C20.0725 26.3175 20.5944 27.2061 20.374 28.2702L23.6139 29.2491L22.5395 30.3829C21.5981 30.0969 20.6718 29.7951 19.7229 29.5171C19.6245 29.6209 19.4736 29.7962 19.292 29.9878L18.6035 30.7144L20.5118 32.5227L19.5584 33.5288L13.8096 28.0811ZM15.6332 27.8998L17.7332 29.8897L18.6638 28.9076C19.375 28.157 19.0805 27.3324 18.5775 26.8557C18.1463 26.4471 17.2751 26.1671 16.5412 26.9416L15.6332 27.8998ZM29.9016 17.335C28.6381 15.0798 28.89 13.2113 31.0396 12.007C33.1892 10.8027 34.9143 11.5636 36.1777 13.8188C37.4412 16.074 37.1893 17.9425 35.0397 19.1468C32.8901 20.3512 31.165 19.5902 29.9016 17.335ZM34.9398 14.5124C33.9666 12.7754 32.8636 12.3595 31.6352 13.0476C30.3877 13.7466 30.1664 14.9045 31.1395 16.6414C32.1127 18.3784 33.2157 18.7944 34.4441 18.1062C35.6916 17.4072 35.9129 16.2494 34.9398 14.5124ZM47.3389 3.72002L48.6937 3.42775L50.111 9.99761L53.6701 9.22982L53.923 10.4019L49.009 11.4619L47.3389 3.72002ZM66.1698 0.193266L67.5534 0.275203L67.1561 6.98445L70.7907 7.19969L70.7198 8.3966L65.7016 8.09942L66.1698 0.193266ZM97.7819 15.2346L95.4276 13.7096L99.7335 7.06241L102.088 8.5874C103.897 9.75956 104.481 11.7107 103.04 13.9356C101.599 16.1606 99.5914 16.4068 97.7819 15.2346ZM101.849 13.1642C102.722 11.8163 102.655 10.3831 101.307 9.51L100.245 8.82225L97.2428 13.4569L98.3045 14.1446C99.6524 15.0177 100.976 14.5121 101.849 13.1642ZM113.949 21.3954C115.947 19.7556 117.831 19.6755 119.394 21.5802C120.957 23.485 120.511 25.3168 118.513 26.9566C116.514 28.5965 114.631 28.6766 113.068 26.7719C111.505 24.8671 111.951 23.0353 113.949 21.3954ZM117.613 25.8597C119.152 24.5967 119.367 23.4378 118.474 22.3493C117.567 21.2439 116.388 21.2294 114.849 22.4924C113.31 23.7554 113.094 24.9143 113.987 26.0028C114.895 27.1082 116.073 27.1227 117.613 25.8597ZM125.087 44.8342L129.561 41.6396L129.53 41.557L124.053 42.0265L123.422 40.3129L130.214 35.7726L130.723 37.1558L125.478 40.611L125.493 40.6523L131.787 40.0461L132.293 41.419L127.108 45.0393L127.127 45.091L133.342 44.268L133.828 45.5892L125.718 46.5477L125.087 44.8342ZM130.907 64.8074L136.099 60.4911L130.51 61.0886L130.362 59.7105L138.238 58.8686L138.398 60.3671L133.074 64.6865L138.794 64.075L138.941 65.4531L131.066 66.2949L130.907 64.8074Z" data-figma-bg-blur-radius="20" fill="white" opacity="0.4"></path> <defs> <clipPath id="i0" transform="translate(20 20)"><path d="M7.28975 64.1871C7.48876 63.094 7.18392 62.4348 6.36144 62.285C5.45238 62.1195 5.19736 62.9676 4.91083 63.8658C4.57902 64.8899 4.00419 65.9592 2.2943 65.6479C0.411252 65.3051 0.275858 63.7151 0.545794 62.2325C0.675836 61.5182 0.882603 60.8738 1.06182 60.258L2.27389 60.4787C2.09962 61.006 1.85347 61.7438 1.72934 62.4256C1.53231 63.5078 1.76928 64.11 2.516 64.246C3.32766 64.3937 3.57872 63.6903 3.85833 62.8915C4.24919 61.7887 4.66763 60.5343 6.5615 60.8791C8.28221 61.1924 8.80826 62.5404 8.48907 64.2936C8.33144 65.1594 8.09415 65.91 7.84212 66.5574L6.61922 66.3347C6.91851 65.5506 7.16956 64.8472 7.28975 64.1871ZM7.62905 47.802C5.38994 46.6722 4.5505 44.844 5.63077 42.7031C5.92809 42.1139 6.29424 41.5347 6.81789 40.9365L7.94726 41.5064C7.59173 41.8691 7.08773 42.4773 6.81519 43.0174C6.12144 44.3923 6.38272 45.5837 8.23882 46.5203C10.2128 47.5163 11.3614 46.8515 11.9461 45.6926C12.2236 45.1427 12.423 44.4301 12.4845 43.7711L13.5942 44.3311C13.4981 45.0588 13.2148 45.8399 12.9423 46.3801C11.8273 48.5897 9.92707 48.9616 7.62905 47.802ZM13.8096 28.0811L15.5952 26.1967C16.8588 24.8633 18.2441 24.5849 19.5855 25.856C20.0725 26.3175 20.5944 27.2061 20.374 28.2702L23.6139 29.2491L22.5395 30.3829C21.5981 30.0969 20.6718 29.7951 19.7229 29.5171C19.6245 29.6209 19.4736 29.7962 19.292 29.9878L18.6035 30.7144L20.5118 32.5227L19.5584 33.5288L13.8096 28.0811ZM15.6332 27.8998L17.7332 29.8897L18.6638 28.9076C19.375 28.157 19.0805 27.3324 18.5775 26.8557C18.1463 26.4471 17.2751 26.1671 16.5412 26.9416L15.6332 27.8998ZM29.9016 17.335C28.6381 15.0798 28.89 13.2113 31.0396 12.007C33.1892 10.8027 34.9143 11.5636 36.1777 13.8188C37.4412 16.074 37.1893 17.9425 35.0397 19.1468C32.8901 20.3512 31.165 19.5902 29.9016 17.335ZM34.9398 14.5124C33.9666 12.7754 32.8636 12.3595 31.6352 13.0476C30.3877 13.7466 30.1664 14.9045 31.1395 16.6414C32.1127 18.3784 33.2157 18.7944 34.4441 18.1062C35.6916 17.4072 35.9129 16.2494 34.9398 14.5124ZM47.3389 3.72002L48.6937 3.42775L50.111 9.99761L53.6701 9.22982L53.923 10.4019L49.009 11.4619L47.3389 3.72002ZM66.1698 0.193266L67.5534 0.275203L67.1561 6.98445L70.7907 7.19969L70.7198 8.3966L65.7016 8.09942L66.1698 0.193266ZM97.7819 15.2346L95.4276 13.7096L99.7335 7.06241L102.088 8.5874C103.897 9.75956 104.481 11.7107 103.04 13.9356C101.599 16.1606 99.5914 16.4068 97.7819 15.2346ZM101.849 13.1642C102.722 11.8163 102.655 10.3831 101.307 9.51L100.245 8.82225L97.2428 13.4569L98.3045 14.1446C99.6524 15.0177 100.976 14.5121 101.849 13.1642ZM113.949 21.3954C115.947 19.7556 117.831 19.6755 119.394 21.5802C120.957 23.485 120.511 25.3168 118.513 26.9566C116.514 28.5965 114.631 28.6766 113.068 26.7719C111.505 24.8671 111.951 23.0353 113.949 21.3954ZM117.613 25.8597C119.152 24.5967 119.367 23.4378 118.474 22.3493C117.567 21.2439 116.388 21.2294 114.849 22.4924C113.31 23.7554 113.094 24.9143 113.987 26.0028C114.895 27.1082 116.073 27.1227 117.613 25.8597ZM125.087 44.8342L129.561 41.6396L129.53 41.557L124.053 42.0265L123.422 40.3129L130.214 35.7726L130.723 37.1558L125.478 40.611L125.493 40.6523L131.787 40.0461L132.293 41.419L127.108 45.0393L127.127 45.091L133.342 44.268L133.828 45.5892L125.718 46.5477L125.087 44.8342ZM130.907 64.8074L136.099 60.4911L130.51 61.0886L130.362 59.7105L138.238 58.8686L138.398 60.3671L133.074 64.6865L138.794 64.075L138.941 65.4531L131.066 66.2949L130.907 64.8074Z"></path> </clipPath></defs> </svg>
```

**SVG-4**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 2 16"> <path d="M1 1V14.75" stroke="white" stroke-linecap="round" stroke-linejoin="round" stroke-width="2"></path> </svg>
```

**SVG-5**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 14 8"> <path d="M1 1L6.625 6.625L12.25 1" stroke="white" stroke-linecap="round" stroke-linejoin="round" stroke-width="2"></path> </svg>
```

**SVG-6**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 1151 356"> <g filter="url(#i0)"> <path d="M141 400L201.305 784.94L446 481.724L305.755 845.245L669.276 705L366.06 949.695L751 1010L366.06 1070.3L669.276 1315L305.755 1174.76L446 1538.28L201.305 1235.06L141 1620L80.6954 1235.06L-164 1538.28L-23.7554 1174.76L-387.276 1315L-84.06 1070.3L-469 1010L-84.06 949.695L-387.276 705L-23.7554 845.245L-164 481.724L80.6954 784.94L141 400Z" fill="#04242E"></path> </g> <defs> <filter color-interpolation-filters="sRGB" filterUnits="userSpaceOnUse" height="2020" id="i0" width="2020" x="-869" y="0"> <feFlood flood-opacity="0" result="BackgroundImageFix"></feFlood> <feBlend in="SourceGraphic" in2="BackgroundImageFix" mode="normal" result="shape"></feBlend> <feGaussianBlur result="effect1_foregroundBlur_13051_2259" stdDeviation="200"></feGaussianBlur> </filter> </defs> </svg>
```

**SVG-7**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 13 14"> <path d="M11.4016 5.12159C12.7349 5.89139 12.7349 7.81589 11.4016 8.58569L3 13.4364C1.66667 14.2062 -6.99004e-07 13.2439 -6.31706e-07 11.7043L-2.07647e-07 2.00296C-1.40349e-07 0.463363 1.66667 -0.498887 3 0.270914L11.4016 5.12159Z" fill="white"></path> </svg>
```

**SVG-8**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 13 14"> <path d="M11.4019 5.12159C12.7352 5.89139 12.7352 7.81589 11.4019 8.58569L3.00024 13.4364C1.66691 14.2062 0.000243442 13.2439 0.000243509 11.7043L0.000243933 2.00296C0.000244 0.463363 1.66691 -0.498887 3.00024 0.270914L11.4019 5.12159Z" fill="white"></path> </svg>
```

**SVG-9**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 13 14"> <path d="M11.4019 5.12171C12.7352 5.89151 12.7352 7.81601 11.4019 8.58581L3.00024 13.4365C1.66691 14.2063 0.000243442 13.244 0.000243509 11.7044L0.000243933 2.00309C0.000244 0.463485 1.66691 -0.498765 3.00024 0.271036L11.4019 5.12171Z" fill="white"></path> </svg>
```

## 15. Acceptance criteria
- At 1440×810, 1280×720 and 390×844 the composition is identical at the key moments: end of the intro, every pause and the final state.
- Positions, sizes, colors, fonts, timings and curves match this specification.
- All texts are real, editable HTML; all links are clickable `<a href="#">` with visible focus; invisible things cannot be clicked.
- Images, video and fonts load from the URLs in section 13 and there are no console errors.
- The layout adapts when the window is resized and follows section 11 on mobile.
- With `prefers-reduced-motion` the final state is shown directly.
