# Replication prompt — BlueBird

> Complete instructions for an AI coding agent. It contains everything needed to rebuild the animated hero «BlueBird» identically: structure, styles, texts, animation timings and curves, mobile view and links to the resources (images, video and fonts).

## Role and goal
Act as a senior front-end developer specialized in web animation. Build a single-screen page (hero) that exactly reproduces the animated design «BlueBird» described here, without adding or removing anything.
Published reference version: https://bluebird-snows.vercel.app — use it to compare your result visually.

## 1. Overview
Hero for a cybersecurity company («BlueBird — Tech Security»), designed at 1920×1080 with a light, pale-blue look. The background is a light blue gradient with a large blurred glow and, full-bleed, a video of a blue bird that flies in and ends in close-up. Once loading finishes, EVERYTHING plays automatically in 5.805 s with no scrolling needed (the page does not scroll): between 0 and 2.556 s the logo slides in from the left, the menu pills drop in from above in a staggered way and the «Resources» button comes down into place; between 3 and 5.555 s the title «Innovation & security», the text and the «Our Solutions» / «Contact us» buttons slide in from the left, the three glass cards rise from the bottom and the «Active Users +323» badge with avatars slides in from the right. The video plays ONCE from the start of the intro, without looping, and stays on its last frame; it is never paused and never depends on scrolling.

## 2. Deliverable and technical rules
- A single `index.html` file with the CSS inside `<style>` and the JavaScript inside `<script>`. Plain (vanilla) JavaScript, no frameworks and no build step.
- The only external dependency: Lenis 1.3.26 for smooth scrolling: `<script src="https://cdn.jsdelivr.net/npm/lenis@1.3.26/dist/lenis.min.js"></script>` before your script (if it fails to load, everything must work with native scrolling).
- Images, video and fonts are ALWAYS loaded from the absolute URLs in section 13 «Resources». Do not download them, do not inline them as base64 and do not use any other resources.
- Every visible text is real, selectable and editable HTML text (never text turned into an image or into SVG paths). The vector shapes in the Appendix are only icons and decoration.
- Lists use `<ul>`/`<li>` (inside `<nav aria-label="…">` where indicated) and links use clickable `<a href="#">`, with `aria-label` where indicated.
- Head: `<html lang="en">`, `<meta charset="utf-8">`, `<meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover">`, `<title>BlueBird — Tech Security</title>`, `<meta name="description" content="BlueBird: innovation &amp; security. AI that ensures total protection.">`, `<meta name="theme-color" content="#c5dce4">`.
- Respect `prefers-reduced-motion: reduce`: the loader works the same, but there is no intro, no smooth scrolling and no snap; t stays fixed on the final state (t = 5.805 s) even when scrolling.
- Do not add elements, texts, effects or sections that are not in this specification.

## 3. Base structure (common HTML + CSS)
```html
<body>
<div class="loader" id="loader" aria-hidden="true">
  <div class="loader-in"><span>BLUEBIRD</span><div class="loader-bar"><i id="loaderBar"></i></div><span id="loaderPct">000</span></div>
</div>
<main class="hero" id="hero" aria-label="BlueBird — Tech Security">
  <div class="stage" id="stage">
    <!-- one layer (div.blk) per «Layer» in section 9, in the same order: later ones are on top -->
  </div>
</main>
<script>/* engine from section 6 */</script>
</body>
```
```css
:root{--bg:#c5dce4;--ink:#467288;color-scheme:light}
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
- Reference design (frame): **1920×1080 px**. Every px measurement in this document is in units of that design.
- The stage `.stage` fills the whole window (W × H = current width × height of the stage).
- Each **layer** (`div.blk`) is a zero-size absolute container scaled by a factor z (with `zoom: z`, or with `transform: translate(…) scale(z)` and `transform-origin: 0 0`; both are valid as long as the on-screen result is the same). Inside a layer, top-level elements use **frame coordinates**, and the design point (X, Y) is drawn on screen at:
  - **BLEED LAYER (cover)**: z = max(W/1920, H/1080); screen = ((W − 1920·z)/2 + X·z, (H − 1080·z)/2 + Y·z). It always covers the screen (the excess is cropped).
  - **CONTAINED LAYER (contain, anchor ax, ay)**: z = s = min(W/1920, H/1080); mx = (W − 1920·s)/2; my = (H − 1080·s)/2; screen = (mx·(1+ax) + X·s, my·(1+ay) + Y·s). Anchors are −1, 0 or 1: −1 = stuck to the left/top edge, 0 = centered, 1 = stuck to the right/bottom edge.
- The «design box» of each layer (x, y, width×height) is its reference rectangle in the frame (its content may extend beyond it); its top-left corner is used to place the layer in the mobile view.
- Nested elements use local coordinates of their container (left/top relative to the parent).
- Recompute everything on every `resize`.

## 5. Fonts
Declare these fonts with `@font-face{font-family:'…';src:url(URL) format('woff2');font-weight:…;font-style:normal}` and use them with the stack `'Font', system-ui, sans-serif`:
- **Host Grotesk** (variable font, weight range 300–800 (font-weight:300 800)): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/bluebird/assets/fonts/HostGrotesk.woff2

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
  - `E0` = spring with mass=1, stiffness=100, damping=15, normalized to 2.555 s (see formula)
- `cubic-bezier(x1, y1, x2, y2)` curves work as in CSS: given the time progress p, solve the parameter u such that X(u) = p and return Y(u).

Spring formula with mass m, stiffness k, damping c and normalized duration D:
- ω0 = √(k/m), ζ = c / (2·√(k·m)).
- If ζ < 1: ωd = ω0·√(1−ζ²); x(τ) = 1 − e^(−ζ·ω0·τ)·(cos(ωd·τ) + (ζ·ω0/ωd)·sin(ωd·τ)).
- If ζ = 1: x(τ) = 1 − e^(−ω0·τ)·(1 + ω0·τ).
- If ζ > 1: q = √(ζ²−1), r1 = −ω0·(ζ−q), r2 = −ω0·(ζ+q); x(τ) = 1 + (r2·e^(r1·τ) − r1·e^(r2·τ)) / (r1 − r2).
- easing(p) = x(p·D) / x(D) for 0 < p < 1; 0 if p ≤ 0; 1 if p ≥ 1.

## 7. Playback (intro and scrolling)
- Total timeline duration: 5.805 s. This hero **autoplays**: there is no scrolling. `.hero` is exactly 100vh tall (track = 0) and the page does not scroll.
- While loading: `html.is-locked` (overflow hidden), `history.scrollRestoration = 'manual'`, `scrollTo(0,0)`.
- When the loader gets the `is-done` class, the timeline advances in real time from t = 0 to t = 5.805 s (t = elapsed seconds, linear; each property's curve provides the smoothness) and stays on the final state.
- Lenis is loaded and initialized the same way as in the other heroes (`new Lenis({lerp:0.085, wheelMultiplier:0.9, smoothWheel:true})`), but here there is nothing to scroll.
- Video («play once» mode): it is downloaded with `fetch` as a Blob during preloading (section 8) and then `video.src = URL.createObjectURL(blob)` (if that fails, the direct URL). `loop` disabled. At the moment the intro starts: `currentTime = 0` and `play()`. It plays from start to end and stays on the last frame. It is never paused and never synced to scrolling. With `prefers-reduced-motion`, show its last frame directly (`currentTime = duration − 0.05`).

## 8. Loader and preloading
- The loader covers the screen with the `--bg` color and shows «BLUEBIRD», a 160×1 px bar and a 3-digit percentage (`000` → `100`) in the `--ink` color.
- Weighted progress: fonts (`document.fonts.ready`) weight 1; each `<img>` element on the stage weight 0.4, even if it repeats a file (it counts once it has loaded —or failed— and `img.decode()` has finished; the video poster does not count); the video weight 1.5 (its download progress). Percentage = Math.round(progress × 100) with 3 digits. The bar uses `transform: scaleX(progress)` and never goes backwards.
- Wait until everything finishes (14 s maximum; if it runs out, continue without cancelling anything); then set the video `src` (the Blob URL if already downloaded, otherwise the direct URL) and wait for its `loadeddata` or `error` (3 s maximum). Then remove `is-locked` from `<html>` (except in the no-Lenis case described in section 7), add `is-done` to the loader (it fades out in 0.9 s) and start the intro.

## 9. Layers and elements
Notation of each line: `[eN]` = suggested identifier; `box` = div; `text` = div with text (the content in quotes, keeping capitalization, line breaks and spaces); `group` = boxless container; `vector` = box containing an inline SVG; `<a href="#">`, `<ul>`, `<li>`, `<nav>` = semantic tag to use; `«name»` = layer name in the design (for reference only, also handy as `data-name`); `pos(x,y)` = left/top; `size A×B` = width×height; after `|` come the literal CSS styles. Every `text` element gets `class="t"` (used by the pointer-events rule in section 3); boxes, groups and vectors may use `class="b"`, `"g"` and `"v"`. Decorative layers (border, glass, inner shadow, background blur, fill layer) are absolute elements (for example `<i>`) covering their container (given `inset`) with inline `pointer-events:none` and `border-radius:inherit` unless stated otherwise. Images go inside an absolute container `inset:0; overflow:hidden; border-radius:inherit`. The order of the lines is the stacking order (later is on top).

#### Layer bk0 «Fondo» — BLEED LAYER (cover), design box x=0 y=0 1920×1080
- [e1] box «Animation» pos(0,0) size 1920×1080 | background:linear-gradient(180deg, #c5dce4 38.64%, #467389 116%)

#### Layer bk1 «Ellipse 1» — CONTAINED LAYER (contain, anchor x=0, y=1), design box x=232.49 y=481 1418.93×957.2
- [e2] box «Ellipse 1» pos(232.49,481) size 1418.93×957.2 | border-radius:50%; background-color:#ddf6ff; filter:blur(200px)

#### Layer bk2 «freepik_video-upscale_2860829408 1» — BLEED LAYER (cover), design box x=0 y=0 1920×1104
- [e3] box «freepik_video-upscale_2860829408 1» pos(0,-7) size 1920×1104
  - video: `<video muted playsinline preload="auto" poster="https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/bluebird/assets/video/0595b4e124.jpg" data-src="https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/bluebird/assets/video/0595b4e124.mp4">` with position:absolute; inset:0; width:100%; height:100%; object-fit:cover; display:block (the `src` is assigned from JavaScript once preloading finishes, see sections 7 and 8)

#### Layer bk4 «Menu» — CONTAINED LAYER (contain, anchor x=0, y=-1), design box x=60 y=37.92 1797.38×43
- [e4] box «Menu» pos(60,37.92) size 1797.38×43
  - [e5] box <a href="#"> aria-label="BlueBird — Home" «image 5» pos(-250,10.04) size 73.38×22.92
      ↳ animation: x: 0.001→2.556s -250→0 (E0)
    - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/bluebird/assets/img/60247e6cf4.webp: absolutely positioned `<img alt="">` at (0,0), 2464×1856 px, max-width:none, transform-origin 0 0 and transform matrix(0.0474,0,0,0.0474,-19.053,-32.862); the container clips it (overflow hidden, same border-radius)
  - [-] list <nav> aria-label="Main navigation"
    - [-] list <ul>
      - [e6] box <li> «Frame 11» pos(490.5,-300) size 78×43 | border-radius:999px
          ↳ animation: y: 0.001→2.556s -300→0 (E0)
        - [-] link <a href="#"> that covers its container's whole box (inset:0)
          - [e7] text pos(18,12) size 42×19 | opacity:0.8; white-space:pre; text-align:left; font-family:'Host Grotesk'; font-weight:700; font-size:16px; line-height:1.2; color:#577a8b | TEXT: "home"
          - border (stroke): inset:0px; border-radius:999px; border:1px solid rgba(86,122,139,0.2)
      - [e8] box <li> «Frame 12» pos(608.5,-385.17) size 79×43 | border-radius:999px
          ↳ animation: y: 0.001→2.556s -385.17→0 (E0)
        - [-] link <a href="#"> that covers its container's whole box (inset:0)
          - [e9] text pos(18,12) size 43×19 | opacity:0.8; white-space:pre; text-align:left; font-family:'Host Grotesk'; font-weight:400; font-size:16px; line-height:1.2; color:#577a8b | TEXT: "about"
      - [e10] box <li> «Frame 13» pos(727.5,-470.33) size 96×43 | border-radius:999px
          ↳ animation: y: 0.001→2.556s -470.33→0 (E0)
        - [-] link <a href="#"> that covers its container's whole box (inset:0)
          - [e11] text pos(18,12) size 60×19 | opacity:0.8; white-space:pre; text-align:left; font-family:'Host Grotesk'; font-weight:400; font-size:16px; line-height:1.2; color:#577a8b | TEXT: "services"
      - [e12] box <li> «Frame 14» pos(863.5,-555.5) size 107×43 | border-radius:999px
          ↳ animation: y: 0.001→2.556s -555.5→0 (E0)
        - [-] link <a href="#"> that covers its container's whole box (inset:0)
          - [e13] text pos(18,12) size 71×19 | opacity:0.8; white-space:pre; text-align:left; font-family:'Host Grotesk'; font-weight:400; font-size:16px; line-height:1.2; color:#577a8b | TEXT: "Industries"
      - [e14] box <li> «Frame 15» pos(1010.5,-640.67) size 167×43 | border-radius:999px
          ↳ animation: y: 0.001→2.556s -640.67→0 (E0)
        - [-] link <a href="#"> that covers its container's whole box (inset:0)
          - [e15] text pos(18,12) size 131×19 | opacity:0.8; white-space:pre; text-align:left; font-family:'Host Grotesk'; font-weight:400; font-size:16px; line-height:1.2; color:#577a8b | TEXT: "threat Intelligence"
      - [e16] box <li> «Frame 16» pos(1217.5,-725.83) size 92×43 | border-radius:999px
          ↳ animation: y: 0.001→2.556s -725.83→0 (E0)
        - [-] link <a href="#"> that covers its container's whole box (inset:0)
          - [e17] text pos(18,12) size 56×19 | opacity:0.8; white-space:pre; text-align:left; font-family:'Host Grotesk'; font-weight:400; font-size:16px; line-height:1.2; color:#577a8b | TEXT: "contact"
  - [e18] box <a href="#"> «Frame 2» pos(1686.38,-811) size 111×43 | border-radius:999px; background-color:#ffffff
      ↳ animation: y: 0.001→2.556s -811→0 (E0)
    - [e19] text pos(18,12) size 75×19 | opacity:0.8; white-space:pre; text-align:left; font-family:'Host Grotesk'; font-weight:700; font-size:16px; line-height:1.2; color:#577a8b | TEXT: "Resources"

#### Layer bk5 «Content» — CONTAINED LAYER (contain, anchor x=0, y=1), design box x=60 y=627.69 1800×392.99
- [e20] box «Content» pos(60,627.69) size 1800×392.99
  - [e21] group «Group 7»
    - [e22] group «Group 1»
      - [e23] text pos(-980,0) size 274×62 | white-space:pre; text-align:right; font-family:'Host Grotesk'; font-weight:400; font-size:62px; line-height:1; letter-spacing:-0.03em; color:#ffffff | TEXT: "Innovation"
          ↳ animation: x: 3→5.555s -980→0 (E0)
      - [e24] text pos(-776.51,62) size 273×62 | white-space:pre; text-align:right; font-family:'Host Grotesk'; line-height:1 | TEXT in runs: "&" {font-family:'Host Grotesk'; font-weight:400; font-size:62px; line-height:1; letter-spacing:-0.03em; color:rgba(255,255,255,0.33)} + " security" {font-family:'Host Grotesk'; font-weight:400; font-size:62px; line-height:1; letter-spacing:-0.03em; color:#ffffff}
          ↳ animation: x: 3→5.555s -776.51→63.49 (E0)
      - [e25] text pos(-606.51,156) size 300×38 | opacity:0.8; white-space:pre-wrap; text-align:left; font-family:'Host Grotesk'; font-weight:400; font-size:16px; line-height:1.2; color:#ffffff | TEXT: "We are at the forefront of merging cutting-edge technology"
          ↳ animation: x: 3→5.555s -606.51→63.49 (E0)
      - [e26] group «Group 8»
        - [e27] box <a href="#"> «Frame 1» pos(-516.51,254) size 239.97×71.97 | border-radius:999px; background:linear-gradient(81.63deg, #0172f8 8.71%, #019dcf 111.04%)
            ↳ animation: x: 3→5.555s -516.51→63.49 (E0)
          - [e28] text pos(24,23.48) size 124×25 | white-space:pre; text-align:left; font-family:'Host Grotesk'; font-weight:500; font-size:21px; line-height:1.2; letter-spacing:-0.02em; color:#ffffff | TEXT: "Our Solutions"
          - [e29] group «Group 2»
            - [e30] box «Ellipse 1» pos(180,12) size 47.97×47.97 | border-radius:50%; background-color:#ffffff
            - [e31] box «CaretRight» pos(193.92,25.92) size 20.12×20.12
              - [e33] vector «Vector» pos(6.79,3.02) size 7.8×14.09
                - vector drawing SVG-1 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
        - [e34] box <a href="#"> «Frame 2» pos(-212.54,255.49) size 149×69 | border-radius:999px; background-color:#ffffff
            ↳ animation: x: 3→5.555s -212.54→317.46 (E0)
          - [e35] text pos(24,22) size 101×25 | white-space:pre; text-align:left; font-family:'Host Grotesk'; font-weight:500; font-size:21px; line-height:1.2; letter-spacing:-0.02em; color:#567a8b | TEXT: "Contact us"
    - [e36] group «Group 2»
      - [e37] vector «Union» pos(1079.82,919.66) size 300×322.65
          ↳ animation: y: 3→5.555s 919.66→69.66 (E0)
        - vector drawing SVG-2 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e38] text pos(1113.79,1104.56) size 174.99×68 | white-space:pre-wrap; text-align:left; font-family:'Host Grotesk'; font-weight:400; font-size:28px; line-height:1.2; letter-spacing:-0.03em; color:#567a8b | TEXT: "Integrated AI Agent"
          ↳ animation: y: 3→5.555s 1104.56→204.56 (E0)
      - [e39] text pos(1113.79,1221.12) size 180.43×57 | opacity:0.8; white-space:pre-wrap; text-align:left; font-family:'Host Grotesk'; font-weight:400; font-size:16px; line-height:1.2; color:#567a8b | TEXT: "Integrated AI agent for personalized client experiences."
          ↳ animation: y: 3→5.555s 1221.12→291.12 (E0)
      - [e40] group «Group 5»
        - [e41] box «image 8» pos(1126.89,911.7) size 88.69×88.76 | opacity:0.5; filter:blur(16.35px)
            ↳ animation: y: 3→5.555s 911.7→111.7 (E0)
          - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/bluebird/assets/img/9d14b49773-439242.webp: absolutely positioned `<img alt="">` at (0,0), 1856×2464 px, max-width:none, transform-origin 0 0 and transform matrix(0.0689,0,0,0.0689,-19.613,-40.53); the container clips it (overflow hidden, same border-radius)
        - [e42] box «image 7» transform translate(1092.08,954.34) rotate(-1.06rad) size 97.18×97.25
            ↳ animation: x: 3→5.555s 1092.09→1109.54 (E0); y: 3→5.555s 954.34→86.64 (E0); rotation(rad): 3→5.555s -1.07→0 (E0)
          - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/bluebird/assets/img/9d14b49773-2d22da.webp: absolutely positioned `<img alt="">` at (0,0), 1856×2464 px, max-width:none, transform-origin 0 0 and transform matrix(0.0755,0,0,0.0755,-21.49,-44.409); the container clips it (overflow hidden, same border-radius)
      - [e43] group <a href="#"> aria-label="Integrated AI Agent" «Group 2»
        - [e44] box «Ellipse 1» transform translate(1309.88,1047.02) rotate(-0.78rad) size 47.97×47.97 | opacity:0.5; border-radius:50%; background-color:rgba(255,255,255,0.45); box-shadow:0px 11px 17px 0px rgba(105,144,163,0.34)
            ↳ animation: x: 3→5.555s 1309.88→1319.82 (E0); y: 3→5.555s 1047.02→173.04 (E0); rotation(rad): 3→5.555s -0.79→0 (E0)
        - [e45] box «ArrowUpRight» transform translate(1329.57,1047.02) rotate(-0.78rad) size 20.12×20.12
            ↳ animation: x: 3→5.555s 1329.57→1333.74 (E0); y: 3→5.555s 1047.02→186.96 (E0); rotation(rad): 3→5.555s -0.79→0 (E0)
          - [e47] vector «Vector» pos(4.28,4.27) size 11.57×11.57
            - vector drawing SVG-3 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
          - [e48] vector «Vector» pos(6.16,4.27) size 9.69×9.69
            - vector drawing SVG-4 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
    - [e49] box «Frame 5» pos(1404,1393) size 396×230 | border-radius:40px; overflow:hidden; background:linear-gradient(58.13deg, rgba(255,255,255,0.2) 13.25%, rgba(129,163,180,0.2) 76.88%)
        ↳ animation: y: 3→5.555s 1393→162.99 (E0)
      - glass effect (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(9.5px) saturate(1.25)
      - inner shadow: inset:0; border-radius:inherit; box-shadow:inset 0.84px -0.84px 0 0 rgba(255,255,255,0.44),inset -0.84px 0.84px 0 0 rgba(255,255,255,0.14),inset 0 0 7.92px 0 rgba(255,255,255,0.14)
      - [e50] group «Group 3»
        - [e51] text pos(24.94,118.7) size 84×90 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Host Grotesk'; font-weight:300; font-size:127.98px; line-height:1.2; letter-spacing:-0.03em; color:#ffffff | TEXT: "4"
            ↳ animation: y: 3→5.555s 118.7→58.7 (E0)
        - [e52] box «Frame 6» pos(93.58,86.7) size 83.83×102.38 | overflow:hidden
            ↳ animation: y: 3→5.555s 86.7→26.7 (E0)
          - [e53] text pos(-0.32,112) size 84×90 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Host Grotesk'; font-weight:300; font-size:127.98px; line-height:1.2; letter-spacing:-0.03em; color:#ffffff | TEXT: "2"
              ↳ animation: y: 3→5.555s 112→31.99 (E0)
        - [e54] text pos(168,144.29) size 55×45 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Host Grotesk'; font-weight:300; font-size:63.99px; line-height:1.2; letter-spacing:-0.03em; color:#ffffff | TEXT: "%"
            ↳ animation: y: 3→5.555s 144.29→84.29 (E0)
      - [e55] text pos(101.47,235.3) size 261.67×38 | white-space:pre-wrap; text-align:left; font-family:'Host Grotesk'; font-weight:400; font-size:16px; line-height:1.2; color:#ffffff | TEXT: "Join us in redefining the future of security with innovative solutions"
          ↳ animation: y: 3→5.555s 235.3→155.3 (E0)
      - [e56] group <a href="#"> aria-label="Join us" «Group 2»
        - [e57] box «Ellipse 1» transform translate(324.18,34.71) rotate(-0.78rad) size 47.97×47.97 | opacity:0.1; border-radius:50%; background-color:#ffffff
            ↳ animation: x: 3→5.555s 324.19→334.12 (E0); y: 3→5.555s 34.71→10.73 (E0); rotation(rad): 3→5.555s -0.79→0 (E0)
        - [e58] box «ArrowUpRight» transform translate(343.87,34.71) rotate(-0.78rad) size 20.12×20.12
            ↳ animation: x: 3→5.555s 343.87→348.04 (E0); y: 3→5.555s 34.71→24.65 (E0); rotation(rad): 3→5.555s -0.79→0 (E0)
          - [e60] vector «Vector» pos(4.28,4.27) size 11.57×11.57
            - vector drawing SVG-5 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
          - [e61] vector «Vector» pos(6.17,4.27) size 9.69×9.69
            - vector drawing SVG-6 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - border (stroke): inset:0px; border-radius:40px; padding:1px; background:linear-gradient(116.55deg, rgba(255,255,255,0.21) 18.57%, rgba(255,255,255,0.85) 49.14%, rgba(255,255,255,0.2) 100%); 1px gradient border (gradient background clipped with mask: linear-gradient content-box exclude)
    - [e62] box «Frame 7» pos(660,592.31) size 396×230 | border-radius:40px; overflow:hidden; background:linear-gradient(58.13deg, rgba(255,255,255,0.2) 13.25%, rgba(129,163,180,0.2) 76.88%)
        ↳ animation: y: 3→5.555s 592.31→162.31 (E0)
      - glass effect (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(9.5px) saturate(1.25)
      - inner shadow: inset:0; border-radius:inherit; box-shadow:inset 0.84px -0.84px 0 0 rgba(255,255,255,0.44),inset -0.84px 0.84px 0 0 rgba(255,255,255,0.14),inset 0 0 7.92px 0 rgba(255,255,255,0.14)
      - [e63] group «Group 4»
        - [e64] group «Mask group»
          - [-] mask transform matrix(0.954,-0.296,0.296,0.954,-212.164,56.106) size 182.06×196.77 | overflow:hidden; border-radius:0px; mask-image:radial-gradient(99.54px 45.34px at 95.61px 151.42px, #ffffff 0%, rgba(153,153,153,0) 100%); mix-blend-mode:multiply
            - compensation container transform matrix(0.954,0.296,-0.296,0.954,219.256,9.401)
              - [e65] box «Rectangle 3» transform translate(-212.16,56.10) rotate(-0.30rad) size 182.06×196.77 | mix-blend-mode:multiply
                  ↳ animation: x: 3→5.555s -212.16→-7.06 (E0); y: 3→5.555s 56.11→24.65 (E0); rotation(rad): 3→5.555s -0.3→0 (E0)
                - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/bluebird/assets/img/4dacaf9e88-65183b.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)
        - [e66] box «Rectangle 5» transform translate(-212.16,56.10) rotate(-0.30rad) size 182.06×196.77
            ↳ animation: x: 3→5.555s -212.16→-7.06 (E0); y: 3→5.555s 56.11→24.65 (E0); rotation(rad): 3→5.555s -0.3→0 (E0)
          - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/bluebird/assets/img/f06877bf9f-59b33d.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)
      - [e67] box «Frame 8» pos(175,237.38) size 123×29 | border-radius:40px; background-color:rgba(255,255,255,0.2)
          ↳ animation: y: 3→5.555s 237.38→57.38 (E0)
        - [e68] text pos(10,6) size 103×17 | white-space:pre; text-align:left; font-family:'Host Grotesk'; font-weight:400; font-size:14px; line-height:1.2; color:#ffffff | TEXT: "Perfect Security "
        - border (stroke): inset:0px; border-radius:40px; padding:1px; background:linear-gradient(101.46deg, rgba(255,255,255,0.21) 16.46%, rgba(255,255,255,0.85) 47.83%, rgba(255,255,255,0.2) 100%); 1px gradient border (gradient background clipped with mask: linear-gradient content-box exclude)
      - [e69] text pos(175,304.62) size 190×68 | white-space:pre-wrap; text-align:left; font-family:'Host Grotesk'; font-weight:400; font-size:28px; line-height:1.2; letter-spacing:-0.03em; color:#ffffff | TEXT: "AI ensures total protection"
          ↳ animation: y: 3→5.555s 304.62→104.62 (E0)
      - [e70] group <a href="#"> aria-label="AI ensures total protection" «Group 2»
        - [e71] box «Ellipse 1» transform translate(325.07,34.71) rotate(-0.78rad) size 47.97×47.97 | opacity:0.1; border-radius:50%; background-color:#ffffff
            ↳ animation: x: 3→5.555s 325.07→335.01 (E0); y: 3→5.555s 34.71→10.73 (E0); rotation(rad): 3→5.555s -0.79→0 (E0)
        - [e72] box «ArrowUpRight» transform translate(344.76,34.71) rotate(-0.78rad) size 20.12×20.12
            ↳ animation: x: 3→5.555s 344.76→348.93 (E0); y: 3→5.555s 34.71→24.65 (E0); rotation(rad): 3→5.555s -0.79→0 (E0)
          - [e74] vector «Vector» pos(4.28,4.27) size 11.57×11.57
            - vector drawing SVG-5 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
          - [e75] vector «Vector» pos(6.17,4.27) size 9.69×9.69
            - vector drawing SVG-6 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - border (stroke): inset:0px; border-radius:40px; padding:1px; background:linear-gradient(116.55deg, rgba(255,255,255,0.21) 18.57%, rgba(255,255,255,0.85) 49.14%, rgba(255,255,255,0.2) 100%); 1px gradient border (gradient background clipped with mask: linear-gradient content-box exclude)
    - [e76] box «Frame 10» pos(2217,30) size 234×46 | border-radius:4px 40px 40px 4px; background:linear-gradient(78.12deg, rgba(255,255,255,0.2) 14.62%, rgba(129,163,180,0.2) 77.26%)
        ↳ animation: x: 3→5.555s 2217→1566 (E0)
      - glass effect (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(9.5px) saturate(1.25)
      - inner shadow: inset:0; border-radius:inherit; box-shadow:inset 0.84px -0.84px 0 0 rgba(255,255,255,0.44),inset -0.84px 0.84px 0 0 rgba(255,255,255,0.14),inset 0 0 7.92px 0 rgba(255,255,255,0.14)
      - [e77] text pos(24,11) size 107×24 | opacity:0.6; white-space:pre; text-align:left; font-family:'Host Grotesk'; font-weight:400; font-size:20px; line-height:1.2; letter-spacing:-0.03em; color:#ffffff | TEXT: "Active Users"
      - [e78] text pos(141,6) size 69×34 | white-space:pre; text-align:left; font-family:'Host Grotesk'; font-weight:500; font-size:28px; line-height:1.2; letter-spacing:-0.03em; color:#ffffff | TEXT: "+323"
      - border (stroke): inset:0px; border-radius:4px 40px 40px 4px; padding:1px; background:linear-gradient(99.6deg, rgba(255,255,255,0.21) 16.3%, rgba(255,255,255,0.85) 47.73%, rgba(255,255,255,0.2) 100%); 1px gradient border (gradient background clipped with mask: linear-gradient content-box exclude)
    - [e79] group «Group 6»
      - [e80] box «Ellipse 1» pos(2082.76,20.64) size 64.73×64.73 | border-radius:50%
          ↳ animation: x: 3→5.555s 2082.76→1393.49 (E0)
        - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/bluebird/assets/img/81fce8013c.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)
        - border (stroke): inset:0px; border-radius:50%; border:0.89px solid #ffffff
      - [e81] box «Ellipse 2» pos(2211,20.64) size 64.73×64.73 | border-radius:50%
          ↳ animation: x: 3→5.555s 2211→1434.48 (E0)
        - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/bluebird/assets/img/4a0bc518bb.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)
        - border (stroke): inset:0px; border-radius:50%; border:0.89px solid #ffffff
      - [e82] box «Ellipse 3» pos(2339.23,20.64) size 64.73×64.73 | border-radius:50%
          ↳ animation: x: 3→5.555s 2339.23→1475.48 (E0)
        - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/bluebird/assets/img/2c63754389.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)
        - border (stroke): inset:0px; border-radius:50%; border:0.89px solid #ffffff
      - [e83] box «Ellipse 4» pos(2467.47,20.64) size 64.73×64.73 | border-radius:50%
          ↳ animation: x: 3→5.555s 2467.47→1516.47 (E0)
        - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/bluebird/assets/img/8c96ba50fb.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)
        - border (stroke): inset:0px; border-radius:50%; border:0.89px solid #ffffff

## 10. Final adjustments
No additional adjustments: everything is described in the previous sections.

## 11. Mobile view
- It activates when the stage width is < 768 px or the width/height ratio is < 0.82 (add the class `is-m` to `<html>`). Mobile reference design: 390×844 px.
- sm = min(W/390, H/844); ox = (W − 390·sm)/2; oy = (H − 844·sm)/2.
- Listed contained layer with {x, y, z, ax, ay}: its scale is sm·z (z = 1 if not given) and the top-left corner of its «design box» is placed on screen at (ox·(1+ax) + x·sm, oy·(1+ay) + y·sm) (ax, ay = 0 if not given); the rest of its content keeps its position relative to that box.
- Bleed layer: z = max(W/1920, H/1080)·(given z or 1); screen = ((W − 1920·z)·fx + X·z, (H − 1080·z)·fy + Y·z), with fx, fy = 0.5 unless another value is given.
- Contained layers that are not in the list are hidden on mobile.
- Per-element adjustments: «hide» = `display:none`; «shift dx, dy» = move the element by that distance in px of its container (if there is also «scale ×z», it is scaled from its top-left corner); «multiply its animated offsets by [kx, ky]» = multiply the total value of offsetX by kx and of offsetY by ky (the mobile «shift dx, dy» is not multiplied); «fade out between t1 and t2» = multiply its opacity by clamp((t2 − t)/(t2 − t1), 0, 1) from t1 on; «place the element point (rx, ry) at (mx, my) of the mobile design at scale e×sm» = draw the element at scale Se = e·sm with its top-left corner (animated x, y) at (ox + mx·sm + (x − rx)·Se, oy + my·sm + (y − ry)·Se); «width» = fixed width in px; «transition … between t1 and t2» = go from its layer's normal placement (the mobile one) to that placement with smoothstep interpolation (u²·(3 − 2u)) of scale and origin between those times.
- On mobile the track uses 48 vh per second (see section 7).
Mobile layer placement:
- layer «Menu»: x=20, y=22, z=0.86
- layer «Content»: x=20, y=567, z=0.74, ay=1
- layer «Ellipse 1»: x=-230, y=470, z=0.6, ay=1
- layer «freepik_video-upscale_2860829408 1»: fx=0.6
Unlisted non-bleed layers are hidden on mobile.
Per-element mobile adjustments:
- [e6] «Frame 11»: hide
- [e8] «Frame 12»: hide
- [e10] «Frame 13»: hide
- [e12] «Frame 14»: hide
- [e14] «Frame 15»: hide
- [e16] «Frame 16»: hide
- [e18] «Frame 2»: shift dx=-1390.4, dy=0 (design units of its layer)
- [e62] «Frame 7»: hide
- [e36] «Group 2»: hide
- [e49] «Frame 5»: hide
- [e76] «Frame 10»: shift dx=-1393, dy=-113.6 (design units of its layer)
- [e79] «Group 6»: shift dx=-1393, dy=-113.6 (design units of its layer)

## 12. Semantics and accessibility
- `<main class="hero" aria-label="BlueBird — Tech Security">`; the loader has `aria-hidden="true"`.
- Lists with `<ul>` and `<li>`; menus inside `<nav aria-label="…">`. Each `<a href="#">` uses the given `aria-label` when its content is not text. If a link covers a whole box, it goes inside it as an absolute `<a class="lk">` covering it (`inset:0`).
- Decorative images with `alt=""`; decorative SVGs with `aria-hidden="true"`.
- Only texts and links receive pointer events (CSS in section 3) and invisible elements get `visibility:hidden`, so nothing that cannot be seen can ever be clicked.
- Visible focus: `outline: 2px solid currentColor; outline-offset: 3px`.

## 13. Resources (absolute URLs)
All resources are hosted in the public repository `danielsnows/F4M-Bluebird` and served by jsDelivr (CDN with CORS enabled):
- `assets/fonts/HostGrotesk.woff2` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/bluebird/assets/fonts/HostGrotesk.woff2
- `assets/img/2c63754389.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/bluebird/assets/img/2c63754389.webp
- `assets/img/4a0bc518bb.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/bluebird/assets/img/4a0bc518bb.webp
- `assets/img/4dacaf9e88-65183b.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/bluebird/assets/img/4dacaf9e88-65183b.webp
- `assets/img/60247e6cf4.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/bluebird/assets/img/60247e6cf4.webp
- `assets/img/81fce8013c.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/bluebird/assets/img/81fce8013c.webp
- `assets/img/8c96ba50fb.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/bluebird/assets/img/8c96ba50fb.webp
- `assets/img/9d14b49773-2d22da.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/bluebird/assets/img/9d14b49773-2d22da.webp
- `assets/img/9d14b49773-439242.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/bluebird/assets/img/9d14b49773-439242.webp
- `assets/img/f06877bf9f-59b33d.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/bluebird/assets/img/f06877bf9f-59b33d.webp
- `assets/video/0595b4e124.jpg` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/bluebird/assets/video/0595b4e124.jpg
- `assets/video/0595b4e124.mp4` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/bluebird/assets/video/0595b4e124.mp4

## 14. Appendix: vector shapes
Each SVG fills 100% of its element's box (`position:absolute; inset:0; width:100%; height:100%; overflow:visible`, `preserveAspectRatio="none"`, `aria-hidden="true"`). If an element says «with fill #xxxxxx (instead of #yyyyyy)», use the same SVG changing that fill color. If an SVG with internal `id`s (masks, gradients) is used more than once, give each copy unique ids and update its `url(#…)` references.

**SVG-1**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 8 15" xmlns="http://www.w3.org/2000/svg"> <path d="M0.754639 0.754639L7.0437 7.0437L0.754639 13.3328" stroke="#0195D7" stroke-linecap="round" stroke-linejoin="round" stroke-width="1.50938"></path> </svg>
```

**SVG-2**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 300 323" xmlns="http://www.w3.org/2000/svg"> <mask fill="white" id="path-1-inside-1_9001_764"> <path d="M159.36 59.0078C163.976 76.5919 179.869 88.8516 198.049 88.8516H260C282.091 88.8516 300 106.76 300 128.852V282.646C300 304.738 282.091 322.646 260 322.646H40C17.9086 322.646 0 304.738 0 282.646V40C0 17.9086 17.9086 0 40 0H113.015C131.195 0 147.088 12.2597 151.704 29.8438L159.36 59.0078Z"></path> </mask> <path d="M159.36 59.0078C163.976 76.5919 179.869 88.8516 198.049 88.8516H260C282.091 88.8516 300 106.76 300 128.852V282.646C300 304.738 282.091 322.646 260 322.646H40C17.9086 322.646 0 304.738 0 282.646V40C0 17.9086 17.9086 0 40 0H113.015C131.195 0 147.088 12.2597 151.704 29.8438L159.36 59.0078Z" fill="url(#paint0_linear_9001_764)"></path> <path d="M159.36 59.0078L158.393 59.2617L159.36 59.0078ZM198.049 88.8516V89.8516H260V88.8516V87.8516H198.049V88.8516ZM300 128.852H299V282.646H300H301V128.852H300ZM260 322.646V321.646H40V322.646V323.646H260V322.646ZM0 282.646H1V40H0H-1V282.646H0ZM40 0V1H113.015V0V-1H40V0ZM151.704 29.8438L150.737 30.0977L158.393 59.2617L159.36 59.0078L160.327 58.7539L152.672 29.5899L151.704 29.8438ZM113.015 0V1C130.741 1 146.237 12.9532 150.737 30.0977L151.704 29.8438L152.672 29.5899C147.94 11.5662 131.65 -1 113.015 -1V0ZM0 40H1C1 18.4609 18.4609 1 40 1V0V-1C17.3563 -1 -1 17.3563 -1 40H0ZM40 322.646V321.646C18.4609 321.646 1 304.186 1 282.646H0H-1C-1 305.29 17.3563 323.646 40 323.646V322.646ZM300 282.646H299C299 304.186 281.539 321.646 260 321.646V322.646V323.646C282.644 323.646 301 305.29 301 282.646H300ZM260 88.8516V89.8516C281.539 89.8516 299 107.312 299 128.852H300H301C301 106.208 282.644 87.8516 260 87.8516V88.8516ZM198.049 88.8516V87.8516C180.324 87.8516 164.828 75.8984 160.327 58.7539L159.36 59.0078L158.393 59.2617C163.124 77.2854 179.415 89.8516 198.049 89.8516V88.8516Z" fill="url(#paint1_linear_9001_764)" mask="url(#path-1-inside-1_9001_764)"></path> <defs> <linearGradient gradientUnits="userSpaceOnUse" id="paint0_linear_9001_764" x1="300" x2="83.5781" y1="341.827" y2="125.405"> <stop stop-color="white" stop-opacity="0.2"></stop> <stop offset="1" stop-color="white"></stop> </linearGradient> <linearGradient gradientUnits="userSpaceOnUse" id="paint1_linear_9001_764" x1="59.58" x2="300" y1="289.703" y2="114.75"> <stop stop-color="white" stop-opacity="0.21"></stop> <stop offset="0.37549" stop-color="white" stop-opacity="0.85"></stop> <stop offset="1" stop-color="white" stop-opacity="0.2"></stop> </linearGradient> </defs> </svg>
```

**SVG-3**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 12 12" xmlns="http://www.w3.org/2000/svg"> <path d="M0.754639 10.8171L10.8171 0.754639" stroke="#567A8B" stroke-linecap="round" stroke-linejoin="round" stroke-width="1.50938"></path> </svg>
```

**SVG-4**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 10 10" xmlns="http://www.w3.org/2000/svg"> <path d="M0.754639 0.754639H8.93042V8.93042" stroke="#567A8B" stroke-linecap="round" stroke-linejoin="round" stroke-width="1.50938"></path> </svg>
```

**SVG-5**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 12 12" xmlns="http://www.w3.org/2000/svg"> <path d="M0.754639 10.8171L10.8171 0.754639" stroke="white" stroke-linecap="round" stroke-linejoin="round" stroke-width="1.50938"></path> </svg>
```

**SVG-6**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 10 10" xmlns="http://www.w3.org/2000/svg"> <path d="M0.754639 0.754639H8.93042V8.93042" stroke="white" stroke-linecap="round" stroke-linejoin="round" stroke-width="1.50938"></path> </svg>
```

## 15. Acceptance criteria
- Compared with https://bluebird-snows.vercel.app, the result matches at 1920×1080, 1280×720 and 390×844 at the key moments: end of the intro, every pause and the final state.
- Positions, sizes, colors, fonts, timings and curves match this specification.
- All texts are real, editable HTML; all links are clickable `<a href="#">` with visible focus; invisible things cannot be clicked.
- Images, video and fonts load from the URLs in section 13 and there are no console errors.
- The layout adapts when the window is resized and follows section 11 on mobile.
- With `prefers-reduced-motion` the final state is shown directly.
