# Prompt de replicación — Glacius — Call for the Frost

> Instrucción completa para un agente de IA que programe. Contiene todo lo necesario para reconstruir de forma idéntica el hero animado «Glacius — Call for the Frost»: estructura, estilos, textos, tiempos y curvas de animación, vista móvil y enlaces a los recursos (imágenes, video y tipografías).

## Rol y objetivo
Actúa como desarrollador/a front-end senior especializado/a en animación web. Construye una página de una sola pantalla (hero) que reproduzca con exactitud el diseño animado «Glacius — Call for the Frost» descrito aquí, sin añadir ni quitar nada.

## 1. Descripción general
Hero de snowboards «Glacius — Call for the Frost», diseñado a 1920×1080 con una estética fría en azules y grises claros. Tras el loader, la intro automática (0 → 2.3 s) despliega sobre una montaña nevada las letras gigantes F-R-O-S-T (la «O» es una letra de cristal que refracta, aclara y aviva lo que hay detrás), el texto curvo «Call for the», el botón circular «Explore» con cuatro miniaturas de tablas y la barra superior (logo Glacius, Home, About us, Services, Contact, «Login» y «Get Started»). Al empezar el scroll la montaña y el cielo se desplazan con paralaje; entre 3.3 y 4.3 s suben nubes y un panel claro de borde difuminado que cubren la montaña; después (4.3 → 8.47 s) aparecen el sello circular con el texto «Glacius» y la frase «Feel the power of absolute precision. Tech shaped for extreme results. Where design meets high-speed action.», que pasa de gris a negro. En el tramo final (9.27 → 11.77 s) la frase sale y aparece la escena «Crafted for the ultimate descent.»: un snowboarder dentro de una gran «O», tres miniaturas en forma de píldora, el texto «Snowboards power every ride…» y el botón «Get Started», con paralaje entre el snowboarder, la «O» y la nieve del fondo. Los textos curvos son texto real sobre un trayecto (editable), no imágenes. Con el ratón, las capas se desplazan suavemente en sentido contrario al cursor (más las cercanas que las lejanas), el scroll es suave y lineal, sin pausas con imán, y los botones y los enlaces del menú cambian sutilmente de tono.

## 2. Entregable y reglas técnicas
- Un único archivo `index.html` con el CSS dentro de `<style>` y el JavaScript dentro de `<script>`. JavaScript puro (vanilla), sin frameworks ni proceso de build.
- Única dependencia externa: la librería Lenis, versión exacta 1.3.26 (paquete npm `lenis`, archivo `dist/lenis.min.js`, que expone la clase global `Lenis`), para el scroll suave. Cárgala con una etiqueta `<script>` desde un CDN público de npm antes de tu script (si no carga, todo debe funcionar con el scroll nativo).
- Imágenes, video y tipografías se cargan SIEMPRE desde las URLs absolutas de la sección 13 «Recursos». No los descargues, no los incrustes en base64 y no uses otros recursos.
- Todo texto visible es texto HTML real, seleccionable y editable (nunca texto convertido en imagen ni en trazados SVG). Las formas vectoriales del Anexo son solo iconos y decoración.
- Listas con `<ul>`/`<li>` (dentro de `<nav aria-label="…">` cuando se indique) y enlaces con `<a href="#">` clicables, con `aria-label` donde se indica.
- Cabecera: `<html lang="en">`, `<meta charset="utf-8">`, `<meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover">`, `<title>Glacius — Call for the Frost</title>`, `<meta name="description" content="Glacius snowboards: call for the frost.">`, `<meta name="theme-color" content="#3b4d6a">`.
- Respeta `prefers-reduced-motion: reduce`: el loader funciona igual, pero no hay intro, ni scroll suave, ni snap ni efectos de puntero; t queda fijo en el estado final (t = 12.57 s) aunque se haga scroll.
- No añadas elementos, textos, efectos ni secciones que no estén en esta especificación.

## 3. Estructura base (HTML + CSS común)
```html
<body>
<div class="loader" id="loader" aria-hidden="true">
  <div class="loader-in"><span>GLACIUS</span><div class="loader-bar"><i id="loaderBar"></i></div><span id="loaderPct">000</span></div>
</div>
<main class="hero" id="hero" aria-label="Glacius — Call for the Frost">
  <div class="stage" id="stage">
    <!-- una capa (div.blk) por cada «Capa» de la sección 9, en el mismo orden: las posteriores quedan encima -->
  </div>
</main>
<script>/* motor de la sección 6 */</script>
</body>
```
```css
:root{--bg:#3b4d6a;--ink:#ffffff;color-scheme:dark}
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
- Diseño de referencia (frame): **1920×1080 px**. Todas las medidas en px de este documento son unidades de ese diseño.
- El escenario `.stage` ocupa la ventana completa (W × H = ancho × alto actual del escenario).
- Cada **capa** (`div.blk`) es un contenedor absoluto de tamaño 0 escalado por un factor z (con `zoom: z`, o con `transform: translate(…) scale(z)` y `transform-origin: 0 0`; ambos son válidos si el resultado en pantalla es el mismo). Dentro de una capa, los elementos de primer nivel usan **coordenadas del frame**, y el punto (X, Y) del diseño se dibuja en pantalla en:
  - **CAPA A SANGRE (cover)**: z = max(W/1920, H/1080); pantalla = ((W − 1920·z)/2 + X·z, (H − 1080·z)/2 + Y·z). Siempre cubre la pantalla (se recorta lo que sobra).
  - **CAPA CONTENIDA (contain, ancla ax, ay)**: z = s = min(W/1920, H/1080); mx = (W − 1920·s)/2; my = (H − 1080·s)/2; pantalla = (mx·(1+ax) + X·s, my·(1+ay) + Y·s). Las anclas valen −1, 0 o 1: −1 = pegada al borde izquierdo/superior, 0 = centrada, 1 = pegada al borde derecho/inferior.
- La «caja de diseño» de cada capa (x, y, ancho×alto) es su rectángulo de referencia en el frame (su contenido puede salirse de él); su esquina superior izquierda se usa para colocar la capa en la vista móvil.
- Los elementos anidados usan coordenadas locales de su contenedor (left/top relativos al padre).
- Recalcula todo en cada `resize` y también cuando cambie el tamaño real de `.stage` aunque no haya `resize` (un `ResizeObserver` sobre `.stage`; p. ej. al aparecer o desaparecer una barra de desplazamiento), de modo que las capas ancladas a los bordes queden siempre pegadas al marco. En cada recálculo asigna además a `.stage` la variable CSS `--s` con la escala vigente (s en escritorio; sm en la vista móvil de la sección 11): `stage.style.setProperty('--s', escala)`; la usan los estilos que deben acompañar al diseño (sección 10).

## 5. Tipografías
Declara estas fuentes con `@font-face{font-family:'…';src:url(URL) format('woff2');font-weight:…;font-style:normal}` y úsalas con la pila `'Fuente', system-ui, sans-serif`:
- **Joane** (peso 400): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/fonts/Joane-Regular.woff2
- **Joane** (peso 600): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/fonts/Joane-SemiBold.woff2
- **Kulim Park** (peso 300): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/fonts/kulim-park-latin-300-normal.woff2
- **Kulim Park** (peso 400): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/fonts/kulim-park-latin-400-normal.woff2
- **Kulim Park** (peso 700): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/fonts/kulim-park-latin-700-normal.woff2
- **Marcellus SC** (peso 400): https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/fonts/marcellus-sc-latin-400-normal.woff2

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
- Las curvas `cubic-bezier(x1, y1, x2, y2)` funcionan como en CSS: dado el progreso temporal p, resolver el parámetro u tal que X(u) = p y devolver Y(u).

## 7. Reproducción (intro y scroll)
- Duración total de la línea de tiempo: 12.57 s. Intro automática: de t = 0 a t = 2.3 s. El resto (2.3 → 12.57 s) se controla con el scroll.
- Altura de `.hero` = 100vh + track. track = round(L (= 8.066 s, ver la correspondencia scroll → tiempo) × 60) = **484vh** en escritorio y round(L (= 8.066 s, ver la correspondencia scroll → tiempo) × 40) = **323vh** en móvil (usa exactamente estos valores; se definen con la variable CSS `--track`). `.stage` es sticky, así que el escenario queda fijo mientras se recorre el track.
- Mientras carga: `html.is-locked` (overflow hidden; se añade antes de la primera medición del escenario, para que una barra de desplazamiento clásica no reduzca el ancho medido), `history.scrollRestoration = 'manual'`, `scrollTo(0,0)`.
- Cuando el loader recibe la clase `is-done` reproduce la intro: t avanza en tiempo real (lineal) de 0 a 2.3 s en 2.3 s. Durante la intro el scroll está bloqueado (Lenis detenido; si Lenis no está disponible, mantén `is-locked` hasta que termine la intro) y los eventos de scroll no cambian t. Al terminar, t = 2.3 y se activa el scroll.
- Scroll suave con Lenis: `new Lenis({lerp:0.1, wheelMultiplier:1, smoothWheel:true})`, `lenis.stop()` hasta acabar la intro y luego `lenis.start()`; llamar `lenis.raf(now)` en el bucle de requestAnimationFrame. Sin Lenis, usar el scroll nativo.
- Correspondencia scroll → tiempo: p = clamp((scrollY − hero.offsetTop) / (hero.offsetHeight − stage.clientHeight), 0, 1); el scroll recorre la línea de tiempo con tramos ponderados: [2.301 – 3.301] con peso 0.4, [4.302 – 5.85] con peso 0.3, [8.47 – 9.27] con peso 0.35 (cada segundo de esos tramos ocupa peso × su recorrido normal de scroll; el resto, peso 1; así las pausas ocupan menos scroll y el desplazamiento nunca queda «en vacío»). Longitud de scroll acumulada ℓ(t) = (t − 2.3) − Σ (1 − peso) × duración de la parte de cada tramo comprendida entre 2.3 y t; L = ℓ(12.57) = 8.066. t es el valor con ℓ(t) = p × L (inversa lineal por tramos) y, a la inversa, el scroll de un tiempo t es hero.offsetTop + ℓ(t)/L × (hero.offsetHeight − stage.clientHeight).
- Sin pausas con imán: la página nunca se desplaza sola; Lenis solo suaviza el scroll.
- En cada resize: recalcular capas, track y t a partir del scroll actual.

## 8. Loader y precarga
- El loader cubre la pantalla con el color `--bg`, muestra «GLACIUS», una barra de 160×1 px y el porcentaje con 3 dígitos (`000` → `100`), en el color `--ink`.
- Progreso ponderado: tipografías (`document.fonts.ready`) peso 1; cada elemento `<img>` del escenario peso 0.4, aunque repita archivo (cuenta cuando ha cargado —o ha fallado— y `img.decode()` ha terminado; el póster del video no cuenta); no hay video. Porcentaje = Math.round(progreso × 100) con 3 dígitos. La barra usa `transform: scaleX(progreso)` y nunca retrocede.
- Se espera a que todo termine (máximo 14 s; si se agota, se continúa sin cancelar nada). Entonces se quita `is-locked` de `<html>` (salvo en el caso sin Lenis descrito en la sección 7), se añade `is-done` al loader (se desvanece en 0.9 s) y empieza la intro.

## 9. Capas y elementos
Notación de cada línea: `[eN]` = identificador sugerido; `caja` = div; `texto` = div con texto (el contenido entre comillas, respetando mayúsculas, saltos de línea y espacios); `grupo` = contenedor sin caja; `vector` = caja que contiene un SVG en línea; `<a href="#">`, `<ul>`, `<li>`, `<nav>` = etiqueta semántica que debe usarse; `«nombre»` = nombre de la capa en el diseño (solo como referencia, también útil como `data-name`); `pos(x,y)` = left/top; `tamaño A×B` = width×height; después de `|` van los estilos CSS literales. Todo elemento `texto` lleva `class="t"` (lo usa la regla de pointer-events de la sección 3); las cajas, grupos y vectores pueden usar `class="b"`, `"g"` y `"v"`. Las capas decorativas (borde, vidrio, sombra interior, desenfoque de fondo, capa de relleno) son elementos absolutos (por ejemplo `<i>`) que cubren su contenedor (`inset` indicado) con `pointer-events:none` en línea y `border-radius:inherit` salvo que se indique otro. Las imágenes van dentro de un contenedor absoluto `inset:0; overflow:hidden; border-radius:inherit`. El orden de las líneas es el orden de apilamiento (lo posterior queda encima).

#### Capa bk0 «Fondo» — CAPA A SANGRE (cover), caja de diseño x=0 y=0 1920×1080
- [e1] caja «hero-01» pos(0,0) tamaño 1920×1080 | background-color:#f4f5f7

#### Capa bk1 «Background» — CAPA A SANGRE (cover), caja de diseño x=0 y=0 2144×1324.36
- [e2] caja «Background» transform translate(2031.21,-69.18) rotate(3.14rad) scale(1,-1) tamaño 2144×1324.36
    ↳ animación: x: 0.3→2.3s 2031.21→1955.81 (E0); y: 0.3→2.3s -69.18→-22.6 (E0); 3.301→4.301s -22.6→-78.6 (linear); opacidad: 6.386→8.47s 1→0 (linear); escala-contenido: 0.3→2.3s 1→0.93 (E0)
  - imagen https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/85c9ccdfc2-97d978.webp: `<img alt="">` absoluta en (0,0) de 2880×1779 px, max-width:none, transform-origin 0 0 y transform matrix(0.7442,0,0,0.7444,0.257,0); el contenedor la recorta (overflow hidden, mismo border-radius)
  - capa de relleno (cubre toda la caja, apilada en este orden): background:linear-gradient(rgba(60,78,106,0.3),rgba(60,78,106,0.3))

#### Capa bk2 «F» — CAPA CONTENIDA (contain, ancla x=-1, y=1), caja de diseño x=199 y=1115.8 225×290
- [e3] texto pos(199,1115.8) tamaño 225×290 | opacity:0.5; mix-blend-mode:lighten; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Joane'; font-weight:400; font-size:400px; line-height:1.4; letter-spacing:0.05em; text-transform:uppercase; background:linear-gradient(180deg, rgba(226,240,255,0.6) 0%, rgba(226,240,255,0.12) 100%); -webkit-background-clip:text; color:transparent | TEXTO: "F"
    ↳ animación: x: 0.3→2.3s 199→197.63 (E0); y: 0.3→2.3s 1115.8→392 (E0); 3.301→4.301s 392→152 (linear); opacidad: 6.386→8.47s 0.5→0 (linear)

#### Capa bk3 «R» — CAPA CONTENIDA (contain, ancla x=-1, y=1), caja de diseño x=437.87 y=755.8 260×290
- [e4] texto pos(437.87,755.8) tamaño 260×290 | opacity:0.5; mix-blend-mode:lighten; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Joane'; font-weight:400; font-size:400px; line-height:1.4; letter-spacing:0.05em; text-transform:uppercase; background:linear-gradient(180deg, rgba(226,240,255,0.6) 0%, rgba(226,240,255,0.12) 100%); -webkit-background-clip:text; color:transparent | TEXTO: "R"
    ↳ animación: y: 0.3→2.3s 755.8→392 (E0); 3.301→4.301s 392→212 (linear); opacidad: 6.386→8.47s 0.5→0 (linear)

#### Capa bk4 «S» — CAPA CONTENIDA (contain, ancla x=1, y=1), caja de diseño x=1233.53 y=755.8 230×290
- [e5] texto pos(1233.53,755.8) tamaño 230×290 | opacity:0.5; mix-blend-mode:lighten; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Joane'; font-weight:400; font-size:400px; line-height:1.4; letter-spacing:0.05em; text-transform:uppercase; background:linear-gradient(180deg, rgba(226,240,255,0.6) 0%, rgba(226,240,255,0.12) 100%); -webkit-background-clip:text; color:transparent | TEXTO: "S"
    ↳ animación: y: 0.3→2.3s 755.8→392 (E0); 3.301→4.301s 392→212 (linear); opacidad: 6.386→8.47s 0.5→0 (linear)

#### Capa bk5 «T» — CAPA CONTENIDA (contain, ancla x=1, y=1), caja de diseño x=1467.36 y=1115.8 256×290
- [e6] texto pos(1467.36,1115.8) tamaño 256×290 | opacity:0.5; mix-blend-mode:lighten; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Joane'; font-weight:400; font-size:400px; line-height:1.4; letter-spacing:0.05em; text-transform:uppercase; background:linear-gradient(180deg, rgba(226,240,255,0.6) 0%, rgba(226,240,255,0.12) 100%); -webkit-background-clip:text; color:transparent | TEXTO: "T"
    ↳ animación: y: 0.3→2.3s 1115.8→392 (E0); 3.301→4.301s 392→152 (linear); opacidad: 6.386→8.47s 0.5→0 (linear)

#### Capa bk6 «Object» — CAPA A SANGRE (cover), caja de diseño x=0 y=540 1920×848
- [e7] caja «Object» transform translate(1920,540) rotate(3.14rad) scale(1,-1) tamaño 1920×848
    ↳ animación: y: 0.3→2.3s 540→392 (E0); 3.301→4.301s 392→202 (linear); opacidad: 6.386→8.47s 1→0 (linear)
  - imagen https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/74e24220b9-7b651c.webp: `<img alt="">` absoluta en (0,0) de 2880×1271 px, max-width:none, transform-origin 0 0 y transform matrix(0.6665,0,0,0.6665,0.23,0.856); el contenedor la recorta (overflow hidden, mismo border-radius)
  - capa de relleno (cubre toda la caja, apilada en este orden): background:linear-gradient(180deg, rgba(222,223,229,0) 61.16%, #f4f5f7 100%)

#### Capa bk7 «Ellipse 3» — CAPA CONTENIDA (contain, ancla x=0, y=1), caja de diseño x=258.02 y=862.34 1402.79×523.49
- [e8] caja «Ellipse 3» pos(258.02,862.34) tamaño 1402.79×523.5 | opacity:0.4
    ↳ animación: opacidad: 6.386→8.47s 0.4→0 (linear)
  - capa de relleno (cubre toda la caja, apilada en este orden): inset:-118px
  - imagen https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/bk-e8-516e6e.webp: `<img alt="">` absoluta, inset:0, width/height 100%, max-width:none (estirada sobre su contenedor, que sobresale de la caja y no recorta)

#### Capa bk8 «Mask group» — CAPA A SANGRE (cover), caja de diseño x=-0.59 y=647.4 1920×865.21
- [e9] grupo «Mask group»
    ↳ animación: opacidad: salto de 1 a 0 en 2.301s
  - [e14] grupo
      ↳ animación: desplazY: 0.3→2.3s 0→342.88 (E0)
    - [-] máscara transform matrix(1,0,0,1,-0.587,647.396) tamaño 1920×865.21 | overflow:hidden; border-radius:0px; mask-image:linear-gradient(180deg, #000000 66.95%, rgba(0,0,0,0) 100%)
      - contenedor de compensación transform matrix(1,0,0,1,0.587,-647.396)
        - [e15] grupo
            ↳ animación: desplazY: 0.3→2.3s 0→-342.88 (E0)
          - [e10] grupo «Group 12»
              ↳ animación: opacidad: salto de 1 a 0 en 2.301s
            - [e11] caja «image 16» pos(-18.89,843.12) tamaño 1938.3×544
                ↳ animación: y: 0.3→2.3s 843.12→1249.69 (E0); opacidad: salto de 1 a 0 en 2.301s
              - capa de relleno (cubre toda la caja, apilada en este orden): inset:-75px
              - imagen https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/bk-e11-59f310.webp: `<img alt="">` absoluta, inset:0, width/height 100%, max-width:none (estirada sobre su contenedor, que sobresale de la caja y no recorta)
            - [e12] caja «image 17» pos(1142.08,790.12) tamaño 997.54×626.84
                ↳ animación: y: 0.3→2.3s 790.12→1258.69 (E0); opacidad: salto de 1 a 0 en 2.301s
              - imagen https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/52f54be031-k-xca7a89.webp: `<img alt="">` absoluta en (0,0) de 1232×928 px, max-width:none, transform-origin 0 0 y transform matrix(0.8359,0,0,0.6754,-32.285,0); el contenedor la recorta (overflow hidden, mismo border-radius)
            - [e13] caja «image 18» pos(-236.22,910.12) tamaño 1757.64×460.95
                ↳ animación: y: 0.3→2.3s 910.12→1319.6 (E0); opacidad: salto de 1 a 0 en 2.301s
              - imagen https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/20f1181785-b9a0ef-k-xc99560.webp: `<img alt="">` absoluta en (0,0) de 1904×640 px, max-width:none, transform-origin 0 0 y transform matrix(0.9498,0,0,0.7454,0,-8.068); el contenedor la recorta (overflow hidden, mismo border-radius)

#### Capa bk9 «O» — CAPA CONTENIDA (contain, ancla x=0, y=1), caja de diseño x=675.13 y=608 583×512
- [e16] texto pos(675.13,608) tamaño 583×512 | white-space:pre; text-align:center; text-box:trim-both cap alphabetic; font-family:'Joane'; font-weight:400; font-size:707.11px; line-height:1.4; letter-spacing:0.05em; text-transform:uppercase; color:transparent | TEXTO: "O" | LETRA DE CRISTAL: dentro del elemento, después del texto, en este orden: `<span class="gx gx-sh" aria-hidden="true" style="-webkit-clip-path:path(evenodd,'M0 0H697V626H0ZM347.6 49.9Q460.1 49.9 531.5 122.1Q602.9 194.2 602.9 311.6Q602.9 427.5 531.1 500.7Q459.3 573.9 347.6 573.9Q235.9 573.9 164.5 500.7Q93.1 427.5 93.1 311.6Q93.1 194.2 164.5 122.1Q235.9 49.9 347.6 49.9ZM347.6 563.3Q426.8 563.3 475.3 496.1Q523.7 428.9 523.7 311.6Q523.7 192.8 475.3 126.7Q426.8 60.5 347.6 60.5Q268.4 60.5 220.3 126.7Q172.3 192.8 172.3 311.6Q172.3 428.9 220.7 496.1Q269.1 563.3 347.6 563.3Z');clip-path:path(evenodd,'M0 0H697V626H0ZM347.6 49.9Q460.1 49.9 531.5 122.1Q602.9 194.2 602.9 311.6Q602.9 427.5 531.1 500.7Q459.3 573.9 347.6 573.9Q235.9 573.9 164.5 500.7Q93.1 427.5 93.1 311.6Q93.1 194.2 164.5 122.1Q235.9 49.9 347.6 49.9ZM347.6 563.3Q426.8 563.3 475.3 496.1Q523.7 428.9 523.7 311.6Q523.7 192.8 475.3 126.7Q426.8 60.5 347.6 60.5Q268.4 60.5 220.3 126.7Q172.3 192.8 172.3 311.6Q172.3 428.9 220.7 496.1Q269.1 563.3 347.6 563.3Z')"><span>O</span></span>` + `<i class="gx gx-lens gx-rf" aria-hidden="true" style="--gxb:brightness(1.256) contrast(1.144) saturate(1.12);-webkit-clip-path:path(evenodd,'M347.6 49.9Q460.1 49.9 531.5 122.1Q602.9 194.2 602.9 311.6Q602.9 427.5 531.1 500.7Q459.3 573.9 347.6 573.9Q235.9 573.9 164.5 500.7Q93.1 427.5 93.1 311.6Q93.1 194.2 164.5 122.1Q235.9 49.9 347.6 49.9ZM347.6 563.3Q426.8 563.3 475.3 496.1Q523.7 428.9 523.7 311.6Q523.7 192.8 475.3 126.7Q426.8 60.5 347.6 60.5Q268.4 60.5 220.3 126.7Q172.3 192.8 172.3 311.6Q172.3 428.9 220.7 496.1Q269.1 563.3 347.6 563.3Z');clip-path:path(evenodd,'M347.6 49.9Q460.1 49.9 531.5 122.1Q602.9 194.2 602.9 311.6Q602.9 427.5 531.1 500.7Q459.3 573.9 347.6 573.9Q235.9 573.9 164.5 500.7Q93.1 427.5 93.1 311.6Q93.1 194.2 164.5 122.1Q235.9 49.9 347.6 49.9ZM347.6 563.3Q426.8 563.3 475.3 496.1Q523.7 428.9 523.7 311.6Q523.7 192.8 475.3 126.7Q426.8 60.5 347.6 60.5Q268.4 60.5 220.3 126.7Q172.3 192.8 172.3 311.6Q172.3 428.9 220.7 496.1Q269.1 563.3 347.6 563.3Z');--gxr:url(#gxre16)"></i>` + `<svg class="gx-defs" aria-hidden="true" width="0" height="0" style="position:absolute;width:0;height:0;overflow:hidden"><filter id="gxre16" x="0" y="0" width="697" height="626" filterUnits="userSpaceOnUse" primitiveUnits="userSpaceOnUse" color-interpolation-filters="sRGB"><feImage href="https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/gx-e16.png" x="0" y="0" width="697" height="626" preserveAspectRatio="none" result="m"/><feDisplacementMap in="SourceGraphic" in2="m" scale="77.85" xChannelSelector="R" yChannelSelector="G"/></filter></svg>` + `<span class="gx gx-fill" aria-hidden="true" style="opacity:0.55;background:linear-gradient(180deg, rgba(226,240,255,0.012) 9.11%, rgba(226,240,255,0.6) 90.89%)!important;-webkit-background-clip:text!important;background-clip:text!important"><span>O</span></span>` + `<span class="gx gx-glow" aria-hidden="true" style="--gw:8.485px;--gb:4.243px;--ga:0.336;-webkit-clip-path:path(evenodd,'M347.6 49.9Q460.1 49.9 531.5 122.1Q602.9 194.2 602.9 311.6Q602.9 427.5 531.1 500.7Q459.3 573.9 347.6 573.9Q235.9 573.9 164.5 500.7Q93.1 427.5 93.1 311.6Q93.1 194.2 164.5 122.1Q235.9 49.9 347.6 49.9ZM347.6 563.3Q426.8 563.3 475.3 496.1Q523.7 428.9 523.7 311.6Q523.7 192.8 475.3 126.7Q426.8 60.5 347.6 60.5Q268.4 60.5 220.3 126.7Q172.3 192.8 172.3 311.6Q172.3 428.9 220.7 496.1Q269.1 563.3 347.6 563.3Z');clip-path:path(evenodd,'M347.6 49.9Q460.1 49.9 531.5 122.1Q602.9 194.2 602.9 311.6Q602.9 427.5 531.1 500.7Q459.3 573.9 347.6 573.9Q235.9 573.9 164.5 500.7Q93.1 427.5 93.1 311.6Q93.1 194.2 164.5 122.1Q235.9 49.9 347.6 49.9ZM347.6 563.3Q426.8 563.3 475.3 496.1Q523.7 428.9 523.7 311.6Q523.7 192.8 475.3 126.7Q426.8 60.5 347.6 60.5Q268.4 60.5 220.3 126.7Q172.3 192.8 172.3 311.6Q172.3 428.9 220.7 496.1Q269.1 563.3 347.6 563.3Z')"><span>O</span></span>` + `<span class="gx gx-line" aria-hidden="true"><span>O</span></span>` + `<span class="gx gx-rim" aria-hidden="true" style="--gxa:0.76;-webkit-mask-image:linear-gradient(135deg,#000 0%,rgba(0,0,0,.08) 40%,rgba(0,0,0,.08) 60%,#000 100%);mask-image:linear-gradient(135deg,#000 0%,rgba(0,0,0,.08) 40%,rgba(0,0,0,.08) 60%,#000 100%)"><span>O</span></span>`
    ↳ animación: y: 0.3→2.3s 608→281 (E0); 3.301→4.301s 281→171 (linear); opacidad: 6.386→8.47s 1→0 (linear)

#### Capa bk10 «Group 15» — CAPA CONTENIDA (contain, ancla x=0, y=1), caja de diseño x=654.18 y=1118.09 610.07×322.34
- [e17] grupo «Group 15»
    ↳ animación: opacidad: 6.386→8.47s 1→0 (linear)
  - [e18] grupo <a href="#"> «Group 13»
      ↳ animación: opacidad: 6.386→8.47s 1→0 (linear)
    - [e19] caja «Ellipse 2» pos(865.02,1118.09) tamaño 188.38×188.38 | opacity:0.5; border-radius:50%
        ↳ animación: y: 0.3→2.3s 1118.09→878.09 (E0); 3.301→4.301s 878.09→828.09 (linear); opacidad: 6.386→8.47s 0.5→0 (linear)
      - borde (stroke): inset:-1px; border-radius:50%; border:1px solid rgba(255,255,255,0.8)
    - [e20] caja «Ellipse 1» pos(898.5,1151.56) tamaño 121.44×121.44 | border-radius:50%; background-color:#ffffff
        ↳ animación: y: 0.3→2.3s 1151.55→911.55 (E0); 3.301→4.301s 911.55→861.55 (linear); opacidad: 6.386→8.47s 1→0 (linear)
    - [e21] texto pos(932.22,1206.78) tamaño 54×10 | white-space:pre; text-align:center; text-box:trim-both cap alphabetic; font-family:'Kulim Park'; font-weight:400; font-size:14px; line-height:1.4; text-transform:uppercase; color:#1f2b3f | TEXTO: "Explore"
        ↳ animación: y: 0.3→2.3s 1206.78→966.78 (E0); 3.301→4.301s 966.78→916.78 (linear); opacidad: 6.386→8.47s 1→0 (linear)
  - [-] lista <ul>
    - [e22] caja <li> «Rectangle 4» pos(654.18,1384.13) tamaño 56.29×56.29 | border-radius:614.43px
        ↳ animación: y: 0.3→2.3s 1384.13→944.13 (E0); 3.301→4.301s 944.13→894.13 (linear); opacidad: 6.386→8.47s 1→0 (linear)
      - [-] enlace <a href="#"> aria-label="Snowboard model 1" que cubre toda la caja de su contenedor (inset:0)
        - imagen https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/118451b301.webp: `<img alt="">` absoluta, inset:0, width/height 100%, object-fit:cover (recortada por el contenedor)
    - [e23] caja <li> «Rectangle 5» pos(745.47,1290) tamaño 84.56×84.56 | border-radius:614.43px
        ↳ animación: y: 0.3→2.3s 1290→930 (E0); 3.301→4.301s 930→880 (linear); opacidad: 6.386→8.47s 1→0 (linear)
      - [-] enlace <a href="#"> aria-label="Snowboard model 2" que cubre toda la caja de su contenedor (inset:0)
        - imagen https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/0f7ebbf2c3.webp: `<img alt="">` absoluta, inset:0, width/height 100%, object-fit:cover (recortada por el contenedor)
    - [e24] caja <li> «Rectangle 6» pos(1088.41,1290) tamaño 84.56×84.56 | border-radius:614.43px
        ↳ animación: y: 0.3→2.3s 1290→930 (E0); 3.301→4.301s 930→880 (linear); opacidad: 6.386→8.47s 1→0 (linear)
      - [-] enlace <a href="#"> aria-label="Snowboard model 3" que cubre toda la caja de su contenedor (inset:0)
        - imagen https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/d10d4fdfd0.webp: `<img alt="">` absoluta, inset:0, width/height 100%, object-fit:cover (recortada por el contenedor)
    - [e25] caja <li> «Rectangle 7» pos(1207.96,1384.13) tamaño 56.29×56.29 | border-radius:614.43px
        ↳ animación: y: 0.3→2.3s 1384.13→944.13 (E0); 3.301→4.301s 944.13→894.13 (linear); opacidad: 6.386→8.47s 1→0 (linear)
      - [-] enlace <a href="#"> aria-label="Snowboard model 4" que cubre toda la caja de su contenedor (inset:0)
        - imagen https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/b78be8999a.webp: `<img alt="">` absoluta, inset:0, width/height 100%, object-fit:cover (recortada por el contenedor)

#### Capa bk11 «Call for the» — CAPA CONTENIDA (contain, ancla x=0, y=-1), caja de diseño x=398.8 y=105.59 1122.39×549.89
- [e26] vector «Call for the» pos(398.8,105.59) tamaño 1122.39×549.89 | opacity:0
    ↳ animación: x: 0.3→2.3s 398.8→398.02 (E0); y: 0.3→2.3s 105.59→173.11 (E0); 3.301→4.301s 173.11→-16.89 (linear); opacidad: 0.3→2.3s 0→1 (E0); 6.386→8.47s 1→0 (linear); 9.27→11.77s 0→1 (linear)
  - texto sobre trayecto circular (texto real y editable, con `fill="currentColor"` para que herede el color animado del elemento). SVG exacto, que ocupa el 100% de la caja (position:absolute; inset:0; width:100%; height:100%; overflow:visible): `<svg preserveAspectRatio="none" width="1122.392" height="549.89" viewBox="0 0 1122.392 549.89"><defs><path id="e26_i1" d="M561.196,549.890A561.196,274.945 0 1 1 561.196,0.000A561.196,274.945 0 1 1 561.196,549.890" fill="none"/><linearGradient id="e26_i0" gradientUnits="userSpaceOnUse" x1="561.196" y1="-66.218" x2="561.196" y2="58.609"><stop offset="0" stop-color="#e2f0ff" stop-opacity="1"/><stop offset="1" stop-color="#e2f0ff" stop-opacity="0.2"/></linearGradient></defs><text fill="url(#e26_i0)" style="font-family:'Joane',system-ui,sans-serif;font-weight:600;font-size:40px;letter-spacing:46.4px;text-transform:uppercase;"><textPath href="#e26_i1" startOffset="50%" text-anchor="middle">Call for the</textPath></text></svg>`

#### Capa bk12 «Group 22» — CAPA CONTENIDA (contain, ancla x=0, y=-1), caja de diseño x=47.41 y=-208.35 1821×125
- [e27] grupo «Group 22»
  - [e28] grupo <a href="#"> «Group 16»
    - [e29] caja «image 21» pos(47.41,-133.35) tamaño 32.65×32.65
        ↳ animación: y: 0.3→2.3s -133.35→25.32 (E0); ex: 3.301→4.301s 0→-1 (linear)
      - imagen https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/5ca960a21e.webp: `<img alt="">` absoluta en (0,0) de 2048×2048 px, max-width:none, transform-origin 0 0 y transform matrix(0.0334,0,0,0.0334,-17.887,-17.887); el contenedor la recorta (overflow hidden, mismo border-radius)
    - [e30] texto pos(85.52,-121.13) tamaño 91×14 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Marcellus SC'; font-weight:400; font-size:19.50px; line-height:1.4; letter-spacing:0.11em; text-transform:uppercase; color:#ffffff | TEXTO: "Glacius"
        ↳ animación: y: 0.3→2.3s -121.13→37.54 (E0); tc: 3.301→4.301s [255,255,255,1]→[0,0,0,1] (linear)
  - [e31] grupo «Group 23»
    - [-] lista <nav> aria-label="Main navigation"
      - [-] lista <ul>
        - [e32] texto <li> pos(670.72,-188.35) tamaño 33×8 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kulim Park'; font-weight:400; font-size:12px; line-height:1.4; letter-spacing:0.02em; text-transform:uppercase; color:#ffffff | TEXTO: "" | enlace en línea: el texto es el contenido de un <a href="#"> dentro del elemento
            ↳ animación: x: 0.3→2.3s 670.72→770.72 (E0); y: 0.3→2.3s -188.35→36.65 (E0); tc: 3.301→4.301s [255,255,255,1]→[0,0,0,1] (linear)
        - [e33] texto <li> pos(830.38,-208.35) tamaño 59×8 | opacity:0.5; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kulim Park'; font-weight:400; font-size:12px; line-height:1.4; letter-spacing:0.02em; text-transform:uppercase; color:#ffffff | TEXTO: "" | enlace en línea: el texto es el contenido de un <a href="#"> dentro del elemento
            ↳ animación: x: 0.3→2.3s 830.38→863.72 (E0); y: 0.3→2.3s -208.35→36.65 (E0); tc: 3.301→4.301s [255,255,255,1]→[0,0,0,1] (linear)
        - [e34] texto <li> pos(1016.05,-208.35) tamaño 50×8 | opacity:0.5; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kulim Park'; font-weight:400; font-size:12px; line-height:1.4; letter-spacing:0.02em; text-transform:uppercase; color:#ffffff | TEXTO: "" | enlace en línea: el texto es el contenido de un <a href="#"> dentro del elemento
            ↳ animación: x: 0.3→2.3s 1016.05→982.72 (E0); y: 0.3→2.3s -208.35→36.65 (E0); tc: 3.301→4.301s [255,255,255,1]→[0,0,0,1] (linear)
        - [e35] texto <li> pos(1192.72,-188.35) tamaño 55×8 | opacity:0.5; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kulim Park'; font-weight:400; font-size:12px; line-height:1.4; letter-spacing:0.02em; text-transform:uppercase; color:#ffffff | TEXTO: "" | enlace en línea: el texto es el contenido de un <a href="#"> dentro del elemento
            ↳ animación: x: 0.3→2.3s 1192.72→1092.72 (E0); y: 0.3→2.3s -188.35→36.65 (E0); tc: 3.301→4.301s [255,255,255,1]→[0,0,0,1] (linear)
  - [e36] grupo «Group 24»
    - [e37] texto <a href="#"> pos(1642.41,-113.35) tamaño 40×10 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kulim Park'; font-weight:700; font-size:14px; line-height:1.4; letter-spacing:0.02em; text-transform:uppercase; color:#ffffff | TEXTO: "Login"
        ↳ animación: y: 0.3→2.3s -113.35→36.65 (E0); tc: 3.301→4.301s [255,255,255,1]→[0,0,0,1] (linear)
    - [e38] caja <a href="#"> «Frame 12» pos(1742.41,-133.35) tamaño 126×50 | mix-blend-mode:lighten; border-radius:999px; background:linear-gradient(103.97deg, rgba(226,240,255,0.4) 13.88%, rgba(226,240,255,0.08) 89.25%)
        ↳ animación: y: 0.3→2.3s -133.35→16.65 (E0)
      - [e39] texto pos(20,20) tamaño 86×10 | white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kulim Park'; font-weight:400; font-size:14px; line-height:1.4; text-transform:uppercase; color:#ffffff | TEXTO: "Get Started"
          ↳ animación: tc: 3.301→4.301s [255,255,255,1]→[0,0,0,1] (linear)
      - borde (stroke): inset:-1px; border-radius:1000px; padding:1px; background:linear-gradient(115.98deg, rgba(255,255,255,0) -18.54%, rgba(255,255,255,0.8) 41.91%, rgba(255,255,255,0) 102.36%); borde degradado de 1px (fondo degradado recortado con mask: linear-gradient content-box exclude)

#### Capa bk13 «Rectangle 8» — CAPA A SANGRE (cover), caja de diseño x=0 y=0 1920×1075.41
- [e40] caja «Rectangle 8» pos(-0.78,2964.59) tamaño 1920×1075.41 | opacity:0; background:linear-gradient(180deg, rgba(197,208,221,0) 0%, #c5d0dd 100%)
    ↳ animación: y: 6.386→8.47s 2964.59→1814.59 (linear); 9.27→11.77s 1814.59→273.26 (linear); opacidad: salto de 0 a 0.7 en 2.301s

#### Capa bk14 «Group 18» — CAPA CONTENIDA (contain, ancla x=0, y=1), caja de diseño x=454.13 y=2636.45 1118.48×1484.87
- [e41] grupo «Group 18» | opacity:0
    ↳ animación: opacidad: salto de 0 a 1 en 2.301s
  - [e42] caja «image 22» transform translate(454.13,2636.45) rotate(0rad) tamaño 1118.48×1484.87 | opacity:0
      ↳ animación: x: 6.386→8.47s 454.13→675.88 (linear); 9.27→11.77s 675.88→454.13 (linear); y: 6.386→8.47s 2636.45→1486.38 (linear); 9.27→11.77s 1486.38→-54.87 (linear); rotación(rad): 6.386→8.47s 0→0.27 (linear); 9.27→11.77s 0.27→0 (linear); opacidad: salto de 0 a 1 en 2.301s
    - imagen https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/a4b9daac19-d35bad-x6f690a.webp: `<img alt="">` absoluta, inset:0, width/height 100%, object-fit:cover (recortada por el contenedor)
  - [e43] texto pos(631.11,2789.24) tamaño 793×697 | opacity:0; white-space:pre; text-align:center; text-box:trim-both cap alphabetic; font-family:'Joane'; font-weight:400; font-size:962.11px; line-height:1.4; letter-spacing:0.05em; text-transform:uppercase; color:transparent | TEXTO: "O" | LETRA DE CRISTAL: dentro del elemento, después del texto, en este orden: `<i class="gx gx-lens gx-rf" aria-hidden="true" style="--gxb:blur(2.041px) brightness(1.256) contrast(1.144) saturate(1.12);-webkit-clip-path:path(evenodd,'M472.4 67.4Q625.4 67.4 722.6 165.5Q819.7 263.6 819.7 423.4Q819.7 581.1 722.1 680.7Q624.4 780.3 472.4 780.3Q320.4 780.3 223.2 680.7Q126.1 581.1 126.1 423.4Q126.1 263.6 223.2 165.5Q320.4 67.4 472.4 67.4ZM472.4 765.9Q580.2 765.9 646.1 674.5Q712 583.1 712 423.4Q712 261.7 646.1 171.8Q580.2 81.8 472.4 81.8Q364.7 81.8 299.2 171.8Q233.8 261.7 233.8 423.4Q233.8 583.1 299.7 674.5Q365.6 765.9 472.4 765.9Z');clip-path:path(evenodd,'M472.4 67.4Q625.4 67.4 722.6 165.5Q819.7 263.6 819.7 423.4Q819.7 581.1 722.1 680.7Q624.4 780.3 472.4 780.3Q320.4 780.3 223.2 680.7Q126.1 581.1 126.1 423.4Q126.1 263.6 223.2 165.5Q320.4 67.4 472.4 67.4ZM472.4 765.9Q580.2 765.9 646.1 674.5Q712 583.1 712 423.4Q712 261.7 646.1 171.8Q580.2 81.8 472.4 81.8Q364.7 81.8 299.2 171.8Q233.8 261.7 233.8 423.4Q233.8 583.1 299.7 674.5Q365.6 765.9 472.4 765.9Z');--gxr:url(#gxre43)"></i>` + `<svg class="gx-defs" aria-hidden="true" width="0" height="0" style="position:absolute;width:0;height:0;overflow:hidden"><filter id="gxre43" x="0" y="0" width="947" height="851" filterUnits="userSpaceOnUse" primitiveUnits="userSpaceOnUse" color-interpolation-filters="sRGB"><feImage href="https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/gx-e43.png" x="0" y="0" width="947" height="851" preserveAspectRatio="none" result="m"/><feDisplacementMap in="SourceGraphic" in2="m" scale="104.99" xChannelSelector="R" yChannelSelector="G"/></filter></svg>` + `<span class="gx gx-fill" aria-hidden="true" style="opacity:0.55;color:rgba(164,164,164,0.12)!important;-webkit-text-fill-color:rgba(164,164,164,0.12)!important"><span>O</span></span>` + `<span class="gx gx-glow" aria-hidden="true" style="--gw:11.545px;--gb:5.773px;--ga:0.336;-webkit-clip-path:path(evenodd,'M472.4 67.4Q625.4 67.4 722.6 165.5Q819.7 263.6 819.7 423.4Q819.7 581.1 722.1 680.7Q624.4 780.3 472.4 780.3Q320.4 780.3 223.2 680.7Q126.1 581.1 126.1 423.4Q126.1 263.6 223.2 165.5Q320.4 67.4 472.4 67.4ZM472.4 765.9Q580.2 765.9 646.1 674.5Q712 583.1 712 423.4Q712 261.7 646.1 171.8Q580.2 81.8 472.4 81.8Q364.7 81.8 299.2 171.8Q233.8 261.7 233.8 423.4Q233.8 583.1 299.7 674.5Q365.6 765.9 472.4 765.9Z');clip-path:path(evenodd,'M472.4 67.4Q625.4 67.4 722.6 165.5Q819.7 263.6 819.7 423.4Q819.7 581.1 722.1 680.7Q624.4 780.3 472.4 780.3Q320.4 780.3 223.2 680.7Q126.1 581.1 126.1 423.4Q126.1 263.6 223.2 165.5Q320.4 67.4 472.4 67.4ZM472.4 765.9Q580.2 765.9 646.1 674.5Q712 583.1 712 423.4Q712 261.7 646.1 171.8Q580.2 81.8 472.4 81.8Q364.7 81.8 299.2 171.8Q233.8 261.7 233.8 423.4Q233.8 583.1 299.7 674.5Q365.6 765.9 472.4 765.9Z')"><span>O</span></span>` + `<span class="gx gx-line" aria-hidden="true"><span>O</span></span>` + `<span class="gx gx-b" aria-hidden="true" style="translate:-2.2px 0"><span>O</span></span>` + `<span class="gx gx-o" aria-hidden="true" style="translate:2.2px 0"><span>O</span></span>` + `<span class="gx gx-rim" aria-hidden="true" style="--gxa:0.76;-webkit-mask-image:linear-gradient(135deg,#000 0%,rgba(0,0,0,.08) 40%,rgba(0,0,0,.08) 60%,#000 100%);mask-image:linear-gradient(135deg,#000 0%,rgba(0,0,0,.08) 40%,rgba(0,0,0,.08) 60%,#000 100%)"><span>O</span></span>`
      ↳ animación: y: 6.386→8.47s 2789.24→1312 (linear); 9.27→11.77s 1312→97.92 (linear); opacidad: salto de 0 a 1 en 2.301s

#### Capa bk15 «image 23» — CAPA A SANGRE (cover), caja de diseño x=0 y=0 2880×1632
- [e44] caja «image 23» transform translate(-562.58,2408) rotate(0rad) tamaño 2880×1632 | opacity:0
    ↳ animación: x: 6.386→8.47s -562.59→-306.22 (linear); 9.27→11.77s -306.22→-562.59 (linear); y: 6.386→8.47s 2408→1645.08 (linear); 9.27→11.77s 1645.08→-283.33 (linear); rotación(rad): 6.386→8.47s 0→0.26 (linear); 9.27→11.77s 0.26→0 (linear); opacidad: salto de 0 a 1 en 2.301s
  - imagen https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/625a207830-0c009b-k-x680d9b.webp: `<img alt="">` absoluta, inset:0, width/height 100%, object-fit:cover (recortada por el contenedor)

#### Capa bk16 «Snowboards power every ride. Design improves control and balance. Shape and flex move together as system.» — CAPA CONTENIDA (contain, ancla x=1, y=1), caja de diseño x=1401.61 y=3720 470×102
- [e45] texto pos(1401.61,3720) tamaño 470×102 | opacity:0; white-space:pre-wrap; text-align:left; font-family:'Kulim Park'; font-weight:300; font-size:24px; line-height:1.4; color:#000000 | TEXTO: "Snowboards power every ride. Design improves control and balance. Shape and flex move together as system."
    ↳ animación: y: 6.386→8.47s 3720→2624 (linear); 9.27→11.77s 2624→728.67 (linear); alto: 6.386→8.47s 102→201 (linear); 9.27→11.77s 201→102 (linear); opacidad: salto de 0 a 1 en 2.301s

#### Capa bk17 «CRAFTED FOR THE ULTIMATE DESCENT.» — CAPA CONTENIDA (contain, ancla x=-1, y=1), caja de diseño x=39.61 y=2901.74 597×372
- [e46] texto pos(39.61,2901.74) tamaño 597×372 | opacity:0; white-space:pre-wrap; text-align:left; text-box:trim-both cap alphabetic; font-family:'Joane'; font-weight:400; font-size:100px; line-height:1; letter-spacing:-0.04em; text-transform:uppercase; color:#000000 | TEXTO: "CRAFTED FOR THE ULTIMATE DESCENT."
    ↳ animación: y: 6.386→8.47s 2901.74→1589 (linear); 9.27→11.77s 1589→210.42 (linear); opacidad: salto de 0 a 1 en 2.301s

#### Capa bk18 «Frame 12» — CAPA CONTENIDA (contain, ancla x=1, y=1), caja de diseño x=1402.2 y=3871.48 227×81
- [e47] caja <a href="#"> «Frame 12» pos(1402.2,3871.48) tamaño 227×81 | opacity:0; border-radius:999px; background-color:#ffffff
    ↳ animación: y: 6.386→8.47s 3871.48→2856 (linear); 9.27→11.77s 2856→880.16 (linear); opacidad: salto de 0 a 1 en 2.301s
  - [e48] texto pos(40,32) tamaño 147×17 | opacity:0; white-space:pre; text-align:left; text-box:trim-both cap alphabetic; font-family:'Kulim Park'; font-weight:400; font-size:24px; line-height:1.4; text-transform:uppercase; color:#000000 | TEXTO: "Get Started"
      ↳ animación: opacidad: salto de 0 a 1 en 2.301s

#### Capa bk19 «Rectangle 11» — CAPA CONTENIDA (contain, ancla x=-1, y=1), caja de diseño x=48.2 y=3452 191×328
- [e49] caja «Rectangle 11» pos(48.2,3452) tamaño 191×328 | opacity:0; border-radius:99px
    ↳ animación: y: 6.386→8.47s 3452→2242 (linear); 9.27→11.77s 2242→701 (linear); opacidad: salto de 0 a 1 en 2.301s
  - imagen https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/9f3a3cc57b.webp: `<img alt="">` absoluta, inset:0, width/height 100%, object-fit:cover (recortada por el contenedor)

#### Capa bk20 «Rectangle 12» — CAPA CONTENIDA (contain, ancla x=-1, y=1), caja de diseño x=247.2 y=3538.24 191×328
- [e50] caja «Rectangle 12» pos(247.2,3538.24) tamaño 191×328 | opacity:0; border-radius:99px
    ↳ animación: y: 6.386→8.47s 3538.24→2562 (linear); 9.27→11.77s 2562→701 (linear); opacidad: salto de 0 a 1 en 2.301s
  - imagen https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/942b055eae.webp: `<img alt="">` absoluta, inset:0, width/height 100%, object-fit:cover (recortada por el contenedor)

#### Capa bk21 «Rectangle 13» — CAPA CONTENIDA (contain, ancla x=-1, y=1), caja de diseño x=446.2 y=3624.48 191×328
- [e51] caja «Rectangle 13» pos(446.2,3624.48) tamaño 191×328 | opacity:0; border-radius:99px
    ↳ animación: y: 6.386→8.47s 3624.48→2856 (linear); 9.27→11.77s 2856→701 (linear); opacidad: salto de 0 a 1 en 2.301s
  - imagen https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/f8f2aa4a48.webp: `<img alt="">` absoluta, inset:0, width/height 100%, object-fit:cover (recortada por el contenedor)

#### Capa bk22 «Group 25» — CAPA A SANGRE (cover), caja de diseño x=0 y=0 1920.59×1596
- [e52] grupo «Group 25» | opacity:0
    ↳ animación: opacidad: salto de 0 a 1 en 2.301s; 9.27→11.77s 1→0 (linear)
  - [e53] caja «Rectangle 14» pos(0,1412.61) tamaño 1920×1053.67 | opacity:0; background-color:#f4f5f7
      ↳ animación: x: 3.301→4.301s 0→-49.62 (linear); y: 3.301→4.301s 1412.61→-127.67 (linear); ancho: 3.301→4.301s 1920→2019.24 (linear); alto: 3.301→4.301s 1053.67→1484.71 (linear); opacidad: salto de 0 a 1 en 2.301s; 9.27→11.77s 1→0 (linear)
  - [e54] grupo «Mask group» | opacity:0
      ↳ animación: opacidad: salto de 0 a 1 en 2.301s; 9.27→11.77s 1→0 (linear)
    - [e59] grupo
        ↳ animación: desplazY: 3.301→4.301s 0→-1540.28 (linear)
      - [-] máscara transform matrix(1,0,0,1,-0.587,870.28) tamaño 1920×865.21 | overflow:hidden; border-radius:0px; mask-image:linear-gradient(180deg, #000000 66.95%, rgba(0,0,0,0) 100%)
        - contenedor de compensación transform matrix(1,0,0,1,0.587,-870.28)
          - [e60] grupo
              ↳ animación: desplazY: 3.301→4.301s 0→1540.28 (linear)
            - [e55] grupo «Group 12» | opacity:0
                ↳ animación: opacidad: salto de 0 a 1 en 2.301s; 9.27→11.77s 1→0 (linear)
              - [e56] caja «image 16» pos(-18.89,1129.69) tamaño 1938.3×544 | opacity:0
                  ↳ animación: y: 3.301→4.301s 1129.69→-514.45 (linear); 4.302→6.386s -514.45→-410.59 (linear); alto: 3.301→4.301s 544→647.86 (linear); 4.302→6.386s 647.86→544 (linear); opacidad: salto de 0 a 1 en 2.301s; 9.27→11.77s 1→0 (linear)
                - capa de relleno (cubre toda la caja, apilada en este orden): inset:-75px
                - imagen https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/bk-e56-35dde9.webp: `<img alt="">` absoluta, inset:0, width/height 100%, max-width:none (estirada sobre su contenedor, que sobresale de la caja y no recorta)
              - [e57] caja «image 17» transform translate(1142.07,1138.69) rotate(0rad) tamaño 997.54×626.84 | opacity:0
                  ↳ animación: x: 3.301→4.301s 1142.08→1115 (linear); 4.302→6.386s 1115→1142.08 (linear); y: 3.301→4.301s 1138.69→-544.49 (linear); 4.302→6.386s -544.49→-401.59 (linear); opacidad: salto de 0 a 1 en 2.301s; 9.27→11.77s 1→0 (linear); escala-contenido: 3.301→4.301s 1→1.18 (linear); 4.302→6.386s 1.18→1 (linear)
                - imagen https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/52f54be031-k-xca7a89.webp: `<img alt="">` absoluta en (0,0) de 1232×928 px, max-width:none, transform-origin 0 0 y transform matrix(0.8359,0,0,0.6754,-32.285,0); el contenedor la recorta (overflow hidden, mismo border-radius)
              - [e58] caja «image 18» pos(-236.22,1199.6) tamaño 1757.64×460.95 | opacity:0
                  ↳ animación: y: 3.301→4.301s 1199.6→-371.95 (linear); 4.302→6.386s -371.95→-340.68 (linear); opacidad: salto de 0 a 1 en 2.301s; 9.27→11.77s 1→0 (linear)
                - imagen https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/20f1181785-b9a0ef-k-xc99560.webp: `<img alt="">` absoluta en (0,0) de 1904×640 px, max-width:none, transform-origin 0 0 y transform matrix(0.9498,0,0,0.7454,0,-8.068); el contenedor la recorta (overflow hidden, mismo border-radius)

#### Capa bk23 «Frame 18» — CAPA CONTENIDA (contain, ancla x=0, y=1), caja de diseño x=-0.2 y=1762.64 1920×639.36
- [e61] caja «Frame 18» pos(-0.2,1762.64) tamaño 1920×639.36 | opacity:0; overflow:hidden; background-color:#cacdd4
    ↳ animación: y: 3.301→4.301s 1762.64→2032.64 (linear); 4.302→6.386s 2032.64→390.39 (linear); 6.386→8.47s 390.39→142.64 (linear); 9.27→11.77s 142.64→-937.36 (linear); opacidad: salto de 0 a 1 en 2.301s
  - [e62] caja «Rectangle 10» pos(0,-560) tamaño 1920×600 | opacity:0; background-color:#000000
      ↳ animación: y: 4.302→6.386s -560→-328.8 (linear); 6.386→8.47s -328.8→20 (linear); alto: 4.302→6.386s 600→477.86 (linear); 6.386→8.47s 477.86→600 (linear); opacidad: salto de 0 a 1 en 2.301s
  - [e63] grupo «Subtract» | mix-blend-mode:lighten; opacity:0
      ↳ animación: opacidad: salto de 0 a 1 en 2.301s
    - [e64] caja pos(0,0) tamaño 1920×639.36 | background-color:rgba(244,245,247,1)
        ↳ animación: y: 3.301→4.301s 0→-131.82 (linear); 4.302→6.386s -131.82→-131.32 (linear); 6.386→8.47s -131.32→-131.82 (linear); alto: 3.301→4.301s 639.36→903 (linear); 4.302→6.386s 903→902 (linear); 6.386→8.47s 902→903 (linear)
    - [e65] texto pos(115.5,185.51) tamaño 1689×372 | opacity:0; white-space:pre-wrap; text-align:center; text-box:trim-both cap alphabetic; font-family:'Joane'; font-weight:400; font-size:100px; line-height:1; letter-spacing:-0.04em; text-transform:uppercase; color:#d1d3d6 | TEXTO: "Feel the power of absolute precision. Tech shaped for extreme results. Where design meets high-speed action."
        ↳ animación: opacidad: salto de 0 a 1 en 2.301s

#### Capa bk24 «Group 21» — CAPA CONTENIDA (contain, ancla x=0, y=1), caja de diseño x=860.44 y=1616.58 199.12×237.42
- [e66] grupo «Group 21» | opacity:0
    ↳ animación: opacidad: salto de 0 a 0.4 en 2.301s
  - [e67] grupo «Group 19» | opacity:0
      ↳ animación: opacidad: salto de 0 a 1 en 2.301s
    - [e68] vector «Glacius Glacius Glacius Glacius» pos(876,1632.14) tamaño 168.01×206.3 | opacity:0
        ↳ animación: y: 3.301→4.301s 1632.14→1362.14 (linear); 4.302→6.386s 1362.14→259.89 (linear); 6.386→8.47s 259.89→-66.5 (linear); 9.27→11.77s -66.5→-1590.22 (linear); opacidad: salto de 0 a 1 en 2.301s
      - texto sobre trayecto circular (texto real y editable, con `fill="currentColor"` para que herede el color animado del elemento). SVG exacto, que ocupa el 100% de la caja (position:absolute; inset:0; width:100%; height:100%; overflow:visible): `<svg preserveAspectRatio="none" width="1122.392" height="549.89" viewBox="0 0 1122.392 549.89"><defs><path id="e26_i1" d="M561.196,549.890A561.196,274.945 0 1 1 561.196,0.000A561.196,274.945 0 1 1 561.196,549.890" fill="none"/><linearGradient id="e26_i0" gradientUnits="userSpaceOnUse" x1="561.196" y1="-66.218" x2="561.196" y2="58.609"><stop offset="0" stop-color="#e2f0ff" stop-opacity="1"/><stop offset="1" stop-color="#e2f0ff" stop-opacity="0.2"/></linearGradient></defs><text fill="url(#e26_i0)" style="font-family:'Joane',system-ui,sans-serif;font-weight:600;font-size:40px;letter-spacing:46.4px;text-transform:uppercase;"><textPath href="#e26_i1" startOffset="50%" text-anchor="middle">Call for the</textPath></text></svg>`
    - [e69] caja «image 21» pos(929.52,1704.81) tamaño 60.95×60.95 | opacity:0
        ↳ animación: y: 3.301→4.301s 1704.81→1434.81 (linear); 4.302→6.386s 1434.81→332.56 (linear); 6.386→8.47s 332.56→6.17 (linear); 9.27→11.77s 6.17→-1517.55 (linear); opacidad: salto de 0 a 1 en 2.301s
      - imagen https://cdn.jsdelivr.net/gh/danielsnows/F4M-Bluebird@main/snows-heroes-frost-neon-fish-boat/frost/assets/img/5ca960a21e-efef81-x9cd1dc.webp: `<img alt="">` absoluta en (0,0) de 2048×2048 px, max-width:none, transform-origin 0 0 y transform matrix(0.0623,0,0,0.0623,-33.396,-33.396); el contenedor la recorta (overflow hidden, mismo border-radius)
  - [e70] caja «Ellipse 4» pos(884.77,1664.11) tamaño 7.63×7.63 | opacity:0; border-radius:50%; background-color:#3b4c68
      ↳ animación: y: 3.301→4.301s 1664.11→1394.11 (linear); 4.302→6.386s 1394.11→291.86 (linear); 6.386→8.47s 291.86→-34.53 (linear); 9.27→11.77s -34.53→-1558.25 (linear); opacidad: salto de 0 a 0.2 en 2.301s
  - [e71] caja «Ellipse 6» pos(1024.11,1657.44) tamaño 7.63×7.63 | opacity:0; border-radius:50%; background-color:#3b4c68
      ↳ animación: y: 3.301→4.301s 1657.44→1387.44 (linear); 4.302→6.386s 1387.44→285.19 (linear); 6.386→8.47s 285.19→-41.21 (linear); 9.27→11.77s -41.21→-1564.93 (linear); opacidad: salto de 0 a 0.2 en 2.301s
  - [e72] caja «Ellipse 7» pos(1024.11,1804.23) tamaño 7.63×7.63 | opacity:0; border-radius:50%; background-color:#3b4c68
      ↳ animación: y: 3.301→4.301s 1804.23→1534.23 (linear); 4.302→6.386s 1534.23→431.98 (linear); 6.386→8.47s 431.98→105.59 (linear); 9.27→11.77s 105.59→-1418.13 (linear); opacidad: salto de 0 a 0.2 en 2.301s
  - [e73] caja «Ellipse 8» pos(892.4,1804.23) tamaño 7.63×7.63 | opacity:0; border-radius:50%; background-color:#3b4c68
      ↳ animación: y: 3.301→4.301s 1804.23→1534.23 (linear); 4.302→6.386s 1534.23→431.98 (linear); 6.386→8.47s 431.98→105.59 (linear); 9.27→11.77s 105.59→-1418.13 (linear); opacidad: salto de 0 a 0.2 en 2.301s

## 10. Ajustes finales
### Efectos de puntero (solo con puntero fino `(hover: hover) and (pointer: fine)` y sin reduced-motion)
- Posición del puntero normalizada respecto al escenario: nx = clamp((x/W − 0.5)·2, −1, 1), ny = clamp((y/H − 0.5)·2, −1, 1) (0 hasta el primer `pointermove` y mientras el puntero esté fuera de la página: `pointerleave` en `document.documentElement`). Suavizado por frame: ns += (n − ns) × 0.06.
- Seguimiento inverso del cursor por elemento: cada elemento listado [fx, fy] se desplaza (ns_x·fx·w, ns_y·fy·w) px del diseño (valores negativos: se mueve en sentido contrario al puntero), sumado en su propiedad CSS `translate` al desplazamiento ligado al tiempo (si lo tiene): [e2] «Background» [-12, -8], [e7] «Object» [-18, -10], [e8] «Ellipse 3» [-20, -12], [e3] «F» [-26, -16], [e4] «R» [-26, -16], [e5] «S» [-26, -16], [e6] «T» [-26, -16], [e16] «O» [-34, -20], [e26] «Call for the» [-22, -14], [e17] «Group 15» [-30, -18], [e56] «image 16» [-8, -16], [e57] «image 17» [-10, -18], [e58] «image 18» [-8, -14], [e66] «Group 21» [-24, -14], [e61] «Frame 18» [0, -10], [e44] «image 23» [-12, -8], [e41] «Group 18» [-40, -24], [e46] «CRAFTED FOR THE ULTIMATE DESCENT.» [-20, -12], [e49] «Rectangle 11» [-26, -16], [e50] «Rectangle 12» [-28, -17], [e51] «Rectangle 13» [-30, -18], [e45] «Snowboards power every ride. Design impr» [-16, -10], [e47] «Frame 12» [-16, -10]. Peso w = 1. Desactivado en móvil.
- Desplazamiento vertical ligado al tiempo de [e7] «Object»: propiedad CSS `translate: 0px Ypx` (se suma a su posición animada), con Y en px del diseño (se escalan con su capa). Fotogramas (t, valor): (2.3 s, 0) → (3.301 s, -90) [lineal] → (4.301 s, -140) [lineal]; entre dos fotogramas se interpola con la curva indicada en el segundo; antes del primero y después del último se mantiene el valor extremo. Se aplica en escritorio y en móvil; si varias entradas afectan al mismo elemento, sus valores se suman.
- Desplazamiento vertical ligado al tiempo de [e2] «Background»: propiedad CSS `translate: 0px Ypx` (se suma a su posición animada), con Y en px del diseño (se escalan con su capa). Fotogramas (t, valor): (2.3 s, 0) → (3.301 s, -30) [lineal] → (4.301 s, -50) [lineal]; entre dos fotogramas se interpola con la curva indicada en el segundo; antes del primero y después del último se mantiene el valor extremo. Se aplica en escritorio y en móvil; si varias entradas afectan al mismo elemento, sus valores se suman.
- Desplazamiento vertical ligado al tiempo de [e42] «image 22»: propiedad CSS `translate: 0px Ypx` (se suma a su posición animada), con Y en px del diseño (se escalan con su capa). Fotogramas (t, valor): (9.27 s, 280) → (11.77 s, 120) [lineal] → (12.57 s, 0) [cubic-bezier(0.25, 0.1, 0.25, 1)]; entre dos fotogramas se interpola con la curva indicada en el segundo; antes del primero y después del último se mantiene el valor extremo. Se aplica en escritorio y en móvil; si varias entradas afectan al mismo elemento, sus valores se suman.
- Desplazamiento vertical ligado al tiempo de [e43] «O»: propiedad CSS `translate: 0px Ypx` (se suma a su posición animada), con Y en px del diseño (se escalan con su capa). Fotogramas (t, valor): (9.27 s, 140) → (11.77 s, 60) [lineal] → (12.57 s, 0) [cubic-bezier(0.25, 0.1, 0.25, 1)]; entre dos fotogramas se interpola con la curva indicada en el segundo; antes del primero y después del último se mantiene el valor extremo. Se aplica en escritorio y en móvil; si varias entradas afectan al mismo elemento, sus valores se suman.
- Desplazamiento vertical ligado al tiempo de [e44] «image 23»: propiedad CSS `translate: 0px Ypx` (se suma a su posición animada), con Y en px del diseño (se escalan con su capa). Fotogramas (t, valor): (9.27 s, -80) → (11.77 s, -40) [lineal] → (12.57 s, 0) [cubic-bezier(0.25, 0.1, 0.25, 1)]; entre dos fotogramas se interpola con la curva indicada en el segundo; antes del primero y después del último se mantiene el valor extremo. Se aplica en escritorio y en móvil; si varias entradas afectan al mismo elemento, sus valores se suman.
- Rendimiento: cada elemento con animación de posición, rotación, escala u opacidad (o con desplazamiento ligado al tiempo) lleva `will-change: transform` para que se componga en su propia capa, salvo los que contienen un descendiente con `mix-blend-mode` (la capa aislaría la mezcla).
- Letras de cristal con refracción: el filtro SVG de cada lente (`<svg class="gx-defs">`, sección 9) solo se aplica como backdrop-filter en navegadores Chromium: añade la clase `gx-rf` a `<html>` si `navigator.userAgentData` existe; en los demás navegadores la lente usa solo `--gxb`.
- CSS literal de las letras de cristal (capas `.gx` descritas en la sección 9):
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
### Estados hover (solo si `matchMedia("(hover: hover) and (pointer: fine)")` se cumple; en táctil no hay hover)
- Enlaces de menú y de texto (cambio sutil de color): [e32] «Home», [e33] «About us», [e34] «Services», [e35] «contact», [e37] «Login». Al entrar el puntero o recibir el foco, para el enlace y cada uno de sus descendientes se lee su color calculado c (si su alfa es 0, p. ej. texto con degradado, se omite) y se guarda como variable `--hvc` = rgb(c + (#8fb5e2 − c) × 0.6) por canal (alfa 1); se añade la clase `hv-tr` y, en el siguiente frame, `is-hv`. Al salir (o perder el foco) se quita `is-hv` y 420 ms después `hv-tr`.
- Botones (cambio sutil de fondo): [e18] «Explore» → capa en su caja hija con superficie visible de mayor tamaño en pantalla con fondo rgba(255,255,255,.18); [e38] «Get Started» → capa en el propio enlace con fondo rgba(255,255,255,.18); [e47] «Get Started» → capa en el propio enlace con fondo rgba(143,181,226,.22). En cada uno se inserta `<i class="hvo" aria-hidden="true">` dentro de la caja indicada, justo después de sus capas de relleno/efecto (`i.pl`, `i.bb`, `i.gl`, `i.is`) y antes de su contenido, con ese color de fondo; al entrar el puntero o recibir el foco el enlace añade `is-hv` a esa capa y lo quita al salir o perder el foco.
- Enlaces de icono o logo sin superficie propia (bajan a 0.72 de opacidad al pasar el ratón): [e22] «Snowboard model 1», [e23] «Snowboard model 2», [e24] «Snowboard model 3», [e25] «Snowboard model 4», [e28] «Glacius». Llevan la clase `hv-ic`.
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
- CSS adicional, literal (los selectores usan los identificadores bkN de las capas y eN de los elementos de la sección 9; `html.is-m` = vista móvil):
```css
.blk[data-k="Group 22"]{z-index:5}
#e53::before{content:"";position:absolute;left:0;right:0;bottom:100%;height:320px;pointer-events:none;background:linear-gradient(180deg,rgba(244,245,247,0) 0%,rgba(244,245,247,.55) 55%,#f4f5f7 100%)}
html:not(.is-m) .blk[data-k="Object"]{scale:1.025;transform-origin:960px 0px}
```

## 11. Vista móvil
- Se activa cuando el ancho del escenario < 768 px o la proporción ancho/alto < 0.82 (añade la clase `is-m` a `<html>`). Diseño móvil de referencia: 390×844 px.
- sm = min(W/390, H/844); ox = (W − 390·sm)/2; oy = (H − 844·sm)/2.
- Capa contenida listada con {x, y, z, ax, ay}: su escala es sm·z (z = 1 si no se indica) y la esquina superior izquierda de su «caja de diseño» se coloca en pantalla en (ox·(1+ax) + x·sm, oy·(1+ay) + y·sm) (ax, ay = 0 si no se indican); el resto de su contenido conserva su posición relativa a esa caja.
- Capa a sangre: z = max(W/1920, H/1080)·(z indicado o 1); pantalla = ((W − 1920·z)·fx + X·z, (H − 1080·z)·fy + Y·z), con fx, fy = 0.5 salvo que se indique otro valor.
- Las capas contenidas que no aparecen en la lista se ocultan en móvil.
- Ajustes por elemento: «ocultar» = `display:none`; «desplazar dx, dy» = mover el elemento esa distancia en px de su contenedor (si además hay «escala ×z», se escala desde su esquina superior izquierda); «multiplicar sus desplazamientos animados por [kx, ky]» = multiplicar el valor total de desplazX por kx y el de desplazY por ky (el «desplazar dx, dy» de móvil no se multiplica); «desvanecer entre t1 y t2» = multiplicar su opacidad por clamp((t2 − t)/(t2 − t1), 0, 1) a partir de t1; «colocar el punto (rx, ry) del elemento en (mx, my) del diseño móvil a escala e×sm» = dibujar el elemento a escala Se = e·sm con su esquina superior izquierda (x, y animados) en (ox + mx·sm + (x − rx)·Se, oy + my·sm + (y − ry)·Se); «ancho» = width fijo en px; «transición … entre t1 y t2» = pasar de la colocación normal de su capa (la de móvil) a esa colocación con interpolación smoothstep (u²·(3 − 2u)) de la escala y del origen entre esos tiempos.
- En móvil el track usa 40 vh por segundo (ver sección 7) y se desactivan los efectos de puntero de la sección 10.
Colocación de capas en móvil:
- capa «F»: x=16.25, y=515.31, z=0.23
- capa «R»: x=72.33, y=430.71, z=0.23
- capa «S»: x=259.39, y=430.71, z=0.23
- capa «T»: x=314.14, y=515.31, z=0.23
- capa «O»: x=128.03, y=395.98, z=0.23
- capa «Call for the»: x=63.11, y=277.89, z=0.23
- capa «Object»: x=-246.6, y=595.08, z=0.46
- capa «Mask group»: x=-246.6, y=644.87, z=0.46
- capa «Ellipse 3»: x=-127.64, y=743.72, z=0.46
- capa «Group 15»: x=36.38, y=829.8, z=0.52
- capa «Group 22»: x=16, y=-150.75, z=0.75
- capa «Frame 18»: x=-1.8, y=689.1, z=0.2
- capa «Group 21»: x=145.22, y=986.12, z=0.5
- capa «CRAFTED FOR THE ULTIMATE DESCENT.»: x=20, y=1572.23, z=0.55
- capa «Group 18»: x=41.64, y=1096.31, z=0.31
- capa «Snowboards power every ride. Design improves control and balance. Shape and flex move together as system.»: x=20, y=2494.8, z=0.6
- capa «Frame 12»: x=20, y=2568.8, z=0.6
Las capas no listadas que no son a sangre se ocultan en móvil.
Ajustes por elemento en móvil:
- [e31] «Group 23»: ocultar
- [e37] «Login»: desplazar dx=-1323, dy=0 (unidades del diseño de su capa)
- [e38] «Frame 12»: desplazar dx=-1344, dy=0 (unidades del diseño de su capa)
- [e61] «Frame 18»: desvanecer entre t=9.3s y 10.1s

## 12. Semántica y accesibilidad
- `<main class="hero" aria-label="Glacius — Call for the Frost">`; el loader lleva `aria-hidden="true"`.
- Listas con `<ul>` y `<li>`; los menús, dentro de `<nav aria-label="…">`. Cada `<a href="#">` usa el `aria-label` indicado cuando su contenido no es texto. Si un enlace cubre una caja completa, va dentro de ella como `<a class="lk">` absoluto que la cubre (`inset:0`).
- Imágenes decorativas con `alt=""`; SVG decorativos con `aria-hidden="true"`.
- Solo los textos y los enlaces reciben eventos de puntero (CSS de la sección 3) y los elementos invisibles llevan `visibility:hidden`, así nunca se puede hacer clic en algo que no se ve.
- Foco visible: `outline: 2px solid currentColor; outline-offset: 3px`.

## 13. Recursos (URLs absolutas)
Todos los recursos se sirven desde un CDN con CORS habilitado; usa estas URLs tal cual:
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

## 14. Anexo: formas vectoriales
Cada SVG ocupa el 100% de la caja de su elemento (`position:absolute; inset:0; width:100%; height:100%; overflow:visible`, `preserveAspectRatio="none"`, `aria-hidden="true"`). Si un elemento indica «con relleno #xxxxxx (en lugar de #yyyyyy)», usa el mismo SVG cambiando ese color de relleno. Si un SVG con `id` internos (máscaras, degradados) se usa más de una vez, da ids únicos a cada copia y actualiza sus referencias `url(#…)`.

No hay formas vectoriales.

## 15. Criterios de aceptación
- A 1920×1080, 1280×720 y 390×844 la composición es idéntica en los momentos clave: fin de la intro, cada pausa y estado final.
- Las posiciones, tamaños, colores, tipografías, tiempos y curvas coinciden con esta especificación.
- Todos los textos son HTML real y editable; todos los enlaces son `<a href="#">` clicables con foco visible; lo invisible no se puede pulsar.
- Las imágenes, el video y las tipografías se cargan desde las URLs de la sección 13 y no hay errores en la consola.
- El diseño se adapta al cambiar el tamaño de la ventana y en móvil sigue la sección 11.
- Con `prefers-reduced-motion` se muestra directamente el estado final.
