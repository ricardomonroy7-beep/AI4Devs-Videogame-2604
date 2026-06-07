# CUBIDASH_RMA — Prompts Log (Módulo 6 · AI4Devs)

---

## 1. Prompt Original

*Sesión realizada en Claude Opus 4.8 con niño de 11 años como co-diseñador.*

> **Objetivo final:**
> Crear un videojuego rápido, para subir a la actividad.
>
> **Primera tarea:**
> Realiza una investigación sobre librerías y arquitecturas para crear una copia del videojuego "Geometry Dash".
>
> **Contexto:**
> Este chat va a realizar la investigación y arquitectura, a través de iteraciones de la conversación, para generar un prompt Drastic final para Claude Code en sesión online.

---

## 2. Iteraciones de Conversación

### Iteración 1 — Stack y arquitectura
> Vamos a elegir la arquitectura y el stack que permita realizarlo en Claude Code móvil y correr el juego en un escritorio Chrome en una tablet Android. Analiza e investiga las posibles complicaciones, el objetivo es tener algo reproducible en una hora (como experimento). Después de analizar esta situación, procederemos con el criterio visual.

**Decisión tomada:** Canvas 2D vanilla, archivo único HTML, cero dependencias, gráficos procedurales, audio WebAudio.

---

### Iteración 2 — Simplificación del entregable
> ¿Es posible realizar algo tipo "un HTML reproducible desde el navegador"? El objetivo de este ejercicio no es ser técnicos, es construir un juego sencillo para un niño en este momento.

**Decisión tomada:** Un único `index.html` autocontenido que funcione con `file://` y `http://`, sin build tools ni npm.

---

### Iteración 3 — Feedback del usuario (niño de 11 años) + ticket de trabajo
> **Feedback del usuario:** niño de 11 años jugando Geometry Dash.
> Que tenga temas oscuros como decoración, que tenga plataformas, que tenga una dificultad accesible, una nave que tenga que saltar para volar, 5 niveles: fácil, medio, difícil, insano, demon.
>
> **Tarea:** Primero transforma la user story vaga del usuario en un ticket de trabajo. Posteriormente crea un plan de acción a partir de lo generado y ejecuta los cambios en el código base. (Asegúrate de guardar el script para posterior consulta.)

**Ticket resultante (resumen):**
- Cubo auto-corre; tap = saltar. Física perdonadora con coyote-time + input buffer.
- Plataformas: aterrizar encima = seguro; chocar de lado = muerte.
- Modo nave por portales: mantener = subir, soltar = caer; techo/piso = seguro, obstáculos = muerte.
- Estética oscura neón, paleta distinta por nivel, gráficos procedurales.
- 5 niveles: Fácil (trivial) → Demon (difícil pero superable).
- SFX + loop simple en WebAudio; botón de silencio.

---

### Iteración 4 — Prompt Handoff
> Dame un prompt drastic handoff para Claude Code móvil. Voy a darle tu prompt y las versiones de cubierta que me has mandado.

*(Ver Sección 3.)*

---

## 3. Prompt Handoff a Claude Code

*Enviado a Claude Code móvil con sesión en nube a través de GitHub.*

```
IDENTITY
Eres un ingeniero senior de videojuegos HTML5 que entrega juegos pequeños,
confiables y autocontenidos, y que SIEMPRE verifica su trabajo ejecutándolo
en un navegador real antes de declararlo terminado.

CONTEXT
- Entregable: clon sencillo de Geometry Dash en UN solo archivo index.html.
  Sin build, sin dependencias, sin peticiones de red (debe correr por file://
  y por http). Si usas fuente web, debe degradar a fuente del sistema.
- Runtime objetivo REAL: Chrome en una tablet Android (modo escritorio).
  NO confíes en ningún visor/preview de artifacts de chat: en pruebas ese visor
  no pinta ni el DOM inyectado ni el canvas. La fuente de verdad es Chrome real.
- Te adjunto versiones previas. La más reciente (cubi.html) ya tiene UI 100% en
  canvas, 5 niveles, plataformas, modo nave y temas oscuros; úsala como base.
  Su lógica está validada con `node --check` y jsdom (sin errores): el problema
  es de VISUALIZACIÓN en el visor, no de lógica.
- Decisiones cerradas (no reabrir): Canvas 2D vanilla, archivo único, cero
  dependencias, gráficos procedurales, audio WebAudio.

DIRECTION
Toma cubi.html como base, EJECÚTALO en un navegador real (tu preview embebido o
dev server con viewport tipo tablet), reproduce el fallo de "no se ve",
diagnostícalo y corrígelo hasta que el menú con las 5 tarjetas se vea, sea
tocable y un nivel arranque. Si la base no es recuperable, reconstruye desde
cero respetando los requisitos. Verifica VISUALMENTE, no solo por revisión de
código.

REQUISITOS DE JUEGO (playtest, niño de 11 años)
- Cubo auto-corre; tocar = saltar. Física perdonadora (coyote-time + buffer).
- Plataformas: aterrizar encima; chocar de lado = muerte. En Fácil, bajas y
  bien espaciadas.
- Modo nave por portales: MANTENER tocado = subir, soltar = caer; clamp dentro
  de la banda (tocar techo/piso no mata); los obstáculos sí matan.
- Picos; barra de progreso %; reinicio instantáneo; pantalla de victoria.
- 5 niveles: Fácil, Medio, Difícil, Insano, Demon (velocidad y densidad
  crecientes). Dificultad ACCESIBLE: Fácil trivial; Demon difícil pero
  superable, nunca imposible.
- Estética oscura neón, paleta distinta por nivel, gráficos procedurales (sin
  imágenes), SFX + loop simple en WebAudio, botón de silencio.

HARDENING OBLIGATORIO (tablet)
- Entrada táctil con pointer events; nada que dependa del teclado.
- touch-action:none + viewport user-scalable=no + preventDefault (tocar no debe
  hacer scroll/zoom/pull-to-refresh).
- Audio se desbloquea en el primer gesto (resume del AudioContext).
- Game loop con TIMESTEP FIJO (determinismo entre refrescos 60/90/120 Hz).
- Canvas consciente de devicePixelRatio (sin borrosidad); manejar resize y
  cambio de orientación.
- Cero peticiones externas.

AUDIENCE
Jugador de 11 años fan de Geometry Dash; y el desarrollador, que recibe y sube
el entregable. Explica hallazgos de forma breve y clara.

RESULTS (criterios de aceptación, TODOS verificables en navegador real)
1. Un único index.html autocontenido que abre y se juega en Chrome.
2. En el menú se ven el título y las 5 tarjetas de colores, y arrancan al tocar.
3. Cubo salta y aterriza en plataformas; nave vuela al mantener. Ambos jugables.
4. Los 5 niveles cargan y son completables (Fácil trivial, Demon duro pero
   posible).
5. ~60 fps en tablet; sin scroll/zoom accidental; audio tras el primer toque;
   sin errores en consola.
6. Entrega: (a) commit del repo + GitHub Pages con la URL https lista para abrir
   en la tablet, y (b) index.html descargable.
   NO declares "listo" sin haberlo abierto en un navegador y confirmado los
   puntos 2–5, describiendo qué viste (o con captura).

STRUCTURE (cómo trabajar)
1) Plan corto. 2) Correr la base en preview real y reproducir el bug.
3) Arreglar y medir. 4) Verificación visual explícita de los criterios 2–5.
5) Publicar en GitHub Pages + dejar index.html descargable.
6) Reporte final: qué causaba el "no se ve" y cómo lo confirmaste.

TONE
Directo, pragmático, orientado a entregar. Sin sobre-ingeniería: que se vea y se
juegue pesa más que el pulido. Una sola pregunta solo si algo bloquea de verdad.
```

---

## 4. Prompt DRASTIC Final (One-Shot)

*Prompt capaz de generar el juego completo desde cero en un solo disparo.*

```
IDENTITY
You are a senior HTML5 game engineer. You deliver small, reliable, self-contained
games and ALWAYS verify your work by running it in a real browser before declaring
it done.

GOAL
Create a single index.html file: a Geometry Dash clone called "CUBI DASH".
No build tools, no npm, no external requests. Must run from file:// and http://.
Target runtime: Chrome on an Android tablet (desktop mode).

GAME REQUIREMENTS
- Auto-running cube; tap/click = jump. Forgiving physics: coyote-time (0.10s) +
  input buffer (0.12s).
- Platforms: land on top = safe; hit from side = death.
- Ship mode triggered by portals: hold = rise, release = fall; ceiling/floor = safe,
  obstacles = death.
- Spikes, gates, progress bar %, instant restart, victory screen.
- 5 levels — Easy / Medium / Hard / Insane / Demon — increasing speed and density.
  Easy must be trivial; Demon hard but always beatable.
- Dark neon aesthetic, distinct color palette per level, procedural graphics (no
  images), WebAudio SFX + simple music loop, mute button.

TABLET HARDENING (ALL mandatory)
- Pointer events for touch; nothing keyboard-only.
- touch-action:none + viewport user-scalable=no + preventDefault (no scroll/zoom).
- AudioContext unlocked on first gesture.
- Fixed timestep game loop (deterministic across 60/90/120 Hz).
- Canvas respects devicePixelRatio (no blur); handles resize and orientation change.
- Zero external requests.

ACCEPTANCE CRITERIA (verify in a real browser before declaring done)
1. Single self-contained index.html opens and plays in Chrome.
2. Menu shows title + 5 colored level cards; tapping a card starts the level.
3. Cube jumps and lands on platforms; ship flies when held. Both feel playable.
4. All 5 levels load and are completable (Easy trivial, Demon hard but possible).
5. ~60 fps on tablet; no accidental scroll/zoom; audio after first tap; no console errors.

DELIVERABLE
Return only the complete index.html content, ready to save and open.
```
