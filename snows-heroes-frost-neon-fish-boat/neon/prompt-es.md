# Prompt de replicación — S5motors — The Perfect Balance of Industrial Speed

> Instrucción completa para un agente de IA que programe. Contiene todo lo necesario para reconstruir de forma idéntica el hero animado «S5motors — The Perfect Balance of Industrial Speed»: estructura, estilos, textos, tiempos y curvas de animación, vista móvil y enlaces a los recursos (imágenes, video y tipografías).

## Rol y objetivo
Actúa como desarrollador/a front-end senior especializado/a en animación web. Construye una página de una sola pantalla (hero) que reproduzca con exactitud el diseño animado «S5motors — The Perfect Balance of Industrial Speed» descrito aquí, sin añadir ni quitar nada.

## 1. Descripción general
Hero de automoción «S5motors — The Perfect Balance of Industrial Speed», diseñado a 1440×810 con una estética nocturna en violetas y azules. Al fondo, a sangre, un video de un deportivo blanco que avanza por un bosque luminoso de setas y flores, con un degradado oscuro en el lado izquierdo que da contraste al texto. La intro automática (0 → 2.05 s) hace entrar todo el contenido: las letras del título «The perfect / Balance / of industrial / Speed» aparecen una a una y entran el globo de líneas, el texto «Experience elegance, power, and thrilling drives…», el botón «View More» con su flecha, la barra superior (logo S5motors, menú en píldora Home · Models · Performance · Technology · Gallery · Contact, «Login» y botón de menú), las cuatro tarjetas de vidrio inferiores (32 km Range, foto del coche, 150 km Top Speed, 75 kWh Battery Capacity) y la tarjeta «Experience Luxury on Wheels». Después, el scroll controla el video (2.05 → 7.6 s): con la interfaz fija, el coche se acerca de frente por el bosque. Entre 4.35 y 6.6 s el contenido pasa a la segunda sección como un desplazamiento vertical: la sección 1 (título, texto, botón y tarjetas) sube y sale por arriba mientras desde abajo entran los cinco bloques numerados —(001) Performance, (002) Innovation, (003) Comfort, (004) Power y (005) Elegance—, cada uno con su texto; a la vez la cámara del video sube hasta la vista superior del coche, el degradado izquierdo da paso a uno radial que oscurece los bordes y el menú se queda fijo. Los botones aclaran su fondo y los enlaces del menú cambian a lavanda al pasar el ratón.

## 2. Entregable y reglas técnicas
- Un único archivo `index.html` con el CSS dentro de `<style>` y el JavaScript dentro de `<script>`. JavaScript puro (vanilla), sin frameworks ni proceso de build.
- Única dependencia externa: la librería Lenis, versión exacta 1.3.26 (paquete npm `lenis`, archivo `dist/lenis.min.js`, que expone la clase global `Lenis`), para el scroll suave. Cárgala con una etiqueta `<script>` desde un CDN público de npm antes de tu script (si no carga, todo debe funcionar con el scroll nativo).
- Imágenes, video y tipografías se cargan SIEMPRE desde las URLs absolutas de la sección 13 «Recursos». No los descargues, no los incrustes en base64 y no uses otros recursos.
- Todo texto visible es texto HTML real, seleccionable y editable (nunca texto convertido en imagen ni en trazados SVG). Las formas vectoriales del Anexo son solo iconos y decoración.
- Listas con `<ul>`/`<li>` (dentro de `<nav aria-label="…">` cuando se indique) y enlaces con `<a href="#">` clicables, con `aria-label` donde se indica.
- Cabecera: `<html lang="en">`, `<meta charset="utf-8">`, `<meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover">`, `<title>The Perfect Balance of Industrial Speed</title>`, `<meta name="description" content="Experience elegance, power, and thrilling drives with a luxury car that redefines performance.">`, `<meta name="theme-color" content="#2a0f4f">`.
- Respeta `prefers-reduced-motion: reduce`: el loader funciona igual, pero no hay intro, ni scroll suave, ni snap; t queda fijo en el estado final (t = 7.6 s) aunque se haga scroll.
- No añadas elementos, textos, efectos ni secciones que no estén en esta especificación.

## 3. Estructura base (HTML + CSS común)
```html
<body>
<div class="loader" id="loader" aria-hidden="true">
  <div class="loader-in"><span>THE PERFECT BALANCE</span><div class="loader-bar"><i id="loaderBar"></i></div><span id="loaderPct">000</span></div>
</div>
<main class="hero" id="hero" aria-label="The Perfect Balance of Industrial Speed">
  <div class="stage" id="stage">
    <!-- una capa (div.blk) por cada «Capa» de la sección 9, en el mismo orden: las posteriores quedan encima -->
  </div>
</main>
<script>/* motor de la sección 6 */</script>
</body>
```
```css
:root{--bg:#2a0f4f;--ink:#ffffff;color-scheme:dark}
html,body{background:var(--bg);margin:0}
body{overflow-x:clip;-webkit-font-smoothing:antialiased;-moz-osx-font-smoothing:grayscale;text-rendering:geometricPrecision}
html.is-locked,html.is-locked body{overflow:hidden}
.hero{position:relative;height:calc(100vh + var(--track,400vh));height:calc(100svh + var(--track,400vh))}
.stage{position:sticky;top:0;height:100vh;height:100svh;overflow:hidden;background:var(--bg)}
.blk{position:absolute;left:0;top:0;width:0;height:0}          /* una capa */
.blk *{box-sizing:border-box}
.blk li{list-style:none;margin:0;padding:0}
.blk ul,.blk nav{position:absolute;left:0;top:0;width:0;height:0;margin:0;padding:0;list-style:none} /* listas: contenedores sin caja */
.blk a{color:inherit;text-decoration:none;cursor:pointer;-webkit-tap-highlight-color:transparent}
.blk a.lk{position:absolute;left:0;top:0;right:0;bottom:0;display:block;border-radius:inherit} /* enlace que cubre toda la caja */
.blk a:focus-visible{outline:2px solid currentColor;outline-offset:3px}
.blk *{pointer-events:none}
.blk .t,.blk .t *,.blk a,.blk a *{pointer-events:auto}   /* solo textos y enlaces reciben el puntero */
.loader{position:fixed;inset:0;z-index:50;display:grid;place-items:center;background:var(--bg);transition:opacity .9s cubic-bezier(.22,1,.36,1),visibility .9s}
.loader.is-done{opacity:0;visibility:hidden;pointer-events:none}
.loader-in{display:grid;justify-items:center;gap:14px;color:var(--ink);font:500 11px/1 ui-monospace,SFMono-Regular,Menlo,monospace;letter-spacing:.24em}
.loader-bar{width:160px;height:1px;background:color-mix(in srgb,var(--ink) 20%,transparent);overflow:hidden}
.loader-bar i{display:block;height:100%;width:100%;background:var(--ink);transform:scaleX(0);transform-origin:left}
```
- Todos los elementos del diseño son `position:absolute`. Los textos llevan `margin:0`. Las imágenes y formas vectoriales usan `display:block`.

## 4. Sistema de coordenadas, capas y escalado
- Diseño de referencia (frame): **1440×810 px**. Todas las medidas en px de este documento son unidades de ese diseño.
- El escenario `.stage` ocupa la ventana completa (W × H = ancho × alto actual del escenario).
- Cada **capa** (`div.blk`) es un contenedor absoluto de tamaño 0 escalado por un factor z (con `zoom: z`, o con `transform: translate(…) scale(z)` y `transform-origin: 0 0`; ambos son válidos si el resultado en pantalla es el mismo). Dentro de una capa, los elementos de primer nivel usan **coordenadas del frame**, y el punto (X, Y) del diseño se dibuja en pantalla en:
  - **CAPA A SANGRE (cover)**: z = max(W/1440, H/810); pantalla = ((W − 1440·z)/2 + X·z, (H − 810·z)/2 + Y·z). Siempre cubre la pantalla (se recorta lo que sobra).
  - **CAPA CONTENIDA (contain, ancla ax, ay)**: z = s = min(W/1440, H/810); mx = (W − 1440·s)/2; my = (H − 810·s)/2; pantalla = (mx·(1+ax) + X·s, my·(1+ay) + Y·s). Las anclas valen −1, 0 o 1: −1 = pegada al borde izquierdo/superior, 0 = centrada, 1 = pegada al borde derecho/inferior.
- La «caja de diseño» de cada capa (x, y, ancho×alto) es su rectángulo de referencia en el frame (su contenido puede salirse de él); su esquina superior izquierda se usa para colocar la capa en la vista móvil.
- Los elementos anidados usan coordenadas locales de su contenedor (left/top relativos al padre).
- Recalcula todo en cada `resize` y también cuando cambie el tamaño real de `.stage` aunque no haya `resize` (un `ResizeObserver` sobre `.stage`; p. ej. al aparecer o desaparecer una barra de desplazamiento), de modo que las capas ancladas a los bordes queden siempre pegadas al marco. En cada recálculo asigna además a `.stage` la variable CSS `--s` con la escala vigente (s en escritorio; sm en la vista móvil de la sección 11): `stage.style.setProperty('--s', escala)`; la usan los estilos que deben acompañar al diseño (sección 10).

## 5. Tipografías
Declara estas fuentes con `@font-face{font-family:'…';src:url(URL) format('woff2');font-weight:…;font-style:normal}` y úsalas con la pila `'Fuente', system-ui, sans-serif`:
- **Gantari** (fuente variable, rango de pesos 100–900 (font-weight:100 900)): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/neon/assets/fonts/gantari-VF-normal.woff2
- **Gantari** (fuente variable, rango de pesos 100–900 (font-weight:100 900)): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/neon/assets/fonts/gantari-VF-italic.woff2
- **Lexend Exa** (fuente variable, rango de pesos 100–900 (font-weight:100 900)): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/neon/assets/fonts/lexend-exa-VF-normal.woff2
- **Lexend Mega** (fuente variable, rango de pesos 100–900 (font-weight:100 900)): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/neon/assets/fonts/lexend-mega-VF-normal.woff2

## 6. Motor de animación
- Hay una única línea de tiempo **t** (segundos). Cada elemento tiene un estado estático (sección 9) y, si se indica «↳ animación», unas propiedades que cambian con t. En cada frame (`requestAnimationFrame`) se evalúan todas las propiedades para el t actual y se aplican a los estilos.
- Notación: `propiedad: t0→t1s v0→v1 (curva)` = entre t0 y t1 la propiedad pasa de v0 a v1 con esa curva de easing. Antes de su primer tramo la propiedad vale el v0 del primer tramo; entre tramos conserva el último valor alcanzado; después del último tramo conserva el valor final. `(linear)` = lineal. `(step-end)` = mantiene v0 y salta a v1 al final del tramo.
- `salto de v0 a v1 en t`: vale v0 antes de t y v1 desde t (incluido). Los números se interpolan tal cual: una rotación de −3 a +3 rad gira 6 rad (no se busca el arco más corto).
- Valor estático = el que figura en el estado estático de la línea (por ejemplo `opacity:0.8`); si no figura: opacidad 1, escalas 1, desplazamientos y rotaciones 0.
- Pista compuesta `base: … | sumar: … | multiplicar: …`: valor = (base, o el valor estático si no hay base) + la suma de todas las pistas «sumar», y ese resultado × el producto de todas las pistas «multiplicar». Cada pista se evalúa con las reglas anteriores (antes de su primer tramo vale su v0).
- Significado de las propiedades:
  - **x, y**: posición de la esquina superior izquierda del elemento en coordenadas de su contenedor (si en su estado estático aparece `pos(x,y)` se aplica como left/top; si aparece `transform translate(x,y) rotate(a)`, como transform con `transform-origin:0 0` y left/top = 0).
  - **ancho, alto**: width / height en px.
  - **opacidad**.
  - **rotación(rad)**: `transform: translate(x,y) rotate(a rad)` con `transform-origin:0 0` (gira alrededor de la esquina superior izquierda). Si el transform estático incluye `scale(1,-1)` (volteo), se mantiene después de `rotate`.
  - **escala-contenido**: `scale(k)` añadido al final del transform (escala el elemento y su contenido desde su esquina superior izquierda; el tamaño indicado es antes de escalar).
  - **desplazX, desplazY**: desplazamiento en px que se suma a x / y (a left/top si el elemento usa `pos`, o dentro del translate; en un grupo: `translate(desplazX, desplazY)`).
  - **rotación(°), escalaX, escalaY**: giro en grados y escala alrededor del centro de la caja, o del punto indicado entre corchetes (pivote o `transform-origin` del grupo). En un elemento: `transform: translate(x,y) rotate(a rad) [scale(k)] translate(cx,cy) rotate(r deg) scale(sx,sy) translate(−cx,−cy)` (si su estado estático es una `matrix(...)`, esta va en lugar de `rotate(a rad)`), con (cx, cy) = centro de la caja o pivote indicado.
  - **radio**: border-radius en px. **color de fondo / de texto rgba**: interpolar cada canal [r, g, b, a] (r, g, b de 0 a 255). **desenfoque**: `filter: blur(px)`.
- Si el estado estático de un elemento es `transform matrix(...)`, esa matriz es su transformación (con `transform-origin:0 0`); si además anima x/y: `transform: translate(x,y) matrix(...)`.
- **Grupo**: contenedor absoluto en (0,0) de tamaño 0, sin caja propia; sus hijos usan las coordenadas del contenedor del grupo. Su opacidad y transformaciones afectan a todos sus hijos.
- Si la opacidad calculada de un elemento (la suya propia, incluidos los multiplicadores y fundidos de las secciones 10 y 11, sin contar la de sus ancestros) es ≤ 0.001, ponle `visibility:hidden` (y quítalo cuando vuelva a ser visible), para que nunca reciba clics ni foco mientras está oculto.
- Curvas de easing usadas:
  - `E0` = cubic-bezier(0, 0, 0.58, 1)
  - `E1` = resorte (spring) masa=1, rigidez=80, amortiguación=20, normalizado a 1.25 s (ver fórmula)
- Las curvas `cubic-bezier(x1, y1, x2, y2)` funcionan como en CSS: dado el progreso temporal p, resolver el parámetro u tal que X(u) = p y devolver Y(u).

Fórmula del resorte (spring) con masa m, rigidez k, amortiguación c y duración normalizada D:
- ω0 = √(k/m), ζ = c / (2·√(k·m)).
- Si ζ < 1: ωd = ω0·√(1−ζ²); x(τ) = 1 − e^(−ζ·ω0·τ)·(cos(ωd·τ) + (ζ·ω0/ωd)·sin(ωd·τ)).
- Si ζ = 1: x(τ) = 1 − e^(−ω0·τ)·(1 + ω0·τ).
- Si ζ > 1: q = √(ζ²−1), r1 = −ω0·(ζ−q), r2 = −ω0·(ζ+q); x(τ) = 1 + (r2·e^(r1·τ) − r1·e^(r2·τ)) / (r1 − r2).
- easing(p) = x(p·D) / x(D) para 0 < p < 1; 0 si p ≤ 0; 1 si p ≥ 1.

## 7. Reproducción (intro y scroll)
- Duración total de la línea de tiempo: 7.6 s. Intro automática: de t = 0 a t = 2.05 s. El resto (2.05 → 7.6 s) se controla con el scroll.
- Altura de `.hero` = 100vh + track. track = round((7.6 − 2.05) × 40) = **222vh** en escritorio y round((7.6 − 2.05) × 34) = **189vh** en móvil (usa exactamente estos valores; se definen con la variable CSS `--track`). `.stage` es sticky, así que el escenario queda fijo mientras se recorre el track.
- Mientras carga: `html.is-locked` (overflow hidden; se añade antes de la primera medición del escenario, para que una barra de desplazamiento clásica no reduzca el ancho medido), `history.scrollRestoration = 'manual'`, `scrollTo(0,0)`.
- Cuando el loader recibe la clase `is-done` reproduce la intro: t avanza en tiempo real (lineal) de 0 a 2.05 s en 2.05 s. Durante la intro el scroll está bloqueado (Lenis detenido; si Lenis no está disponible, mantén `is-locked` hasta que termine la intro) y los eventos de scroll no cambian t. Al terminar, t = 2.05 y se activa el scroll.
- Scroll suave con Lenis: `new Lenis({lerp:0.085, wheelMultiplier:0.9, smoothWheel:true})`, `lenis.stop()` hasta acabar la intro y luego `lenis.start()`; llamar `lenis.raf(now)` en el bucle de requestAnimationFrame. Sin Lenis, usar el scroll nativo.
- Correspondencia scroll → tiempo: p = clamp((scrollY − hero.offsetTop) / (hero.offsetHeight − stage.clientHeight), 0, 1); t = 2.05 + p × (7.6 − 2.05).
- Pausas (holds), en segundos: [2.05 – 4.35], [6.6 – 7.6]. Imán (snap, solo con Lenis; sin Lenis no hay snap): la dirección es el signo del último cambio de scroll; 170 ms después del último evento de scroll (cada evento reinicia el temporizador), si t no está dentro de una pausa (con margen ±0.02 s), desplazar con `lenis.scrollTo` hasta el inicio de la siguiente pausa si se estaba bajando, o hasta el final de la pausa anterior si se estaba subiendo. Duración = clamp(|Δt| × 0.45, 0.6, 1.8) s; easing easeInOutCubic (x < 0.5 ? 4x³ : 1 − (−2x+2)³/2). No hacer snap con `prefers-reduced-motion` ni durante otro snap (el estado «haciendo snap» se libera en `onComplete` o, como seguridad, a los 2.2 s).
- En cada resize: recalcular capas, track y t a partir del scroll actual.
- Video (modo «scrub», controlado por la línea de tiempo): `<video muted playsinline preload="auto">` con su póster; nunca se reproduce solo. Para que el seek sea fiable, descargar el MP4 con `fetch` como Blob (reportando el progreso al loader) y asignar `video.src = URL.createObjectURL(blob)` (si falla, usar la URL directa). En cada frame, si `readyState ≥ 2` y no está en `seeking`: objetivo = min(max(0, t), duración − 0.04); si |currentTime − objetivo| > 0.012, `currentTime = objetivo`.

## 8. Loader y precarga
- El loader cubre la pantalla con el color `--bg`, muestra «THE PERFECT BALANCE», una barra de 160×1 px y el porcentaje con 3 dígitos (`000` → `100`), en el color `--ink`.
- Progreso ponderado: tipografías (`document.fonts.ready`) peso 1; cada elemento `<img>` del escenario peso 0.4, aunque repita archivo (cuenta cuando ha cargado —o ha fallado— y `img.decode()` ha terminado; el póster del video no cuenta); el video peso 6 (progreso de su descarga con fetch). Porcentaje = Math.round(progreso × 100) con 3 dígitos. La barra usa `transform: scaleX(progreso)` y nunca retrocede.
- Se espera a que todo termine (máximo 14 s; si se agota, se continúa sin cancelar nada); después se asigna el `src` del video (la URL Blob si ya está descargada, si no la URL directa) y se espera su `loadeddata` o `error` (máximo 3 s). Entonces se quita `is-locked` de `<html>` (salvo en el caso sin Lenis descrito en la sección 7), se añade `is-done` al loader (se desvanece en 0.9 s) y empieza la intro.

## 9. Capas y elementos
Notación de cada línea: `[eN]` = identificador sugerido; `caja` = div; `texto` = div con texto (el contenido entre comillas, respetando mayúsculas, saltos de línea y espacios); `grupo` = contenedor sin caja; `vector` = caja que contiene un SVG en línea; `<a href="#">`, `<ul>`, `<li>`, `<nav>` = etiqueta semántica que debe usarse; `«nombre»` = nombre de la capa en el diseño (solo como referencia, también útil como `data-name`); `pos(x,y)` = left/top; `tamaño A×B` = width×height; después de `|` van los estilos CSS literales. Todo elemento `texto` lleva `class="t"` (lo usa la regla de pointer-events de la sección 3); las cajas, grupos y vectores pueden usar `class="b"`, `"g"` y `"v"`. Las capas decorativas (borde, vidrio, sombra interior, desenfoque de fondo, capa de relleno) son elementos absolutos (por ejemplo `<i>`) que cubren su contenedor (`inset` indicado) con `pointer-events:none` en línea y `border-radius:inherit` salvo que se indique otro. Las imágenes van dentro de un contenedor absoluto `inset:0; overflow:hidden; border-radius:inherit`. El orden de las líneas es el orden de apilamiento (lo posterior queda encima).

#### Capa bk0 «Fondo» — CAPA A SANGRE (cover), caja de diseño x=0 y=0 1440×810
- [e1] caja «home» pos(0,0) tamaño 1440×810 | background-color:#909b9f

#### Capa bk1 «capcut-edit 1» — CAPA A SANGRE (cover), caja de diseño x=0 y=0 1440×827.23
- [e2] caja «capcut-edit 1» pos(0,-8.62) tamaño 1440×827.23
  - video: `<video muted playsinline preload="auto" poster="https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/neon/assets/video/b65f61a5ac.jpg" data-src="https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/neon/assets/video/b65f61a5ac.mp4">` con position:absolute; inset:0; width:100%; height:100%; object-fit:cover; display:block (el `src` se asigna desde JavaScript al terminar la precarga, ver secciones 7 y 8)

#### Capa bk2 «Rectangle 22» — CAPA CONTENIDA (contain, ancla x=-1, y=0), caja de diseño x=0 y=0 588.9×810
- [e3] caja «Rectangle 22» pos(0,0) tamaño 588.9×810 | opacity:0.6; background:linear-gradient(90deg, #0f0424 2.01%, rgba(65,13,126,0) 92.71%)

#### Capa bk3 «Rectangle 22 s2» — CAPA A SANGRE (cover), caja de diseño x=0 y=0 1440×810
- [e4] caja «Rectangle 22 s2» pos(0,0) tamaño 1440×810 | opacity:0.9; background:radial-gradient(813.99px 813.98px at 681.99px 505.42px, rgba(65,13,126,0.08) 20%, #0f0424 99.45%)

#### Capa bk5 «Experience elegance, power, an... - Animation ▶» — CAPA CONTENIDA (contain, ancla x=-1, y=0), caja de diseño x=53 y=393 401×50
- [e5] caja «Experience elegance, power, an... - Animation ▶» pos(53,393) tamaño 401×50 | overflow:hidden
  - [e6] caja «Experience elegance, power, and thrilling drives» pos(0,0) tamaño 401×50
    - [e7] texto pos(0,-9) tamaño 401×50 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Gantari'; font-weight:400; font-size:18px; line-height:1.4 | TEXTO por tramos: "Experience elegance, power, and thrilling drives
" {font-family:'Gantari'; font-weight:400; font-size:18px; line-height:1.4; color:#ffffff} + "with a luxury car that redefines performance." {font-family:'Gantari'; font-weight:400; font-size:18px; line-height:1.4; color:transparent}
        ↳ animación: y: 0.4→0.7s -9→0 (E0); opacidad: 0.4→0.7s 0→1 (E0)
  - [e8] caja «with a luxury car that redefines performance.» pos(0,0) tamaño 401×50
    - [e9] texto pos(0,-9) tamaño 401×50 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Gantari'; font-weight:400; font-size:18px; line-height:1.4 | TEXTO por tramos: "Experience elegance, power, and thrilling drives
" {font-family:'Gantari'; font-weight:400; font-size:18px; line-height:1.4; color:transparent} + "with a luxury car that redefines performance." {font-family:'Gantari'; font-weight:400; font-size:18px; line-height:1.4; color:#ffffff}
        ↳ animación: y: 0.475→0.775s -9→0 (E0); opacidad: 0.475→0.775s 0→1 (E0)

#### Capa bk6 «Component 3» — CAPA CONTENIDA (contain, ancla x=-1, y=0), caja de diseño x=53 y=471 209.13×69.77
- [e10] caja «Component 3» pos(53,471) tamaño 209.13×69.77 | border-radius:999px
  - [e11] caja «Frame 1597882288» pos(0,0) tamaño 209.13×69.77
    - [e12] caja <a href="#"> «Frame 1171275727» pos(41,0) tamaño 66×70 | opacity:0; border-radius:999px; background-color:#ffffff
        ↳ animación: x: 0.4→1.65s 41→0 (E1); ancho: 0.4→1.65s 66→147.67 (E1); alto: 0.4→1.65s 70→69.77 (E1); opacidad: 0.4→1.65s 0→1 (E1)
      - [e13] texto transform translate(4,31) rotate(0rad) tamaño 58×8 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Gantari'; font-weight:500; font-size:12.34px; line-height:normal; letter-spacing:-0.03em; color:#121212 | TEXTO: "View More"
          ↳ animación: x: 0.4→1.65s 4→26.83 (E1); y: 0.4→1.65s 31→28.38 (E1); alto: 0.4→1.65s 8→8.02 (E1); opacidad: 0.4→1.65s 0→1 (E1); escala-contenido: 0.4→1.65s 1→1.62 (E1)
    - [e14] caja <a href="#"> aria-label="View more" «Frame 1171275728» pos(107,4) tamaño 61.47×61.47 | opacity:0; border-radius:999px; background-color:rgba(0,0,0,0.01)
        ↳ animación: x: 0.4→1.65s 107→147.67 (E1); y: 0.4→1.65s 4→4.15 (E1); opacidad: 0.4→1.65s 0→1 (E1)
      - efecto vidrio (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(2px) saturate(1.25)
      - sombra interior: inset:0; border-radius:inherit; box-shadow:inset 1.2px 0px 0 0 rgba(255,255,255,0.22),inset -1.2px 0px 0 0 rgba(255,255,255,0.07),inset 0 0 19.2px 0 rgba(255,255,255,0.07)
      - [e15] caja «ArrowUpRight» transform translate(20,41.46) rotate(-1.57rad) tamaño 21.47×21.47
          ↳ animación: y: 0.4→1.65s 41.47→20 (E1); rotación(rad): 0.4→1.65s -1.57→0 (E1)
        - [e16] vector «Vector» pos(4.7,4.7) tamaño 12.07×12.07
          - dibujo vectorial SVG-1 (SVG inline, ver Anexo «Formas vectoriales»), ocupa el 100% de su caja

#### Capa bk7 «Group 9» — CAPA CONTENIDA (contain, ancla x=-1, y=-1), caja de diseño x=53 y=152 494×213
- [e17] grupo «Group 9»
  - [e18] grupo «Group 10»
    - [e19] caja «The perfect - Animation ▶» pos(53,152) tamaño 386×42 | overflow:hidden
      - [e20] caja «T» pos(0,0) tamaño 386×42
        - [e21] texto pos(0,-30) tamaño 386×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXTO por tramos: "T" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "he perfect" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animación: y: 0→0.3s -30→0 (E0); opacidad: 0→0.3s 0→1 (E0)
      - [e22] caja «h» pos(0,0) tamaño 386×42
        - [e23] texto pos(0,-30) tamaño 386×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXTO por tramos: "T" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "h" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "e perfect" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animación: y: 0.075→0.375s -30→0 (E0); opacidad: 0.075→0.375s 0→1 (E0)
      - [e24] caja «e» pos(0,0) tamaño 386×42
        - [e25] texto pos(0,-30) tamaño 386×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXTO por tramos: "Th" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "e" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + " perfect" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animación: y: 0.15→0.45s -30→0 (E0); opacidad: 0.15→0.45s 0→1 (E0)
      - [e26] caja « » pos(0,0) tamaño 386×42
        - [e27] texto pos(0,-30) tamaño 386×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXTO por tramos: "The" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + " " {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "perfect" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animación: y: 0.225→0.525s -30→0 (E0)
      - [e28] caja «p» pos(0,0) tamaño 386×42
        - [e29] texto pos(0,-30) tamaño 386×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXTO por tramos: "The " {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "p" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "erfect" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animación: y: 0.3→0.6s -30→0 (E0); opacidad: 0.3→0.6s 0→1 (E0)
      - [e30] caja «e» pos(0,0) tamaño 386×42
        - [e31] texto pos(0,-30) tamaño 386×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXTO por tramos: "The p" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "e" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "rfect" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animación: y: 0.375→0.675s -30→0 (E0); opacidad: 0.375→0.675s 0→1 (E0)
      - [e32] caja «r» pos(0,0) tamaño 386×42
        - [e33] texto pos(0,-30) tamaño 386×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXTO por tramos: "The pe" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "r" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "fect" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animación: y: 0.45→0.75s -30→0 (E0); opacidad: 0.45→0.75s 0→1 (E0)
      - [e34] caja «f» pos(0,0) tamaño 386×42
        - [e35] texto pos(0,-30) tamaño 386×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXTO por tramos: "The per" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "f" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "ect" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animación: y: 0.525→0.825s -30→0 (E0); opacidad: 0.525→0.825s 0→1 (E0)
      - [e36] caja «e» pos(0,0) tamaño 386×42
        - [e37] texto pos(0,-30) tamaño 386×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXTO por tramos: "The perf" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "e" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "ct" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animación: y: 0.6→0.9s -30→0 (E0); opacidad: 0.6→0.9s 0→1 (E0)
      - [e38] caja «c» pos(0,0) tamaño 386×42
        - [e39] texto pos(0,-30) tamaño 386×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXTO por tramos: "The perfe" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "c" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "t" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animación: y: 0.675→0.975s -30→0 (E0); opacidad: 0.675→0.975s 0→1 (E0)
      - [e40] caja «t» pos(0,0) tamaño 386×42
        - [e41] texto pos(0,-30) tamaño 386×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXTO por tramos: "The perfec" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "t" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff}
            ↳ animación: y: 0.75→1.05s -30→0 (E0); opacidad: 0.75→1.05s 0→1 (E0)
    - [e42] caja «of industrial - Animation ▶» pos(104,266) tamaño 443×42 | overflow:hidden
      - [e43] caja «o» pos(0,0) tamaño 443×42
        - [e44] texto pos(0,-30) tamaño 443×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXTO por tramos: "o" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "f industrial" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animación: y: 0.2→0.5s -30→0 (E0); opacidad: 0.2→0.5s 0→1 (E0)
      - [e45] caja «f» pos(0,0) tamaño 443×42
        - [e46] texto pos(0,-30) tamaño 443×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXTO por tramos: "o" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "f" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + " industrial" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animación: y: 0.275→0.575s -30→0 (E0); opacidad: 0.275→0.575s 0→1 (E0)
      - [e47] caja « » pos(0,0) tamaño 443×42
        - [e48] texto pos(0,-30) tamaño 443×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXTO por tramos: "of" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + " " {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "industrial" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animación: y: 0.35→0.65s -30→0 (E0)
      - [e49] caja «i» pos(0,0) tamaño 443×42
        - [e50] texto pos(0,-30) tamaño 443×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXTO por tramos: "of " {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "i" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "ndustrial" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animación: y: 0.425→0.725s -30→0 (E0); opacidad: 0.425→0.725s 0→1 (E0)
      - [e51] caja «n» pos(0,0) tamaño 443×42
        - [e52] texto pos(0,-30) tamaño 443×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXTO por tramos: "of i" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "n" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "dustrial" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animación: y: 0.5→0.8s -30→0 (E0); opacidad: 0.5→0.8s 0→1 (E0)
      - [e53] caja «d» pos(0,0) tamaño 443×42
        - [e54] texto pos(0,-30) tamaño 443×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXTO por tramos: "of in" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "d" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "ustrial" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animación: y: 0.575→0.875s -30→0 (E0); opacidad: 0.575→0.875s 0→1 (E0)
      - [e55] caja «u» pos(0,0) tamaño 443×42
        - [e56] texto pos(0,-30) tamaño 443×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXTO por tramos: "of ind" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "u" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "strial" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animación: y: 0.65→0.95s -30→0 (E0); opacidad: 0.65→0.95s 0→1 (E0)
      - [e57] caja «s» pos(0,0) tamaño 443×42
        - [e58] texto pos(0,-30) tamaño 443×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXTO por tramos: "of indu" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "s" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "trial" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animación: y: 0.725→1.025s -30→0 (E0); opacidad: 0.725→1.025s 0→1 (E0)
      - [e59] caja «t» pos(0,0) tamaño 443×42
        - [e60] texto pos(0,-30) tamaño 443×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXTO por tramos: "of indus" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "t" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "rial" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animación: y: 0.8→1.1s -30→0 (E0); opacidad: 0.8→1.1s 0→1 (E0)
      - [e61] caja «r» pos(0,0) tamaño 443×42
        - [e62] texto pos(0,-30) tamaño 443×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXTO por tramos: "of indust" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "r" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "ial" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animación: y: 0.875→1.175s -30→0 (E0); opacidad: 0.875→1.175s 0→1 (E0)
      - [e63] caja «i» pos(0,0) tamaño 443×42
        - [e64] texto pos(0,-30) tamaño 443×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXTO por tramos: "of industr" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "i" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "al" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animación: y: 0.95→1.25s -30→0 (E0); opacidad: 0.95→1.25s 0→1 (E0)
      - [e65] caja «a» pos(0,0) tamaño 443×42
        - [e66] texto pos(0,-30) tamaño 443×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXTO por tramos: "of industri" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "a" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "l" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animación: y: 1.025→1.325s -30→0 (E0); opacidad: 1.025→1.325s 0→1 (E0)
      - [e67] caja «l» pos(0,0) tamaño 443×42
        - [e68] texto pos(0,-30) tamaño 443×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXTO por tramos: "of industria" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "l" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff}
            ↳ animación: y: 1.1→1.4s -30→0 (E0); opacidad: 1.1→1.4s 0→1 (E0)
    - [e69] caja «speed - Animation ▶» pos(201.57,323) tamaño 200×42 | overflow:hidden
      - [e70] caja «s» pos(0,0) tamaño 200×42
        - [e71] texto pos(0,-30) tamaño 200×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXTO por tramos: "s" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "peed" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animación: y: 0.3→0.6s -30→0 (E0); opacidad: 0.3→0.6s 0→1 (E0)
      - [e72] caja «p» pos(0,0) tamaño 200×42
        - [e73] texto pos(0,-30) tamaño 200×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXTO por tramos: "s" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "p" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "eed" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animación: y: 0.375→0.675s -30→0 (E0); opacidad: 0.375→0.675s 0→1 (E0)
      - [e74] caja «e» pos(0,0) tamaño 200×42
        - [e75] texto pos(0,-30) tamaño 200×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXTO por tramos: "sp" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "e" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "ed" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animación: y: 0.45→0.75s -30→0 (E0); opacidad: 0.45→0.75s 0→1 (E0)
      - [e76] caja «e» pos(0,0) tamaño 200×42
        - [e77] texto pos(0,-30) tamaño 200×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXTO por tramos: "spe" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "e" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "d" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animación: y: 0.525→0.825s -30→0 (E0); opacidad: 0.525→0.825s 0→1 (E0)
      - [e78] caja «d» pos(0,0) tamaño 200×42
        - [e79] texto pos(0,-30) tamaño 200×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXTO por tramos: "spee" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "d" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff}
            ↳ animación: y: 0.6→0.9s -30→0 (E0); opacidad: 0.6→0.9s 0→1 (E0)
    - [e80] caja «Balance - Animation ▶» pos(196,209) tamaño 285×42 | overflow:hidden
      - [e81] caja «B» pos(0,0) tamaño 285×42
        - [e82] texto pos(0,-30) tamaño 285×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXTO por tramos: "B" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "alance" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animación: y: 0.1→0.4s -30→0 (E0); opacidad: 0.1→0.4s 0→1 (E0)
      - [e83] caja «a» pos(0,0) tamaño 285×42
        - [e84] texto pos(0,-30) tamaño 285×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXTO por tramos: "B" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "a" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "lance" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animación: y: 0.175→0.475s -30→0 (E0); opacidad: 0.175→0.475s 0→1 (E0)
      - [e85] caja «l» pos(0,0) tamaño 285×42
        - [e86] texto pos(0,-30) tamaño 285×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXTO por tramos: "Ba" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "l" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "ance" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animación: y: 0.25→0.55s -30→0 (E0); opacidad: 0.25→0.55s 0→1 (E0)
      - [e87] caja «a» pos(0,0) tamaño 285×42
        - [e88] texto pos(0,-30) tamaño 285×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXTO por tramos: "Bal" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "a" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "nce" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animación: y: 0.325→0.625s -30→0 (E0); opacidad: 0.325→0.625s 0→1 (E0)
      - [e89] caja «n» pos(0,0) tamaño 285×42
        - [e90] texto pos(0,-30) tamaño 285×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXTO por tramos: "Bala" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "n" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "ce" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animación: y: 0.4→0.7s -30→0 (E0); opacidad: 0.4→0.7s 0→1 (E0)
      - [e91] caja «c» pos(0,0) tamaño 285×42
        - [e92] texto pos(0,-30) tamaño 285×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXTO por tramos: "Balan" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "c" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff} + "e" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent}
            ↳ animación: y: 0.475→0.775s -30→0 (E0); opacidad: 0.475→0.775s 0→1 (E0)
      - [e93] caja «e» pos(0,0) tamaño 285×42
        - [e94] texto pos(0,-30) tamaño 285×42 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal | TEXTO por tramos: "Balanc" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:transparent} + "e" {font-family:'Lexend Mega'; font-weight:300; font-size:60px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'ss01' 1; color:#ffffff}
            ↳ animación: y: 0.55→0.85s -30→0 (E0); opacidad: 0.55→0.85s 0→1 (E0)
  - [e95] caja «Component 2» pos(53,323) tamaño 138×42
    - [e96] grupo «Group 2» | opacity:0
        ↳ animación: opacidad: 0.3→1.55s 0→1 (E1)
      - [e97] caja «Ellipse 1» pos(-24,0) tamaño 138×42 | border-radius:50%
          ↳ animación: x: 0.3→1.55s -24→0 (E1)
        - borde (stroke): inset:-0.5px; border-radius:50%; padding:1px; background:linear-gradient(90deg, #ffffff 0%, rgba(255,255,255,0.26) 30.77%); borde degradado de 1px (fondo degradado recortado con mask: linear-gradient content-box exclude)
      - [e98] caja «Ellipse 4» pos(-24,7) tamaño 138×28 | border-radius:50%
          ↳ animación: x: 0.3→1.55s -24→0 (E1)
        - borde (stroke): inset:-0.5px; border-radius:50%; padding:1px; background:linear-gradient(90deg, #ffffff 0%, rgba(255,255,255,0.26) 30.77%); borde degradado de 1px (fondo degradado recortado con mask: linear-gradient content-box exclude)
      - [e99] caja «Ellipse 5» pos(-24,15) tamaño 138×12 | border-radius:50%
          ↳ animación: x: 0.3→1.55s -24→0 (E1)
        - borde (stroke): inset:-0.5px; border-radius:50%; padding:1px; background:linear-gradient(90deg, #ffffff 0%, rgba(255,255,255,0.26) 30.77%); borde degradado de 1px (fondo degradado recortado con mask: linear-gradient content-box exclude)
      - [e100] caja «Ellipse 2» pos(16.02,0) tamaño 57.97×42 | border-radius:50%
          ↳ animación: x: 0.3→1.55s 16.02→40.02 (E1)
        - borde (stroke): inset:-0.5px; border-radius:50%; padding:1px; background:linear-gradient(90deg, #ffffff 0%, rgba(255,255,255,0.26) 30.77%); borde degradado de 1px (fondo degradado recortado con mask: linear-gradient content-box exclude)
      - [e101] caja «Ellipse 7» pos(5,0) tamaño 80×42 | border-radius:50%
          ↳ animación: x: 0.3→1.55s 5→29 (E1)
        - borde (stroke): inset:-0.5px; border-radius:50%; padding:1px; background:linear-gradient(90deg, #ffffff 0%, rgba(255,255,255,0.26) 30.77%); borde degradado de 1px (fondo degradado recortado con mask: linear-gradient content-box exclude)
      - [e102] caja «Ellipse 8» pos(-5.96,0) tamaño 101.91×42 | border-radius:50%
          ↳ animación: x: 0.3→1.55s -5.96→18.04 (E1)
        - borde (stroke): inset:-0.5px; border-radius:50%; padding:1px; background:linear-gradient(90deg, #ffffff 0%, rgba(255,255,255,0.26) 30.77%); borde degradado de 1px (fondo degradado recortado con mask: linear-gradient content-box exclude)
      - [e103] caja «Ellipse 9» pos(-16.58,0) tamaño 123.15×42 | border-radius:50%
          ↳ animación: x: 0.3→1.55s -16.58→7.42 (E1)
        - borde (stroke): inset:-0.5px; border-radius:50%; padding:1px; background:linear-gradient(90deg, #ffffff 0%, rgba(255,255,255,0.26) 30.77%); borde degradado de 1px (fondo degradado recortado con mask: linear-gradient content-box exclude)
      - [e104] caja «Ellipse 3» pos(27,0) tamaño 36×42 | border-radius:50%
          ↳ animación: x: 0.3→1.55s 27→51 (E1)
        - borde (stroke): inset:-0.5px; border-radius:50%; padding:1px; background:linear-gradient(90deg, #ffffff 0%, rgba(255,255,255,0.26) 30.77%); borde degradado de 1px (fondo degradado recortado con mask: linear-gradient content-box exclude)
      - [e105] caja «Ellipse 6» pos(38.59,0) tamaño 12.82×42 | border-radius:50%
          ↳ animación: x: 0.3→1.55s 38.59→62.59 (E1)
        - borde (stroke): inset:-0.5px; border-radius:50%; padding:1px; background:linear-gradient(90deg, #ffffff 0%, rgba(255,255,255,0.26) 30.77%); borde degradado de 1px (fondo degradado recortado con mask: linear-gradient content-box exclude)

#### Capa bk8 «image 4» — CAPA CONTENIDA (contain, ancla x=0, y=1), caja de diseño x=842.26 y=798.33 1×0.36
- [e106] caja «image 4» pos(842.26,798.33) tamaño 1×0.36
  - imagen https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/neon/assets/img/ddbf958085.webp: `<img alt="">` absoluta en (0,0) de 1024×574 px, max-width:none, transform-origin 0 0 y transform matrix(0.0043,0,0,0.0043,-1.755,-1.078); el contenedor la recorta (overflow hidden, mismo border-radius)

#### Capa bk9 «Component 4» — CAPA CONTENIDA (contain, ancla x=-1, y=1), caja de diseño x=53 y=637 684×149
- [e107] caja «Component 4» pos(53,637) tamaño 684×149
  - [e108] caja «Frame 1171275741» pos(0,180) tamaño 156×149 | border-radius:32px; background-color:rgba(255,255,255,0.00)
      ↳ animación: y: 0.5→1.75s 180→0 (E1)
    - efecto vidrio (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(16px) saturate(1.25)
    - sombra interior: inset:0; border-radius:inherit; box-shadow:inset 1.2px 0px 0 0 rgba(255,255,255,0.44),inset -1.2px 0px 0 0 rgba(255,255,255,0.14),inset 0 0 24px 0 rgba(255,255,255,0.14)
    - [e109] texto pos(25,160) tamaño 42×9 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Gantari'; font-weight:400; font-size:14px; line-height:1.4; font-feature-settings:'liga' 0,'salt' 1; color:#ffffff | TEXTO: "Range"
        ↳ animación: y: 0.5→1.75s 160→90 (E1)
    - [e110] caja «Frame 20» pos(23.39,78.5) tamaño 75×25
        ↳ animación: y: 0.5→1.75s 78.5→48.5 (E1)
      - [e111] texto pos(0,0) tamaño 43×25 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Exa'; font-weight:600; font-size:35px; line-height:1.2; letter-spacing:-0.09em; text-transform:uppercase; font-feature-settings:'liga' 0,'salt' 1; color:#ffffff | TEXTO: "32"
      - [e112] texto pos(42,0) tamaño 33×13 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Exa'; font-weight:300; font-size:19px; line-height:1.2; letter-spacing:-0.09em; text-transform:uppercase; font-feature-settings:'liga' 0,'salt' 1; color:#ffffff | TEXTO: "km"
  - [e113] caja «Frame 1171275742» pos(176,210) tamaño 156×149 | border-radius:32px; overflow:hidden; background-color:#d8bdff
      ↳ animación: y: 0.5→1.75s 210→0 (E1)
    - [e114] caja «image 8» transform translate(363.08,-5) rotate(3.14rad) scale(1,-1) tamaño 469.08×264
        ↳ animación: x: 0.5→1.75s 363.08→269 (E1); y: 0.5→1.75s -5→-15 (E1); ancho: 0.5→1.75s 469.08→281 (E1); alto: 0.5→1.75s 264→158 (E1)
      - capa de relleno (cubre toda la caja, apilada en este orden): mix-blend-mode:multiply
      - imagen https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/neon/assets/img/1ab5d7ea92.webp: `<img alt="">` absoluta, inset:0, width/height 100%, object-fit:cover (recortada por el contenedor)
      - imagen https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/neon/assets/img/50c30bd7df.webp: `<img alt="">` absoluta, inset:0, width/height 100%, object-fit:cover (recortada por el contenedor)
  - [e115] caja «Frame 1171275743» pos(352,260) tamaño 156×149 | border-radius:32px; background-color:rgba(255,255,255,0.00)
      ↳ animación: y: 0.5→1.75s 260→0 (E1)
    - efecto vidrio (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(16px) saturate(1.25)
    - sombra interior: inset:0; border-radius:inherit; box-shadow:inset 1.2px 0px 0 0 rgba(255,255,255,0.44),inset -1.2px 0px 0 0 rgba(255,255,255,0.14),inset 0 0 24px 0 rgba(255,255,255,0.14)
    - [e116] texto pos(25,160) tamaño 67×9 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Gantari'; font-weight:400; font-size:14px; line-height:1.4; font-feature-settings:'liga' 0,'salt' 1; color:#ffffff | TEXTO: "Top Speed"
        ↳ animación: y: 0.5→1.75s 160→90 (E1)
    - [e117] caja «Frame 21» pos(23.39,78.5) tamaño 97×25
        ↳ animación: y: 0.5→1.75s 78.5→48.5 (E1)
      - [e118] texto pos(0,0) tamaño 65×25 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Exa'; font-weight:600; font-size:35px; line-height:1.2; letter-spacing:-0.09em; text-transform:uppercase; font-feature-settings:'liga' 0,'salt' 1; color:#ffffff | TEXTO: "150"
      - [e119] texto pos(64,0) tamaño 33×13 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Exa'; font-weight:300; font-size:19px; line-height:1.2; letter-spacing:-0.09em; text-transform:uppercase; font-feature-settings:'liga' 0,'salt' 1; color:#ffffff | TEXTO: "km"
  - [e120] caja «Frame 1171275744» pos(528,300) tamaño 156×149 | border-radius:32px; background-color:rgba(255,255,255,0.00)
      ↳ animación: y: 0.5→1.75s 300→0 (E1)
    - efecto vidrio (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(16px) saturate(1.25)
    - sombra interior: inset:0; border-radius:inherit; box-shadow:inset 1.2px 0px 0 0 rgba(255,255,255,0.44),inset -1.2px 0px 0 0 rgba(255,255,255,0.14),inset 0 0 24px 0 rgba(255,255,255,0.14)
    - [e121] texto pos(25,160) tamaño 109×9 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Gantari'; font-weight:400; font-size:14px; line-height:1.4; font-feature-settings:'liga' 0,'salt' 1; color:#ffffff | TEXTO: "Battery Capacity"
        ↳ animación: y: 0.5→1.75s 160→90 (E1)
    - [e122] caja «Frame 20» pos(23.39,78.5) tamaño 91×25
        ↳ animación: y: 0.5→1.75s 78.5→48.5 (E1)
      - [e123] texto pos(0,0) tamaño 42×25 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Exa'; font-weight:600; font-size:35px; line-height:1.2; letter-spacing:-0.09em; text-transform:uppercase; font-feature-settings:'liga' 0,'salt' 1; color:#ffffff | TEXTO: "75"
      - [e124] texto pos(41,0) tamaño 50×13 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Exa'; font-weight:300; font-size:19px; line-height:1.2; letter-spacing:-0.09em; text-transform:uppercase; font-feature-settings:'liga' 0,'salt' 1; color:#ffffff | TEXTO: "kWh"

#### Capa bk10 «Component 5» — CAPA CONTENIDA (contain, ancla x=1, y=1), caja de diseño x=1179 y=385 237×297
- [e125] caja «Component 5» pos(1179,385) tamaño 237×297 | border-radius:32px; overflow:hidden
  - [e126] grupo «Group 33»
    - [e127] caja «Rectangle 27» pos(0,420) tamaño 237×193 | border-radius:32px; background-color:rgba(255,255,255,0.00)
        ↳ animación: y: 0.5→1.75s 420→0 (E1)
      - efecto vidrio (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(0px) saturate(1.25)
      - sombra interior: inset:0; border-radius:inherit; box-shadow:inset 0px -1.2px 0 0 rgba(255,255,255,0.44),inset 0px 1.2px 0 0 rgba(255,255,255,0.14),inset 0 0 24px 0 rgba(255,255,255,0.14)
    - [e128] caja «Rectangle 26» pos(0,367) tamaño 237×193 | border-radius:32px; background:linear-gradient(180deg, #d44eb5 0%, #6009e3 26.17%)
        ↳ animación: y: 0.5→1.75s 367→17 (E1)
      - desenfoque de fondo (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(4.95px)
    - [e129] caja «Frame 1597882325» pos(0,323) tamaño 237×254 | border-radius:32px; overflow:hidden; background:linear-gradient(136.32deg, #ffffff 62.8%, #d1abff 98.02%)
        ↳ animación: y: 0.5→1.75s 323→43 (E1)
      - desenfoque de fondo (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(10px)
      - [e130] caja «Rectangle 25» transform translate(-6,13) rotate(0rad) tamaño 249×153 | border-radius:22px; filter:brightness(1.14)
          ↳ animación: x: 0.5→1.75s -6→10 (E1); y: 0.5→1.75s 13→10 (E1); ancho: 0.5→1.75s 249→249.32 (E1); alto: 0.5→1.75s 153→152.81 (E1); radio: 0.5→1.75s 22→25.28 (E1); escala-contenido: 0.5→1.75s 1→0.87 (E1)
        - imagen https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/neon/assets/img/7b0aed9020-612186.webp: `<img alt="">` absoluta en (0,0) de 1672×941 px, max-width:none, transform-origin 0 0 y transform matrix(0.3112,0,0,0.3120,-146.558,-23.766); el contenedor la recorta (overflow hidden, mismo border-radius)
      - [e131] texto pos(29,205) tamaño 156×64 | white-space:pre-wrap; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:400; font-size:20px; line-height:normal; letter-spacing:-0.21em; text-transform:uppercase; font-feature-settings:'liga' 0,'salt' 1; color:#3e336b | TEXTO: "Experience Luxury on Wheels"
          ↳ animación: y: 0.5→1.75s 205→165 (E1)
      - [e132] caja <a href="#"> aria-label="Experience luxury on wheels" «Frame 1171275728» pos(169,236) tamaño 61.47×61.47 | border-radius:999px; background-color:rgba(255,255,255,0.38)
          ↳ animación: y: 0.5→1.75s 236→186 (E1)
        - efecto vidrio (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(2px) saturate(1.25)
        - sombra interior: inset:0; border-radius:inherit; box-shadow:inset 1.2px 0px 0 0 rgba(255,255,255,0.22),inset -1.2px 0px 0 0 rgba(255,255,255,0.07),inset 0 0 19.2px 0 rgba(255,255,255,0.07)
        - [e133] caja «ArrowUpRight» pos(20,20) tamaño 21.47×21.47
          - [e134] vector «Vector» pos(4.7,4.7) tamaño 12.07×12.07
            - dibujo vectorial SVG-2 (SVG inline, ver Anexo «Formas vectoriales»), ocupa el 100% de su caja
      - borde (stroke): inset:0px; border-radius:32px; padding:2px; background:linear-gradient(-39.03deg, rgba(255,255,255,0.25) 8.63%, rgba(255,255,255,0.11) 95%); borde degradado de 1px (fondo degradado recortado con mask: linear-gradient content-box exclude)

#### Capa bk11 «Group 42» — CAPA CONTENIDA (contain, ancla x=0, y=-1), caja de diseño x=49 y=147 1340×203
- [e135] grupo «Group 42»
  - [e136] grupo «Group 37»
    - [e137] texto pos(86,154) tamaño 304×28 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:40px; line-height:0.97; letter-spacing:-0.21em; text-transform:uppercase; color:#ffffff | TEXTO: "Performance"
    - [e138] texto pos(49,147) tamaño 34×20 | opacity:0.7; white-space:pre; text-align:left; font-family:'Gantari'; font-weight:500; font-size:14px; line-height:1.4; color:#ffffff | TEXTO: "(001)"
    - [e139] texto pos(91,210) tamaño 251×140 | white-space:pre-wrap; text-align:left; font-family:'Gantari'; font-weight:400; font-size:14px; line-height:1.4; color:#ffffff | TEXTO: "Unleash breathtaking acceleration and razor-sharp handling with our precision-engineered powertrains. Every component is tuned for maximum output, delivering an exhilarating drive that commands the road with authority."
  - [e140] grupo «Group 41»
    - [e141] texto pos(1133,154) tamaño 214×28 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:40px; line-height:0.97; letter-spacing:-0.21em; text-transform:uppercase; color:#ffffff | TEXTO: "Elegance"
    - [e142] texto pos(1096,147) tamaño 36×20 | opacity:0.7; white-space:pre; text-align:left; font-family:'Gantari'; font-weight:500; font-size:14px; line-height:1.4; color:#ffffff | TEXTO: "(005)"
    - [e143] texto pos(1138,210) tamaño 251×120 | white-space:pre-wrap; text-align:left; font-family:'Gantari'; font-weight:400; font-size:14px; line-height:1.4; color:#ffffff | TEXTO: "Sculpted lines and refined aesthetics define every curve of our luxury vehicles. Handcrafted details and premium materials create a presence that turns heads and embodies timeless sophistication."

#### Capa bk12 «Group 40» — CAPA CONTENIDA (contain, ancla x=0, y=1), caja de diseño x=123 y=460 1191×279
- [e144] grupo «Group 40»
  - [e145] grupo «Group 38»
    - [e146] texto pos(160,467) tamaño 249×28 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:40px; line-height:0.97; letter-spacing:-0.21em; text-transform:uppercase; color:#ffffff | TEXTO: "Innovation"
    - [e147] texto pos(123,460) tamaño 35×20 | opacity:0.7; white-space:pre; text-align:left; font-family:'Gantari'; font-weight:500; font-size:14px; line-height:1.4; color:#ffffff | TEXTO: "(002)"
    - [e148] texto pos(165,523) tamaño 251×120 | white-space:pre-wrap; text-align:left; font-family:'Gantari'; font-weight:400; font-size:14px; line-height:1.4; color:#ffffff | TEXTO: "Cutting-edge technology seamlessly integrates into every system onboard. From adaptive AI-assisted driving to next-generation infotainment, our vehicles set the standard for automotive advancement."
  - [e149] grupo «Group 40»
    - [e150] texto pos(609,563) tamaño 200×28 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:40px; line-height:0.97; letter-spacing:-0.21em; text-transform:uppercase; color:#ffffff | TEXTO: "Comfort"
    - [e151] texto pos(572,556) tamaño 35×20 | opacity:0.7; white-space:pre; text-align:left; font-family:'Gantari'; font-weight:500; font-size:14px; line-height:1.4; color:#ffffff | TEXTO: "(003)"
    - [e152] texto pos(614,619) tamaño 251×120 | white-space:pre-wrap; text-align:left; font-family:'Gantari'; font-weight:400; font-size:14px; line-height:1.4; color:#ffffff | TEXTO: "Step into a sanctuary of refinement with hand-stitched leather, climate-controlled seating, and near-silent cabins. Every journey becomes an indulgent escape from the demands of the outside world."
  - [e153] grupo «Group 39»
    - [e154] texto pos(1058,467) tamaño 151×28 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:300; font-size:40px; line-height:0.97; letter-spacing:-0.21em; text-transform:uppercase; color:#ffffff | TEXTO: "Power"
    - [e155] texto pos(1021,460) tamaño 37×20 | opacity:0.7; white-space:pre; text-align:left; font-family:'Gantari'; font-weight:500; font-size:14px; line-height:1.4; color:#ffffff | TEXTO: "(004)"
    - [e156] texto pos(1063,523) tamaño 251×120 | white-space:pre-wrap; text-align:left; font-family:'Gantari'; font-weight:400; font-size:14px; line-height:1.4; color:#ffffff | TEXTO: "Massive horsepower figures and torque curves that redefine what a road car can achieve. Our engines are engineered to perform relentlessly, delivering power on demand at every speed."

#### Capa bk13 «Frame 26» — CAPA CONTENIDA (contain, ancla x=1, y=1), caja de diseño x=1344 y=716 69.77×69.77
- [e157] caja <a href="#"> aria-label="Scroll down" «Frame 26» pos(1344,716) tamaño 69.77×69.77 | border-radius:32px; background-color:rgba(255,255,255,0.00)
  - efecto vidrio (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(0px) saturate(1.25)
  - sombra interior: inset:0; border-radius:inherit; box-shadow:inset 1.2px 0px 0 0 rgba(255,255,255,0.44),inset -1.2px 0px 0 0 rgba(255,255,255,0.14),inset 0 0 24px 0 rgba(255,255,255,0.14)
  - [e158] caja «ArrowDown» transform translate(50.88,18.88) rotate(3.14rad) scale(1,-1) tamaño 32×32
    - [e160] vector «Vector» transform matrix(-1,0,0,1,17,4) tamaño 2×24
      - dibujo vectorial SVG-3 (SVG inline, ver Anexo «Formas vectoriales»), ocupa el 100% de su caja
    - [e161] vector «Vector» transform matrix(-1,0,0,1,26,17) tamaño 20×11
      - dibujo vectorial SVG-4 (SVG inline, ver Anexo «Formas vectoriales»), ocupa el 100% de su caja

#### Capa bk14 «menu» — CAPA CONTENIDA (contain, ancla x=0, y=-1), caja de diseño x=24 y=16 1392×52
- [e162] caja «menu» pos(24,16) tamaño 1392×52
  - [e163] caja «Frame 1597882325» pos(648,2) tamaño 96×49 | opacity:0; border-radius:999px; overflow:hidden; background-color:rgba(255,255,255,0.01)
      ↳ animación: x: 0.001→1.251s 648→366.5 (E1); ancho: 0.001→1.251s 96→659 (E1); opacidad: 0.001→1.251s 0→1 (E1)
    - efecto vidrio (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(2px) saturate(1.25)
    - sombra interior: inset:0; border-radius:inherit; box-shadow:inset 1.2px 0px 0 0 rgba(255,255,255,0.22),inset -1.2px 0px 0 0 rgba(255,255,255,0.07),inset 0 0 19.2px 0 rgba(255,255,255,0.07)
    - [-] lista <nav> aria-label="Main navigation"
      - [-] lista <ul>
        - [e164] caja <li> «Frame 1597882289» pos(17,8) tamaño 63×33 | border-radius:99px; background:linear-gradient(53.12deg, rgba(255,255,255,0.33) -16.55%, rgba(255,255,255,0) 107.72%)
            ↳ animación: x: 0.001→1.251s 17→8 (E1)
          - [-] enlace <a href="#"> que cubre toda la caja de su contenedor (inset:0)
            - [e165] texto pos(12,12) tamaño 39×9 | white-space:pre; text-align:right; text-box:trim-both cap alphabetic; font-family:'Gantari'; font-weight:700; font-size:14px; line-height:1.4; color:#ffffff | TEXTO: "Home"
            - borde (stroke): inset:0px; border-radius:99px; padding:1px; background:linear-gradient(-132.35deg, rgba(255,255,255,0.6) 7.93%, rgba(255,255,255,0) 103.28%); borde degradado de 1px (fondo degradado recortado con mask: linear-gradient content-box exclude)
        - [e166] caja <li> «Frame 1597882290» pos(12,8) tamaño 72×33 | border-radius:99px
            ↳ animación: x: 0.001→1.251s 12→103 (E1)
          - [-] enlace <a href="#"> que cubre toda la caja de su contenedor (inset:0)
            - [e167] texto pos(12,12) tamaño 48×9 | white-space:pre; text-align:right; text-box:trim-both cap alphabetic; font-family:'Gantari'; font-weight:400; font-size:14px; line-height:1.4; color:#ffffff | TEXTO: "Models"
        - [e168] caja <li> «Frame 1597882292» pos(-5,8) tamaño 107×33 | border-radius:99px
            ↳ animación: x: 0.001→1.251s -5→207 (E1)
          - [-] enlace <a href="#"> que cubre toda la caja de su contenedor (inset:0)
            - [e169] texto pos(12,12) tamaño 83×9 | white-space:pre; text-align:right; text-box:trim-both cap alphabetic; font-family:'Gantari'; font-weight:400; font-size:14px; line-height:1.4; color:#ffffff | TEXTO: "Performance"
        - [e170] caja <li> «Frame 1597882291» pos(0,8) tamaño 97×33 | border-radius:99px
            ↳ animación: x: 0.001→1.251s 0→346 (E1)
          - [-] enlace <a href="#"> que cubre toda la caja de su contenedor (inset:0)
            - [e171] texto pos(12,12) tamaño 73×9 | white-space:pre; text-align:right; text-box:trim-both cap alphabetic; font-family:'Gantari'; font-weight:400; font-size:14px; line-height:1.4; color:#ffffff | TEXTO: "Technology"
        - [e172] caja <li> «Frame 1597882293» pos(14,8) tamaño 69×33 | border-radius:99px
            ↳ animación: x: 0.001→1.251s 14→475 (E1)
          - [-] enlace <a href="#"> que cubre toda la caja de su contenedor (inset:0)
            - [e173] texto pos(12,12) tamaño 45×9 | white-space:pre; text-align:right; text-box:trim-both cap alphabetic; font-family:'Gantari'; font-weight:400; font-size:14px; line-height:1.4; color:#ffffff | TEXTO: "Gallery"
        - [e174] caja <li> «Frame 1597882294» pos(11,8) tamaño 75×33 | border-radius:99px
            ↳ animación: x: 0.001→1.251s 11→576 (E1)
          - [-] enlace <a href="#"> que cubre toda la caja de su contenedor (inset:0)
            - [e175] texto pos(12,12) tamaño 51×9 | white-space:pre; text-align:right; text-box:trim-both cap alphabetic; font-family:'Gantari'; font-weight:400; font-size:14px; line-height:1.4; color:#ffffff | TEXTO: "Contact"
  - [e176] caja <a href="#"> aria-label="Open menu" «Frame 1597882296» pos(1250,0) tamaño 52×52 | opacity:0; border-radius:32px; background-color:rgba(255,255,255,0.00)
      ↳ animación: x: 0.001→1.251s 1250→1340 (E1); opacidad: 0.001→1.251s 0→1 (E1)
    - efecto vidrio (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(0px) saturate(1.25)
    - sombra interior: inset:0; border-radius:inherit; box-shadow:inset 1.2px 0px 0 0 rgba(255,255,255,0.44),inset -1.2px 0px 0 0 rgba(255,255,255,0.14),inset 0 0 24px 0 rgba(255,255,255,0.14)
    - [e177] grupo «Group 8»
      - [e178] caja «Line 9» transform translate(37.49,20.66) rotate(3.14rad) tamaño 22.98×2 | background-color:#ffffff
      - [e179] caja «Line 11» transform translate(31.86,27) rotate(3.14rad) tamaño 11.73×2 | background-color:#ffffff
      - [e180] caja «Line 10» transform translate(37.49,33.33) rotate(3.14rad) tamaño 22.98×2 | background-color:#ffffff
  - [e181] caja <a href="#"> «Frame 1597882324» pos(1044,9) tamaño 60×33 | opacity:0; border-radius:99px; background:linear-gradient(51.77deg, rgba(255,255,255,0.33) -17.73%, rgba(255,255,255,0) 107.5%)
      ↳ animación: x: 0.001→1.251s 1044→1254 (E1); y: 0.001→1.251s 9→10 (E1); opacidad: 0.001→1.251s 0→1 (E1)
    - desenfoque de fondo (backdrop-filter): inset:0; border-radius:inherit; backdrop-filter:blur(20px)
    - [e182] texto pos(12,12) tamaño 36×9 | white-space:pre; text-align:right; text-box:trim-both cap alphabetic; font-family:'Gantari'; font-weight:700; font-size:14px; line-height:1.4; color:#ffffff | TEXTO: "Login"
    - borde (stroke): inset:0px; border-radius:99px; padding:1px; background:linear-gradient(-133.75deg, rgba(255,255,255,0.6) 7.68%, rgba(255,255,255,0) 104.26%); borde degradado de 1px (fondo degradado recortado con mask: linear-gradient content-box exclude)
  - [e183] grupo <a href="#"> «Group 34» | opacity:0
      ↳ animación: opacidad: 0.001→1.251s 0→1 (E1)
    - [e184] caja «image 9» pos(90,17) tamaño 17.15×17.15
        ↳ animación: x: 0.001→1.251s 90→0 (E1); y: 0.001→1.251s 17→18 (E1); ancho: 0.001→1.251s 17.15→17.14 (E1)
      - imagen https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/neon/assets/img/799dcf4932.webp: `<img alt="">` absoluta en (0,0) de 2048×2048 px, max-width:none, transform-origin 0 0 y transform matrix(0.0331,0,0,0.0331,-25.328,-25.328); el contenedor la recorta (overflow hidden, mismo border-radius)
    - [e185] texto pos(114,21.29) tamaño 66×8 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Lexend Mega'; font-weight:400; font-size:12.00px; line-height:1.4; letter-spacing:-0.13em; color:#ffffff | TEXTO: "S5motors"
        ↳ animación: x: 0.001→1.251s 114→24 (E1); y: 0.001→1.251s 21.29→22.29 (E1)

## 10. Ajustes finales
- [e4] «Rectangle 22 s2»: en escritorio y en móvil, su opacidad se multiplica además por clamp((t − 4.9)/1.1, 0, 1).
- [e3] «Rectangle 22»: en escritorio y en móvil, su opacidad se multiplica además por clamp((6 − t)/1.1, 0, 1) cuando t > 4.9 (si el elemento no tiene animación propia, esa es directamente su opacidad, y con 0 lleva `visibility:hidden`).
- Desplazamiento vertical ligado al tiempo de [e17] «Group 9», [e5] «Experience elegance, power, an... - Animation ▶», [e10] «Component 3», [e107] «Component 4», [e125] «Component 5»: propiedad CSS `translate: 0px Ypx` (se suma a su posición animada), con Y en alturas del escenario: Y px = valor × stage.clientHeight / zoom acumulado del elemento (producto de los `zoom` del elemento y de sus contenedores). Fotogramas (t, valor): (4.9 s, 0) → (6.3 s, -1.12) [cubic-bezier(0.4, 0, 0.75, 1)]; entre dos fotogramas se interpola con la curva indicada en el segundo; antes del primero y después del último se mantiene el valor extremo. Se aplica en escritorio y en móvil; si varias entradas afectan al mismo elemento, sus valores se suman.
- Desplazamiento vertical ligado al tiempo de [e135] «Group 42», [e144] «Group 40»: propiedad CSS `translate: 0px Ypx` (se suma a su posición animada), con Y en alturas del escenario: Y px = valor × stage.clientHeight / zoom acumulado del elemento (producto de los `zoom` del elemento y de sus contenedores). Fotogramas (t, valor): (4.35 s, 1.05) → (4.75 s, 0.74) [cubic-bezier(0.2, 0, 0.4, 1)] → (5.25 s, 0.66) [lineal] → (6.6 s, 0) [cubic-bezier(0.35, 0, 0.2, 1)]; entre dos fotogramas se interpola con la curva indicada en el segundo; antes del primero y después del último se mantiene el valor extremo. Se aplica en escritorio y en móvil; si varias entradas afectan al mismo elemento, sus valores se suman.
### Estados hover (solo si `matchMedia("(hover: hover) and (pointer: fine)")` se cumple; en táctil no hay hover)
- Enlaces de menú y de texto (cambio sutil de color): [e164] «Home», [e166] «Models», [e168] «Performance», [e170] «Technology», [e172] «Gallery», [e174] «Contact». Al entrar el puntero o recibir el foco, para el enlace y cada uno de sus descendientes se lee su color calculado c (si su alfa es 0, p. ej. texto con degradado, se omite) y se guarda como variable `--hvc` = rgb(c + (#d8bdff − c) × 1) por canal (alfa 1); se añade la clase `hv-tr` y, en el siguiente frame, `is-hv`. Al salir (o perder el foco) se quita `is-hv` y 420 ms después `hv-tr`.
- Botones (cambio sutil de fondo): [e12] «View More» → capa en el propio enlace con fondo rgba(216,189,255,.45); [e14] «View more» → capa en el propio enlace con fondo rgba(255,255,255,.16); [e132] «Experience luxury on wheels» → capa en el propio enlace con fondo rgba(255,255,255,.16); [e157] «Scroll down» → capa en el propio enlace con fondo rgba(255,255,255,.16); [e176] «Open menu» → capa en el propio enlace con fondo rgba(255,255,255,.16); [e181] «Login» → capa en el propio enlace con fondo rgba(255,255,255,.16). En cada uno se inserta `<i class="hvo" aria-hidden="true">` dentro de la caja indicada, justo después de sus capas de relleno/efecto (`i.pl`, `i.bb`, `i.gl`, `i.is`) y antes de su contenido, con ese color de fondo; al entrar el puntero o recibir el foco el enlace añade `is-hv` a esa capa y lo quita al salir o perder el foco.
- Enlaces de icono o logo sin superficie propia (bajan a 0.72 de opacidad al pasar el ratón): [e183] «S5motors». Llevan la clase `hv-ic`.
- CSS literal de estos estados:
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

## 11. Vista móvil
- Se activa cuando el ancho del escenario < 768 px o la proporción ancho/alto < 0.82 (añade la clase `is-m` a `<html>`). Diseño móvil de referencia: 390×844 px.
- sm = min(W/390, H/844); ox = (W − 390·sm)/2; oy = (H − 844·sm)/2.
- Capa contenida listada con {x, y, z, ax, ay}: su escala es sm·z (z = 1 si no se indica) y la esquina superior izquierda de su «caja de diseño» se coloca en pantalla en (ox·(1+ax) + x·sm, oy·(1+ay) + y·sm) (ax, ay = 0 si no se indican); el resto de su contenido conserva su posición relativa a esa caja.
- Capa a sangre: z = max(W/1440, H/810)·(z indicado o 1); pantalla = ((W − 1440·z)·fx + X·z, (H − 810·z)·fy + Y·z), con fx, fy = 0.5 salvo que se indique otro valor.
- Las capas contenidas que no aparecen en la lista se ocultan en móvil.
- Ajustes por elemento: «ocultar» = `display:none`; «desplazar dx, dy» = mover el elemento esa distancia en px de su contenedor (si además hay «escala ×z», se escala desde su esquina superior izquierda); «multiplicar sus desplazamientos animados por [kx, ky]» = multiplicar el valor total de desplazX por kx y el de desplazY por ky (el «desplazar dx, dy» de móvil no se multiplica); «desvanecer entre t1 y t2» = multiplicar su opacidad por clamp((t2 − t)/(t2 − t1), 0, 1) a partir de t1; «colocar el punto (rx, ry) del elemento en (mx, my) del diseño móvil a escala e×sm» = dibujar el elemento a escala Se = e·sm con su esquina superior izquierda (x, y animados) en (ox + mx·sm + (x − rx)·Se, oy + my·sm + (y − ry)·Se); «ancho» = width fijo en px; «transición … entre t1 y t2» = pasar de la colocación normal de su capa (la de móvil) a esa colocación con interpolación smoothstep (u²·(3 − 2u)) de la escala y del origen entre esos tiempos.
- En móvil el track usa 34 vh por segundo (ver sección 7).
Colocación de capas en móvil:
- capa «Rectangle 22»: x=0, y=0, z=1.05
- capa «menu»: x=-8, y=14, z=1
- capa «Group 9»: x=12, y=104, z=0.68
- capa «Experience elegance, power, an... - Animation ▶»: x=16, y=262, z=0.85
- capa «Component 3»: x=16, y=318, z=0.85
- capa «Component 5»: x=244, y=448, z=0.55
- capa «Component 4»: x=16, y=682, z=0.52
- capa «Frame 26»: x=170.5, y=776, z=0.7
- capa «Group 42»: x=24, y=92, z=0.7
- capa «Group 40»: x=24, y=243.9, z=0.7
Las capas no listadas que no son a sangre se ocultan en móvil.
Ajustes por elemento en móvil:
- [e163] «Frame 1597882325»: ocultar
- [e181] «Frame 1597882324»: desplazar dx=-994, dy=0 (unidades del diseño de su capa)
- [e176] «Frame 1597882296»: desplazar dx=-1010, dy=0 (unidades del diseño de su capa)
- [e140] «Group 41»: desplazar dx=-1047, dy=808 (unidades del diseño de su capa)
- [e149] «Group 40»: desplazar dx=-449, dy=101 (unidades del diseño de su capa)
- [e153] «Group 39»: desplazar dx=-898, dy=394 (unidades del diseño de su capa)
- [e157] «Frame 26»: desvanecer entre t=7.85s y 8.45s

## 12. Semántica y accesibilidad
- `<main class="hero" aria-label="The Perfect Balance of Industrial Speed">`; el loader lleva `aria-hidden="true"`.
- Listas con `<ul>` y `<li>`; los menús, dentro de `<nav aria-label="…">`. Cada `<a href="#">` usa el `aria-label` indicado cuando su contenido no es texto. Si un enlace cubre una caja completa, va dentro de ella como `<a class="lk">` absoluto que la cubre (`inset:0`).
- Imágenes decorativas con `alt=""`; SVG decorativos con `aria-hidden="true"`.
- Solo los textos y los enlaces reciben eventos de puntero (CSS de la sección 3) y los elementos invisibles llevan `visibility:hidden`, así nunca se puede hacer clic en algo que no se ve.
- Foco visible: `outline: 2px solid currentColor; outline-offset: 3px`.

## 13. Recursos (URLs absolutas)
Todos los recursos se sirven desde un CDN con CORS habilitado; usa estas URLs tal cual:
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

## 14. Anexo: formas vectoriales
Cada SVG ocupa el 100% de la caja de su elemento (`position:absolute; inset:0; width:100%; height:100%; overflow:visible`, `preserveAspectRatio="none"`, `aria-hidden="true"`). Si un elemento indica «con relleno #xxxxxx (en lugar de #yyyyyy)», usa el mismo SVG cambiando ese color de relleno. Si un SVG con `id` internos (máscaras, degradados) se usa más de una vez, da ids únicos a cada copia y actualiza sus referencias `url(#…)`.

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

## 15. Criterios de aceptación
- A 1440×810, 1280×720 y 390×844 la composición es idéntica en los momentos clave: fin de la intro, cada pausa y estado final.
- Las posiciones, tamaños, colores, tipografías, tiempos y curvas coinciden con esta especificación.
- Todos los textos son HTML real y editable; todos los enlaces son `<a href="#">` clicables con foco visible; lo invisible no se puede pulsar.
- Las imágenes, el video y las tipografías se cargan desde las URLs de la sección 13 y no hay errores en la consola.
- El diseño se adapta al cambiar el tamaño de la ventana y en móvil sigue la sección 11.
- Con `prefers-reduced-motion` se muestra directamente el estado final.
