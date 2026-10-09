# Replication prompt — Glacius — Call for the Frost

> Complete instructions for an AI coding agent. It contains everything needed to rebuild the animated hero «Glacius — Call for the Frost» identically: structure, styles, texts, animation timings and curves, mobile view and links to the resources (images, video and fonts).

## Role and goal
Act as a senior front-end developer specialized in web animation. Build a single-screen page (hero) that exactly reproduces the animated design «Glacius — Call for the Frost» described here, without adding or removing anything.

## 1. Overview
Snowboard hero «Glacius — Call for the Frost», designed at 1920×1080 with a cold look in blues and light greys. After the loader, the automatic intro (0 → 2.3 s) lays out, over a snowy mountain, the giant letters F-R-O-S-T (the «O» is a glass letter that refracts, brightens and enlivens what lies behind it), the curved text «Call for the», the round «Explore» button with four board thumbnails and the top bar (Glacius logo, Home, About us, Services, Contact, «Login» and «Get Started»). As scrolling starts the mountain and the sky move with parallax; between 3.3 and 4.3 s clouds and a light panel with a feathered edge rise and cover the mountain; then (4.3 → 8.47 s) the round «Glacius» seal and the sentence «Feel the power of absolute precision. Tech shaped for extreme results. Where design meets high-speed action.» appear, turning from grey to black. In the last stretch (9.27 → 11.77 s) the sentence leaves and the «Crafted for the ultimate descent.» scene comes in: a snowboarder inside a large «O», three pill-shaped thumbnails, the text «Snowboards power every ride…» and the «Get Started» button, with parallax between the rider, the «O» and the snow behind. The curved texts are real text on a path (editable), not images. With the mouse, the layers drift gently against the cursor (closer ones more than distant ones), scrolling is smooth and linear with no snapping pauses, and on hover buttons and menu links subtly change tone.

## 2. Deliverable and technical rules
- A single `index.html` file with the CSS inside `<style>` and the JavaScript inside `<script>`. Plain (vanilla) JavaScript, no frameworks and no build step.
- The only external dependency: the Lenis library, exact version 1.3.26 (npm package `lenis`, file `dist/lenis.min.js`, which exposes the global class `Lenis`), for smooth scrolling. Load it with a `<script>` tag from a public npm CDN before your script (if it fails to load, everything must work with native scrolling).
- Images, video and fonts are ALWAYS loaded from the absolute URLs in section 13 «Resources». Do not download them, do not inline them as base64 and do not use any other resources.
- Every visible text is real, selectable and editable HTML text (never text turned into an image or into SVG paths). The vector shapes in the Appendix are only icons and decoration.
- Lists use `<ul>`/`<li>` (inside `<nav aria-label="…">` where indicated) and links use clickable `<a href="#">`, with `aria-label` where indicated.
- Head: `<html lang="en">`, `<meta charset="utf-8">`, `<meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover">`, `<title>Glacius — Call for the Frost</title>`, `<meta name="description" content="Glacius snowboards: call for the frost.">`, `<meta name="theme-color" content="#3b4d6a">`.
- Respect `prefers-reduced-motion: reduce`: the loader works the same, but there is no intro, no smooth scrolling and no snap and no pointer effects; t stays fixed on the final state (t = 12.57 s) even when scrolling.
- Do not add elements, texts, effects or sections that are not in this specification.

## 3. Base structure (common HTML + CSS)
```html
<body>
<div class="loader" id="loader" aria-hidden="true">
  <div class="loader-in"><span>GLACIUS</span><div class="loader-bar"><i id="loaderBar"></i></div><span id="loaderPct">000</span></div>
</div>
<main class="hero" id="hero" aria-label="Glacius — Call for the Frost">
  <div class="stage" id="stage">
    <!-- one layer (div.blk) per «Layer» in section 9, in the same order: later ones are on top -->
  </div>
</main>
<script>/* engine from section 6 */</script>
</body>
```
```css
:root{--bg:#3b4d6a;--ink:#ffffff;color-scheme:dark}
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
- Recompute everything on every `resize` and also whenever the real size of `.stage` changes without a `resize` (a `ResizeObserver` on `.stage`; e.g. when a scrollbar appears or goes away), so the layers anchored to the edges always stay attached to the frame. On every recompute also set the CSS variable `--s` on `.stage` to the current scale (s on desktop; sm in the mobile view of section 11): `stage.style.setProperty('--s', scale)`; the styles that must follow the design use it (section 10).

## 5. Fonts
Declare these fonts with `@font-face{font-family:'…';src:url(URL) format('woff2');font-weight:…;font-style:normal}` and use them with the stack `'Font', system-ui, sans-serif`:
- **Joane** (weight 400): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/fonts/Joane-Regular.woff2
- **Joane** (weight 600): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/fonts/Joane-SemiBold.woff2
- **Kulim Park** (weight 300): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/fonts/kulim-park-latin-300-normal.woff2
- **Kulim Park** (weight 400): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/fonts/kulim-park-latin-400-normal.woff2
- **Kulim Park** (weight 700): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/fonts/kulim-park-latin-700-normal.woff2
- **Marcellus SC** (weight 400): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/fonts/marcellus-sc-latin-400-normal.woff2

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
- `cubic-bezier(x1, y1, x2, y2)` curves work as in CSS: given the time progress p, solve the parameter u such that X(u) = p and return Y(u).

## 7. Playback (intro and scrolling)
- Total timeline duration: 12.57 s. Automatic intro: from t = 0 to t = 2.3 s. The rest (2.3 → 12.57 s) is driven by scrolling.
- `.hero` height = 100vh + track. track = round(L (= 8.066 s, see the scroll → time mapping) × 60) = **484vh** on desktop and round(L (= 8.066 s, see the scroll → time mapping) × 40) = **323vh** on mobile (use exactly these values; they are set through the CSS variable `--track`). `.stage` is sticky, so the stage stays fixed while the track is scrolled.
- While loading: `html.is-locked` (overflow hidden; add it before the first measurement of the stage, so a classic scrollbar does not narrow the measured width), `history.scrollRestoration = 'manual'`, `scrollTo(0,0)`.
- When the loader gets the `is-done` class, play the intro: t advances in real time (linearly) from 0 to 2.3 s in 2.3 s. During the intro scrolling is blocked (Lenis stopped; if Lenis is not available, keep `is-locked` until the intro ends) and scroll events do not change t. When it ends, t = 2.3 and scrolling is enabled.
- Smooth scrolling with Lenis: `new Lenis({lerp:0.1, wheelMultiplier:1, smoothWheel:true})`, `lenis.stop()` until the intro ends, then `lenis.start()`; call `lenis.raf(now)` in the requestAnimationFrame loop. Without Lenis, use native scrolling.
- Scroll → time mapping: p = clamp((scrollY − hero.offsetTop) / (hero.offsetHeight − stage.clientHeight), 0, 1); the scroll walks the timeline through weighted stretches: [2.301 – 3.301] with weight 0.4, [4.302 – 5.85] with weight 0.3, [8.47 – 9.27] with weight 0.35 (each second of those stretches takes weight × its normal scroll length; the rest weight 1; so pauses take less scroll and scrolling never runs «dead»). Accumulated scroll length ℓ(t) = (t − 2.3) − Σ (1 − weight) × duration of the part of each stretch between 2.3 and t; L = ℓ(12.57) = 8.066. t is the value with ℓ(t) = p × L (piecewise-linear inverse) and, conversely, the scroll position of a time t is hero.offsetTop + ℓ(t)/L × (hero.offsetHeight − stage.clientHeight).
- No snapping pauses: the page never scrolls by itself; Lenis only smooths the scroll.
- On every resize: recompute layers, track and t from the current scroll position.

## 8. Loader and preloading
- The loader covers the screen with the `--bg` color and shows «GLACIUS», a 160×1 px bar and a 3-digit percentage (`000` → `100`) in the `--ink` color.
- Weighted progress: fonts (`document.fonts.ready`) weight 1; each `<img>` element on the stage weight 0.4, even if it repeats a file (it counts once it has loaded —or failed— and `img.decode()` has finished; the video poster does not count); there is no video. Percentage = Math.round(progress × 100) with 3 digits. The bar uses `transform: scaleX(progress)` and never goes backwards.
- Wait until everything finishes (14 s maximum; if it runs out, continue without cancelling anything). Then remove `is-locked` from `<html>` (except in the no-Lenis case described in section 7), add `is-done` to the loader (it fades out in 0.9 s) and start the intro.

## 9. Layers and elements
Notation of each line: `[eN]` = suggested identifier; `box` = div; `text` = div with text (the content in quotes, keeping capitalization, line breaks and spaces); `group` = boxless container; `vector` = box containing an inline SVG; `<a href="#">`, `<ul>`, `<li>`, `<nav>` = semantic tag to use; `«name»` = layer name in the design (for reference only, also handy as `data-name`); `pos(x,y)` = left/top; `size A×B` = width×height; after `|` come the literal CSS styles. Every `text` element gets `class="t"` (used by the pointer-events rule in section 3); boxes, groups and vectors may use `class="b"`, `"g"` and `"v"`. Decorative layers (border, glass, inner shadow, background blur, fill layer) are absolute elements (for example `<i>`) covering their container (given `inset`) with inline `pointer-events:none` and `border-radius:inherit` unless stated otherwise. Images go inside an absolute container `inset:0; overflow:hidden; border-radius:inherit`. The order of the lines is the stacking order (later is on top).

#### Layer bk0 «Fondo» — BLEED LAYER (cover), design box x=0 y=0 1920×1080
- [e1] box «hero-01» pos(0,0) size 1920×1080 | background-color:#f4f5f7

#### Layer bk1 «Background» — BLEED LAYER (cover), design box x=0 y=0 2144×1324.36
- [e2] box «Background» transform translate(2031.21,-69.18) rotate(3.14rad) scale(1,-1) size 2144×1324.36
    ↳ animation: x: 0.3→2.3s 2031.21→1955.81 (E0); y: 0.3→2.3s -69.18→-22.6 (E0); 3.301→4.301s -22.6→-78.6 (linear); opacity: 6.386→8.47s 1→0 (linear); content-scale: 0.3→2.3s 1→0.93 (E0)
  - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/85c9ccdfc2-97d978.webp: absolutely positioned `<img alt="">` at (0,0), 2880×1779 px, max-width:none, transform-origin 0 0 and transform matrix(0.7442,0,0,0.7444,0.257,0); the container clips it (overflow hidden, same border-radius)
  - fill layer (covers the whole box, stacked in this order): background:linear-gradient(rgba(60,78,106,0.3),rgba(60,78,106,0.3))

#### Layer bk2 «F» — CONTAINED LAYER (contain, anchor x=-1, y=1), design box x=199 y=1115.8 225×290
- [e3] text pos(199,1115.8) size 225×290 | opacity:0.5; mix-blend-mode:lighten; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Joane'; font-weight:400; font-size:400px; line-height:1.4; letter-spacing:0.05em; text-transform:uppercase; background:linear-gradient(180deg, rgba(226,240,255,0.6) 0%, rgba(226,240,255,0.12) 100%); -webkit-background-clip:text; color:transparent | TEXT: "F"
    ↳ animation: x: 0.3→2.3s 199→197.63 (E0); y: 0.3→2.3s 1115.8→392 (E0); 3.301→4.301s 392→152 (linear); opacity: 6.386→8.47s 0.5→0 (linear)

#### Layer bk3 «R» — CONTAINED LAYER (contain, anchor x=-1, y=1), design box x=437.87 y=755.8 260×290
- [e4] text pos(437.87,755.8) size 260×290 | opacity:0.5; mix-blend-mode:lighten; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Joane'; font-weight:400; font-size:400px; line-height:1.4; letter-spacing:0.05em; text-transform:uppercase; background:linear-gradient(180deg, rgba(226,240,255,0.6) 0%, rgba(226,240,255,0.12) 100%); -webkit-background-clip:text; color:transparent | TEXT: "R"
    ↳ animation: y: 0.3→2.3s 755.8→392 (E0); 3.301→4.301s 392→212 (linear); opacity: 6.386→8.47s 0.5→0 (linear)

#### Layer bk4 «S» — CONTAINED LAYER (contain, anchor x=1, y=1), design box x=1233.53 y=755.8 230×290
- [e5] text pos(1233.53,755.8) size 230×290 | opacity:0.5; mix-blend-mode:lighten; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Joane'; font-weight:400; font-size:400px; line-height:1.4; letter-spacing:0.05em; text-transform:uppercase; background:linear-gradient(180deg, rgba(226,240,255,0.6) 0%, rgba(226,240,255,0.12) 100%); -webkit-background-clip:text; color:transparent | TEXT: "S"
    ↳ animation: y: 0.3→2.3s 755.8→392 (E0); 3.301→4.301s 392→212 (linear); opacity: 6.386→8.47s 0.5→0 (linear)

#### Layer bk5 «T» — CONTAINED LAYER (contain, anchor x=1, y=1), design box x=1467.36 y=1115.8 256×290
- [e6] text pos(1467.36,1115.8) size 256×290 | opacity:0.5; mix-blend-mode:lighten; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Joane'; font-weight:400; font-size:400px; line-height:1.4; letter-spacing:0.05em; text-transform:uppercase; background:linear-gradient(180deg, rgba(226,240,255,0.6) 0%, rgba(226,240,255,0.12) 100%); -webkit-background-clip:text; color:transparent | TEXT: "T"
    ↳ animation: y: 0.3→2.3s 1115.8→392 (E0); 3.301→4.301s 392→152 (linear); opacity: 6.386→8.47s 0.5→0 (linear)

#### Layer bk6 «Object» — BLEED LAYER (cover), design box x=0 y=540 1920×848
- [e7] box «Object» transform translate(1920,540) rotate(3.14rad) scale(1,-1) size 1920×848
    ↳ animation: y: 0.3→2.3s 540→392 (E0); 3.301→4.301s 392→202 (linear); opacity: 6.386→8.47s 1→0 (linear)
  - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/74e24220b9-7b651c.webp: absolutely positioned `<img alt="">` at (0,0), 2880×1271 px, max-width:none, transform-origin 0 0 and transform matrix(0.6665,0,0,0.6665,0.23,0.856); the container clips it (overflow hidden, same border-radius)
  - fill layer (covers the whole box, stacked in this order): background:linear-gradient(180deg, rgba(222,223,229,0) 61.16%, #f4f5f7 100%)

#### Layer bk7 «Ellipse 3» — CONTAINED LAYER (contain, anchor x=0, y=1), design box x=258.02 y=862.34 1402.79×523.49
- [e8] box «Ellipse 3» pos(258.02,862.34) size 1402.79×523.5 | opacity:0.4
    ↳ animation: opacity: 6.386→8.47s 0.4→0 (linear)
  - fill layer (covers the whole box, stacked in this order): inset:-118px
  - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/bk-e8-516e6e.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, max-width:none (stretched over its container, which overhangs the box and does not clip)

#### Layer bk8 «Mask group» — BLEED LAYER (cover), design box x=-0.59 y=647.4 1920×865.21
- [e9] group «Mask group»
    ↳ animation: opacity: jump from 1 to 0 at 2.301s
  - [e14] group
      ↳ animation: offsetY: 0.3→2.3s 0→342.88 (E0)
    - [-] mask transform matrix(1,0,0,1,-0.587,647.396) size 1920×865.21 | overflow:hidden; border-radius:0px; mask-image:linear-gradient(180deg, #000000 66.95%, rgba(0,0,0,0) 100%)
      - compensation container transform matrix(1,0,0,1,0.587,-647.396)
        - [e15] group
            ↳ animation: offsetY: 0.3→2.3s 0→-342.88 (E0)
          - [e10] group «Group 12»
              ↳ animation: opacity: jump from 1 to 0 at 2.301s
            - [e11] box «image 16» pos(-18.89,843.12) size 1938.3×544
                ↳ animation: y: 0.3→2.3s 843.12→1249.69 (E0); opacity: jump from 1 to 0 at 2.301s
              - fill layer (covers the whole box, stacked in this order): inset:-75px
              - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/bk-e11-59f310.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, max-width:none (stretched over its container, which overhangs the box and does not clip)
            - [e12] box «image 17» pos(1142.08,790.12) size 997.54×626.84
                ↳ animation: y: 0.3→2.3s 790.12→1258.69 (E0); opacity: jump from 1 to 0 at 2.301s
              - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/52f54be031-k-xca7a89.webp: absolutely positioned `<img alt="">` at (0,0), 1232×928 px, max-width:none, transform-origin 0 0 and transform matrix(0.8359,0,0,0.6754,-32.285,0); the container clips it (overflow hidden, same border-radius)
            - [e13] box «image 18» pos(-236.22,910.12) size 1757.64×460.95
                ↳ animation: y: 0.3→2.3s 910.12→1319.6 (E0); opacity: jump from 1 to 0 at 2.301s
              - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/20f1181785-b9a0ef-k-xc99560.webp: absolutely positioned `<img alt="">` at (0,0), 1904×640 px, max-width:none, transform-origin 0 0 and transform matrix(0.9498,0,0,0.7454,0,-8.068); the container clips it (overflow hidden, same border-radius)

#### Layer bk9 «O» — CONTAINED LAYER (contain, anchor x=0, y=1), design box x=675.13 y=608 583×512
- [e16] text pos(675.13,608) size 583×512 | white-space:pre; text-align:center; text-box:trim-both cap alphabetic; font-family:'Joane'; font-weight:400; font-size:707.11px; line-height:1.4; letter-spacing:0.05em; text-transform:uppercase; color:transparent | TEXT: "O" | GLASS LETTER: inside the element, after the text, in this order: `<span class="gx gx-sh" aria-hidden="true" style="-webkit-clip-path:path(evenodd,'M0 0H697V626H0ZM347.6 49.9Q460.1 49.9 531.5 122.1Q602.9 194.2 602.9 311.6Q602.9 427.5 531.1 500.7Q459.3 573.9 347.6 573.9Q235.9 573.9 164.5 500.7Q93.1 427.5 93.1 311.6Q93.1 194.2 164.5 122.1Q235.9 49.9 347.6 49.9ZM347.6 563.3Q426.8 563.3 475.3 496.1Q523.7 428.9 523.7 311.6Q523.7 192.8 475.3 126.7Q426.8 60.5 347.6 60.5Q268.4 60.5 220.3 126.7Q172.3 192.8 172.3 311.6Q172.3 428.9 220.7 496.1Q269.1 563.3 347.6 563.3Z');clip-path:path(evenodd,'M0 0H697V626H0ZM347.6 49.9Q460.1 49.9 531.5 122.1Q602.9 194.2 602.9 311.6Q602.9 427.5 531.1 500.7Q459.3 573.9 347.6 573.9Q235.9 573.9 164.5 500.7Q93.1 427.5 93.1 311.6Q93.1 194.2 164.5 122.1Q235.9 49.9 347.6 49.9ZM347.6 563.3Q426.8 563.3 475.3 496.1Q523.7 428.9 523.7 311.6Q523.7 192.8 475.3 126.7Q426.8 60.5 347.6 60.5Q268.4 60.5 220.3 126.7Q172.3 192.8 172.3 311.6Q172.3 428.9 220.7 496.1Q269.1 563.3 347.6 563.3Z')"><span>O</span></span>` + `<i class="gx gx-lens gx-rf" aria-hidden="true" style="--gxb:brightness(1.256) contrast(1.144) saturate(1.12);-webkit-clip-path:path(evenodd,'M347.6 49.9Q460.1 49.9 531.5 122.1Q602.9 194.2 602.9 311.6Q602.9 427.5 531.1 500.7Q459.3 573.9 347.6 573.9Q235.9 573.9 164.5 500.7Q93.1 427.5 93.1 311.6Q93.1 194.2 164.5 122.1Q235.9 49.9 347.6 49.9ZM347.6 563.3Q426.8 563.3 475.3 496.1Q523.7 428.9 523.7 311.6Q523.7 192.8 475.3 126.7Q426.8 60.5 347.6 60.5Q268.4 60.5 220.3 126.7Q172.3 192.8 172.3 311.6Q172.3 428.9 220.7 496.1Q269.1 563.3 347.6 563.3Z');clip-path:path(evenodd,'M347.6 49.9Q460.1 49.9 531.5 122.1Q602.9 194.2 602.9 311.6Q602.9 427.5 531.1 500.7Q459.3 573.9 347.6 573.9Q235.9 573.9 164.5 500.7Q93.1 427.5 93.1 311.6Q93.1 194.2 164.5 122.1Q235.9 49.9 347.6 49.9ZM347.6 563.3Q426.8 563.3 475.3 496.1Q523.7 428.9 523.7 311.6Q523.7 192.8 475.3 126.7Q426.8 60.5 347.6 60.5Q268.4 60.5 220.3 126.7Q172.3 192.8 172.3 311.6Q172.3 428.9 220.7 496.1Q269.1 563.3 347.6 563.3Z');--gxr:url(#gxre16)"></i>` + `<svg class="gx-defs" aria-hidden="true" width="0" height="0" style="position:absolute;width:0;height:0;overflow:hidden"><filter id="gxre16" x="0" y="0" width="697" height="626" filterUnits="userSpaceOnUse" primitiveUnits="userSpaceOnUse" color-interpolation-filters="sRGB"><feImage href="https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/gx-e16.png" x="0" y="0" width="697" height="626" preserveAspectRatio="none" result="m"/><feDisplacementMap in="SourceGraphic" in2="m" scale="77.85" xChannelSelector="R" yChannelSelector="G"/></filter></svg>` + `<span class="gx gx-fill" aria-hidden="true" style="opacity:0.55;background:linear-gradient(180deg, rgba(226,240,255,0.012) 9.11%, rgba(226,240,255,0.6) 90.89%)!important;-webkit-background-clip:text!important;background-clip:text!important"><span>O</span></span>` + `<span class="gx gx-glow" aria-hidden="true" style="--gw:8.485px;--gb:4.243px;--ga:0.336;-webkit-clip-path:path(evenodd,'M347.6 49.9Q460.1 49.9 531.5 122.1Q602.9 194.2 602.9 311.6Q602.9 427.5 531.1 500.7Q459.3 573.9 347.6 573.9Q235.9 573.9 164.5 500.7Q93.1 427.5 93.1 311.6Q93.1 194.2 164.5 122.1Q235.9 49.9 347.6 49.9ZM347.6 563.3Q426.8 563.3 475.3 496.1Q523.7 428.9 523.7 311.6Q523.7 192.8 475.3 126.7Q426.8 60.5 347.6 60.5Q268.4 60.5 220.3 126.7Q172.3 192.8 172.3 311.6Q172.3 428.9 220.7 496.1Q269.1 563.3 347.6 563.3Z');clip-path:path(evenodd,'M347.6 49.9Q460.1 49.9 531.5 122.1Q602.9 194.2 602.9 311.6Q602.9 427.5 531.1 500.7Q459.3 573.9 347.6 573.9Q235.9 573.9 164.5 500.7Q93.1 427.5 93.1 311.6Q93.1 194.2 164.5 122.1Q235.9 49.9 347.6 49.9ZM347.6 563.3Q426.8 563.3 475.3 496.1Q523.7 428.9 523.7 311.6Q523.7 192.8 475.3 126.7Q426.8 60.5 347.6 60.5Q268.4 60.5 220.3 126.7Q172.3 192.8 172.3 311.6Q172.3 428.9 220.7 496.1Q269.1 563.3 347.6 563.3Z')"><span>O</span></span>` + `<span class="gx gx-line" aria-hidden="true"><span>O</span></span>` + `<span class="gx gx-rim" aria-hidden="true" style="--gxa:0.76;-webkit-mask-image:linear-gradient(135deg,#000 0%,rgba(0,0,0,.08) 40%,rgba(0,0,0,.08) 60%,#000 100%);mask-image:linear-gradient(135deg,#000 0%,rgba(0,0,0,.08) 40%,rgba(0,0,0,.08) 60%,#000 100%)"><span>O</span></span>`
    ↳ animation: y: 0.3→2.3s 608→281 (E0); 3.301→4.301s 281→171 (linear); opacity: 6.386→8.47s 1→0 (linear)

#### Layer bk10 «Group 15» — CONTAINED LAYER (contain, anchor x=0, y=1), design box x=654.18 y=1118.09 610.07×322.34
- [e17] group «Group 15»
    ↳ animation: opacity: 6.386→8.47s 1→0 (linear)
  - [e18] group <a href="#"> «Group 13»
      ↳ animation: opacity: 6.386→8.47s 1→0 (linear)
    - [e19] box «Ellipse 2» pos(865.02,1118.09) size 188.38×188.38 | opacity:0.5; border-radius:50%
        ↳ animation: y: 0.3→2.3s 1118.09→878.09 (E0); 3.301→4.301s 878.09→828.09 (linear); opacity: 6.386→8.47s 0.5→0 (linear)
      - border (stroke): inset:-1px; border-radius:50%; border:1px solid rgba(255,255,255,0.8)
    - [e20] box «Ellipse 1» pos(898.5,1151.56) size 121.44×121.44 | border-radius:50%; background-color:#ffffff
        ↳ animation: y: 0.3→2.3s 1151.55→911.55 (E0); 3.301→4.301s 911.55→861.55 (linear); opacity: 6.386→8.47s 1→0 (linear)
    - [e21] text pos(932.22,1206.78) size 54×10 | white-space:pre; text-align:center; text-box:trim-both cap alphabetic; font-family:'Kulim Park'; font-weight:400; font-size:14px; line-height:1.4; text-transform:uppercase; color:#1f2b3f | TEXT: "Explore"
        ↳ animation: y: 0.3→2.3s 1206.78→966.78 (E0); 3.301→4.301s 966.78→916.78 (linear); opacity: 6.386→8.47s 1→0 (linear)
  - [-] list <ul>
    - [e22] box <li> «Rectangle 4» pos(654.18,1384.13) size 56.29×56.29 | border-radius:614.43px
        ↳ animation: y: 0.3→2.3s 1384.13→944.13 (E0); 3.301→4.301s 944.13→894.13 (linear); opacity: 6.386→8.47s 1→0 (linear)
      - [-] link <a href="#"> aria-label="Snowboard model 1" that covers its container's whole box (inset:0)
        - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/118451b301.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)
    - [e23] box <li> «Rectangle 5» pos(745.47,1290) size 84.56×84.56 | border-radius:614.43px
        ↳ animation: y: 0.3→2.3s 1290→930 (E0); 3.301→4.301s 930→880 (linear); opacity: 6.386→8.47s 1→0 (linear)
      - [-] link <a href="#"> aria-label="Snowboard model 2" that covers its container's whole box (inset:0)
        - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/0f7ebbf2c3.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)
    - [e24] box <li> «Rectangle 6» pos(1088.41,1290) size 84.56×84.56 | border-radius:614.43px
        ↳ animation: y: 0.3→2.3s 1290→930 (E0); 3.301→4.301s 930→880 (linear); opacity: 6.386→8.47s 1→0 (linear)
      - [-] link <a href="#"> aria-label="Snowboard model 3" that covers its container's whole box (inset:0)
        - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/d10d4fdfd0.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)
    - [e25] box <li> «Rectangle 7» pos(1207.96,1384.13) size 56.29×56.29 | border-radius:614.43px
        ↳ animation: y: 0.3→2.3s 1384.13→944.13 (E0); 3.301→4.301s 944.13→894.13 (linear); opacity: 6.386→8.47s 1→0 (linear)
      - [-] link <a href="#"> aria-label="Snowboard model 4" that covers its container's whole box (inset:0)
        - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/b78be8999a.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)

#### Layer bk11 «Call for the» — CONTAINED LAYER (contain, anchor x=0, y=-1), design box x=398.8 y=105.59 1122.39×549.89
- [e26] vector «Call for the» pos(398.8,105.59) size 1122.39×549.89 | opacity:0
    ↳ animation: x: 0.3→2.3s 398.8→398.02 (E0); y: 0.3→2.3s 105.59→173.11 (E0); 3.301→4.301s 173.11→-16.89 (linear); opacity: 0.3→2.3s 0→1 (E0); 6.386→8.47s 1→0 (linear); 9.27→11.77s 0→1 (linear)
  - text on a circular path (real, editable text, with `fill="currentColor"` so it inherits the element's animated color). Exact SVG, filling 100% of the box (position:absolute; inset:0; width:100%; height:100%; overflow:visible): `<svg preserveAspectRatio="none" width="1122.392" height="549.89" viewBox="0 0 1122.392 549.89"><defs><path id="e26_i1" d="M561.196,549.890A561.196,274.945 0 1 1 561.196,0.000A561.196,274.945 0 1 1 561.196,549.890" fill="none"/><linearGradient id="e26_i0" gradientUnits="userSpaceOnUse" x1="561.196" y1="-66.218" x2="561.196" y2="58.609"><stop offset="0" stop-color="#e2f0ff" stop-opacity="1"/><stop offset="1" stop-color="#e2f0ff" stop-opacity="0.2"/></linearGradient></defs><text fill="url(#e26_i0)" style="font-family:'Joane',system-ui,sans-serif;font-weight:600;font-size:40px;letter-spacing:46.4px;text-transform:uppercase;"><textPath href="#e26_i1" startOffset="50%" text-anchor="middle">Call for the</textPath></text></svg>`

#### Layer bk12 «Group 22» — CONTAINED LAYER (contain, anchor x=0, y=-1), design box x=47.41 y=-208.35 1821×125
- [e27] group «Group 22»
  - [e28] group <a href="#"> «Group 16»
    - [e29] box «image 21» pos(47.41,-133.35) size 32.65×32.65
        ↳ animation: y: 0.3→2.3s -133.35→25.32 (E0); ex: 3.301→4.301s 0→-1 (linear)
      - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/5ca960a21e.webp: absolutely positioned `<img alt="">` at (0,0), 2048×2048 px, max-width:none, transform-origin 0 0 and transform matrix(0.0334,0,0,0.0334,-17.887,-17.887); the container clips it (overflow hidden, same border-radius)
    - [e30] text pos(85.52,-121.13) size 91×14 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Marcellus SC'; font-weight:400; font-size:19.50px; line-height:1.4; letter-spacing:0.11em; text-transform:uppercase; color:#ffffff | TEXT: "Glacius"
        ↳ animation: y: 0.3→2.3s -121.13→37.54 (E0); tc: 3.301→4.301s [255,255,255,1]→[0,0,0,1] (linear)
  - [e31] group «Group 23»
    - [-] list <nav> aria-label="Main navigation"
      - [-] list <ul>
        - [e32] text <li> pos(670.72,-188.35) size 33×8 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kulim Park'; font-weight:400; font-size:12px; line-height:1.4; letter-spacing:0.02em; text-transform:uppercase; color:#ffffff | TEXT: "" | inline link: the text is the content of an <a href="#"> inside the element
            ↳ animation: x: 0.3→2.3s 670.72→770.72 (E0); y: 0.3→2.3s -188.35→36.65 (E0); tc: 3.301→4.301s [255,255,255,1]→[0,0,0,1] (linear)
        - [e33] text <li> pos(830.38,-208.35) size 59×8 | opacity:0.5; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kulim Park'; font-weight:400; font-size:12px; line-height:1.4; letter-spacing:0.02em; text-transform:uppercase; color:#ffffff | TEXT: "" | inline link: the text is the content of an <a href="#"> inside the element
            ↳ animation: x: 0.3→2.3s 830.38→863.72 (E0); y: 0.3→2.3s -208.35→36.65 (E0); tc: 3.301→4.301s [255,255,255,1]→[0,0,0,1] (linear)
        - [e34] text <li> pos(1016.05,-208.35) size 50×8 | opacity:0.5; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kulim Park'; font-weight:400; font-size:12px; line-height:1.4; letter-spacing:0.02em; text-transform:uppercase; color:#ffffff | TEXT: "" | inline link: the text is the content of an <a href="#"> inside the element
            ↳ animation: x: 0.3→2.3s 1016.05→982.72 (E0); y: 0.3→2.3s -208.35→36.65 (E0); tc: 3.301→4.301s [255,255,255,1]→[0,0,0,1] (linear)
        - [e35] text <li> pos(1192.72,-188.35) size 55×8 | opacity:0.5; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kulim Park'; font-weight:400; font-size:12px; line-height:1.4; letter-spacing:0.02em; text-transform:uppercase; color:#ffffff | TEXT: "" | inline link: the text is the content of an <a href="#"> inside the element
            ↳ animation: x: 0.3→2.3s 1192.72→1092.72 (E0); y: 0.3→2.3s -188.35→36.65 (E0); tc: 3.301→4.301s [255,255,255,1]→[0,0,0,1] (linear)
  - [e36] group «Group 24»
    - [e37] text <a href="#"> pos(1642.41,-113.35) size 40×10 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kulim Park'; font-weight:700; font-size:14px; line-height:1.4; letter-spacing:0.02em; text-transform:uppercase; color:#ffffff | TEXT: "Login"
        ↳ animation: y: 0.3→2.3s -113.35→36.65 (E0); tc: 3.301→4.301s [255,255,255,1]→[0,0,0,1] (linear)
    - [e38] box <a href="#"> «Frame 12» pos(1742.41,-133.35) size 126×50 | mix-blend-mode:lighten; border-radius:999px; background:linear-gradient(103.97deg, rgba(226,240,255,0.4) 13.88%, rgba(226,240,255,0.08) 89.25%)
        ↳ animation: y: 0.3→2.3s -133.35→16.65 (E0)
      - [e39] text pos(20,20) size 86×10 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kulim Park'; font-weight:400; font-size:14px; line-height:1.4; text-transform:uppercase; color:#ffffff | TEXT: "Get Started"
          ↳ animation: tc: 3.301→4.301s [255,255,255,1]→[0,0,0,1] (linear)
      - border (stroke): inset:-1px; border-radius:1000px; padding:1px; background:linear-gradient(115.98deg, rgba(255,255,255,0) -18.54%, rgba(255,255,255,0.8) 41.91%, rgba(255,255,255,0) 102.36%); 1px gradient border (gradient background clipped with mask: linear-gradient content-box exclude)

#### Layer bk13 «Rectangle 8» — BLEED LAYER (cover), design box x=0 y=0 1920×1075.41
- [e40] box «Rectangle 8» pos(-0.78,2964.59) size 1920×1075.41 | opacity:0; background:linear-gradient(180deg, rgba(197,208,221,0) 0%, #c5d0dd 100%)
    ↳ animation: y: 6.386→8.47s 2964.59→1814.59 (linear); 9.27→11.77s 1814.59→273.26 (linear); opacity: jump from 0 to 0.7 at 2.301s

#### Layer bk14 «Group 18» — CONTAINED LAYER (contain, anchor x=0, y=1), design box x=454.13 y=2636.45 1118.48×1484.87
- [e41] group «Group 18» | opacity:0
    ↳ animation: opacity: jump from 0 to 1 at 2.301s
  - [e42] box «image 22» transform translate(454.13,2636.45) rotate(0rad) size 1118.48×1484.87 | opacity:0
      ↳ animation: x: 6.386→8.47s 454.13→675.88 (linear); 9.27→11.77s 675.88→454.13 (linear); y: 6.386→8.47s 2636.45→1486.38 (linear); 9.27→11.77s 1486.38→-54.87 (linear); rotation(rad): 6.386→8.47s 0→0.27 (linear); 9.27→11.77s 0.27→0 (linear); opacity: jump from 0 to 1 at 2.301s
    - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/a4b9daac19-d35bad-x6f690a.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)
  - [e43] text pos(631.11,2789.24) size 793×697 | opacity:0; white-space:pre; text-align:center; text-box:trim-both cap alphabetic; font-family:'Joane'; font-weight:400; font-size:962.11px; line-height:1.4; letter-spacing:0.05em; text-transform:uppercase; color:transparent | TEXT: "O" | GLASS LETTER: inside the element, after the text, in this order: `<i class="gx gx-lens gx-rf" aria-hidden="true" style="--gxb:blur(2.041px) brightness(1.256) contrast(1.144) saturate(1.12);-webkit-clip-path:path(evenodd,'M472.4 67.4Q625.4 67.4 722.6 165.5Q819.7 263.6 819.7 423.4Q819.7 581.1 722.1 680.7Q624.4 780.3 472.4 780.3Q320.4 780.3 223.2 680.7Q126.1 581.1 126.1 423.4Q126.1 263.6 223.2 165.5Q320.4 67.4 472.4 67.4ZM472.4 765.9Q580.2 765.9 646.1 674.5Q712 583.1 712 423.4Q712 261.7 646.1 171.8Q580.2 81.8 472.4 81.8Q364.7 81.8 299.2 171.8Q233.8 261.7 233.8 423.4Q233.8 583.1 299.7 674.5Q365.6 765.9 472.4 765.9Z');clip-path:path(evenodd,'M472.4 67.4Q625.4 67.4 722.6 165.5Q819.7 263.6 819.7 423.4Q819.7 581.1 722.1 680.7Q624.4 780.3 472.4 780.3Q320.4 780.3 223.2 680.7Q126.1 581.1 126.1 423.4Q126.1 263.6 223.2 165.5Q320.4 67.4 472.4 67.4ZM472.4 765.9Q580.2 765.9 646.1 674.5Q712 583.1 712 423.4Q712 261.7 646.1 171.8Q580.2 81.8 472.4 81.8Q364.7 81.8 299.2 171.8Q233.8 261.7 233.8 423.4Q233.8 583.1 299.7 674.5Q365.6 765.9 472.4 765.9Z');--gxr:url(#gxre43)"></i>` + `<svg class="gx-defs" aria-hidden="true" width="0" height="0" style="position:absolute;width:0;height:0;overflow:hidden"><filter id="gxre43" x="0" y="0" width="947" height="851" filterUnits="userSpaceOnUse" primitiveUnits="userSpaceOnUse" color-interpolation-filters="sRGB"><feImage href="https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/gx-e43.png" x="0" y="0" width="947" height="851" preserveAspectRatio="none" result="m"/><feDisplacementMap in="SourceGraphic" in2="m" scale="104.99" xChannelSelector="R" yChannelSelector="G"/></filter></svg>` + `<span class="gx gx-fill" aria-hidden="true" style="opacity:0.55;color:rgba(164,164,164,0.12)!important;-webkit-text-fill-color:rgba(164,164,164,0.12)!important"><span>O</span></span>` + `<span class="gx gx-glow" aria-hidden="true" style="--gw:11.545px;--gb:5.773px;--ga:0.336;-webkit-clip-path:path(evenodd,'M472.4 67.4Q625.4 67.4 722.6 165.5Q819.7 263.6 819.7 423.4Q819.7 581.1 722.1 680.7Q624.4 780.3 472.4 780.3Q320.4 780.3 223.2 680.7Q126.1 581.1 126.1 423.4Q126.1 263.6 223.2 165.5Q320.4 67.4 472.4 67.4ZM472.4 765.9Q580.2 765.9 646.1 674.5Q712 583.1 712 423.4Q712 261.7 646.1 171.8Q580.2 81.8 472.4 81.8Q364.7 81.8 299.2 171.8Q233.8 261.7 233.8 423.4Q233.8 583.1 299.7 674.5Q365.6 765.9 472.4 765.9Z');clip-path:path(evenodd,'M472.4 67.4Q625.4 67.4 722.6 165.5Q819.7 263.6 819.7 423.4Q819.7 581.1 722.1 680.7Q624.4 780.3 472.4 780.3Q320.4 780.3 223.2 680.7Q126.1 581.1 126.1 423.4Q126.1 263.6 223.2 165.5Q320.4 67.4 472.4 67.4ZM472.4 765.9Q580.2 765.9 646.1 674.5Q712 583.1 712 423.4Q712 261.7 646.1 171.8Q580.2 81.8 472.4 81.8Q364.7 81.8 299.2 171.8Q233.8 261.7 233.8 423.4Q233.8 583.1 299.7 674.5Q365.6 765.9 472.4 765.9Z')"><span>O</span></span>` + `<span class="gx gx-line" aria-hidden="true"><span>O</span></span>` + `<span class="gx gx-b" aria-hidden="true" style="translate:-2.2px 0"><span>O</span></span>` + `<span class="gx gx-o" aria-hidden="true" style="translate:2.2px 0"><span>O</span></span>` + `<span class="gx gx-rim" aria-hidden="true" style="--gxa:0.76;-webkit-mask-image:linear-gradient(135deg,#000 0%,rgba(0,0,0,.08) 40%,rgba(0,0,0,.08) 60%,#000 100%);mask-image:linear-gradient(135deg,#000 0%,rgba(0,0,0,.08) 40%,rgba(0,0,0,.08) 60%,#000 100%)"><span>O</span></span>`
      ↳ animation: y: 6.386→8.47s 2789.24→1312 (linear); 9.27→11.77s 1312→97.92 (linear); opacity: jump from 0 to 1 at 2.301s

#### Layer bk15 «image 23» — BLEED LAYER (cover), design box x=0 y=0 2880×1632
- [e44] box «image 23» transform translate(-562.58,2408) rotate(0rad) size 2880×1632 | opacity:0
    ↳ animation: x: 6.386→8.47s -562.59→-306.22 (linear); 9.27→11.77s -306.22→-562.59 (linear); y: 6.386→8.47s 2408→1645.08 (linear); 9.27→11.77s 1645.08→-283.33 (linear); rotation(rad): 6.386→8.47s 0→0.26 (linear); 9.27→11.77s 0.26→0 (linear); opacity: jump from 0 to 1 at 2.301s
  - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/625a207830-0c009b-k-x680d9b.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)

#### Layer bk16 «Snowboards power every ride. Design improves control and balance. Shape and flex move together as system.» — CONTAINED LAYER (contain, anchor x=1, y=1), design box x=1401.61 y=3720 470×102
- [e45] text pos(1401.61,3720) size 470×102 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Kulim Park'; font-weight:300; font-size:24px; line-height:1.4; color:#000000 | TEXT: "Snowboards power every ride. Design improves control and balance. Shape and flex move together as system."
    ↳ animation: y: 6.386→8.47s 3720→2624 (linear); 9.27→11.77s 2624→728.67 (linear); height: 6.386→8.47s 102→201 (linear); 9.27→11.77s 201→102 (linear); opacity: jump from 0 to 1 at 2.301s

#### Layer bk17 «CRAFTED FOR THE ULTIMATE DESCENT.» — CONTAINED LAYER (contain, anchor x=-1, y=1), design box x=39.61 y=2901.74 597×372
- [e46] text pos(39.61,2901.74) size 597×372 | opacity:0; white-space:pre-wrap; text-align:left; text-box:trim-both cap alphabetic; font-family:'Joane'; font-weight:400; font-size:100px; line-height:1; letter-spacing:-0.04em; text-transform:uppercase; color:#000000 | TEXT: "CRAFTED FOR THE ULTIMATE DESCENT."
    ↳ animation: y: 6.386→8.47s 2901.74→1589 (linear); 9.27→11.77s 1589→210.42 (linear); opacity: jump from 0 to 1 at 2.301s

#### Layer bk18 «Frame 12» — CONTAINED LAYER (contain, anchor x=1, y=1), design box x=1402.2 y=3871.48 227×81
- [e47] box <a href="#"> «Frame 12» pos(1402.2,3871.48) size 227×81 | opacity:0; border-radius:999px; background-color:#ffffff
    ↳ animation: y: 6.386→8.47s 3871.48→2856 (linear); 9.27→11.77s 2856→880.16 (linear); opacity: jump from 0 to 1 at 2.301s
  - [e48] text pos(40,32) size 147×17 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kulim Park'; font-weight:400; font-size:24px; line-height:1.4; text-transform:uppercase; color:#000000 | TEXT: "Get Started"
      ↳ animation: opacity: jump from 0 to 1 at 2.301s

#### Layer bk19 «Rectangle 11» — CONTAINED LAYER (contain, anchor x=-1, y=1), design box x=48.2 y=3452 191×328
- [e49] box «Rectangle 11» pos(48.2,3452) size 191×328 | opacity:0; border-radius:99px
    ↳ animation: y: 6.386→8.47s 3452→2242 (linear); 9.27→11.77s 2242→701 (linear); opacity: jump from 0 to 1 at 2.301s
  - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/9f3a3cc57b.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)

#### Layer bk20 «Rectangle 12» — CONTAINED LAYER (contain, anchor x=-1, y=1), design box x=247.2 y=3538.24 191×328
- [e50] box «Rectangle 12» pos(247.2,3538.24) size 191×328 | opacity:0; border-radius:99px
    ↳ animation: y: 6.386→8.47s 3538.24→2562 (linear); 9.27→11.77s 2562→701 (linear); opacity: jump from 0 to 1 at 2.301s
  - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/942b055eae.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)

#### Layer bk21 «Rectangle 13» — CONTAINED LAYER (contain, anchor x=-1, y=1), design box x=446.2 y=3624.48 191×328
- [e51] box «Rectangle 13» pos(446.2,3624.48) size 191×328 | opacity:0; border-radius:99px
    ↳ animation: y: 6.386→8.47s 3624.48→2856 (linear); 9.27→11.77s 2856→701 (linear); opacity: jump from 0 to 1 at 2.301s
  - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/f8f2aa4a48.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, object-fit:cover (clipped by the container)

#### Layer bk22 «Group 25» — BLEED LAYER (cover), design box x=0 y=0 1920.59×1596
- [e52] group «Group 25» | opacity:0
    ↳ animation: opacity: jump from 0 to 1 at 2.301s; 9.27→11.77s 1→0 (linear)
  - [e53] box «Rectangle 14» pos(0,1412.61) size 1920×1053.67 | opacity:0; background-color:#f4f5f7
      ↳ animation: x: 3.301→4.301s 0→-49.62 (linear); y: 3.301→4.301s 1412.61→-127.67 (linear); width: 3.301→4.301s 1920→2019.24 (linear); height: 3.301→4.301s 1053.67→1484.71 (linear); opacity: jump from 0 to 1 at 2.301s; 9.27→11.77s 1→0 (linear)
  - [e54] group «Mask group» | opacity:0
      ↳ animation: opacity: jump from 0 to 1 at 2.301s; 9.27→11.77s 1→0 (linear)
    - [e59] group
        ↳ animation: offsetY: 3.301→4.301s 0→-1540.28 (linear)
      - [-] mask transform matrix(1,0,0,1,-0.587,870.28) size 1920×865.21 | overflow:hidden; border-radius:0px; mask-image:linear-gradient(180deg, #000000 66.95%, rgba(0,0,0,0) 100%)
        - compensation container transform matrix(1,0,0,1,0.587,-870.28)
          - [e60] group
              ↳ animation: offsetY: 3.301→4.301s 0→1540.28 (linear)
            - [e55] group «Group 12» | opacity:0
                ↳ animation: opacity: jump from 0 to 1 at 2.301s; 9.27→11.77s 1→0 (linear)
              - [e56] box «image 16» pos(-18.89,1129.69) size 1938.3×544 | opacity:0
                  ↳ animation: y: 3.301→4.301s 1129.69→-514.45 (linear); 4.302→6.386s -514.45→-410.59 (linear); height: 3.301→4.301s 544→647.86 (linear); 4.302→6.386s 647.86→544 (linear); opacity: jump from 0 to 1 at 2.301s; 9.27→11.77s 1→0 (linear)
                - fill layer (covers the whole box, stacked in this order): inset:-75px
                - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/bk-e56-35dde9.webp: absolutely positioned `<img alt="">`, inset:0, width/height 100%, max-width:none (stretched over its container, which overhangs the box and does not clip)
              - [e57] box «image 17» transform translate(1142.07,1138.69) rotate(0rad) size 997.54×626.84 | opacity:0
                  ↳ animation: x: 3.301→4.301s 1142.08→1115 (linear); 4.302→6.386s 1115→1142.08 (linear); y: 3.301→4.301s 1138.69→-544.49 (linear); 4.302→6.386s -544.49→-401.59 (linear); opacity: jump from 0 to 1 at 2.301s; 9.27→11.77s 1→0 (linear); content-scale: 3.301→4.301s 1→1.18 (linear); 4.302→6.386s 1.18→1 (linear)
                - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/52f54be031-k-xca7a89.webp: absolutely positioned `<img alt="">` at (0,0), 1232×928 px, max-width:none, transform-origin 0 0 and transform matrix(0.8359,0,0,0.6754,-32.285,0); the container clips it (overflow hidden, same border-radius)
              - [e58] box «image 18» pos(-236.22,1199.6) size 1757.64×460.95 | opacity:0
                  ↳ animation: y: 3.301→4.301s 1199.6→-371.95 (linear); 4.302→6.386s -371.95→-340.68 (linear); opacity: jump from 0 to 1 at 2.301s; 9.27→11.77s 1→0 (linear)
                - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/20f1181785-b9a0ef-k-xc99560.webp: absolutely positioned `<img alt="">` at (0,0), 1904×640 px, max-width:none, transform-origin 0 0 and transform matrix(0.9498,0,0,0.7454,0,-8.068); the container clips it (overflow hidden, same border-radius)

#### Layer bk23 «Frame 18» — CONTAINED LAYER (contain, anchor x=0, y=1), design box x=-0.2 y=1762.64 1920×639.36
- [e61] box «Frame 18» pos(-0.2,1762.64) size 1920×639.36 | opacity:0; overflow:hidden; background-color:#cacdd4
    ↳ animation: y: 3.301→4.301s 1762.64→2032.64 (linear); 4.302→6.386s 2032.64→390.39 (linear); 6.386→8.47s 390.39→142.64 (linear); 9.27→11.77s 142.64→-937.36 (linear); opacity: jump from 0 to 1 at 2.301s
  - [e62] box «Rectangle 10» pos(0,-560) size 1920×600 | opacity:0; background-color:#000000
      ↳ animation: y: 4.302→6.386s -560→-328.8 (linear); 6.386→8.47s -328.8→20 (linear); height: 4.302→6.386s 600→477.86 (linear); 6.386→8.47s 477.86→600 (linear); opacity: jump from 0 to 1 at 2.301s
  - [e63] group «Subtract» | mix-blend-mode:lighten; opacity:0
      ↳ animation: opacity: jump from 0 to 1 at 2.301s
    - [e64] box pos(0,0) size 1920×639.36 | background-color:rgba(244,245,247,1)
        ↳ animation: y: 3.301→4.301s 0→-131.82 (linear); 4.302→6.386s -131.82→-131.32 (linear); 6.386→8.47s -131.32→-131.82 (linear); height: 3.301→4.301s 639.36→903 (linear); 4.302→6.386s 903→902 (linear); 6.386→8.47s 902→903 (linear)
    - [e65] text pos(115.5,185.51) size 1689×372 | opacity:0; white-space:pre-wrap; text-align:center; text-box:trim-both cap alphabetic; font-family:'Joane'; font-weight:400; font-size:100px; line-height:1; letter-spacing:-0.04em; text-transform:uppercase; color:#d1d3d6 | TEXT: "Feel the power of absolute precision. Tech shaped for extreme results. Where design meets high-speed action."
        ↳ animation: opacity: jump from 0 to 1 at 2.301s

#### Layer bk24 «Group 21» — CONTAINED LAYER (contain, anchor x=0, y=1), design box x=860.44 y=1616.58 199.12×237.42
- [e66] group «Group 21» | opacity:0
    ↳ animation: opacity: jump from 0 to 0.4 at 2.301s
  - [e67] group «Group 19» | opacity:0
      ↳ animation: opacity: jump from 0 to 1 at 2.301s
    - [e68] vector «Glacius Glacius Glacius Glacius» pos(876,1632.14) size 168.01×206.3 | opacity:0
        ↳ animation: y: 3.301→4.301s 1632.14→1362.14 (linear); 4.302→6.386s 1362.14→259.89 (linear); 6.386→8.47s 259.89→-66.5 (linear); 9.27→11.77s -66.5→-1590.22 (linear); opacity: jump from 0 to 1 at 2.301s
      - text on a circular path (real, editable text, with `fill="currentColor"` so it inherits the element's animated color). Exact SVG, filling 100% of the box (position:absolute; inset:0; width:100%; height:100%; overflow:visible): `<svg preserveAspectRatio="none" width="1122.392" height="549.89" viewBox="0 0 1122.392 549.89"><defs><path id="e26_i1" d="M561.196,549.890A561.196,274.945 0 1 1 561.196,0.000A561.196,274.945 0 1 1 561.196,549.890" fill="none"/><linearGradient id="e26_i0" gradientUnits="userSpaceOnUse" x1="561.196" y1="-66.218" x2="561.196" y2="58.609"><stop offset="0" stop-color="#e2f0ff" stop-opacity="1"/><stop offset="1" stop-color="#e2f0ff" stop-opacity="0.2"/></linearGradient></defs><text fill="url(#e26_i0)" style="font-family:'Joane',system-ui,sans-serif;font-weight:600;font-size:40px;letter-spacing:46.4px;text-transform:uppercase;"><textPath href="#e26_i1" startOffset="50%" text-anchor="middle">Call for the</textPath></text></svg>`
    - [e69] box «image 21» pos(929.52,1704.81) size 60.95×60.95 | opacity:0
        ↳ animation: y: 3.301→4.301s 1704.81→1434.81 (linear); 4.302→6.386s 1434.81→332.56 (linear); 6.386→8.47s 332.56→6.17 (linear); 9.27→11.77s 6.17→-1517.55 (linear); opacity: jump from 0 to 1 at 2.301s
      - image https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/5ca960a21e-efef81-x9cd1dc.webp: absolutely positioned `<img alt="">` at (0,0), 2048×2048 px, max-width:none, transform-origin 0 0 and transform matrix(0.0623,0,0,0.0623,-33.396,-33.396); the container clips it (overflow hidden, same border-radius)
  - [e70] box «Ellipse 4» pos(884.77,1664.11) size 7.63×7.63 | opacity:0; border-radius:50%; background-color:#3b4c68
      ↳ animation: y: 3.301→4.301s 1664.11→1394.11 (linear); 4.302→6.386s 1394.11→291.86 (linear); 6.386→8.47s 291.86→-34.53 (linear); 9.27→11.77s -34.53→-1558.25 (linear); opacity: jump from 0 to 0.2 at 2.301s
  - [e71] box «Ellipse 6» pos(1024.11,1657.44) size 7.63×7.63 | opacity:0; border-radius:50%; background-color:#3b4c68
      ↳ animation: y: 3.301→4.301s 1657.44→1387.44 (linear); 4.302→6.386s 1387.44→285.19 (linear); 6.386→8.47s 285.19→-41.21 (linear); 9.27→11.77s -41.21→-1564.93 (linear); opacity: jump from 0 to 0.2 at 2.301s
  - [e72] box «Ellipse 7» pos(1024.11,1804.23) size 7.63×7.63 | opacity:0; border-radius:50%; background-color:#3b4c68
      ↳ animation: y: 3.301→4.301s 1804.23→1534.23 (linear); 4.302→6.386s 1534.23→431.98 (linear); 6.386→8.47s 431.98→105.59 (linear); 9.27→11.77s 105.59→-1418.13 (linear); opacity: jump from 0 to 0.2 at 2.301s
  - [e73] box «Ellipse 8» pos(892.4,1804.23) size 7.63×7.63 | opacity:0; border-radius:50%; background-color:#3b4c68
      ↳ animation: y: 3.301→4.301s 1804.23→1534.23 (linear); 4.302→6.386s 1534.23→431.98 (linear); 6.386→8.47s 431.98→105.59 (linear); 9.27→11.77s 105.59→-1418.13 (linear); opacity: jump from 0 to 0.2 at 2.301s

## 10. Final adjustments
### Pointer effects (only with a fine pointer `(hover: hover) and (pointer: fine)` and no reduced-motion)
- Pointer position normalized to the stage: nx = clamp((x/W − 0.5)·2, −1, 1), ny = clamp((y/H − 0.5)·2, −1, 1) (0 until the first `pointermove` and while the pointer is outside the page: `pointerleave` on `document.documentElement`). Per-frame smoothing: ns += (n − ns) × 0.06.
- Inverted cursor follow per element: each listed element [fx, fy] moves by (ns_x·fx·w, ns_y·fy·w) design px (negative values: it moves against the pointer), added in its CSS `translate` property to its time-linked offset (if any): [e2] «Background» [-12, -8], [e7] «Object» [-18, -10], [e8] «Ellipse 3» [-20, -12], [e3] «F» [-26, -16], [e4] «R» [-26, -16], [e5] «S» [-26, -16], [e6] «T» [-26, -16], [e16] «O» [-34, -20], [e26] «Call for the» [-22, -14], [e17] «Group 15» [-30, -18], [e56] «image 16» [-8, -16], [e57] «image 17» [-10, -18], [e58] «image 18» [-8, -14], [e66] «Group 21» [-24, -14], [e61] «Frame 18» [0, -10], [e44] «image 23» [-12, -8], [e41] «Group 18» [-40, -24], [e46] «CRAFTED FOR THE ULTIMATE DESCENT.» [-20, -12], [e49] «Rectangle 11» [-26, -16], [e50] «Rectangle 12» [-28, -17], [e51] «Rectangle 13» [-30, -18], [e45] «Snowboards power every ride. Design impr» [-16, -10], [e47] «Frame 12» [-16, -10]. Weight w = 1. Disabled on mobile.
- Time-linked vertical offset of [e7] «Object»: CSS property `translate: 0px Ypx` (added to its animated position), with Y in design px (scaled with its layer). Keyframes (t, value): (2.3 s, 0) → (3.301 s, -90) [linear] → (4.301 s, -140) [linear]; between two keyframes interpolate with the curve given on the second; before the first and after the last keep the end value. Applies on desktop and mobile; if several entries affect the same element their values add up.
- Time-linked vertical offset of [e2] «Background»: CSS property `translate: 0px Ypx` (added to its animated position), with Y in design px (scaled with its layer). Keyframes (t, value): (2.3 s, 0) → (3.301 s, -30) [linear] → (4.301 s, -50) [linear]; between two keyframes interpolate with the curve given on the second; before the first and after the last keep the end value. Applies on desktop and mobile; if several entries affect the same element their values add up.
- Time-linked vertical offset of [e42] «image 22»: CSS property `translate: 0px Ypx` (added to its animated position), with Y in design px (scaled with its layer). Keyframes (t, value): (9.27 s, 280) → (11.77 s, 120) [linear] → (12.57 s, 0) [cubic-bezier(0.25, 0.1, 0.25, 1)]; between two keyframes interpolate with the curve given on the second; before the first and after the last keep the end value. Applies on desktop and mobile; if several entries affect the same element their values add up.
- Time-linked vertical offset of [e43] «O»: CSS property `translate: 0px Ypx` (added to its animated position), with Y in design px (scaled with its layer). Keyframes (t, value): (9.27 s, 140) → (11.77 s, 60) [linear] → (12.57 s, 0) [cubic-bezier(0.25, 0.1, 0.25, 1)]; between two keyframes interpolate with the curve given on the second; before the first and after the last keep the end value. Applies on desktop and mobile; if several entries affect the same element their values add up.
- Time-linked vertical offset of [e44] «image 23»: CSS property `translate: 0px Ypx` (added to its animated position), with Y in design px (scaled with its layer). Keyframes (t, value): (9.27 s, -80) → (11.77 s, -40) [linear] → (12.57 s, 0) [cubic-bezier(0.25, 0.1, 0.25, 1)]; between two keyframes interpolate with the curve given on the second; before the first and after the last keep the end value. Applies on desktop and mobile; if several entries affect the same element their values add up.
- Performance: every element with position, rotation, scale or opacity animation (or a time-linked offset) gets `will-change: transform` so it is composited on its own layer, except those holding a descendant with `mix-blend-mode` (the layer would isolate the blend).
- Glass letters with refraction: the SVG filter of each lens (`<svg class="gx-defs">`, section 9) only works as a backdrop-filter in Chromium browsers: add the class `gx-rf` to `<html>` when `navigator.userAgentData` exists; in other browsers the lens uses `--gxb` only.
- Literal CSS for the glass letters (the `.gx` layers described in section 9):
```css
.blk .gx{position:absolute;inset:calc(var(--gp,0px) * -1);padding:var(--gp,0px);text-box:inherit;white-space:inherit;text-align:inherit;display:inherit;flex-direction:inherit;justify-content:inherit;pointer-events:none;user-select:none;-webkit-user-select:none}
.blk .gx,.blk .gx *{background:none!important;-webkit-background-clip:border-box!important;background-clip:border-box!important;text-shadow:none!important;color:transparent!important;-webkit-text-fill-color:transparent!important;pointer-events:none!important}
.blk .gx-fill *{color:inherit!important;-webkit-text-fill-color:inherit!important}
.blk .gx-lens{-webkit-backdrop-filter:var(--gxb,blur(1px) brightness(1.25) saturate(1.15) contrast(1.05));backdrop-filter:var(--gxb,blur(1px) brightness(1.25) saturate(1.15) contrast(1.05))}
html.gx-rf .blk .gx-lens.gx-rf{backdrop-filter:var(--gxr) var(--gxb)}
.blk .gx-glow{filter:blur(var(--gb,3px))}
.blk .gx-glow *{-webkit-text-stroke:var(--gw,8px) rgba(255,255,255,var(--ga,.35))}
.blk .gx-lift{mix-blend-mode:soft-light}
.blk .gx-lift,.blk .gx-lift *{color:#fff!important;-webkit-text-fill-color:#fff!important}
.blk .gx-depth{filter:blur(5px)}
.blk .gx-depth *{-webkit-text-stroke:10px rgba(52,72,104,.16)}
.blk .gx-line *{-webkit-text-stroke:1.2px rgba(96,118,150,.35)}
.blk .gx-rim *{-webkit-text-stroke:2.6px rgba(255,255,255,var(--gxa,.9))}
.blk .gx-b,.blk .gx-o{-webkit-mask-image:linear-gradient(90deg,transparent 55%,#000 85%);mask-image:linear-gradient(90deg,transparent 55%,#000 85%)}
.blk .gx-b *{-webkit-text-stroke:2px rgba(70,150,255,.55)}
.blk .gx-o *{-webkit-text-stroke:2px rgba(255,150,60,.45)}
```
### Hover states (only when `matchMedia("(hover: hover) and (pointer: fine)")` matches; no hover on touch)
- Menu and text links (subtle colour change): [e32] «Home», [e33] «About us», [e34] «Services», [e35] «contact», [e37] «Login». On pointer enter or focus, for the link and each of its descendants read its computed colour c (skip it if its alpha is 0, e.g. gradient text) and store the variable `--hvc` = rgb(c + (#8fb5e2 − c) × 0.6) per channel (alpha 1); add the class `hv-tr` and, on the next frame, `is-hv`. On leave (or blur) remove `is-hv` and, 420 ms later, `hv-tr`.
- Buttons (subtle background change): [e18] «Explore» → layer in its child box with a visible surface that is largest on screen with background rgba(255,255,255,.18); [e38] «Get Started» → layer in the link itself with background rgba(255,255,255,.18); [e47] «Get Started» → layer in the link itself with background rgba(143,181,226,.22). In each one insert `<i class="hvo" aria-hidden="true">` inside the given box, right after its fill/effect layers (`i.pl`, `i.bb`, `i.gl`, `i.is`) and before its content, with that background colour; on pointer enter or focus the link adds `is-hv` to that layer and removes it on leave or blur.
- Icon or logo links without a surface of their own (drop to 0.72 opacity on hover): [e22] «Snowboard model 1», [e23] «Snowboard model 2», [e24] «Snowboard model 3», [e25] «Snowboard model 4», [e28] «Glacius». They get the class `hv-ic`.
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
.blk[data-k="Group 22"]{z-index:5}
#e53::before{content:"";position:absolute;left:0;right:0;bottom:100%;height:320px;pointer-events:none;background:linear-gradient(180deg,rgba(244,245,247,0) 0%,rgba(244,245,247,.55) 55%,#f4f5f7 100%)}
html:not(.is-m) .blk[data-k="Object"]{scale:1.025;transform-origin:960px 0px}
```

## 11. Mobile view
- It activates when the stage width is < 768 px or the width/height ratio is < 0.82 (add the class `is-m` to `<html>`). Mobile reference design: 390×844 px.
- sm = min(W/390, H/844); ox = (W − 390·sm)/2; oy = (H − 844·sm)/2.
- Listed contained layer with {x, y, z, ax, ay}: its scale is sm·z (z = 1 if not given) and the top-left corner of its «design box» is placed on screen at (ox·(1+ax) + x·sm, oy·(1+ay) + y·sm) (ax, ay = 0 if not given); the rest of its content keeps its position relative to that box.
- Bleed layer: z = max(W/1920, H/1080)·(given z or 1); screen = ((W − 1920·z)·fx + X·z, (H − 1080·z)·fy + Y·z), with fx, fy = 0.5 unless another value is given.
- Contained layers that are not in the list are hidden on mobile.
- Per-element adjustments: «hide» = `display:none`; «shift dx, dy» = move the element by that distance in px of its container (if there is also «scale ×z», it is scaled from its top-left corner); «multiply its animated offsets by [kx, ky]» = multiply the total value of offsetX by kx and of offsetY by ky (the mobile «shift dx, dy» is not multiplied); «fade out between t1 and t2» = multiply its opacity by clamp((t2 − t)/(t2 − t1), 0, 1) from t1 on; «place the element point (rx, ry) at (mx, my) of the mobile design at scale e×sm» = draw the element at scale Se = e·sm with its top-left corner (animated x, y) at (ox + mx·sm + (x − rx)·Se, oy + my·sm + (y − ry)·Se); «width» = fixed width in px; «transition … between t1 and t2» = go from its layer's normal placement (the mobile one) to that placement with smoothstep interpolation (u²·(3 − 2u)) of scale and origin between those times.
- On mobile the track uses 40 vh per second (see section 7) and the pointer effects from section 10 are disabled.
Mobile layer placement:
- layer «F»: x=16.25, y=515.31, z=0.23
- layer «R»: x=72.33, y=430.71, z=0.23
- layer «S»: x=259.39, y=430.71, z=0.23
- layer «T»: x=314.14, y=515.31, z=0.23
- layer «O»: x=128.03, y=395.98, z=0.23
- layer «Call for the»: x=63.11, y=277.89, z=0.23
- layer «Object»: x=-246.6, y=595.08, z=0.46
- layer «Mask group»: x=-246.6, y=644.87, z=0.46
- layer «Ellipse 3»: x=-127.64, y=743.72, z=0.46
- layer «Group 15»: x=36.38, y=829.8, z=0.52
- layer «Group 22»: x=16, y=-150.75, z=0.75
- layer «Frame 18»: x=-1.8, y=689.1, z=0.2
- layer «Group 21»: x=145.22, y=986.12, z=0.5
- layer «CRAFTED FOR THE ULTIMATE DESCENT.»: x=20, y=1572.23, z=0.55
- layer «Group 18»: x=41.64, y=1096.31, z=0.31
- layer «Snowboards power every ride. Design improves control and balance. Shape and flex move together as system.»: x=20, y=2494.8, z=0.6
- layer «Frame 12»: x=20, y=2568.8, z=0.6
Unlisted non-bleed layers are hidden on mobile.
Per-element mobile adjustments:
- [e31] «Group 23»: hide
- [e37] «Login»: shift dx=-1323, dy=0 (design units of its layer)
- [e38] «Frame 12»: shift dx=-1344, dy=0 (design units of its layer)
- [e61] «Frame 18»: fade out between t=9.3s and 10.1s

## 12. Semantics and accessibility
- `<main class="hero" aria-label="Glacius — Call for the Frost">`; the loader has `aria-hidden="true"`.
- Lists with `<ul>` and `<li>`; menus inside `<nav aria-label="…">`. Each `<a href="#">` uses the given `aria-label` when its content is not text. If a link covers a whole box, it goes inside it as an absolute `<a class="lk">` covering it (`inset:0`).
- Decorative images with `alt=""`; decorative SVGs with `aria-hidden="true"`.
- Only texts and links receive pointer events (CSS in section 3) and invisible elements get `visibility:hidden`, so nothing that cannot be seen can ever be clicked.
- Visible focus: `outline: 2px solid currentColor; outline-offset: 3px`.

## 13. Resources (absolute URLs)
All resources are served from a CDN with CORS enabled; use these URLs exactly as given:
- `assets/fonts/Joane-Regular.woff2` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/fonts/Joane-Regular.woff2
- `assets/fonts/Joane-SemiBold.woff2` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/fonts/Joane-SemiBold.woff2
- `assets/fonts/kulim-park-latin-300-normal.woff2` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/fonts/kulim-park-latin-300-normal.woff2
- `assets/fonts/kulim-park-latin-400-normal.woff2` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/fonts/kulim-park-latin-400-normal.woff2
- `assets/fonts/kulim-park-latin-700-normal.woff2` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/fonts/kulim-park-latin-700-normal.woff2
- `assets/fonts/marcellus-sc-latin-400-normal.woff2` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/fonts/marcellus-sc-latin-400-normal.woff2
- `assets/img/0f7ebbf2c3.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/0f7ebbf2c3.webp
- `assets/img/118451b301.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/118451b301.webp
- `assets/img/20f1181785-b9a0ef-k-xc99560.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/20f1181785-b9a0ef-k-xc99560.webp
- `assets/img/52f54be031-k-xca7a89.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/52f54be031-k-xca7a89.webp
- `assets/img/5ca960a21e-efef81-x9cd1dc.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/5ca960a21e-efef81-x9cd1dc.webp
- `assets/img/5ca960a21e.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/5ca960a21e.webp
- `assets/img/625a207830-0c009b-k-x680d9b.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/625a207830-0c009b-k-x680d9b.webp
- `assets/img/74e24220b9-7b651c.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/74e24220b9-7b651c.webp
- `assets/img/85c9ccdfc2-97d978.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/85c9ccdfc2-97d978.webp
- `assets/img/942b055eae.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/942b055eae.webp
- `assets/img/9f3a3cc57b.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/9f3a3cc57b.webp
- `assets/img/a4b9daac19-d35bad-x6f690a.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/a4b9daac19-d35bad-x6f690a.webp
- `assets/img/b78be8999a.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/b78be8999a.webp
- `assets/img/bk-e11-59f310.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/bk-e11-59f310.webp
- `assets/img/bk-e56-35dde9.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/bk-e56-35dde9.webp
- `assets/img/bk-e8-516e6e.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/bk-e8-516e6e.webp
- `assets/img/d10d4fdfd0.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/d10d4fdfd0.webp
- `assets/img/f8f2aa4a48.webp` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/f8f2aa4a48.webp
- `assets/img/gx-e16.png` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/gx-e16.png
- `assets/img/gx-e43.png` → https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/gx-e43.png

## 14. Appendix: vector shapes
Each SVG fills 100% of its element's box (`position:absolute; inset:0; width:100%; height:100%; overflow:visible`, `preserveAspectRatio="none"`, `aria-hidden="true"`). If an element says «with fill #xxxxxx (instead of #yyyyyy)», use the same SVG changing that fill color. If an SVG with internal `id`s (masks, gradients) is used more than once, give each copy unique ids and update its `url(#…)` references.

No vector shapes.

## 15. Acceptance criteria
- At 1920×1080, 1280×720 and 390×844 the composition is identical at the key moments: end of the intro, every pause and the final state.
- Positions, sizes, colors, fonts, timings and curves match this specification.
- All texts are real, editable HTML; all links are clickable `<a href="#">` with visible focus; invisible things cannot be clicked.
- Images, video and fonts load from the URLs in section 13 and there are no console errors.
- The layout adapts when the window is resized and follows section 11 on mobile.
- With `prefers-reduced-motion` the final state is shown directly.
