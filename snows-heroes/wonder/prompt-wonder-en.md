# Replication prompt — Wonder

> Complete instructions for an AI coding agent. It contains everything needed to rebuild the animated hero «Wonder» identically: structure, styles, texts, animation timings and curves, mobile view and links to the resources (images, video and fonts).

## Role and goal
Act as a senior front-end developer specialized in web animation. Build a single-screen page (hero) that exactly reproduces the animated design «Wonder» described here, without adding or removing anything.
Published reference version: https://wonder-snows.vercel.app — use it to compare your result visually.

## 1. Overview
Hero «Step into Wonder», designed at 1440×810, with no video (only images, texts and shapes). Opening scene: a garden arch with flowers, the title «Step › Into Wonder», a text, cards («Watch Demo», «32 Global Partners»), the slide indicator and a round button with the circular text «Enter Experience». After a 0.8 s wait once the loader is gone, the intro (0 → 2.001 s) expands the stage from a centered frame to full screen. With scrolling, the scene transforms (2.002 → 6.003 s) into a world of clouds with the title «Create Beyond Reality», its subtitle and a ring of 21 cards that then rotates in 4 steps of 15° (up to 14.304 s), with pauses between steps. It also has pointer parallax, a custom cursor and a preview of the transition when hovering the round button.

## 2. Deliverable and technical rules
- A single `index.html` file with the CSS inside `<style>` and the JavaScript inside `<script>`. Plain (vanilla) JavaScript, no frameworks and no build step.
- The only external dependency: Lenis 1.3.26 for smooth scrolling: `<script src="https://cdn.jsdelivr.net/npm/lenis@1.3.26/dist/lenis.min.js"></script>` before your script (if it fails to load, everything must work with native scrolling).
- Images, video and fonts are ALWAYS loaded from the absolute URLs in section 13 «Resources». Do not download them, do not inline them as base64 and do not use any other resources.
- Every visible text is real, selectable and editable HTML text (never text turned into an image or into SVG paths). The vector shapes in the Appendix are only icons and decoration.
- Lists use `<ul>`/`<li>` (inside `<nav aria-label="…">` where indicated) and links use clickable `<a href="#">`, with `aria-label` where indicated.
- Head: `<html lang="en">`, `<meta charset="utf-8">`, `<meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover">`, `<title>Step into Wonder</title>`, `<meta name="description" content="Step into Wonder: designing immersive digital experiences that blur the line between imagination, AI and reality.">`, `<meta name="theme-color" content="#1a1410">`.
- Respect `prefers-reduced-motion: reduce`: the loader works the same, but there is no intro, no smooth scrolling and no snap and no pointer effects; t stays fixed on the final state (t = 14.304 s) even when scrolling.
- Do not add elements, texts, effects or sections that are not in this specification.

## 3. Base structure (common HTML + CSS)
```html
<body>
<div class="loader" id="loader" aria-hidden="true">
  <div class="loader-in"><span>WONDER</span><div class="loader-bar"><i id="loaderBar"></i></div><span id="loaderPct">000</span></div>
</div>
<main class="hero" id="hero" aria-label="Step into Wonder">
  <div class="stage" id="stage">
    <!-- one layer (div.blk) per «Layer» in section 9, in the same order: later ones are on top -->
  </div>
</main>
<script>/* engine from section 6 */</script>
</body>
```
```css
:root{--bg:#1a1410;--ink:#f6e9e4;color-scheme:dark}
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
- **Imprima** (weight 400): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/wonder/assets/fonts/Imprima.woff2
- **Viaoda Libre** (weight 400): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/wonder/assets/fonts/ViaodaLibre.woff2

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
  - `E1` = cubic-bezier(0.4037, -0.0259, 0, 0.9886)
  - `E2` = cubic-bezier(1, -0.0284, 0, 1.0266)
  - `E4` = cubic-bezier(0.76, 0, 0.24, 1)
  - `E5` = cubic-bezier(1, -0.0296, 0, 1.0946)
- `cubic-bezier(x1, y1, x2, y2)` curves work as in CSS: given the time progress p, solve the parameter u such that X(u) = p and return Y(u).

## 7. Playback (intro and scrolling)
- Total timeline duration: 14.304 s. Automatic intro: from t = 0 to t = 2.001 s. The rest (2.001 → 14.304 s) is driven by scrolling.
- `.hero` height = 100vh + track. track = round((14.304 − 2.001) × 40) = **492vh** on desktop and round((14.304 − 2.001) × 36) = **443vh** on mobile (use exactly these values; they are set through the CSS variable `--track`). `.stage` is sticky, so the stage stays fixed while the track is scrolled.
- While loading: `html.is-locked` (overflow hidden), `history.scrollRestoration = 'manual'`, `scrollTo(0,0)`.
- When the loader gets the `is-done` class, wait 0.8 s and play the intro: t advances in real time (linearly) from 0 to 2.001 s in 2.001 s (during the wait, t = 0). During the intro scrolling is blocked (Lenis stopped; if Lenis is not available, keep `is-locked` until the intro ends) and scroll events do not change t. When it ends, t = 2.001 and scrolling is enabled.
- Smooth scrolling with Lenis: `new Lenis({lerp:0.085, wheelMultiplier:0.9, smoothWheel:true})`, `lenis.stop()` until the intro ends, then `lenis.start()`; call `lenis.raf(now)` in the requestAnimationFrame loop. Without Lenis, use native scrolling.
- Scroll → time mapping: p = clamp((scrollY − hero.offsetTop) / (hero.offsetHeight − stage.clientHeight), 0, 1); t = 2.001 + p × (14.304 − 2.001).
- Pauses (holds), in seconds: [2.001 – 2.001], [6.004 – 7.004], [8.004 – 9.004], [10.004 – 11.004], [12.004 – 13.004], [14.004 – 14.304]. Snap (Lenis only; no snap without Lenis): the direction is the sign of the last scroll change; 170 ms after the last scroll event (every event restarts the timer), if t is not inside a pause (with a ±0.02 s margin), scroll with `lenis.scrollTo` to the start of the next pause when scrolling down, or to the end of the previous pause when scrolling up. Duration = clamp(|Δt| × 0.45, 0.6, 1.8) s; easing easeInOutCubic (x < 0.5 ? 4x³ : 1 − (−2x+2)³/2). No snap with `prefers-reduced-motion` or during another snap (the «snapping» state is released in `onComplete` or, as a safety net, after 2.2 s).
- On every resize: recompute layers, track and t from the current scroll position.

## 8. Loader and preloading
- The loader covers the screen with the `--bg` color and shows «WONDER», a 160×1 px bar and a 3-digit percentage (`000` → `100`) in the `--ink` color.
- Weighted progress: fonts (`document.fonts.ready`) weight 1; each `<img>` element on the stage weight 0.4, even if it repeats a file (it counts once it has loaded —or failed— and `img.decode()` has finished; the video poster does not count); there is no video. Percentage = Math.round(progress × 100) with 3 digits. The bar uses `transform: scaleX(progress)` and never goes backwards.
- Wait until everything finishes (14 s maximum; if it runs out, continue without cancelling anything). Then remove `is-locked` from `<html>` (except in the no-Lenis case described in section 7), add `is-done` to the loader (it fades out in 0.9 s) and start the intro.

## 9. Layers and elements
Notation of each line: `[eN]` = suggested identifier; `box` = div; `text` = div with text (the content in quotes, keeping capitalization, line breaks and spaces); `group` = boxless container; `vector` = box containing an inline SVG; `<a href="#">`, `<ul>`, `<li>`, `<nav>` = semantic tag to use; `«name»` = layer name in the design (for reference only, also handy as `data-name`); `pos(x,y)` = left/top; `size A×B` = width×height; after `|` come the literal CSS styles. Every `text` element gets `class="t"` (used by the pointer-events rule in section 3); boxes, groups and vectors may use `class="b"`, `"g"` and `"v"`. Decorative layers (border, glass, inner shadow, background blur, fill layer) are absolute elements (for example `<i>`) covering their container (given `inset`) with inline `pointer-events:none` and `border-radius:inherit` unless stated otherwise. Images go inside an absolute container `inset:0; overflow:hidden; border-radius:inherit`. The order of the lines is the stacking order (later is on top).

#### Layer bk0 «Fondo» — BLEED LAYER (cover), design box x=0 y=0 1440×810
- [e1] box «home» pos(0,0) size 1440×810 | background-color:#454640

#### Layer bk1 «STAGE2» — BLEED LAYER (cover), design box x=0 y=0 1218×685.13
- [e2] box «STAGE2» pos(111,62.44) size 1218×685.12 | overflow:hidden; background-color:#454640
    ↳ animation: x: 0.001→2.001s 111→0 (E4); y: 0.001→2.001s 62.44→0 (E4); width: 0.001→2.001s 1218→1440 (E4); height: 0.001→2.001s 685.13→810 (E4)
  - [e3] group «Group 50»
    - [e4] box «clouds 1» transform translate(-5.43,-17.69) rotate(0rad) size 1228.88×741.8
        ↳ animation: x: 0.001→2.001s -5.44→-6.43 (E4); 2.002→3.002s -6.43→133 (E1); 3.003→6.003s 133→-6.43 (E2); y: 0.001→2.001s -17.7→-20.92 (E4); 2.002→3.002s -20.92→3.24 (E1); 3.003→6.003s 3.24→-20.92 (E2); content-scale: 0.001→2.001s 1→1.18 (E4); 2.002→3.002s 1.18→0.96 (E1); 3.003→6.003s 0.96→1.18 (E2)
      - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/wonder/assets/img/52e4066599.webp: absolutely positioned `<img alt="">` at (0,0), 2880×1738 px, max-width:none, transform-origin 0 0 and transform matrix(0.4266,0,0,0.4267,0,0.157); the container clips it (overflow hidden, same border-radius)
    - [e5] box «Preenchimento generativo 1» pos(-5.44,480.6) size 1228.88×302.72
        ↳ animation: x: 0.001→2.001s -5.44→-6.43 (E4); 2.002→3.002s -6.43→-271.75 (E1); 3.003→6.003s -271.75→-6.43 (E2); y: 0.001→2.001s 480.6→568.2 (E4); 2.002→3.002s 568.2→482.83 (E1); 3.003→6.003s 482.83→568.2 (E2); width: 0.001→2.001s 1228.88→1452.86 (E4); 2.002→3.002s 1452.86→1983.51 (E1); 3.003→6.003s 1983.51→1452.86 (E2); height: 0.001→2.001s 302.72→357.89 (E4); 2.002→3.002s 357.89→488.61 (E1); 3.003→6.003s 488.61→357.89 (E2)
      - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/wonder/assets/img/f4c0464736.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)
  - [e6] box «Rectangle 6» transform translate(0,420.37) rotate(0rad) scale(1,-1) size 1218×420.38 | opacity:0.6; background:linear-gradient(180deg, rgba(12,10,31,0.01) 0%, #0c0a1f 100%)
      ↳ animation: y: 0.001→2.001s 420.38→497 (E4); width: 0.001→2.001s 1218→1440 (E4); height: 0.001→2.001s 420.38→497 (E4)
  - [e7] text transform translate(439.83,6.06) rotate(0rad) size 339×27 | white-space:pre; text-align:center; font-family:'Viaoda Libre'; font-weight:400; font-size:29.15px; line-height:0.94; text-transform:uppercase; font-feature-settings:'ss02' 1; color:#ffffff | TEXT: "Create Beyond Reality"
      ↳ animation: x: 0.001→2.001s 439.83→520 (E4); 2.002→3.002s 520→607 (E1); 3.003→6.003s 607→272.5 (E2); y: 0.001→2.001s 6.06→7.16 (E4); 2.002→3.002s 7.16→311.18 (E1); 3.003→6.003s 311.18→146 (E2); width: 0.001→2.001s 339→338.33 (E4); 2.002→3.002s 338.33→338.27 (E1); 3.003→6.003s 338.27→338.33 (E2); height: 0.001→2.001s 27→27.07 (E4); 3.003→6.003s 27.06→27.22 (E2); opacity: 2.002→3.002s 1→0 (E1); 3.003→6.003s 0→1 (E2); content-scale: 0.001→2.001s 1→1.18 (E4); 2.002→3.002s 1.18→0.67 (E1); 3.003→6.003s 0.67→2.65 (E2)
  - [e8] text transform translate(467.24,40.46) rotate(0rad) size 283.52×22 | white-space:pre-wrap; text-align:center; font-family:'Imprima'; font-weight:400; font-size:9.07px; line-height:1.2; color:#ffffff | TEXT: "Exclusive journeys to breathtaking destinations curated for travelers seeking rare and unforgettable experiences."
      ↳ animation: x: 0.001→2.001s 467.24→552.4 (E4); 2.002→3.002s 552.4→691.16 (E1); 3.003→6.003s 691.16→345 (E2); y: 0.001→2.001s 40.46→47.84 (E4); 2.002→3.002s 47.84→345.6 (E1); 3.003→6.003s 345.6→237 (E2); height: 0.001→2.001s 22→21.99 (E4); 2.002→3.002s 21.99→14.88 (E1); 3.003→6.003s 14.88→21.93 (E2); opacity: 2.002→3.002s 1→0 (E1); 3.003→6.003s 0→1 (E2); content-scale: 0.001→2.001s 1→1.18 (E4); 2.002→3.002s 1.18→0.2 (E1); 3.003→6.003s 0.2→2.65 (E2)
  - [e9] group «Group 59»
      ↳ animation: opacity: 2.002→3.002s 1→0 (E1)
    - [e10] group «Group 56»
        ↳ animation: opacity: 2.002→3.002s 1→0 (E1)
      - [-] list <ul>
        - (list of 7 <li> elements with the SAME inner structure; the first one is fully detailed as the template and the others only state what changes)
        - [e11] box <li> «Frame 1597882305» pos(479.14,-904.1) size 259.93×280.63 | border-radius:46.00px; overflow:hidden; background-color:#c7e9f5
            ↳ animation: x: 0.001→2.001s 479.14→566.47 (E4); y: 0.001→2.001s -904.1→-1068.89 (E4); width: 0.001→2.001s 259.93→307.3 (E4); height: 0.001→2.001s 280.63→331.78 (E4); opacity: 2.002→3.002s 1→0 (E1); radius: 0.001→2.001s 46→54.39 (E4)
          - [-] link <a href="#"> that covers its container's whole box (inset:0)
            - [e12] text transform translate(19.27,102.03) rotate(0rad) size 186.03×70 | white-space:pre-wrap; text-align:left; font-family:'Viaoda Libre'; font-weight:400; font-size:36.80px; line-height:0.94; color:#263237 | TEXT: "Private Retreats"
                ↳ animation: x: 0.001→2.001s 19.28→22.79 (E4); y: 0.001→2.001s 102.04→120.63 (E4); height: 0.001→2.001s 70→69.36 (E4); opacity: 2.002→3.002s 1→0 (E1); content-scale: 0.001→2.001s 1→1.18 (E4)
            - [e13] text transform translate(19.27,190.02) rotate(0rad) size 210.77×66 | opacity:0.6; white-space:pre-wrap; text-align:left; font-family:'Imprima'; font-weight:400; font-size:18.40px; line-height:1.2; color:#263237 | TEXT: "Discover secluded destinations far from crowded tourism."
                ↳ animation: x: 0.001→2.001s 19.28→22.79 (E4); y: 0.001→2.001s 190.03→224.66 (E4); height: 0.001→2.001s 66→65.98 (E4); opacity: 2.002→3.002s 0.6→0 (E1); content-scale: 0.001→2.001s 1→1.18 (E4)
            - [e14] group «Group 44»
                ↳ animation: opacity: 2.002→3.002s 1→0 (E1)
              - [e15] box «Ellipse 13» pos(193.86,13.8) size 52.26×52.26 | opacity:0.2; border-radius:50%; background-color:#263237
                  ↳ animation: x: 0.001→2.001s 193.86→229.2 (E4); y: 0.001→2.001s 13.8→16.32 (E4); width: 0.001→2.001s 52.26→61.79 (E4); height: 0.001→2.001s 52.26→61.79 (E4); opacity: 2.002→3.002s 0.2→0 (E1)
              - [e16] vector «Union» transform matrix(0.647,-0.647,-0.647,-0.647,222.657,49.246) size 12.53×22.59
                  ↳ animation: x: 0.001→2.001s 0→37.61 (E4); y: 0.001→2.001s 0→6.31 (E4); width: 0.001→2.001s 12.53→14.81 (E4); height: 0.001→2.001s 22.59→26.71 (E4); opacity: 2.002→3.002s 1→0 (E1)
                - vector drawing SVG-1 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
        - [e17] <li> card same as the template | position/rotation: see table; size 259.93×280.63 | border-radius:46.00px; overflow:hidden; background-color:#f5ebc7 | content colors (template → this card): #263237 → #373326 | texts: "Curated Adventures" / "Experiences tailored to your lifestyle and preferences." | vector shapes: SVG-1 with fill #f5ebc7 (instead of #c7e9f5) (in the same order as the template)
        - [e23] <li> card same as the template | position/rotation: see table; size 259.93×280.63 | border-radius:46.00px; overflow:hidden; background-color:#cac7f5 | content colors (template → this card): #263237 → #262637 | texts: "Luxury Concierge" / "Dedicated support for every stage of your trip." | vector shapes: SVG-1 with fill #cac7f5 (instead of #c7e9f5) (in the same order as the template)
        - [e29] <li> card same as the template | position/rotation: see table; size 259.93×280.63 | border-radius:46.00px; overflow:hidden; background-color:#c7e9f5 | texts: "Private Retreats" / "Discover secluded destinations far from crowded tourism."
        - [e35] <li> card same as the template | position/rotation: see table; size 259.93×280.63 | border-radius:46.00px; overflow:hidden; background-color:#e2f5c7 | content colors (template → this card): #263237 → #313726 | texts: "Nature Escapes" / "Reconnect with untouched landscapes and silence." | vector shapes: SVG-1 with fill #e2f5c7 (instead of #c7e9f5) (in the same order as the template)
        - [e41] <li> card same as the template | position/rotation: see table; size 259.93×280.63 | border-radius:46.00px; overflow:hidden; background-color:#f5c7c7 | content colors (template → this card): #263237 → #372626 | texts: "Exclusive Access" / "Unlock locations unavailable to the public." | vector shapes: SVG-1 with fill #f5c7c7 (instead of #c7e9f5) (in the same order as the template)
        - [e47] <li> card same as the template | position/rotation: see table; size 259.93×280.63 | border-radius:46.00px; overflow:hidden; background-color:#dec7f5 | content colors (template → this card): #263237 → #2c2637 | texts: "Private Retreats" / "Discover secluded destinations far from crowded tourism." | vector shapes: SVG-1 with fill #dec7f5 (instead of #c7e9f5) (in the same order as the template)
          (The <li> cards above: each card box animates according to the following table; their inner elements animate exactly like the template.)

Card animation table (x, y). Shared segments: T1 = 0.001→2.001s (E4). Each cell is the value at the END of that segment (before the first segment it equals «start»; between segments it holds; if a property does not change in a segment, the value is repeated):

| card | property | start | T1 |
|---|---|---|---|
| e11 | x | 479.14 | 566.47 |
| e11 | y | -904.1 | -1068.89 |
| e17 | x | 800.51 | 946.42 |
| e17 | y | -886.95 | -1048.61 |
| e23 | x | 1106.5 | 1308.18 |
| e23 | y | -787.21 | -930.69 |
| e29 | x | 1376.24 | 1627.09 |
| e29 | y | -611.67 | -723.16 |
| e35 | x | 166.62 | 196.99 |
| e35 | y | -819.68 | -969.08 |
| e41 | x | -113.4 | -134.07 |
| e41 | y | -657.25 | -777.04 |
| e47 | x | -341.83 | -404.14 |
| e47 | y | -427.87 | -505.86 |

The other animated card properties (width, height, opacity, radius) animate EXACTLY like the template.
    - [e53] group «Group 57»
        ↳ animation: opacity: 2.002→3.002s 1→0 (E1)
      - [-] list <ul>
        - (list of 7 <li> elements with the SAME inner structure; the first one is fully detailed as the template and the others only state what changes)
        - [e54] box <li> «Frame 1597882305» transform translate(1746.83,587.46) rotate(1.95rad) size 259.93×280.63 | border-radius:46.00px; overflow:hidden; background-color:#c7e9f5
            ↳ animation: x: 0.001→2.001s 1746.83→2065.22 (E4); y: 0.001→2.001s 587.46→694.54 (E4); width: 0.001→2.001s 259.93→307.3 (E4); height: 0.001→2.001s 280.63→331.78 (E4); opacity: 2.002→3.002s 1→0 (E1); radius: 0.001→2.001s 46→54.39 (E4)
          - [-] link <a href="#"> that covers its container's whole box (inset:0)
            - [e55] text transform translate(19.27,102.03) rotate(0rad) size 186.03×70 | white-space:pre-wrap; text-align:left; font-family:'Viaoda Libre'; font-weight:400; font-size:36.80px; line-height:0.94; color:#263237 | TEXT: "Private Retreats"
                ↳ animation: x: 0.001→2.001s 19.28→22.79 (E4); y: 0.001→2.001s 102.04→120.63 (E4); height: 0.001→2.001s 70→69.36 (E4); opacity: 2.002→3.002s 1→0 (E1); content-scale: 0.001→2.001s 1→1.18 (E4)
            - [e56] text transform translate(19.27,190.02) rotate(0rad) size 210.77×66 | opacity:0.6; white-space:pre-wrap; text-align:left; font-family:'Imprima'; font-weight:400; font-size:18.40px; line-height:1.2; color:#263237 | TEXT: "Discover secluded destinations far from crowded tourism."
                ↳ animation: x: 0.001→2.001s 19.28→22.79 (E4); y: 0.001→2.001s 190.03→224.66 (E4); height: 0.001→2.001s 66→65.98 (E4); opacity: 2.002→3.002s 0.6→0 (E1); content-scale: 0.001→2.001s 1→1.18 (E4)
            - [e57] group «Group 44»
                ↳ animation: opacity: 2.002→3.002s 1→0 (E1)
              - [e58] box «Ellipse 13» transform translate(193.86,13.80) rotate(0rad) size 52.26×52.26 | opacity:0.2; border-radius:50%; background-color:#263237
                  ↳ animation: x: 0.001→2.001s 193.86→229.2 (E4); y: 0.001→2.001s 13.8→16.32 (E4); width: 0.001→2.001s 52.26→61.79 (E4); height: 0.001→2.001s 52.26→61.79 (E4); opacity: 2.002→3.002s 0.2→0 (E1)
              - [e59] vector «Union» transform matrix(0.647,-0.647,-0.647,-0.647,222.654,49.241) size 12.53×22.59
                  ↳ animation: x: 0.001→2.001s 0→37.61 (E4); y: 0.001→2.001s 0→6.31 (E4); width: 0.001→2.001s 12.53→14.81 (E4); height: 0.001→2.001s 22.59→26.71 (E4); opacity: 2.002→3.002s 1→0 (E1)
                - vector drawing SVG-1 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
        - [e60] <li> card same as the template | transform translate(1610.23,878.86) rotate(2.21rad) size 259.93×280.63 | border-radius:46.00px; overflow:hidden; background-color:#f5ebc7 | content colors (template → this card): #263237 → #373326 | texts: "Curated Adventures" / "Experiences tailored to your lifestyle and preferences." | vector shapes: SVG-1 with fill #f5ebc7 (instead of #c7e9f5) (in the same order as the template)
            ↳ card animation: x: 0.001→2.001s 1610.23→1903.72 (E4); y: 0.001→2.001s 878.87→1039.05 (E4); width: 0.001→2.001s 259.93→307.3 (E4); height: 0.001→2.001s 280.63→331.78 (E4); opacity: 2.002→3.002s 1→0 (E1); radius: 0.001→2.001s 46→54.39 (E4) (its inner elements animate exactly like the template)
        - [e66] <li> card same as the template | transform translate(1402.87,1124.98) rotate(2.47rad) size 259.93×280.63 | border-radius:46.00px; overflow:hidden; background-color:#cac7f5 | content colors (template → this card): #263237 → #262637 | texts: "Luxury Concierge" / "Dedicated support for every stage of your trip." | vector shapes: SVG-1 with fill #cac7f5 (instead of #c7e9f5) (in the same order as the template)
            ↳ card animation: x: 0.001→2.001s 1402.87→1658.57 (E4); y: 0.001→2.001s 1124.99→1330.03 (E4); width: 0.001→2.001s 259.93→307.3 (E4); height: 0.001→2.001s 280.63→331.78 (E4); opacity: 2.002→3.002s 1→0 (E1); radius: 0.001→2.001s 46→54.39 (E4) (its inner elements animate exactly like the template)
        - [e72] <li> card same as the template | transform translate(1138.87,1309.05) rotate(2.74rad) size 259.93×280.63 | border-radius:46.00px; overflow:hidden; background-color:#c7e9f5 | texts: "Private Retreats" / "Discover secluded destinations far from crowded tourism."
            ↳ card animation: x: 0.001→2.001s 1138.87→1346.45 (E4); y: 0.001→2.001s 1309.05→1547.65 (E4); width: 0.001→2.001s 259.93→307.3 (E4); height: 0.001→2.001s 280.63→331.78 (E4); opacity: 2.002→3.002s 1→0 (E1); radius: 0.001→2.001s 46→54.39 (E4) (its inner elements animate exactly like the template)
        - [e78] <li> card same as the template | transform translate(1785.96,266.11) rotate(1.69rad) size 259.93×280.63 | border-radius:46.00px; overflow:hidden; background-color:#e2f5c7 | content colors (template → this card): #263237 → #313726 | texts: "Nature Escapes" / "Reconnect with untouched landscapes and silence." | vector shapes: SVG-1 with fill #e2f5c7 (instead of #c7e9f5) (in the same order as the template)
            ↳ card animation: x: 0.001→2.001s 1785.96→2111.48 (E4); y: 0.001→2.001s 266.12→314.62 (E4); width: 0.001→2.001s 259.93→307.3 (E4); height: 0.001→2.001s 280.63→331.78 (E4); opacity: 2.002→3.002s 1→0 (E1); radius: 0.001→2.001s 46→54.39 (E4) (its inner elements animate exactly like the template)
        - [e84] <li> card same as the template | transform translate(1740.59,-54.40) rotate(1.43rad) size 259.93×280.63 | border-radius:46.00px; overflow:hidden; background-color:#f5c7c7 | content colors (template → this card): #263237 → #372626 | texts: "Exclusive Access" / "Unlock locations unavailable to the public." | vector shapes: SVG-1 with fill #f5c7c7 (instead of #c7e9f5) (in the same order as the template)
            ↳ card animation: x: 0.001→2.001s 1740.59→2057.84 (E4); y: 0.001→2.001s -54.41→-64.33 (E4); width: 0.001→2.001s 259.93→307.3 (E4); height: 0.001→2.001s 280.63→331.78 (E4); opacity: 2.002→3.002s 1→0 (E1); radius: 0.001→2.001s 46→54.39 (E4) (its inner elements animate exactly like the template)
        - [e90] <li> card same as the template | transform translate(1613.81,-352.26) rotate(1.17rad) size 259.93×280.63 | border-radius:46.00px; overflow:hidden; background-color:#dec7f5 | content colors (template → this card): #263237 → #2c2637 | texts: "Private Retreats" / "Discover secluded destinations far from crowded tourism." | vector shapes: SVG-1 with fill #dec7f5 (instead of #c7e9f5) (in the same order as the template)
            ↳ card animation: x: 0.001→2.001s 1613.81→1907.95 (E4); y: 0.001→2.001s -352.27→-416.48 (E4); width: 0.001→2.001s 259.93→307.3 (E4); height: 0.001→2.001s 280.63→331.78 (E4); opacity: 2.002→3.002s 1→0 (E1); radius: 0.001→2.001s 46→54.39 (E4) (its inner elements animate exactly like the template)
    - [e96] group «Group 58»
        ↳ animation: opacity: 2.002→3.002s 1→0 (E1)
      - [-] list <ul>
        - (list of 7 <li> elements with the SAME inner structure; the first one is fully detailed as the template and the others only state what changes)
        - [e97] box <li> «Frame 1597882305» transform translate(-431.78,828.35) rotate(-1.95rad) size 259.93×280.63 | border-radius:46.00px; overflow:hidden; background-color:#c7e9f5
            ↳ animation: x: 0.001→2.001s -431.78→-510.48 (E4); y: 0.001→2.001s 828.36→979.34 (E4); width: 0.001→2.001s 259.93→307.3 (E4); height: 0.001→2.001s 280.63→331.78 (E4); opacity: 2.002→3.002s 1→0 (E1); radius: 0.001→2.001s 46→54.39 (E4)
          - [-] link <a href="#"> that covers its container's whole box (inset:0)
            - [e98] text transform translate(19.27,102.03) rotate(0rad) size 186.03×70 | white-space:pre-wrap; text-align:left; font-family:'Viaoda Libre'; font-weight:400; font-size:36.80px; line-height:0.94; color:#263237 | TEXT: "Private Retreats"
                ↳ animation: x: 0.001→2.001s 19.28→22.79 (E4); y: 0.001→2.001s 102.04→120.63 (E4); height: 0.001→2.001s 70→69.36 (E4); opacity: 2.002→3.002s 1→0 (E1); content-scale: 0.001→2.001s 1→1.18 (E4)
            - [e99] text transform translate(19.27,190.02) rotate(0rad) size 210.77×66 | opacity:0.6; white-space:pre-wrap; text-align:left; font-family:'Imprima'; font-weight:400; font-size:18.40px; line-height:1.2; color:#263237 | TEXT: "Discover secluded destinations far from crowded tourism."
                ↳ animation: x: 0.001→2.001s 19.28→22.79 (E4); y: 0.001→2.001s 190.03→224.66 (E4); height: 0.001→2.001s 66→65.98 (E4); opacity: 2.002→3.002s 0.6→0 (E1); content-scale: 0.001→2.001s 1→1.18 (E4)
            - [e100] group «Group 44»
                ↳ animation: opacity: 2.002→3.002s 1→0 (E1)
              - [e101] box «Ellipse 13» transform translate(193.86,13.80) rotate(0rad) size 52.26×52.26 | opacity:0.2; border-radius:50%; background-color:#263237
                  ↳ animation: x: 0.001→2.001s 193.86→229.2 (E4); y: 0.001→2.001s 13.8→16.32 (E4); width: 0.001→2.001s 52.26→61.79 (E4); height: 0.001→2.001s 52.26→61.79 (E4); opacity: 2.002→3.002s 0.2→0 (E1)
              - [e102] vector «Union» transform matrix(0.647,-0.647,-0.647,-0.647,222.651,49.25) size 12.53×22.59
                  ↳ animation: x: 0.001→2.001s 0→37.61 (E4); y: 0.001→2.001s 0→6.31 (E4); width: 0.001→2.001s 12.53→14.81 (E4); height: 0.001→2.001s 22.59→26.71 (E4); opacity: 2.002→3.002s 1→0 (E1)
                - vector drawing SVG-1 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
        - [e103] <li> card same as the template | transform translate(-536.59,524.06) rotate(-1.69rad) size 259.93×280.63 | border-radius:46.00px; overflow:hidden; background-color:#f5ebc7 | content colors (template → this card): #263237 → #373326 | texts: "Curated Adventures" / "Experiences tailored to your lifestyle and preferences." | vector shapes: SVG-1 with fill #f5ebc7 (instead of #c7e9f5) (in the same order as the template)
            ↳ card animation: x: 0.001→2.001s -536.59→-634.39 (E4); y: 0.001→2.001s 524.07→619.59 (E4); width: 0.001→2.001s 259.93→307.3 (E4); height: 0.001→2.001s 280.63→331.78 (E4); opacity: 2.002→3.002s 1→0 (E1); radius: 0.001→2.001s 46→54.39 (E4) (its inner elements animate exactly like the template)
        - [e109] <li> card same as the template | transform translate(-559.07,203.02) rotate(-1.43rad) size 259.93×280.63 | border-radius:46.00px; overflow:hidden; background-color:#cac7f5 | content colors (template → this card): #263237 → #262637 | texts: "Luxury Concierge" / "Dedicated support for every stage of your trip." | vector shapes: SVG-1 with fill #cac7f5 (instead of #c7e9f5) (in the same order as the template)
            ↳ card animation: x: 0.001→2.001s -559.07→-660.97 (E4); y: 0.001→2.001s 203.02→240.03 (E4); width: 0.001→2.001s 259.93→307.3 (E4); height: 0.001→2.001s 280.63→331.78 (E4); opacity: 2.002→3.002s 1→0 (E1); radius: 0.001→2.001s 46→54.39 (E4) (its inner elements animate exactly like the template)
        - [e115] <li> card same as the template | transform translate(-497.69,-112.9) rotate(-1.17rad) size 259.93×280.63 | border-radius:46.00px; overflow:hidden; background-color:#c7e9f5 | texts: "Private Retreats" / "Discover secluded destinations far from crowded tourism."
            ↳ card animation: x: 0.001→2.001s -497.69→-588.4 (E4); y: 0.001→2.001s -112.9→-133.48 (E4); width: 0.001→2.001s 259.93→307.3 (E4); height: 0.001→2.001s 280.63→331.78 (E4); opacity: 2.002→3.002s 1→0 (E1); radius: 0.001→2.001s 46→54.39 (E4) (its inner elements animate exactly like the template)
        - [e121] <li> card same as the template | transform translate(-236.16,1086.28) rotate(-2.21rad) size 259.93×280.63 | border-radius:46.00px; overflow:hidden; background-color:#e2f5c7 | content colors (template → this card): #263237 → #313726 | texts: "Nature Escapes" / "Reconnect with untouched landscapes and silence." | vector shapes: SVG-1 with fill #e2f5c7 (instead of #c7e9f5) (in the same order as the template)
            ↳ card animation: x: 0.001→2.001s -236.17→-279.21 (E4); y: 0.001→2.001s 1086.29→1284.28 (E4); width: 0.001→2.001s 259.93→307.3 (E4); height: 0.001→2.001s 280.63→331.78 (E4); opacity: 2.002→3.002s 1→0 (E1); radius: 0.001→2.001s 46→54.39 (E4) (its inner elements animate exactly like the template)
        - [e127] <li> card same as the template | transform translate(19.54,1284.8) rotate(-2.47rad) size 259.93×280.63 | border-radius:46.00px; overflow:hidden; background-color:#f5c7c7 | content colors (template → this card): #263237 → #372626 | texts: "Exclusive Access" / "Unlock locations unavailable to the public." | vector shapes: SVG-1 with fill #f5c7c7 (instead of #c7e9f5) (in the same order as the template)
            ↳ card animation: x: 0.001→2.001s 19.54→23.11 (E4); y: 0.001→2.001s 1284.8→1518.98 (E4); width: 0.001→2.001s 259.93→307.3 (E4); height: 0.001→2.001s 280.63→331.78 (E4); opacity: 2.002→3.002s 1→0 (E1); radius: 0.001→2.001s 46→54.39 (E4) (its inner elements animate exactly like the template)
        - [e133] <li> card same as the template | transform translate(317.91,1410.36) rotate(-2.74rad) size 259.93×280.63 | border-radius:46.00px; overflow:hidden; background-color:#dec7f5 | content colors (template → this card): #263237 → #2c2637 | texts: "Private Retreats" / "Discover secluded destinations far from crowded tourism." | vector shapes: SVG-1 with fill #dec7f5 (instead of #c7e9f5) (in the same order as the template)
            ↳ card animation: x: 0.001→2.001s 317.92→375.86 (E4); y: 0.001→2.001s 1410.37→1667.43 (E4); width: 0.001→2.001s 259.93→307.3 (E4); height: 0.001→2.001s 280.63→331.78 (E4); opacity: 2.002→3.002s 1→0 (E1); radius: 0.001→2.001s 46→54.39 (E4) (its inner elements animate exactly like the template)
    - [e139] box «Ellipse 14» pos(-275.24,-615.88) size 1768.68×1768.68 | border-radius:50%
        ↳ animation: x: 0.001→2.001s -275.24→-325.41 (E4); y: 0.001→2.001s -615.88→-728.14 (E4); width: 0.001→2.001s 1768.68→2091.05 (E4); height: 0.001→2.001s 1768.68→2091.05 (E4); opacity: 2.002→3.002s 1→0 (E1)
  - [e140] box «Object» transform translate(1678.66,493.37) rotate(3.14rad) scale(1,-1) size 2075.05×457.08
      ↳ animation: x: 0.001→2.001s 1678.66→1984.63 (E4); 2.002→3.002s 1984.63→2730.88 (E1); 3.003→6.003s 2730.88→1695.47 (E2); y: 0.001→2.001s 493.38→583.31 (E4); 2.002→3.002s 583.31→328.92 (E1); 3.003→6.003s 328.92→397 (E2); opacity: jump from 1 to 0 at 6.004s; content-scale: 0.001→2.001s 1→1.18 (E4); 2.002→3.002s 1.18→1.9 (E1); 3.003→6.003s 1.9→0.9 (E2)
    - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/wonder/assets/img/32ca8d6612-b43a76.webp: absolutely positioned `<img alt="">` at (0,0), 2880×1092 px, max-width:none, transform-origin 0 0 and transform matrix(0.7205,0,0,0.4051,0,14.681); the container clips it (overflow hidden, same border-radius)
  - [e141] box «Circle Carousel» transform translate(-608.63,1367.97) rotate(-0.52rad) size 2171.6×1737.28 | opacity:0
      ↳ animation: x: 3.003→6.003s -608.63→-538 (E2); y: 3.003→6.003s 1367.98→345 (E2); width: 3.003→6.003s 2171.6→2515 (E2); height: 3.003→6.003s 1737.28→2012 (E2); rotation(rad): 3.003→6.003s -0.52→0 (E2); opacity: 2.002→3.002s 0→1 (E1)
    - [e142] group «Group 59» | opacity:0
        ↳ animation: opacity: 2.002→3.002s 0→1 (E1)
      - [-] list <ul>
        - (list of 21 <li> elements with the SAME inner structure; the first one is fully detailed as the template and the others only state what changes)
        - [e143] box <li> «Frame 1597882305» transform translate(988.42,0) rotate(0rad) size 195.14×210.68 | opacity:0; border-radius:34.53px; overflow:hidden; background-color:#c7e9f5
            ↳ animation: x: 3.003→6.003s 988.43→1145 (E2); 7.004→8.004s 1145→888.42 (E5); 9.004→10.004s 888.42→657 (E5); 11.004→12.004s 657→466.52 (E5); 13.004→14.004s 466.52→329.95 (E5); y: 7.004→8.004s 0→63.44 (E5); 9.004→10.004s 63.44→191.13 (E5); 11.004→12.004s 191.13→374.37 (E5); 13.004→14.004s 374.37→600.66 (E5); width: 3.003→6.003s 195.14→226 (E2); height: 3.003→6.003s 210.68→244 (E2); rotation(rad): 7.004→8.004s 0→-0.26 (E5); 9.004→10.004s -0.26→-0.52 (E5); 11.004→12.004s -0.52→-0.79 (E5); 13.004→14.004s -0.79→-1.05 (E5); opacity: 2.002→3.002s 0→1 (E1); radius: 3.003→6.003s 34.54→40 (E2)
          - [-] link <a href="#"> that covers its container's whole box (inset:0)
            - [e144] text transform translate(14.47,76.91) rotate(0rad) size 139.67×52 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Viaoda Libre'; font-weight:400; font-size:27.63px; line-height:0.94; color:#263237 | TEXT: "Private Retreats"
                ↳ animation: x: 3.003→6.003s 14.47→16.76 (E2); y: 3.003→6.003s 76.92→88.72 (E2); height: 3.003→6.003s 52→51.81 (E2); opacity: 2.002→3.002s 0→1 (E1); content-scale: 3.003→6.003s 1→1.16 (E2)
            - [e145] text transform translate(14.47,142.97) rotate(0rad) size 158.24×51 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Imprima'; font-weight:400; font-size:13.81px; line-height:1.2; color:#263237 | TEXT: "Discover secluded destinations far from crowded tourism."
                ↳ animation: x: 3.003→6.003s 14.47→16.76 (E2); y: 3.003→6.003s 142.98→165.22 (E2); height: 3.003→6.003s 51→49.22 (E2); opacity: 2.002→3.002s 0→0.6 (E1); content-scale: 3.003→6.003s 1→1.16 (E2)
            - [e146] group «Group 44» | opacity:0
                ↳ animation: opacity: 2.002→3.002s 0→1 (E1)
              - [e147] box «Ellipse 13» pos(145.4,10.36) size 39.24×39.24 | opacity:0; border-radius:50%; background-color:#263237
                  ↳ animation: x: 3.003→6.003s 145.4→168.56 (E2); y: 3.003→6.003s 10.36→12 (E2); width: 3.003→6.003s 39.24→45.44 (E2); height: 3.003→6.003s 39.24→45.44 (E2); opacity: 2.002→3.002s 0→0.2 (E1)
              - [e148] vector «Union» pos(157.24,23.53) size 14.16×14.16 | opacity:0
                  ↳ animation: x: 3.003→6.003s 157.24→181.89 (E2); y: 3.003→6.003s 23.53→27.64 (E2); width: 3.003→6.003s 14.16→16.4 (E2); height: 3.003→6.003s 14.16→16.4 (E2); opacity: 2.002→3.002s 0→1 (E1)
                - vector drawing SVG-2 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
        - [e149] <li> card same as the template | position/rotation: see table; size 195.14×210.68 | opacity:0; border-radius:34.53px; overflow:hidden; background-color:#f5ebc7 | content colors (template → this card): #263237 → #373326 | texts: "Curated Adventures" / "Experiences tailored to your lifestyle and preferences." | vector shapes: (see below) (in the same order as the template)
            (in this card, the vector matching the template's one is different:)
            - [e154] vector «Union» transform matrix(0.965,0.258,-0.258,0.965,157.818,22.349) size 14.02×10.97 | opacity:0
                ↳ animation: x: 3.003→6.003s 0→24.64 (E2); y: 3.003→6.003s 0→4.11 (E2); width: 3.003→6.003s 14.02→16.24 (E2); height: 3.003→6.003s 10.97→12.7 (E2); opacity: 2.002→3.002s 0→1 (E1)
              - vector drawing SVG-3 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
        - [e155] <li> card same as the template | position/rotation: see table; size 195.14×210.68 | opacity:0; border-radius:34.53px; overflow:hidden; background-color:#cac7f5 | content colors (template → this card): #263237 → #262637 | texts: "Luxury Concierge" / "Dedicated support for every stage of your trip." | vector shapes: (see below) (in the same order as the template)
            (in this card, the vector matching the template's one is different:)
            - [e160] vector «Union» pos(156.91,23.86) size 12.23×12.23 | opacity:0
                ↳ animation: x: 3.003→6.003s 156.91→181.55 (E2); y: 3.003→6.003s 23.86→27.97 (E2); width: 3.003→6.003s 12.23→14.16 (E2); height: 3.003→6.003s 12.23→14.16 (E2); opacity: 2.002→3.002s 0→1 (E1)
              - vector drawing SVG-4 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
        - [e161] <li> card same as the template | position/rotation: see table; size 195.14×210.68 | opacity:0; border-radius:34.53px; overflow:hidden; background-color:#c7e9f5 | texts: "Private Retreats" / "Discover secluded destinations far from crowded tourism." | vector shapes: (see below) (in the same order as the template)
            (in this card, the vector matching the template's one is different:)
            - [e166] vector «Union» transform matrix(0.965,-0.258,0.258,0.965,156.435,24.489) size 10.97×14.02 | opacity:0
                ↳ animation: x: 3.003→6.003s 0→24.64 (E2); y: 3.003→6.003s 0→4.11 (E2); width: 3.003→6.003s 10.97→12.7 (E2); height: 3.003→6.003s 14.02→16.24 (E2); opacity: 2.002→3.002s 0→1 (E1)
              - vector drawing SVG-5 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
        - [e167] <li> card same as the template | position/rotation: see table; size 195.14×210.68 | opacity:0; border-radius:34.53px; overflow:hidden; background-color:#e2f5c7 | content colors (template → this card): #263237 → #313726 | texts: "Nature Escapes" / "Reconnect with untouched landscapes and silence." | vector shapes: (see below) (in the same order as the template)
            (in this card, the vector matching the template's one is different:)
            - [e172] vector «Union» transform matrix(0.965,0.258,-0.258,0.965,158.289,21.772) size 16.24×12.7 | opacity:0
                ↳ animation: x: 3.003→6.003s 0→24.64 (E2); y: 3.003→6.003s 0→4.11 (E2); width: 3.003→6.003s 16.24→18.81 (E2); height: 3.003→6.003s 12.7→14.71 (E2); opacity: 2.002→3.002s 0→1 (E1)
              - vector drawing SVG-6 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
        - [e173] <li> card same as the template | position/rotation: see table; size 195.14×210.68 | opacity:0; border-radius:34.53px; overflow:hidden; background-color:#f5c7c7 | content colors (template → this card): #263237 → #372626 | texts: "Exclusive Access" / "Unlock locations unavailable to the public." | vector shapes: (see below) (in the same order as the template)
            (in this card, the vector matching the template's one is different:)
            - [e178] vector «Union» transform matrix(0.866,0.500,-0.500,0.866,160.188,20.245) size 17.55×11.06 | opacity:0
                ↳ animation: x: 3.003→6.003s 0→24.64 (E2); y: 3.003→6.003s 0→4.11 (E2); width: 3.003→6.003s 17.55→20.33 (E2); height: 3.003→6.003s 11.06→12.81 (E2); opacity: 2.002→3.002s 0→1 (E1)
              - vector drawing SVG-7 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
        - [e179] <li> card same as the template | position/rotation: see table; size 195.14×210.68 | opacity:0; border-radius:34.53px; overflow:hidden; background-color:#dec7f5 | content colors (template → this card): #263237 → #2c2637 | texts: "Private Retreats" / "Discover secluded destinations far from crowded tourism." | vector shapes: (see below) (in the same order as the template)
            (in this card, the vector matching the template's one is different:)
            - [e184] vector «Union» transform matrix(0.707,0.707,-0.707,0.707,162.839,19.383) size 17.99×9.34 | opacity:0
                ↳ animation: x: 3.003→6.003s 0→24.64 (E2); y: 3.003→6.003s 0→4.11 (E2); width: 3.003→6.003s 17.99→20.83 (E2); height: 3.003→6.003s 9.34→10.82 (E2); opacity: 2.002→3.002s 0→1 (E1)
              - vector drawing SVG-8 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
        - [e185] <li> card same as the template | position/rotation: see table; size 195.14×210.68 | opacity:0; border-radius:34.53px; overflow:hidden; background-color:#c7e9f5 | texts: "Private Retreats" / "Discover secluded destinations far from crowded tourism." | vector shapes: (see below) (in the same order as the template)
            (in this card, the vector matching the template's one is different:)
            - [e190] vector «Union» transform matrix(-0.122,-0.992,0.992,-0.122,158.836,38.966) size 15.23×13.5 | opacity:0
                ↳ animation: x: 3.003→6.003s 0→24.64 (E2); y: 3.003→6.003s 0→4.11 (E2); width: 3.003→6.003s 15.23→17.64 (E2); height: 3.003→6.003s 13.5→15.63 (E2); opacity: 2.002→3.002s 0→1 (E1)
              - vector drawing SVG-9 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
        - [e191] <li> card same as the template | position/rotation: see table; size 195.14×210.68 | opacity:0; border-radius:34.53px; overflow:hidden; background-color:#f5ebc7 | content colors (template → this card): #263237 → #373326 | texts: "Curated Adventures" / "Experiences tailored to your lifestyle and preferences." | vector shapes: (see below) (in the same order as the template)
            (in this card, the vector matching the template's one is different:)
            - [e196] vector «Union» transform matrix(-0.375,-0.926,0.926,-0.375,162.869,40.352) size 16.96×11.95 | opacity:0
                ↳ animation: x: 3.003→6.003s 0→24.64 (E2); y: 3.003→6.003s 0→4.11 (E2); width: 3.003→6.003s 16.96→19.64 (E2); height: 3.003→6.003s 11.95→13.84 (E2); opacity: 2.002→3.002s 0→1 (E1)
              - vector drawing SVG-10 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
        - [e197] <li> card same as the template | position/rotation: see table; size 195.14×210.68 | opacity:0; border-radius:34.53px; overflow:hidden; background-color:#cac7f5 | content colors (template → this card): #263237 → #262637 | texts: "Luxury Concierge" / "Dedicated support for every stage of your trip." | vector shapes: (see below) (in the same order as the template)
            (in this card, the vector matching the template's one is different:)
            - [e202] vector «Union» transform matrix(-0.602,-0.797,0.797,-0.602,167.026,39.75) size 17.87×10.25 | opacity:0
                ↳ animation: x: 3.003→6.003s 0→24.64 (E2); y: 3.003→6.003s 0→4.11 (E2); width: 3.003→6.003s 17.87→20.7 (E2); height: 3.003→6.003s 10.25→11.87 (E2); opacity: 2.002→3.002s 0→1 (E1)
              - vector drawing SVG-11 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
        - [e203] <li> card same as the template | position/rotation: see table; size 195.14×210.68 | opacity:0; border-radius:34.53px; overflow:hidden; background-color:#c7e9f5 | texts: "Private Retreats" / "Discover secluded destinations far from crowded tourism." | vector shapes: (see below) (in the same order as the template)
            (in this card, the vector matching the template's one is different:)
            - [e208] vector «Union» transform matrix(-0.788,-0.614,0.614,-0.788,169.375,38.672) size 17.89×10.15 | opacity:0
                ↳ animation: x: 3.003→6.003s 0→24.64 (E2); y: 3.003→6.003s 0→4.11 (E2); width: 3.003→6.003s 17.89→20.72 (E2); height: 3.003→6.003s 10.15→11.76 (E2); opacity: 2.002→3.002s 0→1 (E1)
              - vector drawing SVG-12 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
        - [e209] <li> card same as the template | position/rotation: see table; size 195.14×210.68 | opacity:0; border-radius:34.53px; overflow:hidden; background-color:#e2f5c7 | content colors (template → this card): #263237 → #313726 | texts: "Nature Escapes" / "Reconnect with untouched landscapes and silence." | vector shapes: (see below) (in the same order as the template)
            (in this card, the vector matching the template's one is different:)
            - [e214] vector «Union» transform matrix(0.138,-0.990,0.990,0.138,155.846,35.88) size 13.42×15.35 | opacity:0
                ↳ animation: x: 3.003→6.003s 0→24.64 (E2); y: 3.003→6.003s 0→4.11 (E2); width: 3.003→6.003s 13.42→15.54 (E2); height: 3.003→6.003s 15.35→17.78 (E2); opacity: 2.002→3.002s 0→1 (E1)
              - vector drawing SVG-13 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
        - [e215] <li> card same as the template | position/rotation: see table; size 195.14×210.68 | opacity:0; border-radius:34.53px; overflow:hidden; background-color:#f5c7c7 | content colors (template → this card): #263237 → #372626 | texts: "Exclusive Access" / "Unlock locations unavailable to the public." | vector shapes: (see below) (in the same order as the template)
            (in this card, the vector matching the template's one is different:)
            - [e220] vector «Union» transform matrix(0.389,-0.920,0.920,0.389,154.566,31.824) size 11.85×17.04 | opacity:0
                ↳ animation: x: 3.003→6.003s 0→24.64 (E2); y: 3.003→6.003s 0→4.11 (E2); width: 3.003→6.003s 11.85→13.72 (E2); height: 3.003→6.003s 17.04→19.73 (E2); opacity: 2.002→3.002s 0→1 (E1)
              - vector drawing SVG-14 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
        - [e221] <li> card same as the template | position/rotation: see table; size 195.14×210.68 | opacity:0; border-radius:34.53px; overflow:hidden; background-color:#dec7f5 | content colors (template → this card): #263237 → #2c2637 | texts: "Private Retreats" / "Discover secluded destinations far from crowded tourism." | vector shapes: (see below) (in the same order as the template)
            (in this card, the vector matching the template's one is different:)
            - [e226] vector «Union» transform matrix(0.614,-0.788,0.788,0.614,155.268,27.675) size 10.15×17.89 | opacity:0
                ↳ animation: x: 3.003→6.003s 0→24.64 (E2); y: 3.003→6.003s 0→4.11 (E2); width: 3.003→6.003s 10.15→11.76 (E2); height: 3.003→6.003s 17.89→20.72 (E2); opacity: 2.002→3.002s 0→1 (E1)
              - vector drawing SVG-15 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
        - [e227] <li> card same as the template | position/rotation: see table; size 195.14×210.68 | opacity:0; border-radius:34.53px; overflow:hidden; background-color:#c7e9f5 | texts: "Private Retreats" / "Discover secluded destinations far from crowded tourism." | vector shapes: (see below) (in the same order as the template)
            (in this card, the vector matching the template's one is different:)
            - [e232] vector «Union» transform matrix(-0.602,0.797,-0.797,-0.602,175.601,30.5) size 10.25×17.87 | opacity:0
                ↳ animation: x: 3.003→6.003s 0→24.64 (E2); y: 3.003→6.003s 0→4.11 (E2); width: 3.003→6.003s 10.25→11.87 (E2); height: 3.003→6.003s 17.87→20.7 (E2); opacity: 2.002→3.002s 0→1 (E1)
              - vector drawing SVG-16 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
        - [e233] <li> card same as the template | position/rotation: see table; size 195.14×210.68 | opacity:0; border-radius:34.53px; overflow:hidden; background-color:#f5ebc7 | content colors (template → this card): #263237 → #373326 | texts: "Curated Adventures" / "Experiences tailored to your lifestyle and preferences." | vector shapes: (see below) (in the same order as the template)
            (in this card, the vector matching the template's one is different:)
            - [e238] vector «Union» transform matrix(-0.375,0.926,-0.926,-0.375,174.789,27.362) size 11.95×16.96 | opacity:0
                ↳ animation: x: 3.003→6.003s 0→24.64 (E2); y: 3.003→6.003s 0→4.11 (E2); width: 3.003→6.003s 11.95→13.84 (E2); height: 3.003→6.003s 16.96→19.64 (E2); opacity: 2.002→3.002s 0→1 (E1)
              - vector drawing SVG-17 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
        - [e239] <li> card same as the template | position/rotation: see table; size 195.14×210.68 | opacity:0; border-radius:34.53px; overflow:hidden; background-color:#cac7f5 | content colors (template → this card): #263237 → #262637 | texts: "Luxury Concierge" / "Dedicated support for every stage of your trip." | vector shapes: (see below) (in the same order as the template)
            (in this card, the vector matching the template's one is different:)
            - [e244] vector «Union» transform matrix(-0.122,0.992,-0.992,-0.122,172.747,24.575) size 13.5×15.23 | opacity:0
                ↳ animation: x: 3.003→6.003s 0→24.64 (E2); y: 3.003→6.003s 0→4.11 (E2); width: 3.003→6.003s 13.5→15.63 (E2); height: 3.003→6.003s 15.23→17.64 (E2); opacity: 2.002→3.002s 0→1 (E1)
              - vector drawing SVG-18 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
        - [e245] <li> card same as the template | position/rotation: see table; size 195.14×210.68 | opacity:0; border-radius:34.53px; overflow:hidden; background-color:#c7e9f5 | texts: "Private Retreats" / "Discover secluded destinations far from crowded tourism." | vector shapes: (see below) (in the same order as the template)
            (in this card, the vector matching the template's one is different:)
            - [e250] vector «Union» transform matrix(0.138,0.990,-0.990,0.138,170.225,22.045) size 15.35×13.42 | opacity:0
                ↳ animation: x: 3.003→6.003s 0→24.64 (E2); y: 3.003→6.003s 0→4.11 (E2); width: 3.003→6.003s 15.35→17.78 (E2); height: 3.003→6.003s 13.42→15.54 (E2); opacity: 2.002→3.002s 0→1 (E1)
              - vector drawing SVG-19 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
        - [e251] <li> card same as the template | position/rotation: see table; size 195.14×210.68 | opacity:0; border-radius:34.53px; overflow:hidden; background-color:#e2f5c7 | content colors (template → this card): #263237 → #313726 | texts: "Nature Escapes" / "Reconnect with untouched landscapes and silence." | vector shapes: (see below) (in the same order as the template)
            (in this card, the vector matching the template's one is different:)
            - [e256] vector «Union» transform matrix(-0.788,0.614,-0.614,-0.788,175.262,33.438) size 10.15×17.89 | opacity:0
                ↳ animation: x: 3.003→6.003s 0→24.64 (E2); y: 3.003→6.003s 0→4.11 (E2); width: 3.003→6.003s 10.15→11.76 (E2); height: 3.003→6.003s 17.89→20.72 (E2); opacity: 2.002→3.002s 0→1 (E1)
              - vector drawing SVG-20 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
        - [e257] <li> card same as the template | position/rotation: see table; size 195.14×210.68 | opacity:0; border-radius:34.53px; overflow:hidden; background-color:#f5c7c7 | content colors (template → this card): #263237 → #372626 | texts: "Exclusive Access" / "Unlock locations unavailable to the public." | vector shapes: (see below) (in the same order as the template)
            (in this card, the vector matching the template's one is different:)
            - [e262] vector «Union» transform matrix(-0.920,0.389,-0.389,-0.920,174.036,35.739) size 11.85×17.04 | opacity:0
                ↳ animation: x: 3.003→6.003s 0→24.64 (E2); y: 3.003→6.003s 0→4.11 (E2); width: 3.003→6.003s 11.85→13.72 (E2); height: 3.003→6.003s 17.04→19.73 (E2); opacity: 2.002→3.002s 0→1 (E1)
              - vector drawing SVG-21 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
        - [e263] <li> card same as the template | position/rotation: see table; size 195.14×210.68 | opacity:0; border-radius:34.53px; overflow:hidden; background-color:#dec7f5 | content colors (template → this card): #263237 → #2c2637 | texts: "Private Retreats" / "Discover secluded destinations far from crowded tourism." | vector shapes: (see below) (in the same order as the template)
            (in this card, the vector matching the template's one is different:)
            - [e268] vector «Union» transform matrix(-0.990,0.138,-0.138,-0.990,172.347,37.255) size 13.42×15.35 | opacity:0
                ↳ animation: x: 3.003→6.003s 0→24.64 (E2); y: 3.003→6.003s 0→4.11 (E2); width: 3.003→6.003s 13.42→15.54 (E2); height: 3.003→6.003s 15.35→17.78 (E2); opacity: 2.002→3.002s 0→1 (E1)
              - vector drawing SVG-22 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
          (The <li> cards above: each card box animates according to the following table; their inner elements animate exactly like the template.)

Card animation table (x, y, rotation(rad)). Shared segments: T1 = 3.003→6.003s (E2); T2 = 7.004→8.004s (E5); T3 = 9.004→10.004s (E5); T4 = 11.004→12.004s (E5); T5 = 13.004→14.004s (E5). Each cell is the value at the END of that segment (before the first segment it equals «start»; between segments it holds; if a property does not change in a segment, the value is repeated):

| card | property | start | T1 | T2 | T3 | T4 | T5 |
|---|---|---|---|---|---|---|---|
| e143 | x | 988.43 | 1145 | 888.42 | 657 | 466.52 | 329.95 |
| e143 | y | 0 | 0 | 63.44 | 191.13 | 374.37 | 600.66 |
| e143 | rotation(rad) | 0 | 0 | -0.2618 | -0.5236 | -0.7854 | -1.0472 |
| e149 | x | 1229.7 | 1424.43 | 1162.18 | 906.45 | 674.65 | 482.58 |
| e149 | y | 12.87 | 14.91 | 5.53 | 64.33 | 187.33 | 366.12 |
| e149 | rotation(rad) | 0.2618 | 0.2618 | 0 | -0.2618 | -0.5236 | -0.7854 |
| e155 | x | 1459.42 | 1690.47 | 1441.61 | 1180.21 | 924.09 | 690.7 |
| e155 | y | 87.76 | 101.63 | 20.44 | 6.41 | 60.53 | 179.08 |
| e155 | rotation(rad) | 0.5236 | 0.5236 | 0.2618 | 0 | -0.2618 | -0.5236 |
| e161 | x | 1661.93 | 1925.01 | 1707.66 | 1459.64 | 1197.86 | 940.15 |
| e161 | y | 219.54 | 254.26 | 107.16 | 21.33 | 2.61 | 52.28 |
| e161 | rotation(rad) | 0.7854 | 0.7854 | 0.5236 | 0.2618 | 0 | -0.2618 |
| e167 | x | 753.81 | 873.27 | 644.95 | 458.38 | 326.28 | 257.66 |
| e167 | y | 63.38 | 73.4 | 204.67 | 390.57 | 618.41 | 872.68 |
| e167 | rotation(rad) | -0.2618 | -0.2618 | -0.5236 | -0.7854 | -1.0472 | -1.309 |
| e173 | x | 543.58 | 629.81 | 446.33 | 318.15 | 253.99 | 258.23 |
| e173 | y | 185.33 | 214.63 | 404.11 | 634.61 | 890.43 | 1154.15 |
| e173 | rotation(rad) | -0.5236 | -0.5236 | -0.7854 | -1.0472 | -1.309 | -1.5708 |
| e179 | x | 372.08 | 431.19 | 306.1 | 245.85 | 254.56 | 331.63 |
| e179 | y | 357.53 | 414.07 | 648.15 | 906.63 | 1171.9 | 1425.87 |
| e179 | rotation(rad) | -0.7854 | -0.7854 | -1.0472 | -1.309 | -1.5708 | -1.8326 |
| e185 | x | 1940.15 | 2247.22 | 2288.74 | 2259.99 | 2162.94 | 2004.19 |
| e185 | y | 1119.8 | 1296.87 | 1030.85 | 763.15 | 512.01 | 294.54 |
| e185 | rotation(rad) | 1.9558 | 1.9558 | 1.694 | 1.4322 | 1.1704 | 0.9086 |
| e191 | x | 1837.6 | 2128.46 | 2239.6 | 2283.82 | 2258.11 | 2164.23 |
| e191 | y | 1338.57 | 1550.24 | 1306.33 | 1041.95 | 775.15 | 524.08 |
| e191 | rotation(rad) | 2.2176 | 2.2176 | 1.9558 | 1.694 | 1.4322 | 1.1704 |
| e197 | x | 1681.93 | 1948.16 | 2120.83 | 2234.68 | 2281.94 | 2259.41 |
| e197 | y | 1523.35 | 1764.24 | 1559.69 | 1317.43 | 1053.95 | 787.22 |
| e197 | rotation(rad) | 2.4794 | 2.4794 | 2.2176 | 1.9558 | 1.694 | 1.4322 |
| e203 | x | 1483.73 | 1718.62 | 1940.53 | 2115.91 | 2232.8 | 2283.24 |
| e203 | y | 1661.54 | 1924.28 | 1773.69 | 1570.8 | 1329.43 | 1066.03 |
| e203 | rotation(rad) | 2.7412 | 2.7412 | 2.4794 | 2.2176 | 1.9558 | 1.694 |
| e209 | x | 1969.53 | 2281.25 | 2249.29 | 2149.76 | 1989.43 | 1779.23 |
| e209 | y | 878.55 | 1017.47 | 752.16 | 504.17 | 290.38 | 125.37 |
| e209 | rotation(rad) | 1.694 | 1.694 | 1.4322 | 1.1704 | 0.9086 | 0.6468 |
| e215 | x | 1935.47 | 2241.8 | 2139.06 | 1976.25 | 1764.47 | 1518.16 |
| e215 | y | 637.91 | 738.78 | 493.18 | 282.54 | 121.21 | 20.19 |
| e215 | rotation(rad) | 1.4322 | 1.4322 | 1.1704 | 0.9086 | 0.6468 | 0.385 |
| e221 | x | 1840.29 | 2131.57 | 1965.55 | 1751.3 | 1503.4 | 1238.76 |
| e221 | y | 414.29 | 479.8 | 271.55 | 113.37 | 16.03 | -13.83 |
| e221 | rotation(rad) | 1.1704 | 1.1704 | 0.9086 | 0.6468 | 0.385 | 0.1232 |
| e227 | x | 304.55 | 352.98 | 513.25 | 724.25 | 971.61 | 1238.45 |
| e227 | y | 1300.65 | 1506.33 | 1723.43 | 1891.66 | 1999.54 | 2039.73 |
| e227 | rotation(rad) | -1.9558 | -1.9558 | -2.2176 | -2.4794 | -2.7412 | -3.003 |
| e233 | x | 225.86 | 261.85 | 356.75 | 513.05 | 720.09 | 963.77 |
| e233 | y | 1072.21 | 1241.76 | 1491.46 | 1708.1 | 1876.9 | 1986.37 |
| e233 | rotation(rad) | -1.694 | -1.694 | -1.9558 | -2.2176 | -2.4794 | -2.7412 |
| e239 | x | 208.99 | 242.3 | 265.62 | 356.55 | 508.89 | 712.25 |
| e239 | y | 831.18 | 962.62 | 1226.89 | 1476.13 | 1693.34 | 1863.72 |
| e239 | rotation(rad) | -1.4322 | -1.4322 | -1.694 | -1.9558 | -2.2176 | -2.4794 |
| e245 | x | 255.07 | 295.67 | 246.08 | 265.42 | 352.39 | 501.05 |
| e245 | y | 594 | 687.93 | 947.75 | 1211.56 | 1461.37 | 1680.16 |
| e245 | rotation(rad) | -1.1704 | -1.1704 | -1.4322 | -1.694 | -1.9558 | -2.2176 |
| e251 | x | 451.41 | 523.06 | 735.58 | 983.68 | 1250.45 | 1517.71 |
| e251 | y | 1494.29 | 1730.59 | 1896.03 | 2000.84 | 2037.86 | 2004.57 |
| e251 | rotation(rad) | -2.2176 | -2.2176 | -2.4794 | -2.7412 | -3.003 | 3.0184 |
| e257 | x | 643.39 | 745.39 | 995.01 | 1262.53 | 1529.71 | 1778.36 |
| e257 | y | 1643.33 | 1903.19 | 2005.21 | 2039.15 | 2002.69 | 1898.32 |
| e257 | rotation(rad) | -2.4794 | -2.4794 | -2.7412 | -3.003 | 3.0184 | 2.7566 |
| e263 | x | 867.39 | 1004.82 | 1273.86 | 1541.79 | 1790.36 | 2002.62 |
| e263 | y | 1737.6 | 2012.37 | 2043.52 | 2003.98 | 1896.45 | 1728.24 |
| e263 | rotation(rad) | -2.7412 | -2.7412 | -3.003 | 3.0184 | 2.7566 | 2.4948 |

The other animated card properties (width, height, opacity, radius) animate EXACTLY like the template.
      - [e269] box «Ellipse 14» transform translate(422.30,216.38) rotate(0rad) size 1327.85×1327.85 | opacity:0; border-radius:50%
          ↳ animation: x: 3.003→6.003s 422.31→489.09 (E2); 7.004→8.004s 489.09→319.72 (E5); 9.004→10.004s 319.72→214.26 (E5); 11.004→12.004s 214.26→179.92 (E5); 13.004→14.004s 179.92→219.02 (E5); y: 3.003→6.003s 216.38→250.6 (E2); 7.004→8.004s 250.6→475.26 (E5); 9.004→10.004s 475.26→736.11 (E5); 11.004→12.004s 736.11→1015.37 (E5); 13.004→14.004s 1015.37→1293.99 (E5); width: 3.003→6.003s 1327.85→1537.82 (E2); height: 3.003→6.003s 1327.85→1537.82 (E2); rotation(rad): 7.004→8.004s 0→-0.26 (E5); 9.004→10.004s -0.26→-0.52 (E5); 11.004→12.004s -0.52→-0.79 (E5); 13.004→14.004s -0.79→-1.05 (E5); opacity: 2.002→3.002s 0→1 (E1)
    - [e270] box «Component 4» pos(463.88,45.17) size 1243.38×356.61 | opacity:0
        ↳ animation: x: 3.003→6.003s 463.88→537.5 (E2); 7.004→8.004s 537.5→537.66 (E5); 9.004→10.004s 537.66→537.82 (E5); 11.004→12.004s 537.82→537.66 (E5); y: 3.003→6.003s 45.17→52.31 (E2); width: 3.003→6.003s 1243.38→1440 (E2); height: 3.003→6.003s 356.61→413 (E2); opacity: 2.002→3.002s 0→1 (E1)
      - [e271] box «Object» transform translate(1464.27,0.39) rotate(3.14rad) scale(1,-1) size 1618.93×356.61 | opacity:0
          ↳ animation: x: 3.003→6.003s 1464.28→1695.47 (E2); 7.004→8.004s 1695.47→1615.47 (E5); 9.004→10.004s 1615.47→1535.47 (E5); 11.004→12.004s 1535.47→1455.47 (E5); 13.004→14.004s 1455.47→1440 (E5); y: 3.003→6.003s 0.39→0 (E2); opacity: 2.002→3.002s 0→1 (E1); content-scale: 3.003→6.003s 1→1.16 (E2)
        - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/wonder/assets/img/32ca8d6612-b43a76.webp: absolutely positioned `<img alt="">` at (0,0), 2880×1092 px, max-width:none, transform-origin 0 0 and transform matrix(0.5621,0,0,0.3160,0,11.454); the container clips it (overflow hidden, same border-radius)
  - [e272] box «Component 1» pos(656,683) size 81.22×34 | opacity:0; border-radius:999px; overflow:hidden
      ↳ animation: opacity: 3.003→6.003s 0→1 (E2)
    - [e273] box «cursor» pos(47.22,0) size 34×34 | opacity:0; mix-blend-mode:exclusion; border-radius:99px; background-color:#ffffff
        ↳ animation: opacity: 3.003→6.003s 0→1 (E2)

#### Layer bk2 «bg1 1» — BLEED LAYER (cover), design box x=0 y=0 2899.36×1725.04
- [e274] box «bg1 1» pos(-729.68,-457.52) size 2899.36×1725.04
    ↳ animation: x: 0.001→2.001s -729.68→-38.17 (E4); 3.003→6.003s -38.17→-4095.58 (E2); y: 0.001→2.001s -457.52→-46.09 (E4); 3.003→6.003s -46.09→-2305.84 (E2); width: 0.001→2.001s 2899.36→1516.34 (E4); 3.003→6.003s 1516.34→9405.17 (E2); height: 0.001→2.001s 1725.04→902.18 (E4); 3.003→6.003s 902.18→5595.8 (E2); opacity: jump from 1 to 0 at 6.004s
  - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/wonder/assets/img/3dc8ffc5b2.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)

#### Layer bk3 «Rectangle 1» — CONTAINED LAYER (contain, anchor x=0, y=1), design box x=0 y=497 1440×313
- [e275] box «Rectangle 1» pos(0,497) size 1440×313 | opacity:0.6; background:linear-gradient(180deg, rgba(0,0,0,0) 0%, #000000 100%)
    ↳ animation: x: 3.003→6.003s 0→-3816.07 (E2); y: 3.003→6.003s 497→1023.99 (E2); width: 3.003→6.003s 1440→8931.67 (E2); height: 3.003→6.003s 313→1941.4 (E2); opacity: jump from 0.6 to 0 at 6.004s

#### Layer bk4 «Group 51» — CONTAINED LAYER (contain, anchor x=-1, y=0), design box x=171 y=96 332×415
- [e276] group «Group 51»
    ↳ animation: opacity: jump from 1 to 0 at 6.004s
  - [e277] group «Group 46»
      ↳ animation: opacity: jump from 1 to 0 at 6.004s
    - [e278] box «Frame 1597882309» pos(171,96) size 135×56
        ↳ animation: x: 3.003→6.003s 171→-3207.91 (E2); y: 0.001→2.001s 96→196 (E4); 3.003→6.003s 196→-1134.94 (E2); width: 3.003→6.003s 135→1172.79 (E2); height: 3.003→6.003s 56→486.49 (E2); opacity: jump from 1 to 0 at 6.004s
      - [e279] text transform translate(0,0) rotate(0rad) size 135×56 | white-space:pre; text-align:left; font-family:'Viaoda Libre'; font-weight:400; font-size:60px; line-height:0.94; text-transform:uppercase; color:#ffffff | TEXT: "Step"
          ↳ animation: width: 3.003→6.003s 135→134.22 (E2); height: 3.003→6.003s 56→56.4 (E2); opacity: jump from 1 to 0 at 6.004s; content-scale: 3.003→6.003s 1→8.69 (E2)
    - [e280] vector «Union» pos(329.28,109.17) size 12.53×22.59
        ↳ animation: x: 3.003→6.003s 329.28→-1842.72 (E2); y: 0.001→2.001s 109.17→209.17 (E4); 3.003→6.003s 209.17→-837.03 (E2); width: 3.003→6.003s 12.53→108.85 (E2); height: 3.003→6.003s 22.59→196.25 (E2); opacity: jump from 1 to 0 at 6.004s
      - vector drawing SVG-1 with fill #F3CEB9 (instead of #c7e9f5) (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
    - [e281] box «Frame 1597882310» pos(367,96) size 136×56
        ↳ animation: x: 3.003→6.003s 367→-1505.19 (E2); y: 0.001→2.001s 96→196 (E4); 3.003→6.003s 196→-1134.94 (E2); width: 3.003→6.003s 136→1181.48 (E2); height: 3.003→6.003s 56→486.49 (E2); opacity: jump from 1 to 0 at 6.004s
      - [e282] text transform translate(0,0) rotate(0rad) size 136×56 | white-space:pre; text-align:right; font-family:'Viaoda Libre'; font-weight:400; font-size:60px; line-height:0.94; text-transform:uppercase; color:#ffffff | TEXT: "Into"
          ↳ animation: width: 3.003→6.003s 136→135.14 (E2); height: 3.003→6.003s 56→56.4 (E2); opacity: jump from 1 to 0 at 6.004s; content-scale: 3.003→6.003s 1→8.69 (E2)
    - [e283] box «Frame 1597882311» pos(171,157.01) size 329×72
        ↳ animation: x: 3.003→6.003s 171→-3207.91 (E2); y: 0.001→2.001s 157.01→257.01 (E4); 3.003→6.003s 257.01→-604.9 (E2); width: 3.003→6.003s 329→2858.14 (E2); height: 3.003→6.003s 72→625.49 (E2); opacity: jump from 1 to 0 at 6.004s
      - [e284] text transform translate(0,0) rotate(0rad) size 329×72 | white-space:pre; text-align:left; font-family:'Viaoda Libre'; font-weight:400; font-size:77.11px; line-height:0.94; text-transform:uppercase; font-feature-settings:'ss02' 1; color:#ffffff | TEXT: "Wonder"
          ↳ animation: width: 3.003→6.003s 329→328.06 (E2); height: 3.003→6.003s 72→72.52 (E2); opacity: jump from 1 to 0 at 6.004s; content-scale: 3.003→6.003s 1→8.69 (E2)
  - [e285] box «Frame 1597882312» pos(229,435) size 196.38×76
      ↳ animation: x: 3.003→6.003s 229→-2704.04 (E2); y: 0.001→2.001s 435→355 (E4); 3.003→6.003s 355→246.35 (E2); width: 3.003→6.003s 196.38→1706.05 (E2); height: 3.003→6.003s 76→660.24 (E2); opacity: jump from 1 to 0 at 6.004s
    - [e286] text transform translate(0,0) rotate(0rad) size 196.38×76 | white-space:pre-wrap; text-align:left; font-family:'Imprima'; font-weight:400; font-size:16px; line-height:1.2; color:#ffffff | TEXT: "Designing immersive digital experiences that blur the line between imagination, AI, and reality."
        ↳ animation: height: 3.003→6.003s 76→76.89 (E2); opacity: jump from 1 to 0 at 6.004s; content-scale: 3.003→6.003s 1→8.69 (E2)

#### Layer bk5 «Group 43» — CONTAINED LAYER (contain, anchor x=0, y=1), design box x=658.53 y=808 122.93×122.93
- [e287] group <a href="#"> aria-label="Enter Experience" «Group 43» | opacity:0.6
    ↳ animation: opacity: 2.002→3.002s 0.6→1 (E1); jump from 1 to 0 at 6.004s
  - [e288] box «Ellipse 12» pos(658.53,808) size 122.93×122.93 | border-radius:50%
      ↳ animation: x: 3.003→6.003s 658.53→268.51 (E2); y: 0.001→2.001s 808→658 (E4); 3.003→6.003s 658→3263.1 (E2); width: 3.003→6.003s 122.93→762.51 (E2); height: 3.003→6.003s 122.93→762.51 (E2); opacity: jump from 1 to 0 at 6.004s; background color rgba: 2.002→3.002s [255,255,255,0]→[255,255,255,1] (E1)
    - border (stroke): inset:0px; border-radius:50%; border:1px dashed #ffffff
  - [e289] vector «Enter Experience Enter Experience» transform matrix(0,-1,1,0,669.192,918.307) size 96.95×101.98 | color:rgba(255,255,255,1)
      ↳ animation: x: 3.003→6.003s 0→-70.23 (E2); y: 0.001→2.001s 0→-150 (E4); 3.003→6.003s -150→2774.89 (E2); width: 3.003→6.003s 96.95→601.34 (E2); height: 3.003→6.003s 101.98→632.54 (E2); opacity: jump from 1 to 0 at 6.004s; text color rgba: 2.002→3.002s [255,255,255,1]→[0,0,0,1] (E1); rotation(°): 0.001→2.001s 0→90 (E4); 2.002→3.002s 90→180 (E1) [rotation around point [48.84,50.81] of the box]
    - text on a circular path (real, editable text, with `fill="currentColor"` so it inherits the element's animated color). Exact SVG, filling 100% of the box (position:absolute; inset:0; width:100%; height:100%; overflow:visible): `<svg preserveAspectRatio="none" width="97" height="102" viewBox="0 0 97 102" xmlns="http://www.w3.org/2000/svg"><defs><path id="e289_tp" d="M6.922,39.185A43.5,43.5 0 1 1 90.758,62.435A43.5,43.5 0 1 1 6.922,39.185" fill="none"/></defs><text fill="currentColor" style="font-family:'Imprima',system-ui,sans-serif;font-size:10.5px;letter-spacing:1.75px;text-transform:uppercase;"><textPath href="#e289_tp" startOffset="0%">Enter Experience</textPath><textPath href="#e289_tp" startOffset="50%">Enter Experience</textPath></text></svg>`
  - [e290] group «Group 45»
      ↳ animation: opacity: jump from 1 to 0 at 6.004s
    - [e291] vector «Union» pos(708.7,822.72) size 22.59×12.53 | color:rgba(255,255,255,1)
        ↳ animation: x: 3.003→6.003s 708.7→573.05 (E2); y: 0.001→2.001s 822.72→702.72 (E4); 3.003→6.003s 702.72→3612.34 (E2); width: 3.003→6.003s 22.59→140.12 (E2); height: 3.003→6.003s 12.53→77.72 (E2); opacity: jump from 1 to 0 at 6.004s; text color rgba: 2.002→3.002s [255,255,255,1]→[0,0,0,1] (E1)
      - vector drawing SVG-23 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
    - [e292] vector «Union» pos(708.7,903.68) size 22.59×12.53 | color:rgba(255,255,255,1)
        ↳ animation: x: 3.003→6.003s 708.7→573.05 (E2); y: 0.001→2.001s 903.68→723.68 (E4); 3.003→6.003s 723.68→3663.83 (E2); width: 3.003→6.003s 22.59→140.12 (E2); height: 3.003→6.003s 12.53→77.72 (E2); opacity: jump from 1 to 0 at 6.004s; text color rgba: 2.002→3.002s [255,255,255,1]→[0,0,0,1] (E1)
      - vector drawing SVG-24 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box

#### Layer bk6 «Group 49» — CONTAINED LAYER (contain, anchor x=1, y=-1), design box x=891 y=-236.5 573.59×733.5
- [e293] group «Group 49»
    ↳ animation: opacity: jump from 1 to 0 at 6.004s
  - [e294] group «Group 48»
      ↳ animation: opacity: jump from 1 to 0 at 6.004s
    - [-] list <ul>
      - [e295] box <li> «Frame 1597882305» pos(1085.8,-96.5) size 184×193 | border-radius:40px; overflow:hidden; background-color:#160e05
          ↳ animation: x: 3.003→6.003s 1085.79→2918.63 (E2); y: 0.001→2.001s -96.5→273.5 (E4); 3.003→6.003s 273.5→-397.4 (E2); width: 3.003→6.003s 184→1208.23 (E2); height: 3.003→6.003s 193→1267.32 (E2); opacity: jump from 1 to 0 at 6.004s; radius: 3.003→6.003s 40→262.66 (E2)
        - [e296] box «image 33» pos(-9.61,-112.11) size 203.21×341.24
            ↳ animation: x: 3.003→6.003s -9.61→-63.08 (E2); y: 3.003→6.003s -112.11→-736.18 (E2); width: 3.003→6.003s 203.21→1334.38 (E2); height: 3.003→6.003s 341.24→2240.76 (E2); opacity: jump from 1 to 0 at 6.004s
          - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/wonder/assets/img/f2dcd97549.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)
        - [e297] box «Frame 1597882306» pos(0,103) size 184×90 | background:linear-gradient(180deg, rgba(0,0,0,0) 0%, rgba(0,0,0,0.36) 100%)
            ↳ animation: y: 3.003→6.003s 103→676.34 (E2); width: 3.003→6.003s 184→1208.23 (E2); height: 3.003→6.003s 90→590.98 (E2); opacity: jump from 1 to 0 at 6.004s
          - background blur (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(15px); mask:linear-gradient(180deg, rgba(0,0,0,0), rgba(0,0,0,1))
          - [e298] text transform translate(74.32,34.69) rotate(0rad) size 88.22×46 | white-space:pre-wrap; text-align:left; font-family:'Imprima'; font-weight:400; font-size:19px; line-height:1.2; color:#ffffff | TEXT: "Global Partners"
              ↳ animation: x: 3.003→6.003s 74.32→488.04 (E2); y: 3.003→6.003s 34.69→227.8 (E2); height: 3.003→6.003s 46→45.69 (E2); opacity: jump from 1 to 0 at 6.004s; content-scale: 3.003→6.003s 1→6.57 (E2)
          - [e299] text transform translate(21.35,38.69) rotate(0rad) size 38×38 | white-space:pre; text-align:left; font-family:'Viaoda Libre'; font-weight:400; font-size:40px; line-height:0.94; text-transform:uppercase; color:#ffffff | TEXT: "32"
              ↳ animation: x: 3.003→6.003s 21.35→140.22 (E2); y: 3.003→6.003s 38.69→254.07 (E2); width: 3.003→6.003s 38→37.77 (E2); height: 3.003→6.003s 38→37.62 (E2); opacity: jump from 1 to 0 at 6.004s; content-scale: 3.003→6.003s 1→6.57 (E2)
      - [e300] box <li> «Frame 1597882306» pos(891,53.5) size 184×193 | border-radius:40px; overflow:hidden; background-color:#160e05
          ↳ animation: x: 3.003→6.003s 891→1590.46 (E2); y: 0.001→2.001s 53.5→273.5 (E4); 3.003→6.003s 273.5→-425.19 (E2); width: 3.003→6.003s 184→1261.21 (E2); height: 3.003→6.003s 193→1322.9 (E2); opacity: jump from 1 to 0 at 6.004s; radius: 3.003→6.003s 40→274.18 (E2)
        - [e301] box «image 33» pos(-0.31,0) size 184.61×310.01
            ↳ animation: x: 3.003→6.003s -0.31→-2.09 (E2); width: 3.003→6.003s 184.61→1265.41 (E2); height: 3.003→6.003s 310.01→2124.93 (E2); opacity: jump from 1 to 0 at 6.004s
          - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/wonder/assets/img/dbbd57eae5.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)
        - [e302] box <a href="#"> «Frame 1597882306» pos(0,103) size 184×90 | background:linear-gradient(180deg, rgba(0,0,0,0) 0%, rgba(0,0,0,0.36) 100%)
            ↳ animation: y: 3.003→6.003s 103→706.01 (E2); width: 3.003→6.003s 184→1261.21 (E2); height: 3.003→6.003s 90→616.9 (E2); opacity: jump from 1 to 0 at 6.004s
          - background blur (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(15px); mask:linear-gradient(180deg, rgba(0,0,0,0), rgba(0,0,0,1))
          - [e303] text transform translate(74.32,34.69) rotate(0rad) size 88.22×46 | white-space:pre-wrap; text-align:left; font-family:'Imprima'; font-weight:400; font-size:19px; line-height:1.2; color:#ffffff | TEXT: "Watch Demo"
              ↳ animation: x: 3.003→6.003s 74.32→509.44 (E2); y: 3.003→6.003s 34.69→237.79 (E2); height: 3.003→6.003s 46→45.52 (E2); opacity: jump from 1 to 0 at 6.004s; content-scale: 3.003→6.003s 1→6.85 (E2)
          - [e304] group «Group 44»
              ↳ animation: opacity: jump from 1 to 0 at 6.004s
            - [e305] box «Ellipse 13» pos(12,32.56) size 45.44×45.44 | border-radius:50%; background-color:#ffffff
                ↳ animation: x: 3.003→6.003s 12→82.25 (E2); y: 3.003→6.003s 32.56→223.17 (E2); width: 3.003→6.003s 45.44→311.48 (E2); height: 3.003→6.003s 45.44→311.48 (E2); opacity: jump from 1 to 0 at 6.004s
            - [e306] vector «Polygon 2» pos(30.75,49.03) size 10.28×12.51
                ↳ animation: x: 3.003→6.003s 30.75→280.5 (E2); y: 3.003→6.003s 49.03→317.76 (E2); width: 3.003→6.003s 10.28→70.46 (E2); height: 3.003→6.003s 12.51→85.75 (E2); opacity: jump from 1 to 0 at 6.004s; radius: 3.003→6.003s 2.1→14.37 (E2)
              - vector drawing SVG-25 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
      - [e307] box <li> «Frame 1597882307» pos(1280.59,-236.5) size 184×193 | border-radius:40px; overflow:hidden; background-color:#160e05
          ↳ animation: x: 3.003→6.003s 1280.59→4126.85 (E2); y: 0.001→2.001s -236.5→273.5 (E4); 3.003→6.003s 273.5→-362.28 (E2); width: 3.003→6.003s 184→1141.27 (E2); height: 3.003→6.003s 193→1197.09 (E2); opacity: jump from 1 to 0 at 6.004s; radius: 3.003→6.003s 40→248.1 (E2)
        - [e308] box «image 33» pos(-18.97,-91.34) size 221.94×372.69
            ↳ animation: x: 3.003→6.003s -18.97→-117.67 (E2); y: 3.003→6.003s -91.34→-566.55 (E2); width: 3.003→6.003s 221.94→1376.6 (E2); height: 3.003→6.003s 372.69→2311.65 (E2); opacity: jump from 1 to 0 at 6.004s
          - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/wonder/assets/img/ba4ecdd8c0.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)
        - [e309] box <a href="#"> «Frame 1597882306» pos(0,103) size 184×90 | background:linear-gradient(180deg, rgba(0,0,0,0) 0%, rgba(0,0,0,0.36) 100%)
            ↳ animation: y: 3.003→6.003s 103→638.86 (E2); width: 3.003→6.003s 184→1141.27 (E2); height: 3.003→6.003s 90→558.23 (E2); opacity: jump from 1 to 0 at 6.004s
          - background blur (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(15px); mask:linear-gradient(180deg, rgba(0,0,0,0), rgba(0,0,0,1))
          - [e310] text transform translate(74.32,34.69) rotate(0rad) size 88.22×46 | white-space:pre-wrap; text-align:left; font-family:'Imprima'; font-weight:400; font-size:19px; line-height:1.2; color:#ffffff | TEXT: "Watch Demo"
              ↳ animation: x: 3.003→6.003s 74.32→460.99 (E2); y: 3.003→6.003s 34.69→215.18 (E2); height: 3.003→6.003s 46→45.47 (E2); opacity: jump from 1 to 0 at 6.004s; content-scale: 3.003→6.003s 1→6.2 (E2)
          - [e311] group «Group 44»
              ↳ animation: opacity: jump from 1 to 0 at 6.004s
            - [e312] box «Ellipse 13» pos(12,32.56) size 45.44×45.44 | border-radius:50%; background-color:#ffffff
                ↳ animation: x: 3.003→6.003s 12→74.43 (E2); y: 3.003→6.003s 32.56→201.94 (E2); width: 3.003→6.003s 45.44→281.86 (E2); height: 3.003→6.003s 45.44→281.86 (E2); opacity: jump from 1 to 0 at 6.004s
            - [e313] vector «Polygon 2» pos(30.75,49.03) size 10.28×12.51
                ↳ animation: x: 3.003→6.003s 30.75→252.69 (E2); y: 3.003→6.003s 49.03→287.84 (E2); width: 3.003→6.003s 10.28→63.76 (E2); height: 3.003→6.003s 12.51→77.59 (E2); opacity: jump from 1 to 0 at 6.004s; radius: 3.003→6.003s 2.1→13 (E2)
              - vector drawing SVG-26 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
  - [e314] group «Group 47»
      ↳ animation: opacity: jump from 1 to 0 at 6.004s
    - [-] list <ul>
      - [e315] box <li> «Rectangle 2» pos(920,489) size 17.51×8 | border-radius:99px; background-color:#ffffff
          ↳ animation: x: 3.003→6.003s 920→1890.27 (E2); y: 3.003→6.003s 489→974.37 (E2); width: 3.003→6.003s 17.51→108.59 (E2); height: 3.003→6.003s 8→49.62 (E2); opacity: jump from 1 to 0 at 6.004s; radius: 3.003→6.003s 99→614.05 (E2)
        - [-] link <a href="#"> aria-label="Slide 1" that covers its container's whole box (inset:0)
      - [e316] box <li> «Rectangle 3» pos(954.51,489) size 17.51×8 | opacity:0.4; border-radius:99px; background-color:#ffffff
          ↳ animation: x: 3.003→6.003s 954.51→2104.31 (E2); y: 3.003→6.003s 489→974.37 (E2); width: 3.003→6.003s 17.51→108.59 (E2); height: 3.003→6.003s 8→49.62 (E2); opacity: jump from 0.4 to 0 at 6.004s; radius: 3.003→6.003s 99→614.05 (E2)
        - [-] link <a href="#"> aria-label="Slide 2" that covers its container's whole box (inset:0)
      - [e317] box <li> «Rectangle 4» pos(989.02,489) size 17.51×8 | opacity:0.3; border-radius:99px; background-color:#ffffff
          ↳ animation: x: 3.003→6.003s 989.02→2318.35 (E2); y: 3.003→6.003s 489→974.37 (E2); width: 3.003→6.003s 17.51→108.59 (E2); height: 3.003→6.003s 8→49.62 (E2); opacity: jump from 0.3 to 0 at 6.004s; radius: 3.003→6.003s 99→614.05 (E2)
        - [-] link <a href="#"> aria-label="Slide 3" that covers its container's whole box (inset:0)
      - [e318] box <li> «Rectangle 5» pos(1023.52,489) size 17.51×8 | opacity:0.2; border-radius:99px; background-color:#ffffff
          ↳ animation: x: 3.003→6.003s 1023.52→2532.39 (E2); y: 3.003→6.003s 489→974.37 (E2); width: 3.003→6.003s 17.51→108.59 (E2); height: 3.003→6.003s 8→49.62 (E2); opacity: jump from 0.2 to 0 at 6.004s; radius: 3.003→6.003s 99→614.05 (E2)
        - [-] link <a href="#"> aria-label="Slide 4" that covers its container's whole box (inset:0)

#### Layer bk7 «left-full 1» — BLEED LAYER (cover), design box x=0 y=0 1890.46×810
- [e319] box «left-full 1» pos(-783.31,0) size 1890.46×810
    ↳ animation: x: 0.001→2.001s -783.31→-1410.46 (E4); 3.003→6.003s -1410.46→-16257.75 (E2); y: 3.003→6.003s 0→-2756.34 (E2); width: 3.003→6.003s 1890.46→14982.23 (E2); height: 3.003→6.003s 810→6419.39 (E2); opacity: jump from 1 to 0 at 6.004s
  - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/wonder/assets/img/a3c24edf47.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)

#### Layer bk8 «right-full 1» — BLEED LAYER (cover), design box x=0 y=0 1890.46×809.08
- [e320] box «right-full 1» pos(141,0) size 1890.46×809.08
    ↳ animation: x: 0.001→2.001s 141→987 (E4); 3.003→6.003s 987→2742.53 (E2); y: 3.003→6.003s 0→-2756.34 (E2); width: 3.003→6.003s 1890.46→14982.23 (E2); height: 3.003→6.003s 809.08→6412.07 (E2); opacity: jump from 1 to 0 at 6.004s
  - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/wonder/assets/img/652486234b.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)

#### Layer bk9 «Frame 1597882308» — CONTAINED LAYER (contain, anchor x=0, y=-1), design box x=82 y=-105.32 1274.14×48.41
- [e321] box «Frame 1597882308» pos(82,-105.32) size 1274.14×48.41
    ↳ animation: y: 0.001→2.001s -105.32→14.68 (E4)
  - [e322] group «Group 54»
    - [-] list <nav> aria-label="Main navigation"
      - [-] list <ul>
        - [e323] text <li> pos(0,-143.8) size 34×16 | white-space:pre; text-align:left; font-family:'Imprima'; font-weight:400; font-size:13px; line-height:1.2; color:#ffffff | TEXT: "Home" | inline link: the text is the content of an <a href="#"> inside the element
            ↳ animation: y: 0.001→2.001s -143.8→16.2 (E4)
        - [e324] text <li> pos(188.79,-83.8) size 37×16 | opacity:0.6; white-space:pre; text-align:left; font-family:'Imprima'; font-weight:400; font-size:13px; line-height:1.2; color:#ffffff | TEXT: "Studio" | inline link: the text is the content of an <a href="#"> inside the element
            ↳ animation: y: 0.001→2.001s -83.8→16.2 (E4)
        - [e325] text <li> pos(380.58,-33.8) size 66×16 | opacity:0.6; white-space:pre; text-align:left; font-family:'Imprima'; font-weight:400; font-size:13px; line-height:1.2; color:#ffffff | TEXT: "Experiences" | inline link: the text is the content of an <a href="#"> inside the element
            ↳ animation: y: 0.001→2.001s -33.8→16.2 (E4)
  - [e326] vector <a href="#"> aria-label="Wonder — Home" «Union» pos(643.65,53.02) size 42.48×40.7
      ↳ animation: x: 0.001→2.001s 643.65→615.83 (E4); y: 0.001→2.001s 53.02→3.06 (E4); width: 0.001→2.001s 42.48→43.76 (E4); height: 0.001→2.001s 40.7→39.04 (E4)
    - vector drawing SVG-27 (inline SVG, see Appendix «Vector shapes»), fills 100% of its box
  - [e327] group «Group 53»
    - [-] list <nav> aria-label="Secondary navigation"
      - [-] list <ul>
        - [e328] text <li> pos(804.56,-23.8) size 73×16 | opacity:0.6; white-space:pre; text-align:left; font-family:'Imprima'; font-weight:400; font-size:13px; line-height:1.2; color:#ffffff | TEXT: "Technologies" | inline link: the text is the content of an <a href="#"> inside the element
            ↳ animation: y: 0.001→2.001s -23.8→16.2 (E4)
        - [e329] text <li> pos(1032.35,-73.8) size 43×16 | opacity:0.6; white-space:pre; text-align:left; font-family:'Imprima'; font-weight:400; font-size:13px; line-height:1.2; color:#ffffff | TEXT: "Journal" | inline link: the text is the content of an <a href="#"> inside the element
            ↳ animation: y: 0.001→2.001s -73.8→16.2 (E4)
        - [e330] text <li> pos(1230.14,-133.8) size 44×16 | opacity:0.6; white-space:pre; text-align:left; font-family:'Imprima'; font-weight:400; font-size:13px; line-height:1.2; color:#ffffff | TEXT: "Contact" | inline link: the text is the content of an <a href="#"> inside the element
            ↳ animation: y: 0.001→2.001s -133.8→16.2 (E4)

## 10. Final adjustments
### Pointer effects and special adjustments (only with a fine pointer `(hover: hover) and (pointer: fine)` and no reduced-motion)
- Pointer position normalized to the stage: nx = clamp((x/W − 0.5)·2, −1, 1), ny = clamp((y/H − 0.5)·2, −1, 1) (0 until the first `pointermove` and while the pointer is outside the page: `pointerleave` on `document.documentElement`). Per-frame smoothing: ns += (n − ns) × 0.06.
- Per-layer parallax: each listed layer moves with the CSS `translate` property: `translate: (ns_x·fx·w)px (ns_y·fy·w)px` (design px; they scale with the layer): «STAGE2» [20, 12], «bg1 1» [40, 30], «Group 51» [100, 0], «Group 49» [60, 20], «left-full 1» [140, 36], «right-full 1» [140, 36]. Weight w = 1 until t = 3.0 s, then decreases linearly to 0 at t = 4.5 s. Disabled on mobile.
- Custom cursor: a `div.cur` (aria-hidden) inside the stage, a white circle of 34 px × scale s (the scale of the contained layers), `border-radius:50%; mix-blend-mode:exclusion; pointer-events:none; z-index:20`. It follows the pointer with 0.3 smoothing per frame, centered on it. Opacity = 1 until t = 3.0 s, then decreases linearly to 0 at t = 3.8 s (0 when the pointer leaves). While its opacity is > 0.5, the system cursor is hidden on the stage (`cursor:none`). Disabled on mobile. CSS: `.cur{position:absolute;left:0;top:0;width:34px;height:34px;border-radius:50%;background:#fff;mix-blend-mode:exclusion;pointer-events:none;z-index:20;opacity:0;will-change:transform,opacity}` and `html.has-cur .stage, html.has-cur .stage *{cursor:none}`.
- Hover preview: if the pointer is inside the circle of [e288] «Ellipse 12» (radius = half its on-screen width) and t is between 1.952 and 3.002 s, a value hv moves toward 1 (otherwise toward 0) with 0.12 smoothing per frame. The effective time used to paint the elements (section 6) is t_eff = max(t, 2.002 + hv × 1.0) if hv > 0 and t is in [1.952, 3.002]; in any other case t_eff = t. So hovering the button previews the 2.002 → 3.002 s transition and leaving it goes back. The parallax and cursor weights use t (not t_eff).
- Title and subtitle: [e7] «Create Beyond Reality» and [e8] «Exclusive journeys to breathtaking desti» belong to the bleed layer «STAGE2». On desktop, between t = 3.3 s and 4.8 s they gradually move from their layer's «cover» placement to a «contain» placement of the frame, so their final size does not depend on the crop: with w = smoothstep(u) = u²·(3 − 2u), u = clamp((t − 3.3)/1.5, 0, 1), scale = z_cover + (s − z_cover)·w and origin = O_cover + (O_contain − O_cover)·w, where z_cover = max(W/1440, H/810), O_cover = ((W − 1440·z_cover)/2, (H − 810·z_cover)/2), s = min(W/1440, H/810) and O_contain = ((W − 1440·s)/2, (H − 810·s)/2); the element is drawn at origin + (x, y)·scale. (On mobile they follow section 11.) On both desktop and mobile, their opacity is also multiplied by clamp((t − 3.3)/0.9, 0, 1).
- `html{scrollbar-gutter:stable}`.

## 11. Mobile view
- It activates when the stage width is < 768 px or the width/height ratio is < 0.82 (add the class `is-m` to `<html>`). Mobile reference design: 390×844 px.
- sm = min(W/390, H/844); ox = (W − 390·sm)/2; oy = (H − 844·sm)/2.
- Listed contained layer with {x, y, z, ax, ay}: its scale is sm·z (z = 1 if not given) and the top-left corner of its «design box» is placed on screen at (ox·(1+ax) + x·sm, oy·(1+ay) + y·sm) (ax, ay = 0 if not given); the rest of its content keeps its position relative to that box.
- Bleed layer: z = max(W/1440, H/810)·(given z or 1); screen = ((W − 1440·z)·fx + X·z, (H − 810·z)·fy + Y·z), with fx, fy = 0.5 unless another value is given.
- Contained layers that are not in the list are hidden on mobile.
- Per-element adjustments: «hide» = `display:none`; «shift dx, dy» = move the element by that distance in px of its container (if there is also «scale ×z», it is scaled from its top-left corner); «multiply its animated offsets by [kx, ky]» = multiply the total value of offsetX by kx and of offsetY by ky (the mobile «shift dx, dy» is not multiplied); «fade out between t1 and t2» = multiply its opacity by clamp((t2 − t)/(t2 − t1), 0, 1) from t1 on; «place the element point (rx, ry) at (mx, my) of the mobile design at scale e×sm» = draw the element at scale Se = e·sm with its top-left corner (animated x, y) at (ox + mx·sm + (x − rx)·Se, oy + my·sm + (y − ry)·Se); «width» = fixed width in px; «transition … between t1 and t2» = go from its layer's normal placement (the mobile one) to that placement with smoothstep interpolation (u²·(3 − 2u)) of scale and origin between those times.
- On mobile the track uses 36 vh per second (see section 7) and the pointer effects from section 10 are disabled.
Mobile layer placement:
- layer «Group 51»: x=24, y=30, z=0.9
- layer «Group 49»: x=23, y=164, z=0.6
- layer «Group 43»: x=146, y=836, z=0.8
- layer «Frame 1597882308»: x=-442, y=-104, z=1
- layer «Rectangle 1»: x=-555, y=518, z=1.04, ay=1
Unlisted non-bleed layers are hidden on mobile.
Per-element mobile adjustments:
- [e322] «Group 54»: hide
- [e327] «Group 53»: hide
- [e7] «Create Beyond Reality»: place the element point (272.5,146) at (20,128) of the mobile design at scale 0.39×sm; transition from the desktop placement to the mobile one between t=3.3s and 4.8s
- [e8] «Exclusive journeys to breathtaking desti»: place the element point (345,237) at (34,164) of the mobile design at scale 0.62×sm; width 196.6; transition from the desktop placement to the mobile one between t=3.3s and 4.8s

## 12. Semantics and accessibility
- `<main class="hero" aria-label="Step into Wonder">`; the loader has `aria-hidden="true"`.
- Lists with `<ul>` and `<li>`; menus inside `<nav aria-label="…">`. Each `<a href="#">` uses the given `aria-label` when its content is not text. If a link covers a whole box, it goes inside it as an absolute `<a class="lk">` covering it (`inset:0`).
- Decorative images with `alt=""`; decorative SVGs with `aria-hidden="true"`.
- Only texts and links receive pointer events (CSS in section 3) and invisible elements get `visibility:hidden`, so nothing that cannot be seen can ever be clicked.
- Visible focus: `outline: 2px solid currentColor; outline-offset: 3px`.

## 13. Resources (absolute URLs)
All resources are hosted in the public repository `danielsnows/F4M-Bluebird` and served by jsDelivr (CDN with CORS enabled):
- `assets/fonts/Imprima.woff2` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/wonder/assets/fonts/Imprima.woff2
- `assets/fonts/ViaodaLibre.woff2` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/wonder/assets/fonts/ViaodaLibre.woff2
- `assets/img/32ca8d6612-b43a76.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/wonder/assets/img/32ca8d6612-b43a76.webp
- `assets/img/3dc8ffc5b2.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/wonder/assets/img/3dc8ffc5b2.webp
- `assets/img/52e4066599.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/wonder/assets/img/52e4066599.webp
- `assets/img/652486234b.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/wonder/assets/img/652486234b.webp
- `assets/img/a3c24edf47.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/wonder/assets/img/a3c24edf47.webp
- `assets/img/ba4ecdd8c0.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/wonder/assets/img/ba4ecdd8c0.webp
- `assets/img/dbbd57eae5.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/wonder/assets/img/dbbd57eae5.webp
- `assets/img/f2dcd97549.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/wonder/assets/img/f2dcd97549.webp
- `assets/img/f4c0464736.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@67222f8/snows-heroes/wonder/assets/img/f4c0464736.webp

## 14. Appendix: vector shapes
Each SVG fills 100% of its element's box (`position:absolute; inset:0; width:100%; height:100%; overflow:visible`, `preserveAspectRatio="none"`, `aria-hidden="true"`). If an element says «with fill #xxxxxx (instead of #yyyyyy)», use the same SVG changing that fill color. If an SVG with internal `id`s (masks, gradients) is used more than once, give each copy unique ids and update its `url(#…)` references.

**SVG-1**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 13 23" xmlns="http://www.w3.org/2000/svg"> <path d="M4.26696 13.9642C5.74054 12.4908 5.74065 10.1017 4.2672 8.62816L0.903418 5.26408C-0.300672 4.05988 -0.300713 2.10762 0.903329 0.903374C2.10755 -0.301054 4.06018 -0.301136 5.2645 0.903191L10.321 5.95966C13.2679 8.90663 13.268 13.6846 10.3211 16.6317L5.26437 21.6887C4.0601 22.8931 2.10752 22.8931 0.903211 21.6888C-0.301111 20.4845 -0.301067 18.5318 0.903309 17.3276L4.26696 13.9642Z" fill="#c7e9f5"></path> </svg>
```

**SVG-2**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 15 15" xmlns="http://www.w3.org/2000/svg"> <path d="M5.47614 4.91184C7.56001 4.91175 9.24939 6.60098 9.24948 8.68484L9.24962 11.7054C9.24968 13.0617 10.3491 14.1611 11.7053 14.1612C13.0617 14.1613 14.1614 13.0618 14.1614 11.7053L14.1614 7.54657C14.1614 3.37892 10.7829 0.000339023 6.61525 0.000216887L2.45599 9.49964e-05C1.09958 5.52459e-05 -2.30212e-05 1.09963 -2.30212e-05 2.45604C-2.30212e-05 3.81246 1.09961 4.91204 2.45603 4.91198L5.47614 4.91184Z" fill="#C7E9F5"></path> </svg>
```

**SVG-3**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 15 11" xmlns="http://www.w3.org/2000/svg"> <path d="M5.18898 4.57485C6.92698 4.10907 8.7135 5.14041 9.17928 6.87841L9.85443 9.39767C10.1576 10.5288 11.3202 11.2001 12.4514 10.8971C13.5827 10.594 14.2541 9.43122 13.951 8.29991L13.0216 4.83133C12.0902 1.35535 8.51739 -0.70748 5.04139 0.223802L1.57239 1.15321C0.441084 1.45631 -0.230297 2.61913 0.0728321 3.75043C0.375965 4.88173 1.53883 5.55308 2.67012 5.2499L5.18898 4.57485Z" fill="#F5EBC7"></path> </svg>
```

**SVG-4**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 13 13" xmlns="http://www.w3.org/2000/svg"> <path d="M4.72852 4.24108C6.52785 4.241 7.98657 5.69958 7.98665 7.49891L7.98677 10.1071C7.98682 11.2781 8.9361 12.2274 10.1071 12.2275C11.2784 12.2276 12.2279 11.2782 12.2279 10.107L12.2279 6.51605C12.2279 2.91745 9.31069 0.000194685 5.7121 8.94326e-05L2.12074 -1.56085e-05C0.949543 -4.98641e-05 7.75799e-05 0.949387 7.75126e-05 2.12059C7.74453e-05 3.2918 0.949562 4.24125 2.12078 4.24119L4.72852 4.24108Z" fill="#CAC7F5"></path> </svg>
```

**SVG-5**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 11 15" xmlns="http://www.w3.org/2000/svg"> <path d="M4.09134 4.84434C5.82938 5.30996 6.86089 7.09639 6.39526 8.83443L5.72033 11.3538C5.4173 12.4849 6.08853 13.6476 7.21964 13.9508C8.35092 14.254 9.51381 13.5827 9.81695 12.4513L10.7464 8.98277C11.6777 5.50679 9.615 1.93392 6.13905 1.00243L2.6701 0.0728171C1.53882 -0.230345 0.37597 0.441001 0.0728408 1.57229C-0.230292 2.7036 0.441106 3.86644 1.57243 4.16952L4.09134 4.84434Z" fill="#C7E9F5"></path> </svg>
```

**SVG-6**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 17 13" xmlns="http://www.w3.org/2000/svg"> <path d="M6.00943 5.29811C8.02227 4.75867 10.0913 5.9531 10.6307 7.96594L11.4126 10.8836C11.7637 12.1936 13.1102 12.971 14.4202 12.6201C15.7305 12.2691 16.5081 10.9224 16.157 9.61222L15.0806 5.59515C14.002 1.56952 9.86418 -0.819529 5.83851 0.259019L1.82093 1.3354C0.510735 1.68642 -0.26681 3.03313 0.0842535 4.34332C0.435321 5.65352 1.78207 6.43103 3.09226 6.0799L6.00943 5.29811Z" fill="#E2F5C7"></path> </svg>
```

**SVG-7**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 18 12" xmlns="http://www.w3.org/2000/svg"> <path d="M6.30001 5.83548C8.10465 4.79347 10.4123 5.41169 11.4543 7.21633L12.9647 9.83218C13.6429 11.0067 15.1447 11.4091 16.3193 10.7311C17.4941 10.053 17.8966 8.5509 17.2184 7.3762L15.139 3.7746C13.0552 0.165314 8.44005 -1.07138 4.8307 1.01233L1.22861 3.09186C0.0539083 3.77003 -0.34859 5.27209 0.329613 6.44677C1.00782 7.62147 2.50992 8.02392 3.68459 7.34565L6.30001 5.83548Z" fill="#F5C7C7"></path> </svg>
```

**SVG-8**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 18 10" xmlns="http://www.w3.org/2000/svg"> <path d="M6.32783 6.48843C7.80128 5.01485 10.1903 5.01474 11.6639 6.48819L13.7999 8.62399C14.7589 9.58294 16.3137 9.58297 17.2728 8.62406C18.232 7.665 18.2321 6.10991 17.2729 5.15077L14.3323 2.21008C11.3853 -0.736894 6.60732 -0.736967 3.66027 2.20992L0.719135 5.15088C-0.240018 6.10997 -0.240039 7.66503 0.719085 8.62415C1.67822 9.58328 3.2333 9.58325 4.19239 8.62407L6.32783 6.48843Z" fill="#DEC7F5"></path> </svg>
```

**SVG-9**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 16 14" xmlns="http://www.w3.org/2000/svg"> <path d="M9.96383 5.15125C9.70778 7.21933 7.82371 8.68827 5.75563 8.43222L2.75792 8.06108C1.41198 7.89444 0.185746 8.85035 0.0189321 10.1963C-0.147906 11.5424 0.80816 12.7689 2.1543 12.9356L6.28153 13.4468C10.4176 13.959 14.1858 11.0215 14.6982 6.88544L15.2095 2.75772C15.3763 1.41161 14.4202 0.185188 13.0741 0.0184651C11.728 -0.148259 10.5016 0.807877 10.3349 2.15402L9.96383 5.15125Z" fill="#C7E9F5"></path> </svg>
```

**SVG-10**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 17 12" xmlns="http://www.w3.org/2000/svg"> <path d="M11.0925 4.33341C10.3099 6.26475 8.10982 7.19601 6.17848 6.41343L3.37897 5.27907C2.12202 4.76975 0.690162 5.37572 0.180682 6.63261C-0.328875 7.88969 0.277179 9.32182 1.5343 9.83127L5.3886 11.3932C9.25113 12.9585 13.6513 11.0963 15.2167 7.23385L16.7789 3.37914C17.2884 2.12205 16.6823 0.68997 15.4252 0.180526C14.1681 -0.328923 12.736 0.277217 12.2266 1.53436L11.0925 4.33341Z" fill="#F5EBC7"></path> </svg>
```

**SVG-11**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 18 11" xmlns="http://www.w3.org/2000/svg"> <path d="M11.631 3.38604C10.3752 5.04902 8.00908 5.37913 6.3461 4.12334L3.93558 2.30307C2.85327 1.48578 1.31337 1.70052 0.495944 2.78271C-0.321606 3.86507 -0.106866 5.40527 0.975562 6.22272L4.29426 8.72903C7.62005 11.2407 12.3522 10.5808 14.864 7.25507L17.3707 3.93605C18.1882 2.85366 17.9734 1.31351 16.891 0.49606C15.8085 -0.321396 14.2684 -0.106559 13.451 0.975903L11.631 3.38604Z" fill="#CAC7F5"></path> </svg>
```

**SVG-12**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 18 11" xmlns="http://www.w3.org/2000/svg"> <path d="M11.5458 3.98413C9.90243 5.26543 7.53149 4.97188 6.25019 3.32848L4.39293 0.946341C3.55903 -0.123218 2.01602 -0.314358 0.946357 0.519398C-0.12347 1.35328 -0.314679 2.89657 0.519293 3.96633L3.07623 7.24618C5.63863 10.533 10.3804 11.1204 13.6673 8.55808L16.9476 6.00094C18.0174 5.16701 18.2085 3.62375 17.3746 2.554C16.5406 1.48425 14.9973 1.29314 13.9276 2.12716L11.5458 3.98413Z" fill="#C7E9F5"></path> </svg>
```

**SVG-13**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 14 16" xmlns="http://www.w3.org/2000/svg"> <path d="M8.32456 5.78717C8.6125 7.85105 7.17281 9.75757 5.10893 10.0455L2.11731 10.4629C0.774095 10.6503 -0.162942 11.891 0.0242785 13.2342C0.211528 14.5777 1.45245 15.5149 2.79588 15.3275L6.91478 14.7531C11.0425 14.1774 13.922 10.3646 13.3464 6.23692L12.772 2.11751C12.5847 0.774103 11.3438 -0.163077 10.0004 0.0242827C8.65698 0.211645 7.71984 1.45262 7.90726 2.79603L8.32456 5.78717Z" fill="#E2F5C7"></path> </svg>
```

**SVG-14**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 12 18" xmlns="http://www.w3.org/2000/svg"> <path d="M6.28517 6.19467C7.09746 8.1137 6.20028 10.3279 4.28125 11.1402L1.49958 12.3176C0.250641 12.8463 -0.333347 14.2872 0.19515 15.5362C0.723727 16.7854 2.16494 17.3696 3.4141 16.8409L7.24397 15.22C11.082 13.5956 12.8766 9.16744 11.2523 5.32934L9.63133 1.49896C9.1027 0.249806 7.6615 -0.334266 6.41236 0.194408C5.16321 0.723089 4.57918 2.16433 5.10792 3.41345L6.28517 6.19467Z" fill="#F5C7C7"></path> </svg>
```

**SVG-15**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 11 18" xmlns="http://www.w3.org/2000/svg"> <path d="M3.98413 6.34772C5.26543 7.99112 4.97188 10.3621 3.32848 11.6434L0.946341 13.5006C-0.123218 14.3345 -0.314358 15.8775 0.519398 16.9472C1.35328 18.017 2.89657 18.2082 3.96633 17.3743L7.24618 14.8173C10.533 12.2549 11.1204 7.5132 8.55808 4.22627L6.00094 0.945958C5.16701 -0.123809 3.62375 -0.31497 2.554 0.518991C1.48425 1.35296 1.29314 2.89625 2.12716 3.96597L3.98413 6.34772Z" fill="#DEC7F5"></path> </svg>
```

**SVG-16**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 11 18" xmlns="http://www.w3.org/2000/svg"> <path d="M6.13055 11.5211C4.87464 9.85826 5.20454 7.49211 6.86742 6.23619L9.27779 4.41572C10.36 3.59834 10.5748 2.05845 9.7576 0.976106C8.94024 -0.106397 7.40006 -0.321275 6.31763 0.496175L2.99891 3.00246C-0.326889 5.5141 -0.98697 10.2462 1.52457 13.5721L4.03106 16.8913C4.84846 17.9738 6.38861 18.1886 7.47103 17.3711C8.55346 16.5537 8.76823 15.0135 7.95074 13.9311L6.13055 11.5211Z" fill="#C7E9F5"></path> </svg>
```

**SVG-17**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 12 17" xmlns="http://www.w3.org/2000/svg"> <path d="M5.53415 10.7809C4.75141 8.84959 5.68248 6.64945 7.61376 5.86671L10.4132 4.73211C11.6701 4.22269 12.2761 2.79086 11.7669 1.53389C11.2575 0.276721 9.82545 -0.329462 8.56833 0.17998L4.71401 1.74192C0.851478 3.30719 -1.01088 7.70725 0.554278 11.5698L2.11629 15.4247C2.62569 16.6818 4.05775 17.2879 5.31486 16.7785C6.57198 16.269 7.17806 14.8369 6.66856 13.5798L5.53415 10.7809Z" fill="#F5EBC7"></path> </svg>
```

**SVG-18**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 14 16" xmlns="http://www.w3.org/2000/svg"> <path d="M5.07258 9.47357C4.81636 7.40552 6.28515 5.52132 8.3532 5.2651L11.3509 4.89371C12.6968 4.72696 13.6528 3.50078 13.4862 2.15482C13.3196 0.808668 12.0932 -0.147508 10.7471 0.0192073L6.61984 0.530356C2.48379 1.0426 -0.453927 4.81071 0.0581917 8.94678L0.569281 13.0745C0.735956 14.4206 1.96234 15.3768 3.30847 15.2101C4.6546 15.0433 5.61069 13.8169 5.44391 12.4708L5.07258 9.47357Z" fill="#CAC7F5"></path> </svg>
```

**SVG-19**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 16 14" xmlns="http://www.w3.org/2000/svg"> <path d="M5.30531 8.31121C5.59307 6.2473 7.49948 4.80746 9.56338 5.09523L12.555 5.51235C13.8983 5.69963 15.139 4.76265 15.3265 3.41945C15.514 2.07604 14.5768 0.835032 13.2334 0.64766L9.11452 0.0731836C4.98683 -0.502518 1.17395 2.37687 0.598124 6.50454L0.023459 10.6239C-0.163949 11.9673 0.773188 13.2083 2.11659 13.3956C3.46001 13.583 4.70095 12.6458 4.88826 11.3024L5.30531 8.31121Z" fill="#C7E9F5"></path> </svg>
```

**SVG-20**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 11 18" xmlns="http://www.w3.org/2000/svg"> <path d="M6.82543 11.6433C5.18192 10.3621 4.88818 7.99119 6.16934 6.34768L8.02641 3.96539C8.86021 2.89576 8.66915 1.35274 7.59963 0.518795C6.52994 -0.315275 4.98664 -0.124205 4.15266 0.945546L1.5957 4.22538C-0.966721 7.51222 -0.37954 12.254 2.90722 14.8165L6.18737 17.3738C7.25708 18.2078 8.80035 18.0167 9.63432 16.947C10.4683 15.8772 10.2771 14.334 9.20734 13.5L6.82543 11.6433Z" fill="#E2F5C7"></path> </svg>
```

**SVG-21**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 12 18" xmlns="http://www.w3.org/2000/svg"> <path d="M7.57029 11.1409C5.65119 10.3287 4.75382 8.11463 5.56596 6.19553L6.74316 3.41377C7.27172 2.16478 6.6878 0.723791 5.43888 0.195075C4.18977 -0.33372 2.74851 0.250276 2.21982 1.49943L0.598865 5.32929C-1.02555 9.16734 0.768881 13.5955 4.60687 15.2201L8.43715 16.8413C9.68627 17.37 11.1275 16.786 11.6562 15.5369C12.1849 14.2877 11.6008 12.8465 10.3516 12.3179L7.57029 11.1409Z" fill="#F5C7C7"></path> </svg>
```

**SVG-22**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 14 16" xmlns="http://www.w3.org/2000/svg"> <path d="M8.31121 10.0453C6.2473 9.75751 4.80746 7.85111 5.09523 5.78721L5.51235 2.79555C5.69963 1.45232 4.76265 0.21156 3.41945 0.0241039C2.07604 -0.163381 0.835032 0.773744 0.64766 2.11717L0.0731836 6.23607C-0.502518 10.3638 2.37687 14.1766 6.50454 14.7525L10.6239 15.3271C11.9673 15.5145 13.2083 14.5774 13.3956 13.234C13.583 11.8906 12.6458 10.6496 11.3024 10.4623L8.31121 10.0453Z" fill="#DEC7F5"></path> </svg>
```

**SVG-23**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 23 13" xmlns="http://www.w3.org/2000/svg"> <path d="M8.62775 8.2642C10.1012 6.79062 12.4902 6.79051 13.9638 8.26396L17.3279 11.6277C18.5321 12.8318 20.4844 12.8319 21.6886 11.6278C22.893 10.4236 22.8931 8.47098 21.6888 7.26666L16.6323 2.21018C13.6853 -0.736786 8.90739 -0.736856 5.96033 2.21003L0.903269 7.26679C-0.301075 8.47106 -0.301101 10.4236 0.903207 11.6279C2.10753 12.8323 4.06013 12.8322 5.2644 11.6279L8.62775 8.2642Z" fill="currentColor"></path> </svg>
```

**SVG-24**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 23 13" xmlns="http://www.w3.org/2000/svg"> <path d="M8.62775 4.26705C10.1012 5.74063 12.4902 5.74074 13.9638 4.26729L17.3279 0.903509C18.5321 -0.30058 20.4844 -0.300621 21.6886 0.903421C22.893 2.10765 22.8931 4.06027 21.6888 5.26459L16.6323 10.3211C13.6853 13.268 8.90738 13.2681 5.96033 10.3212L0.903269 5.26446C-0.301075 4.06019 -0.301102 2.10761 0.903207 0.903302C2.10753 -0.30102 4.06013 -0.300976 5.2644 0.9034L8.62775 4.26705Z" fill="currentColor"></path> </svg>
```

**SVG-25**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 11 13" xmlns="http://www.w3.org/2000/svg"> <path d="M9.36786 4.5214C10.5882 5.35359 10.5882 7.15309 9.36786 7.98528L3.27743 12.1387C1.88588 13.0876 -1.11555e-05 12.0911 -1.10818e-05 10.4067L-1.07187e-05 2.09995C-1.06451e-05 0.415617 1.88588 -0.580968 3.27743 0.368007L9.36786 4.5214Z" fill="#F69070"></path> </svg>
```

**SVG-26**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 11 13" xmlns="http://www.w3.org/2000/svg"> <path d="M9.36792 4.52115C10.5882 5.35335 10.5882 7.15284 9.36792 7.98504L3.27749 12.1384C1.88594 13.0874 4.98797e-05 12.0908 4.99533e-05 10.4065L5.03164e-05 2.0997C5.039e-05 0.415373 1.88594 -0.581212 3.2775 0.367763L9.36792 4.52115Z" fill="#F69070"></path> </svg>
```

**SVG-27**
```html
<svg preserveAspectRatio="none" fill="none" viewBox="0 0 43 41" xmlns="http://www.w3.org/2000/svg"> <path d="M18.6178 36.5039C20.0663 35.0552 22.415 35.0551 23.8636 36.5036L27.1708 39.8105C28.3547 40.9943 30.2739 40.9943 31.4578 39.8106C32.6419 38.6268 32.642 36.7071 31.458 35.5232L26.487 30.5522C23.5899 27.6551 18.8927 27.655 15.9955 30.5521L11.0239 35.5233C9.83994 36.7072 9.83991 38.6268 11.0239 39.8107C12.2078 40.9947 14.1274 40.9946 15.3113 39.8106L18.6178 36.5039Z" fill="#F3CEB9"></path> <path d="M35.038 28.3856C34.1078 26.5603 34.8335 24.3266 36.6588 23.3964L40.8258 21.273C42.3174 20.5129 42.9106 18.6876 42.1507 17.1959C41.3906 15.7039 39.565 15.1106 38.0731 15.8708L31.8093 19.0624C28.1587 20.9224 26.7071 25.3897 28.5671 29.0404L31.7588 35.3048C32.5189 36.7967 34.3445 37.3899 35.8363 36.6297C37.3282 35.8696 37.9213 34.0439 37.1611 32.5521L35.038 28.3856Z" fill="#F3CEB9"></path> <path d="M32.3911 10.2604C30.3678 10.581 28.4676 9.20055 28.147 7.17716L27.4152 2.5579C27.1532 0.904399 25.6005 -0.223745 23.947 0.0380055C22.2932 0.299796 21.1648 1.85274 21.4268 3.50651L22.5265 10.45C23.1674 14.4967 26.9675 17.2577 31.0143 16.6169L37.9584 15.5172C39.6121 15.2554 40.7404 13.7024 40.4785 12.0487C40.2166 10.3949 38.6636 9.26666 37.0098 9.52866L32.3911 10.2604Z" fill="#F3CEB9"></path> <path d="M14.3351 7.17668C14.0148 9.2001 12.1147 10.5807 10.0913 10.2603L5.47198 9.52889C3.81846 9.26707 2.26572 10.3951 2.00369 12.0486C1.74162 13.7024 2.86987 15.2554 4.52363 15.5173L11.4671 16.6171C15.5138 17.258 19.314 14.4972 19.955 10.4504L21.0551 3.5064C21.317 1.85267 20.1888 0.299694 18.535 0.0377676C16.8813 -0.224161 15.3283 0.904179 15.0665 2.55795L14.3351 7.17668Z" fill="#F3CEB9"></path> <path d="M5.82274 23.396C7.64813 24.326 8.374 26.5597 7.44403 28.3851L5.32096 32.5523C4.56099 34.044 5.15403 35.8693 6.64562 36.6295C8.13744 37.3897 9.96312 36.7966 10.7233 35.3047L13.9148 29.041C15.7749 25.3903 14.3235 20.923 10.6729 19.0629L4.40867 15.8708C2.91683 15.1106 1.09121 15.7038 0.331073 17.1957C-0.429075 18.6875 0.164151 20.5132 1.65606 21.2732L5.82274 23.396Z" fill="#F3CEB9"></path> </svg>
```

## 15. Acceptance criteria
- Compared with https://wonder-snows.vercel.app, the result matches at 1440×810, 1280×720 and 390×844 at the key moments: end of the intro, every pause and the final state.
- Positions, sizes, colors, fonts, timings and curves match this specification.
- All texts are real, editable HTML; all links are clickable `<a href="#">` with visible focus; invisible things cannot be clicked.
- Images, video and fonts load from the URLs in section 13 and there are no console errors.
- The layout adapts when the window is resized and follows section 11 on mobile.
- With `prefers-reduced-motion` the final state is shown directly.
