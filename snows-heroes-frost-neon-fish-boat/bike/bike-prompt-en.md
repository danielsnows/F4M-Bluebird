# Replication prompt — Zephyr — The Future of Cycling

> Complete instructions for an AI coding agent. It contains everything needed to rebuild the animated hero «Zephyr — The Future of Cycling» identically: structure, styles, texts, animation timings and curves, mobile view and links to the resources (images, video and fonts).

## Role and goal
Act as a senior front-end developer specialized in web animation. Build a single-screen page (hero) that exactly reproduces the animated design «Zephyr — The Future of Cycling» described here, without adding or removing anything.

## 1. Overview
Premium road-bike hero («Zephyr», by the «Cycle» brand), designed at 1440×810 in black and white and framed by a 10 px white border with rounded corners. Full-bleed in the background, mirrored horizontally, a cinematic video that opens on a close-up of the handlebar and pulls back to reveal the whole bike (it plays once and rests on its last frame), with a dark gradient and a progressive blur along the bottom. The entrance animation plays by itself, without scrolling (0 → 3.55 s): the white top tab with the search, the Collection / Designs / Patterns menu and the «Contact» button slides in from the right; the «CYCLE» logo and the numbers 01–05 slide into place; the bottom-left card with a second, looping video of the front wheel rises from below while its arrow turns; and the «09/VYPER — ZEPHYR» title, the «Experience the future of cycling…» text, the «32KM Range» and «3.2H Charge Time» glass cards (with background blur) and the circular «12.6KM» gauge with the bike photo arrive. On hover, menu links and numbers subtly change tone, the «Contact» button lightens and the icons dim slightly.

## 2. Deliverable and technical rules
- A single `index.html` file with the CSS inside `<style>` and the JavaScript inside `<script>`. Plain (vanilla) JavaScript, no frameworks and no build step.
- The only external dependency: the Lenis library, exact version 1.3.26 (npm package `lenis`, file `dist/lenis.min.js`, which exposes the global class `Lenis`), for smooth scrolling. Load it with a `<script>` tag from a public npm CDN before your script (if it fails to load, everything must work with native scrolling).
- Images, video and fonts are ALWAYS loaded from the absolute URLs in section 13 «Resources». Do not download them, do not inline them as base64 and do not use any other resources.
- Every visible text is real, selectable and editable HTML text (never text turned into an image or into SVG paths). The vector shapes in the Appendix are only icons and decoration.
- Lists use `<ul>`/`<li>` (inside `<nav aria-label="…">` where indicated) and links use clickable `<a href="#">`, with `aria-label` where indicated.
- Head: `<html lang="en">`, `<meta charset="utf-8">`, `<meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover">`, `<title>Zephyr — The Future of Cycling</title>`, `<meta name="description" content="Experience the future of cycling with the revolutionary Zephyr bicycle today.">`, `<meta name="theme-color" content="#ffffff">`.
- Respect `prefers-reduced-motion: reduce`: the loader works the same, but there is no intro, no smooth scrolling and no snap; t stays fixed on the final state (t = 3.55 s) even when scrolling.
- Do not add elements, texts, effects or sections that are not in this specification.

## 3. Base structure (common HTML + CSS)
```html
<body>
<div class="loader" id="loader" aria-hidden="true">
  <div class="loader-in"><span>ZEPHYR</span><div class="loader-bar"><i id="loaderBar"></i></div><span id="loaderPct">000</span></div>
</div>
<main class="hero" id="hero" aria-label="Zephyr — The Future of Cycling">
  <div class="stage" id="stage">
    <!-- one layer (div.blk) per «Layer» in section 9, in the same order: later ones are on top -->
  </div>
</main>
<script>/* engine from section 6 */</script>
</body>
```
```css
:root{--bg:#ffffff;--ink:#1b1b1f;color-scheme:light}
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
- **Cindie Mono** (weight 400): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@d014d61/snows-heroes-frost-neon-fish-boat/bike/assets/fonts/cindiemono-d.woff2
- **Clash Display Variable** (variable font, weight range 200–700 (font-weight:200 700)): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@d014d61/snows-heroes-frost-neon-fish-boat/bike/assets/fonts/ClashDisplay-Variable.woff2

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
  - `E0` = spring with mass=1, stiffness=80, damping=20, normalized to 1.667 s (see formula)
  - `E1` = spring with mass=1, stiffness=80, damping=20, normalized to 1.25 s (see formula)
  - `E2` = spring with mass=1, stiffness=80, damping=20, normalized to 0.625 s (see formula)
- `cubic-bezier(x1, y1, x2, y2)` curves work as in CSS: given the time progress p, solve the parameter u such that X(u) = p and return Y(u).

Spring formula with mass m, stiffness k, damping c and normalized duration D:
- ω0 = √(k/m), ζ = c / (2·√(k·m)).
- If ζ < 1: ωd = ω0·√(1−ζ²); x(τ) = 1 − e^(−ζ·ω0·τ)·(cos(ωd·τ) + (ζ·ω0/ωd)·sin(ωd·τ)).
- If ζ = 1: x(τ) = 1 − e^(−ω0·τ)·(1 + ω0·τ).
- If ζ > 1: q = √(ζ²−1), r1 = −ω0·(ζ−q), r2 = −ω0·(ζ+q); x(τ) = 1 + (r2·e^(r1·τ) − r1·e^(r2·τ)) / (r1 − r2).
- easing(p) = x(p·D) / x(D) for 0 < p < 1; 0 if p ≤ 0; 1 if p ≥ 1.

## 7. Playback (intro and scrolling)
- Total timeline duration: 3.55 s. This hero **autoplays**: there is no scrolling. `.hero` is exactly 100vh tall (track = 0) and the page does not scroll.
- While loading: `html.is-locked` (overflow hidden; add it before the first measurement of the stage, so a classic scrollbar does not narrow the measured width), `history.scrollRestoration = 'manual'`, `scrollTo(0,0)`.
- When the loader gets the `is-done` class, the timeline advances in real time from t = 0 to t = 3.55 s (t = elapsed seconds, linear; each property's curve provides the smoothness) and stays on the final state.
- Lenis is loaded and initialized the same way as in the other heroes (`new Lenis({lerp:0.085, wheelMultiplier:0.9, smoothWheel:true})`), but here there is nothing to scroll.
- Video («play once» mode): it is downloaded with `fetch` as a Blob during preloading (section 8) and then `video.src = URL.createObjectURL(blob)` (if that fails, the direct URL). `loop` disabled. At the moment the intro starts: `currentTime = 0` and `play()`. It plays from start to end and stays on the last frame. It is never paused and never synced to scrolling. With `prefers-reduced-motion`, show its last frame directly (`currentTime = duration − 0.05`).
- Video («loop» mode): `loop` enabled; downloaded as a Blob during preloading and played (`play()`) as soon as t reaches its start; if t goes back before its start, it is paused.

## 8. Loader and preloading
- The loader covers the screen with the `--bg` color and shows «ZEPHYR», a 160×1 px bar and a 3-digit percentage (`000` → `100`) in the `--ink` color.
- Weighted progress: fonts (`document.fonts.ready`) weight 1; each `<img>` element on the stage weight 0.4, even if it repeats a file (it counts once it has loaded —or failed— and `img.decode()` has finished; the video poster does not count); the video weight 1.5 (its download progress). Percentage = Math.round(progress × 100) with 3 digits. The bar uses `transform: scaleX(progress)` and never goes backwards.
- Wait until everything finishes (14 s maximum; if it runs out, continue without cancelling anything); then set the video `src` (the Blob URL if already downloaded, otherwise the direct URL) and wait for its `loadeddata` or `error` (3 s maximum). Then remove `is-locked` from `<html>` (except in the no-Lenis case described in section 7), add `is-done` to the loader (it fades out in 0.9 s) and start the intro.

## 9. Layers and elements
Notation of each line: `[eN]` = suggested identifier; `box` = div; `text` = div with text (the content in quotes, keeping capitalization, line breaks and spaces); `group` = boxless container; `vector` = box containing an inline SVG; `<a href="#">`, `<ul>`, `<li>`, `<nav>` = semantic tag to use; `«name»` = layer name in the design (for reference only, also handy as `data-name`); `pos(x,y)` = left/top; `size A×B` = width×height; after `|` come the literal CSS styles. Every `text` element gets `class="t"` (used by the pointer-events rule in section 3); boxes, groups and vectors may use `class="b"`, `"g"` and `"v"`. Decorative layers (border, glass, inner shadow, background blur, fill layer) are absolute elements (for example `<i>`) covering their container (given `inset`) with inline `pointer-events:none` and `border-radius:inherit` unless stated otherwise. Images go inside an absolute container `inset:0; overflow:hidden; border-radius:inherit`. The order of the lines is the stacking order (later is on top).

#### Layer bk0 «Fondo» — BLEED LAYER (cover), design box x=0 y=0 1440×810
- [e1] box «home» pos(0,0) size 1440×810 | border-radius:40px; background-color:#ffffff
  - border (stroke): inset:0px; border-radius:40px; border:10px solid #ffffff

#### Layer bk1 «capcut-edit 1» — BLEED LAYER (cover), design box x=0 y=0 1440×810
- [e2] box «capcut-edit 1» transform translate(1440,0) rotate(3.14rad) scale(1,-1) size 1440×810
  - video: `<video muted playsinline preload="auto" poster="https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@d014d61/snows-heroes-frost-neon-fish-boat/bike/assets/video/72f339ca58.jpg" data-src="https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@d014d61/snows-heroes-frost-neon-fish-boat/bike/assets/video/72f339ca58.mp4">` with position:absolute; inset:0; width:100%; height:100%; object-fit:cover; display:block (the `src` is assigned from JavaScript once preloading finishes, see sections 7 and 8)
  - fill layer (covers the whole box, stacked in this order): background:linear-gradient(180deg, rgba(0,0,0,0) 27.37%, rgba(0,0,0,0.8) 96.55%)
  - background blur (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(20px); mask:linear-gradient(180deg, rgba(0,0,0,0) 70.13%, rgba(0,0,0,1) 100%)

#### Layer bk2 «Component 5» — CONTAINED LAYER (contain, anchor x=-1, y=1), design box x=10 y=441.2 432×359.21
- [e3] box «Component 5» pos(10,441.2) size 432×359.21
  - [e4] group «Group 9»
    - [e5] vector «Rectangle 2» pos(0,290) size 432×359
        ↳ animation: y: 1.5→3.167s 290→0 (E0)
      - vector drawing SVG-1 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
    - [e6] group «Mask group»
      - [e8] group
          ↳ animation: offsetY: 1.5→3.167s 0→-290 (E0)
        - [-] mask size 4367.77×4648.8 | mask:url('data:image/svg+xml,%3Csvg%20width%3D%22367.77%22%20height%3D%22180.41%22%20viewBox%3D%220%200%20367.77%20180.41%22%20fill%3D%22none%22%20xmlns%3D%22http%3A//www.w3.org/2000/svg%22%3E%3Cg%20transform%3D%22matrix%281%200%200%201%200%200%29%22%3E%3Cpath%20fill-rule%3D%22nonzero%22%20d%3D%22M162.53%200%20C168.50%200%20173.87%203.60%20176.15%209.11%20L189.26%2040.92%20C196.52%2058.52%20213.68%2070.01%20232.72%2070.01%20L274.1%2070.01%20C287.87%2070.01%20300.28%2078.32%20305.53%2091.05%20L332.68%20156.92%20C338.54%20171.13%20352.4%20180.41%20367.77%20180.41%20L30%20180.41%20C13.43%20180.41%201.52e-05%20166.97%200%20150.41%20L0%2030%20C5.46e-05%2013.43%2013.43%20-3.51e-15%2030%200%20L162.53%200%20Z%22%20fill%3D%22%23d9d9d9%22/%3E%3C/g%3E%3C/svg%3E') no-repeat 0px 468.39px/367.77px 180.41px
          - [e9] group
              ↳ animation: offsetY: 1.5→3.167s 0→290 (E0)
            - [e7] box «small 1» transform translate(525.70,377.58) rotate(3.14rad) scale(1,-1) size 705.75×396.98
                ↳ animation: x: 1.5→3.167s 525.7→365.66 (E0); y: 1.5→3.167s 377.58→177.6 (E0); width: 1.5→3.167s 705.75→385.66 (E0); height: 1.5→3.167s 396.98→216.93 (E0)
              - video: `<video muted playsinline preload="auto" poster="https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@d014d61/snows-heroes-frost-neon-fish-boat/bike/assets/video/8e1e447019.jpg" data-src="https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@d014d61/snows-heroes-frost-neon-fish-boat/bike/assets/video/8e1e447019.mp4">` with position:absolute; inset:0; width:100%; height:100%; object-fit:cover; display:block (the `src` is assigned from JavaScript once preloading finishes, see sections 7 and 8)
    - [e10] vector <a href="#"> aria-label="View details" «Union» transform matrix(-0.258,-0.965,0.965,-0.258,229.379,509.047) size 16.47×15.89
        ↳ animation: y: 1.5→3.167s 0→-290 (E0); rotation(°): 1.5→3.167s 0→105 (E0) [rotation around point [8.06,7.48] of the box]
      - vector drawing SVG-2 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box

#### Layer bk3 «Component 2» — CONTAINED LAYER (contain, anchor x=1, y=-1), design box x=716.18 y=9.82 713.82×143.08
- [e11] box «Component 2» pos(716.18,9.82) size 713.82×143.08
  - [e12] group «Group 26»
    - [e13] group «Group 25»
      - [e14] vector «Rectangle 2» pos(807.3,0.3) size 234.94×63.77
          ↳ animation: x: 0.3→1.55s 807.3→0 (E1)
        - vector drawing SVG-3 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e15] box <a href="#"> aria-label="Search" «MagnifyingGlass» pos(886.12,20.18) size 20×20
          ↳ animation: x: 0.3→1.55s 886.12→78.82 (E1)
        - [e17] vector «Vector» pos(2,2) size 13.5×13.5
          - vector drawing SVG-4 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
        - [e18] vector «Vector» pos(12.67,12.67) size 5.33×5.33
          - vector drawing SVG-5 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
    - [e19] group «Group 28»
      - [e20] vector «Rectangle 1» pos(590.67,0.14) size 620.45×142.94
          ↳ animation: x: 0.3→1.55s 590.67→93.37 (E1)
        - vector drawing SVG-6 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e21] box «Frame 10» pos(659.12,6.18) size 552×52
          ↳ animation: x: 0.3→1.55s 659.12→161.82 (E1)
        - [-] list <nav> aria-label="Main navigation"
          - [-] list <ul>
            - [e22] box <li> «Frame 5» pos(445,1.5) size 147×49 | border-radius:999px
                ↳ animation: x: 0.3→1.55s 445→0 (E1)
              - [-] link <a href="#"> that covers its container's whole box (inset:0)
                - [e23] text pos(20,20) size 107×9 | white-space:pre; text-align:right; text-box:trim-both cap alphabetic; font-family:'Clash Display Variable'; font-weight:400; font-size:13px; line-height:normal; letter-spacing:0.22em; text-transform:uppercase; color:#000000 | TEXT: "Collection"
            - [e24] box <li> «Frame 6» pos(480,1.5) size 112×49 | border-radius:999px
                ↳ animation: x: 0.3→1.55s 480→151 (E1)
              - [-] link <a href="#"> that covers its container's whole box (inset:0)
                - [e25] text pos(20,20) size 72×9 | white-space:pre; text-align:right; text-box:trim-both cap alphabetic; font-family:'Clash Display Variable'; font-weight:400; font-size:13px; line-height:normal; letter-spacing:0.22em; text-transform:uppercase; color:#000000 | TEXT: "Designs"
            - [e26] box <li> «Frame 7» pos(464,1.5) size 128×49 | border-radius:999px
                ↳ animation: x: 0.3→1.55s 464→267 (E1)
              - [-] link <a href="#"> that covers its container's whole box (inset:0)
                - [e27] text pos(20,20) size 88×9 | white-space:pre; text-align:right; text-box:trim-both cap alphabetic; font-family:'Clash Display Variable'; font-weight:400; font-size:13px; line-height:normal; letter-spacing:0.22em; text-transform:uppercase; color:#000000 | TEXT: "Patterns"
        - [e28] box <a href="#"> «Frame 8» pos(439,0) size 153×52 | border-radius:999px; background-color:#000000
            ↳ animation: x: 0.3→1.55s 439→399 (E1)
          - [e29] box «Frame 9» pos(8,8) size 36×36 | border-radius:999px; overflow:hidden; background-color:#ffffff
            - [e30] box «PaperPlaneTilt» pos(11,11) size 14×14
              - [e32] vector «Vector» pos(0.44,1.31) size 12.25×12.25
                - vector drawing SVG-7 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
          - [e33] text pos(52,21.5) size 81×9 | white-space:pre; text-align:right; text-box:trim-both cap alphabetic; font-family:'Clash Display Variable'; font-weight:400; font-size:13px; line-height:normal; letter-spacing:0.22em; text-transform:uppercase; color:#ffffff | TEXT: "Contact"

#### Layer bk4 «Component 7» — CONTAINED LAYER (contain, anchor x=1, y=1), design box x=920.89 y=691 229×55
- [e34] box «Component 7» pos(920.89,691) size 229×55 | overflow:hidden
  - [e35] text pos(-230,0) size 229×55 | white-space:pre-wrap; text-align:left; text-box:trim-both cap alphabetic; font-family:'Clash Display Variable'; font-weight:400; font-size:16px; line-height:1.4; color:#ffffff | TEXT: "Experience the future of cycling with the revolutionary Zephyr bicycle today."
      ↳ animation: x: 2→3.25s -230→0 (E1)

#### Layer bk5 «Component 8» — CONTAINED LAYER (contain, anchor x=1, y=1), design box x=1221.63 y=570.3 162.75×169.41
- [e36] box «Component 8» pos(1221.63,570.3) size 162.75×169.41
  - [e37] group «Group 15»
    - [e38] group «Group 13»
      - [e39] group «Repeat group 1»
        - [e40] box «Line 8» transform translate(119.26,414.63) rotate(-2.04rad) scale(1,-1) size 5.95×1 | opacity:0.4; background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 119.26→43.48 (E1); y: 2.3→3.55s 414.63→14.78 (E1); rotation(rad): 2.3→3.55s -2.04→1.1 (E1)
        - [e41] box «Line 8» transform translate(129.60,406.91) rotate(-2.19rad) scale(1,-1) size 3.75×1 | opacity:0.4; background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 129.61→33.14 (E1); y: 2.3→3.55s 406.91→22.5 (E1); rotation(rad): 2.3→3.55s -2.2→0.94 (E1)
        - [e42] box «Line 8» transform translate(139.26,398.55) rotate(-2.35rad) scale(1,-1) size 3.75×1 | opacity:0.4; background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 139.27→23.48 (E1); y: 2.3→3.55s 398.56→30.85 (E1); rotation(rad): 2.3→3.55s -2.36→0.79 (E1)
        - [e43] box «Line 8» transform translate(147.49,388.79) rotate(-2.51rad) scale(1,-1) size 3.75×1 | opacity:0.4; background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 147.5→15.25 (E1); y: 2.3→3.55s 388.8→40.61 (E1); rotation(rad): 2.3→3.55s -2.51→0.63 (E1)
        - [e44] box «Line 8» transform translate(154.10,377.86) rotate(-2.67rad) scale(1,-1) size 3.75×1 | opacity:0.4; background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 154.1→8.64 (E1); y: 2.3→3.55s 377.87→51.54 (E1); rotation(rad): 2.3→3.55s -2.67→0.47 (E1)
        - [e45] box «Line 8» transform translate(158.91,366.04) rotate(-2.82rad) scale(1,-1) size 3.75×1 | opacity:0.4; background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 158.92→3.83 (E1); y: 2.3→3.55s 366.04→63.37 (E1); rotation(rad): 2.3→3.55s -2.83→0.31 (E1)
        - [e46] box «Line 8» transform translate(161.82,353.60) rotate(-2.98rad) scale(1,-1) size 3.75×1 | opacity:0.4; background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 161.82→0.92 (E1); y: 2.3→3.55s 353.61→75.8 (E1); rotation(rad): 2.3→3.55s -2.98→0.16 (E1)
        - [e47] box «Line 8» transform translate(162.74,340.87) rotate(3.14rad) scale(1,-1) size 3.75×1 | opacity:0.4; background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 162.74→0 (E1); y: 2.3→3.55s 340.87→88.54 (E1); rotation(rad): 2.3→3.55s 3.14→0 (E1)
        - [e48] box «Line 8» transform translate(161.66,328.14) rotate(2.98rad) scale(1,-1) size 3.75×1 | opacity:0.4; background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 161.66→1.08 (E1); y: 2.3→3.55s 328.15→101.26 (E1); rotation(rad): 2.3→3.55s 2.98→-0.16 (E1)
        - [e49] box «Line 8» transform translate(158.60,315.75) rotate(2.82rad) scale(1,-1) size 3.75×1 | opacity:0.4; background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 158.61→4.14 (E1); y: 2.3→3.55s 315.75→113.66 (E1); rotation(rad): 2.3→3.55s 2.83→-0.31 (E1)
        - [e50] box «Line 8» transform translate(153.64,303.98) rotate(2.67rad) scale(1,-1) size 3.75×1 | opacity:0.4; background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 153.65→9.1 (E1); y: 2.3→3.55s 303.98→125.43 (E1); rotation(rad): 2.3→3.55s 2.67→-0.47 (E1)
        - [e51] box «Line 8» transform translate(146.91,293.13) rotate(2.51rad) scale(1,-1) size 3.75×1 | opacity:0.4; background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 146.91→15.83 (E1); y: 2.3→3.55s 293.14→136.27 (E1); rotation(rad): 2.3→3.55s 2.51→-0.63 (E1)
        - [e52] box «Line 8» transform translate(138.55,283.48) rotate(2.35rad) scale(1,-1) size 3.75×1 | opacity:0.4; background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 138.56→24.19 (E1); y: 2.3→3.55s 283.48→145.93 (E1); rotation(rad): 2.3→3.55s 2.36→-0.79 (E1)
        - [e53] box «Line 8» transform translate(128.79,275.24) rotate(2.19rad) scale(1,-1) size 3.75×1 | opacity:0.4; background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 128.8→33.95 (E1); y: 2.3→3.55s 275.25→154.16 (E1); rotation(rad): 2.3→3.55s 2.2→-0.94 (E1)
        - [e54] box «Line 8» transform translate(117.86,268.64) rotate(2.04rad) scale(1,-1) size 3.75×1 | opacity:0.4; background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 117.87→44.88 (E1); y: 2.3→3.55s 268.64→160.77 (E1); rotation(rad): 2.3→3.55s 2.04→-1.1 (E1)
        - [e55] box «Line 8» transform translate(106.04,263.82) rotate(1.88rad) scale(1,-1) size 3.75×1 | opacity:0.4; background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 106.04→56.7 (E1); y: 2.3→3.55s 263.83→165.58 (E1); rotation(rad): 2.3→3.55s 1.88→-1.26 (E1)
        - [e56] box «Line 8» transform translate(93.60,260.92) rotate(1.72rad) scale(1,-1) size 3.75×1 | opacity:0.4; background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 93.61→69.14 (E1); y: 2.3→3.55s 260.92→168.49 (E1); rotation(rad): 2.3→3.55s 1.73→-1.41 (E1)
        - [e57] box «Line 8» transform translate(80.87,260) rotate(1.57rad) scale(1,-1) size 3.75×1 | opacity:0.4; background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 80.87→81.87 (E1); y: 2.3→3.55s 260→169.41 (E1); rotation(rad): 2.3→3.55s 1.57→-1.57 (E1)
        - [e58] box «Line 8» transform translate(68.14,261.08) rotate(1.41rad) scale(1,-1) size 3.75×1 | opacity:0.4; background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 68.15→94.6 (E1); y: 2.3→3.55s 261.08→168.33 (E1); rotation(rad): 2.3→3.55s 1.41→-1.73 (E1)
        - [e59] box «Line 8» transform translate(55.75,264.13) rotate(1.25rad) scale(1,-1) size 3.75×1 | opacity:0.4; background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 55.75→106.99 (E1); y: 2.3→3.55s 264.14→165.27 (E1); rotation(rad): 2.3→3.55s 1.26→-1.88 (E1)
        - [e60] box «Line 8» transform translate(43.98,269.09) rotate(1.09rad) scale(1,-1) size 3.75×1 | opacity:0.4; background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 43.98→118.76 (E1); y: 2.3→3.55s 269.1→160.31 (E1); rotation(rad): 2.3→3.55s 1.1→-2.04 (E1)
        - [e61] box «Line 8» transform translate(33.13,275.83) rotate(0.94rad) scale(1,-1) size 3.75×1 | opacity:0.4; background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 33.14→129.61 (E1); y: 2.3→3.55s 275.83→153.58 (E1); rotation(rad): 2.3→3.55s 0.94→-2.2 (E1)
        - [e62] box «Line 8» transform translate(23.48,284.18) rotate(0.78rad) scale(1,-1) size 3.75×1 | opacity:0.4; background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 23.48→139.27 (E1); y: 2.3→3.55s 284.19→145.22 (E1); rotation(rad): 2.3→3.55s 0.79→-2.36 (E1)
        - [e63] box «Line 8» transform translate(15.24,293.94) rotate(0.62rad) scale(1,-1) size 3.75×1 | opacity:0.4; background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 15.25→147.5 (E1); y: 2.3→3.55s 293.95→135.46 (E1); rotation(rad): 2.3→3.55s 0.63→-2.51 (E1)
        - [e64] box «Line 8» transform translate(8.64,304.87) rotate(0.47rad) scale(1,-1) size 3.75×1 | opacity:0.4; background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 8.64→154.1 (E1); y: 2.3→3.55s 304.88→124.53 (E1); rotation(rad): 2.3→3.55s 0.47→-2.67 (E1)
        - [e65] box «Line 8» transform translate(3.82,316.70) rotate(0.31rad) scale(1,-1) size 3.75×1 | opacity:0.4; background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 3.83→158.92 (E1); y: 2.3→3.55s 316.7→112.71 (E1); rotation(rad): 2.3→3.55s 0.31→-2.83 (E1)
        - [e66] box «Line 8» transform translate(0.92,329.13) rotate(0.15rad) scale(1,-1) size 3.75×1 | opacity:0.4; background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 0.92→161.82 (E1); y: 2.3→3.55s 329.14→100.27 (E1); rotation(rad): 2.3→3.55s 0.16→-2.98 (E1)
        - [e67] box «Line 8» transform translate(0,341.87) rotate(0rad) scale(1,-1) size 3.75×1 | opacity:0.4; background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 0→162.75 (E1); y: 2.3→3.55s 341.87→87.54 (E1); rotation(rad): 2.3→3.55s 0→3.14 (E1)
        - [e68] box «Line 8» transform translate(1.08,354.59) rotate(-0.15rad) scale(1,-1) size 3.75×1 | opacity:0.4; background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 1.08→161.67 (E1); y: 2.3→3.55s 354.6→74.81 (E1); rotation(rad): 2.3→3.55s -0.16→2.98 (E1)
        - [e69] box «Line 8» transform translate(4.13,366.99) rotate(-0.31rad) scale(1,-1) size 3.75×1 | opacity:0.4; background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 4.14→158.61 (E1); y: 2.3→3.55s 366.99→62.42 (E1); rotation(rad): 2.3→3.55s -0.31→2.83 (E1)
        - [e70] box «Line 8» transform translate(9.09,378.76) rotate(-0.47rad) scale(1,-1) size 3.75×1 | opacity:0.4; background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 9.1→153.65 (E1); y: 2.3→3.55s 378.76→50.65 (E1); rotation(rad): 2.3→3.55s -0.47→2.67 (E1)
        - [e71] box «Line 8» transform translate(15.83,389.60) rotate(-0.62rad) scale(1,-1) size 3.75×1 | opacity:0.4; background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 15.83→146.91 (E1); y: 2.3→3.55s 389.61→39.8 (E1); rotation(rad): 2.3→3.55s -0.63→2.51 (E1)
        - [e72] box «Line 8» transform translate(24.18,399.26) rotate(-0.78rad) scale(1,-1) size 3.75×1 | opacity:0.4; background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 24.19→138.56 (E1); y: 2.3→3.55s 399.27→30.15 (E1); rotation(rad): 2.3→3.55s -0.79→2.36 (E1)
        - [e73] box «Line 8» transform translate(33.94,407.49) rotate(-0.94rad) scale(1,-1) size 3.75×1 | opacity:0.4; background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 33.95→128.8 (E1); y: 2.3→3.55s 407.5→21.91 (E1); rotation(rad): 2.3→3.55s -0.94→2.2 (E1)
        - [e74] box «Line 8» transform translate(44.38,415.06) rotate(-1.09rad) scale(1,-1) size 5.91×1 | opacity:0.4; background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 44.38→118.36 (E1); y: 2.3→3.55s 415.07→14.34 (E1); rotation(rad): 2.3→3.55s -1.1→2.04 (E1)
      - [e75] group «Group 12»
        - [e76] box «Line 8» transform translate(81.87,429.41) rotate(-1.57rad) scale(1,-1) size 17.08×1 | background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 81.87→80.87 (E1); y: 2.3→3.55s 429.41→0 (E1); rotation(rad): 2.3→3.55s -1.57→1.57 (E1)
        - [e77] box «Line 8» transform translate(95.29,426.09) rotate(-1.72rad) scale(1,-1) size 12.72×1 | background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 95.3→67.45 (E1); y: 2.3→3.55s 426.1→3.31 (E1); rotation(rad): 2.3→3.55s -1.73→1.41 (E1)
        - [e78] box «Line 8» transform translate(107.71,420.81) rotate(-1.88rad) scale(1,-1) size 8.39×1 | background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 107.71→55.03 (E1); y: 2.3→3.55s 420.82→8.59 (E1); rotation(rad): 2.3→3.55s -1.88→1.26 (E1)
        - [e79] box «Line 8» transform translate(55.98,421.11) rotate(-1.25rad) scale(1,-1) size 8.38×1 | background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 55.99→106.76 (E1); y: 2.3→3.55s 421.12→8.29 (E1); rotation(rad): 2.3→3.55s -1.26→1.88 (E1)
        - [e80] box «Line 8» transform translate(68.43,426.25) rotate(-1.41rad) scale(1,-1) size 12.72×1 | background-color:#ffffff
            ↳ animation: x: 2.3→3.55s 68.43→94.31 (E1); y: 2.3→3.55s 426.26→3.16 (E1); rotation(rad): 2.3→3.55s -1.41→1.73 (E1)
    - [e81] text pos(46.37,339.89) size 70×13 | white-space:pre; text-align:center; text-box:trim-both cap alphabetic; font-family:'Clash Display Variable'; font-weight:600; font-size:20px; line-height:normal | TEXT in runs: "12.6" {font-family:'Clash Display Variable'; font-weight:600; font-size:20px; line-height:normal; letter-spacing:-0.01em; text-transform:uppercase; color:#ffffff} + "KM" {font-family:'Clash Display Variable'; font-weight:300; font-size:20px; line-height:normal; letter-spacing:-0.01em; text-transform:uppercase; color:#ffffff}
        ↳ animation: y: 2.3→3.55s 339.89→39.89 (E1)
    - [e82] box «Ellipse 3» pos(11.57,267.54) size 139.61×139.61 | border-radius:50%
        ↳ animation: x: 2.3→3.55s 11.57→39.53 (E1); y: 2.3→3.55s 267.54→63.47 (E1); width: 2.3→3.55s 139.61→83.68 (E1); height: 2.3→3.55s 139.61→83.68 (E1)
      - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@d014d61/snows-heroes-frost-neon-fish-boat/bike/assets/img/ed62c75711.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)

#### Layer bk6 «Component 6» — CONTAINED LAYER (contain, anchor x=0, y=1), design box x=404.52 y=655.01 419×96.99
- [e83] box «Component 6» pos(404.52,655.01) size 419×96.99
  - [e84] group «Group 27»
    - [e85] text pos(0,269.99) size 419×67 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Clash Display Variable'; font-weight:700; font-size:100px; line-height:normal; letter-spacing:-0.01em; text-transform:uppercase; color:#ffffff | TEXT: "Zephyr"
        ↳ animation: y: 1.8→3.05s 269.99→29.99 (E1)
    - [e86] text pos(0,160) size 245×11 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Clash Display Variable'; font-weight:400; font-size:17px; line-height:normal; letter-spacing:1.34em; text-transform:uppercase; color:#ffffff | TEXT: "09/vyper"
        ↳ animation: y: 1.8→3.05s 160→0 (E1)
    - [e87] box «Line 9» pos(278.48,165) size 21.25×1 | background-color:#ffffff
        ↳ animation: y: 1.8→3.05s 165→5 (E1); width: 1.8→3.05s 21.26→140.52 (E1)

#### Layer bk7 «Component 4» — CONTAINED LAYER (contain, anchor x=1, y=0), design box x=1067.67 y=200 312×298
- [e88] box «Component 4» pos(1067.67,200) size 312×298
  - [e89] box «Frame 1597882319» pos(0,0) size 312×298
    - [e90] box «Frame 1171275741» pos(0,-40) size 156×149 | opacity:0; border-radius:32px 32px 0px 32px; background-color:rgba(255,255,255,0.00)
        ↳ animation: y: 2.5→3.125s -40→0 (E2); opacity: 2.5→3.125s 0→1 (E2)
      - glass effect (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(0px) saturate(1.25)
      - inner shadow: inset:0; border-radius:inherit; box-shadow:inset 1.2px 0px 0 0 rgba(255,255,255,0.11),inset -1.2px 0px 0 0 rgba(255,255,255,0.03),inset 0 0 24px 0 rgba(255,255,255,0.03)
      - [e91] text pos(25,110) size 43×9 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Clash Display Variable'; font-weight:400; font-size:14px; line-height:1.4; font-feature-settings:'liga' 0,'salt' 1; color:#ffffff | TEXT: "Range"
          ↳ animation: y: 2.5→3.125s 110→90 (E2)
      - [e92] box «Frame 20» pos(23.39,28.5) size 114×27
          ↳ animation: y: 2.5→3.125s 28.5→48.5 (E2)
        - [e93] text pos(0,0) size 53×27 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Clash Display Variable'; font-weight:600; font-size:40px; line-height:1.2; text-transform:uppercase; font-feature-settings:'liga' 0,'salt' 1; color:#ffffff | TEXT: "32"
        - [e94] text pos(52,0) size 62×27 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Clash Display Variable'; font-weight:300; font-size:40px; line-height:1.2; text-transform:uppercase; font-feature-settings:'liga' 0,'salt' 1; color:#ffffff | TEXT: "km"
      - border (stroke): inset:-0.5px; border-radius:32px 32px 0px 32px; padding:1px; background:linear-gradient(-43.63deg, rgba(255,255,255,0.03) 0%, rgba(255,255,255,0.2) 55.25%, rgba(255,255,255,0) 102.57%); 1px gradient border (gradient background clipped with mask: linear-gradient content-box exclude)
    - [e95] box «Frame 1171275742» pos(156,189) size 156×149 | opacity:0; border-radius:0px 32px 32px 32px; background-color:rgba(255,255,255,0.00)
        ↳ animation: y: 2.5→3.125s 189→149 (E2); opacity: 2.5→3.125s 0→1 (E2)
      - glass effect (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(0px) saturate(1.25)
      - inner shadow: inset:0; border-radius:inherit; box-shadow:inset 1.2px 0px 0 0 rgba(255,255,255,0.11),inset -1.2px 0px 0 0 rgba(255,255,255,0.03),inset 0 0 24px 0 rgba(255,255,255,0.03)
      - [e96] text pos(25,110) size 86×9 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Clash Display Variable'; font-weight:400; font-size:14px; line-height:1.4; font-feature-settings:'liga' 0,'salt' 1; color:#ffffff | TEXT: "Charge Time"
          ↳ animation: y: 2.5→3.125s 110→90 (E2)
      - [e97] box «Frame 21» pos(23.39,28.5) size 90×27
          ↳ animation: y: 2.5→3.125s 28.5→48.5 (E2)
        - [e98] text pos(0,0) size 63×27 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Clash Display Variable'; font-weight:600; font-size:40px; line-height:1.2; text-transform:uppercase; font-feature-settings:'liga' 0,'salt' 1; color:#ffffff | TEXT: "3.2 "
        - [e99] text pos(62,0) size 28×27 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Clash Display Variable'; font-weight:300; font-size:40px; line-height:1.2; text-transform:uppercase; font-feature-settings:'liga' 0,'salt' 1; color:#ffffff | TEXT: "H"
      - border (stroke): inset:-0.5px; border-radius:0px 32px 32px 32px; padding:1px; background:linear-gradient(-43.63deg, rgba(255,255,255,0.03) 0%, rgba(255,255,255,0.2) 55.25%, rgba(255,255,255,0) 102.57%); 1px gradient border (gradient background clipped with mask: linear-gradient content-box exclude)

#### Layer bk8 «Component 3» — CONTAINED LAYER (contain, anchor x=-1, y=0), design box x=43 y=186 53×271
- [e100] box «Component 3» pos(43,186) size 53×271
  - [-] list <nav> aria-label="Models"
    - [-] list <ul>
      - [e101] text <li> pos(-130,0) size 28×19 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Clash Display Variable'; font-weight:400; font-size:28px; line-height:1.4; color:#ffffff | TEXT: "" | inline link: the text is the content of an <a href="#"> inside the element
          ↳ animation: x: 0.3→0.925s -130→0 (E2)
      - [e102] text <li> pos(-213,69) size 27×13 | opacity:0.8; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Clash Display Variable'; font-weight:400; font-size:20px; line-height:1.4; color:#ffffff | TEXT: "" | inline link: the text is the content of an <a href="#"> inside the element
          ↳ animation: x: 0.3→0.925s -213→0 (E2)
      - [e103] text <li> pos(-313,132) size 27×13 | opacity:0.6; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Clash Display Variable'; font-weight:400; font-size:20px; line-height:1.4; color:#ffffff | TEXT: "" | inline link: the text is the content of an <a href="#"> inside the element
          ↳ animation: x: 0.3→0.925s -313→0 (E2)
      - [e104] text <li> pos(-373,195) size 28×13 | opacity:0.4; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Clash Display Variable'; font-weight:400; font-size:20px; line-height:1.4; color:#ffffff | TEXT: "" | inline link: the text is the content of an <a href="#"> inside the element
          ↳ animation: x: 0.3→0.925s -373→0 (E2)
      - [e105] text <li> pos(-453,258) size 27×13 | opacity:0.2; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Clash Display Variable'; font-weight:400; font-size:20px; line-height:1.4; color:#ffffff | TEXT: "" | inline link: the text is the content of an <a href="#"> inside the element
          ↳ animation: x: 0.3→0.925s -453→0 (E2)

#### Layer bk9 «Component 1» — CONTAINED LAYER (contain, anchor x=-1, y=-1), design box x=43 y=40 150.53×21.76
- [e106] box «Component 1» pos(43,40) size 150.53×21.76 | overflow:hidden
  - [e107] group <a href="#"> «Group 11»
    - [e108] text pos(63.53,-62.65) size 87×13 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Cindie Mono'; font-weight:400; font-size:12.7px; line-height:normal; letter-spacing:-0.05em; color:#ffffff | TEXT: "Cycle"
        ↳ animation: y: 0.3→0.925s -62.65→7.35 (E2)
    - [e109] vector «Union» pos(0,-30) size 43.8×21.76
        ↳ animation: y: 0.3→0.925s -30→0 (E2)
      - vector drawing SVG-8 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box

## 10. Final adjustments
### Hover states (only when `matchMedia("(hover: hover) and (pointer: fine)")` matches; no hover on touch)
- Menu and text links (subtle colour change): [e22] «Collection», [e24] «Designs», [e26] «Patterns», [e101] «01», [e102] «02», [e103] «03», [e104] «04», [e105] «05». On pointer enter or focus, for the link and each of its descendants read its computed colour c (skip it if its alpha is 0, e.g. gradient text) and store the variable `--hvc` = rgb(c + (#8a8a8a − c) × 0.45) per channel (alpha 1); add the class `hv-tr` and, on the next frame, `is-hv`. On leave (or blur) remove `is-hv` and, 420 ms later, `hv-tr`.
- Buttons (subtle background change): [e28] «Contact» → layer in the link itself with background rgba(255,255,255,.16). In each one insert `<i class="hvo" aria-hidden="true">` inside the given box, right after its fill/effect layers (`i.pl`, `i.bb`, `i.gl`, `i.is`) and before its content, with that background colour; on pointer enter or focus the link adds `is-hv` to that layer and removes it on leave or blur.
- Icon or logo links without a surface of their own (drop to 0.72 opacity on hover): [e10] «View details», [e15] «Search», [e107] «Cycle». They get the class `hv-ic`.
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
- Additional CSS, literal (selectors use the layer identifiers bkN and element identifiers eN from section 9; `html.is-m` = mobile view):
```css
#stage::after{content:'';position:absolute;z-index:60;pointer-events:none;inset:calc(10px * var(--s,1) + .5px);border-radius:calc(30px * var(--s,1) - .5px);box-shadow:0 0 0 2000px #fff}
html.is-m #stage::after{inset:calc(10px * var(--s,1) + 2px);border-radius:calc(30px * var(--s,1) - 2px)}
#e34{overflow:visible!important;clip-path:inset(-4px 0 -14px 0)}
html.is-m #e20::after{content:'';position:absolute;left:176px;top:74.5px;width:60px;height:46px;pointer-events:none;background:radial-gradient(circle at 0 100%,transparent 45px,#fff 45.7px)}
#e90,#e95{-webkit-backdrop-filter:blur(20px) saturate(1.25);backdrop-filter:blur(20px) saturate(1.25)}#e90>.gl,#e95>.gl{display:none}
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
- layer «capcut-edit 1»: fx=0.5, fy=0.5
- layer «Component 1»: x=26, y=30, z=1, ax=-1, ay=-1
- layer «Component 2»: x=-333.8, y=9.8, z=1, ax=1, ay=-1
- layer «Component 3»: x=26, y=140, z=0.8, ax=-1
- layer «Component 4»: x=150, y=122, z=0.7, ax=1
- layer «Component 8»: x=256, y=344, z=0.66, ax=1
- layer «Component 6»: x=26, y=470, z=0.78, ax=-1
- layer «Component 7»: x=26, y=566, z=1, ax=-1
- layer «Component 5»: x=10, y=540, z=0.82, ay=1, ax=-1
Unlisted non-bleed layers are hidden on mobile.
Per-element mobile adjustments:
- [e22] «Frame 5»: hide
- [e24] «Frame 6»: hide
- [e26] «Frame 7»: hide
- [e14] «Rectangle 2»: hide
- [e15] «MagnifyingGlass»: hide
- [e20] «Rectangle 1»: shift dx=399.4, dy=0 (design units of its layer)
- [e37] «Group 15»: multiply its animated offsets by [1,0.3]
- [e84] «Group 27»: 

## 12. Semantics and accessibility
- `<main class="hero" aria-label="Zephyr — The Future of Cycling">`; the loader has `aria-hidden="true"`.
- Lists with `<ul>` and `<li>`; menus inside `<nav aria-label="…">`. Each `<a href="#">` uses the given `aria-label` when its content is not text. If a link covers a whole box, it goes inside it as an absolute `<a class="lk">` covering it (`inset:0`).
- Decorative images with `alt=""`; decorative SVGs with `aria-hidden="true"`.
- Only texts and links receive pointer events (CSS in section 3) and invisible elements get `visibility:hidden`, so nothing that cannot be seen can ever be clicked.
- Visible focus: `outline: 2px solid currentColor; outline-offset: 3px`.

## 13. Resources (absolute URLs)
All resources are served from a CDN with CORS enabled; use these URLs exactly as given:
- `assets/fonts/ClashDisplay-Variable.woff2` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@d014d61/snows-heroes-frost-neon-fish-boat/bike/assets/fonts/ClashDisplay-Variable.woff2
- `assets/fonts/cindiemono-d.woff2` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@d014d61/snows-heroes-frost-neon-fish-boat/bike/assets/fonts/cindiemono-d.woff2
- `assets/img/ed62c75711.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@d014d61/snows-heroes-frost-neon-fish-boat/bike/assets/img/ed62c75711.webp
- `assets/video/72f339ca58.jpg` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@d014d61/snows-heroes-frost-neon-fish-boat/bike/assets/video/72f339ca58.jpg
- `assets/video/72f339ca58.mp4` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@d014d61/snows-heroes-frost-neon-fish-boat/bike/assets/video/72f339ca58.mp4
- `assets/video/8e1e447019.jpg` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@d014d61/snows-heroes-frost-neon-fish-boat/bike/assets/video/8e1e447019.jpg
- `assets/video/8e1e447019.mp4` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@d014d61/snows-heroes-frost-neon-fish-boat/bike/assets/video/8e1e447019.mp4

## 14. Appendix: vector shapes
Each SVG fills 100% of its element's box (`position:absolute; inset:0; width:100%; height:100%; overflow:visible`, `preserveAspectRatio="none"`, `aria-hidden="true"`). If an element says «with fill #xxxxxx (instead of #yyyyyy)», use the same SVG changing that fill color. If an SVG with internal `id`s (masks, gradients) is used more than once, give each copy unique ids and update its `url(#…)` references.

**SVG-1**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 432 359"> <path d="M432 358.854H0V-0.000244141V99.3264C0 137.986 31.3401 169.326 70 169.326H142.679H226.835C255.077 169.326 280.553 186.298 291.432 212.36L334.682 315.966C345.606 342.134 371.237 359.127 399.593 358.999L432 358.854Z" fill="white"></path> </svg>
```

**SVG-2**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 17 16"> <path d="M12.7217 6.3041L16.1882 9.77058V14.9012H12.5767V6.44911L3.13643 15.8894L0.582838 13.3358L10.3076 3.61102L2.73254e-05 3.61171V0.000216815H13.9184L16.472 2.55381L12.7217 6.3041Z" fill="black"></path> </svg>
```

**SVG-3**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 235 64"> <foreignObject height="143.767" width="314.944" x="-40" y="-40"><div style="backdrop-filter:blur(20px);clip-path:url(#i0);height:100%;width:100%"></div></foreignObject><path d="M0 0.000418134H234.944V32.5854C234.944 49.8067 220.983 63.7673 203.762 63.7673H87.8691C66.9565 63.7673 48.0923 51.2003 40.0361 31.9016L36.943 24.492C30.726 9.59907 16.1383 -0.0719304 0 0.000418134Z" data-figma-bg-blur-radius="40" fill="white" fill-opacity="0.1"></path> <defs> <clipPath id="i0" transform="translate(40 40)"><path d="M0 0.000418134H234.944V32.5854C234.944 49.8067 220.983 63.7673 203.762 63.7673H87.8691C66.9565 63.7673 48.0923 51.2003 40.0361 31.9016L36.943 24.492C30.726 9.59907 16.1383 -0.0719304 0 0.000418134Z"></path> </clipPath></defs> </svg>
```

**SVG-4**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 14 14"> <path d="M6.75 13C10.2018 13 13 10.2018 13 6.75C13 3.29822 10.2018 0.5 6.75 0.5C3.29822 0.5 0.5 3.29822 0.5 6.75C0.5 10.2018 3.29822 13 6.75 13Z" stroke="white" stroke-linecap="round" stroke-linejoin="round"></path> </svg>
```

**SVG-5**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 6 6"> <path d="M0.5 0.5L4.83047 4.83047" stroke="white" stroke-linecap="round" stroke-linejoin="round"></path> </svg>
```

**SVG-6**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 621 143"> <path d="M0 0.00702175H620.448V142.945V120.499C620.448 95.6466 600.301 75.4994 575.448 75.4994H563.617H93.1246C74.969 75.4994 58.5917 64.5891 51.5977 47.8347L43.1778 27.6648C36.1555 10.8426 19.678 -0.0811956 1.44914 0.000525206L0 0.00702175Z" fill="white"></path> </svg>
```

**SVG-7**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 13 13"> <path d="M12.2171 1.10996C12.2171 1.10996 12.2171 1.11543 12.2171 1.11816L9.03433 11.6149C8.98615 11.7854 8.88699 11.937 8.75013 12.0496C8.61328 12.1621 8.44529 12.23 8.2687 12.2443C8.24355 12.2465 8.21839 12.2476 8.19324 12.2476C8.02775 12.2481 7.86557 12.2013 7.72584 12.1126C7.58611 12.024 7.47466 11.8972 7.40464 11.7472L5.41402 7.66207C5.3941 7.62113 5.38745 7.57499 5.395 7.5301C5.40255 7.4852 5.42392 7.44377 5.45613 7.4116L8.62363 4.2441C8.70221 4.16138 8.74537 4.05124 8.74391 3.93716C8.74245 3.82308 8.69648 3.71408 8.61581 3.6334C8.53513 3.55273 8.42613 3.50676 8.31205 3.5053C8.19797 3.50384 8.08783 3.547 8.00511 3.62558L4.83597 6.79308C4.8038 6.82529 4.76237 6.84666 4.71747 6.85421C4.67258 6.86176 4.62644 6.85511 4.5855 6.83519L0.496518 4.84511C0.336622 4.7684 0.203811 4.64492 0.115683 4.49102C0.0275555 4.33712 -0.0117277 4.16008 0.00303924 3.98335C0.0178062 3.80662 0.0859261 3.63855 0.198372 3.50141C0.310818 3.36427 0.462281 3.26454 0.632689 3.21543L11.1294 0.0326143H11.1376C11.2871 -0.00937396 11.445 -0.0108459 11.5952 0.0283494C11.7454 0.0675446 11.8825 0.145996 11.9924 0.255654C12.1023 0.365313 12.181 0.50223 12.2205 0.652359C12.26 0.802487 12.2588 0.960422 12.2171 1.10996Z" fill="#020202"></path> </svg>
```

**SVG-8**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 44 22"> <path d="M40.375 8.20801H43.8027L37.5459 21.7598H23.9941L26.9531 15.3506L15.3857 17.7852L13.5508 21.7598H0L4.63477 11.7217L1.04883 10.1104L2.75781 6.30566L6.99219 8.20801H19.8086L17.5664 13.0625L29.1328 10.6289L30.252 8.20801H35.2705L31.1826 2.40332L34.5947 0L40.375 8.20801Z" fill="white"></path> </svg>
```

## 15. Acceptance criteria
- At 1440×810, 1280×720 and 390×844 the composition is identical at the key moments: end of the intro, every pause and the final state.
- Positions, sizes, colors, fonts, timings and curves match this specification.
- All texts are real, editable HTML; all links are clickable `<a href="#">` with visible focus; invisible things cannot be clicked.
- Images, video and fonts load from the URLs in section 13 and there are no console errors.
- The layout adapts when the window is resized and follows section 11 on mobile.
- With `prefers-reduced-motion` the final state is shown directly.
