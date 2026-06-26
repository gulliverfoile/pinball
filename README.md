# 🎱 Pinball Hexagonal - Juego de Pinball en el Navegador

Juego de pinball clásico implementado en **JavaScript** con arquitectura hexagonal (puertos y adaptadores), renderizado 2D en canvas y motor de física **Matter.js**. Funciona completamente en el navegador, sin dependencias de backend.

---

## ¿Qué es?

Un pinball interactivo donde controlas dos flippers para mantener la bola en juego, golpear bumpers y evitar los drenajes laterales. El objetivo es conseguir la máxima puntuación antes de perder las tres vidas.

Incluye:
- Física realista (gravedad, colisiones, rebotes) gracias a Matter.js.
- Flippers accionados por teclado con respuesta inmediata.
- Bumpers que otorgan puntos y activan luces especiales (bonus, multiball).
- Sensores de drenaje que restan vidas y reinician la bola.
- Interfaz visual con indicadores de puntuación, vidas, luces y modo.
- Arquitectura modular y extensible (fácil de migrar o añadir componentes).

---

## ¿Cómo funciona?

### Arquitectura hexagonal

El código se organiza en **puertos** (interfaces abstractas) y **adaptadores** (implementaciones concretas) siguiendo el patrón de puertos y adaptadores (hexagonal). Esto permite cambiar cualquier pieza externa (motor de física, renderizador, entrada) sin tocar la lógica central del juego.
┌──────────────────────────────┐
│ DOMINIO │
│ (GameDomain: puntuación, │
│ vidas, luces, reglas) │
└─────────────┬────────────────┘
│
┌─────────────┼────────────────┐
│ PUERTOS │ │
│ IPhysics │ IRenderer │
│ IInput │ IConfig │
└─────┬───────┴───────┬────────┘
│ │
┌─────────▼──────┐ ┌─────▼──────────┐
│ Adaptadores │ │ Adaptadores │
│ - Matter.js │ │ - Canvas 2D │
│ - Teclado │ │ - JSON config │
└────────────────┘ └────────────────┘

text

- **Dominio**: gestiona el estado del juego (puntos, vidas, luces) y contiene las reglas de negocio puras (sin efectos secundarios).
- **Puertos**: definen contratos para física, renderizado, entrada y carga de configuración.
- **Adaptadores**: implementan esos contratos usando librerías concretas (Matter.js, Canvas API, eventos de teclado, objeto JSON).
- **EventBus**: sistema de eventos desacoplado que comunica los adaptadores con el dominio.

Cuando ocurre un suceso (colisión, tecla presionada), el adaptador correspondiente emite un evento al bus. El dominio lo procesa y actualiza su estado, luego el renderizador pinta el estado actual.

---

## Cómo ejecutarlo

### Requisitos
- Navegador moderno con soporte para módulos ES (Chrome, Firefox, Edge, Safari).
- **Servidor local** (obligatorio por seguridad de módulos). No funciona abriendo el archivo `.html` directamente desde el sistema de archivos.

### Pasos

1. Guarda el código en un archivo, por ejemplo `pinball.html`.
2. Abre una terminal en la carpeta que contiene el archivo.
3. Ejecuta un servidor local. Puedes usar cualquiera de estos comandos:
   ```bash
   # Con Node.js (si tienes npx)
   npx serve .

   # Con Python 3
   python -m http.server 8000

   # Con PHP
   php -S localhost:8000
Abre el navegador y visita http://localhost:8000/pinball.html (o el puerto que hayas elegido).

¡A jugar! Usa las teclas indicadas en pantalla.

Controles
Tecla(s)	Acción
← / A	Activar flipper izquierdo
→ / D	Activar flipper derecho
Espacio	Lanzar bola
Los flippers se activan mientras mantengas la tecla; al soltar vuelven a su posición de reposo.

Estructura del código
Clases principales
EventBus: emisor/receptor de eventos personalizado.

GameDomain: estado del juego (puntos, vidas, luces) y lógica para procesar eventos (bumper_hit, drain, etc.).

MatterPhysicsAdapter: implementa IPhysics usando Matter.js. Crea paredes, flippers, bumpers, sensores y la bola. Actualiza la simulación cada frame.

CanvasRendererAdapter: implementa IRenderer dibujando en un <canvas> todos los elementos del juego.

KeyboardInputAdapter: implementa IInput capturando eventos de teclado y emitiéndolos al bus.

JsonConfigAdapter: implementa IConfig devolviendo una configuración de mesa (bumpers, drenajes, etc.) desde un objeto JSON.

PinballGame: orquestador que inicializa todos los componentes, suscribe eventos y arranca el bucle de juego (requestAnimationFrame).

Flujo de juego
Al iniciar, se carga la configuración de la mesa.

Se crea el motor físico, los cuerpos (paredes, flippers, bumpers, bola) y los sensores.

El bucle de animación (60 fps) ejecuta en cada frame:

physics.update(delta) → avanza la simulación.

physics.sync(domain) → obtiene posiciones reales de la bola y ángulos de flippers.

renderer.draw(domain, physicsState) → pinta el estado en el canvas.

Los eventos de colisión (Matter.js) y de teclado se transforman en eventos del bus.

El dominio reacciona modificando puntuación, vidas y luces.

Si la bola cae en un drenaje, el dominio resta una vida y, tras un breve retardo, se reinicia la bola.

Posibles mejoras (para migrar a arquitectura hexagonal completa)
La base ya es hexagonal, pero se puede extender:

Física: implementar otro adaptador (por ejemplo, Planck.js) sin cambiar el dominio.

Renderizado: migrar a WebGL o a una librería como PixiJS.

Configuración dinámica: cargar mesas desde un archivo JSON externo o desde una API.

Sonido: añadir un puerto ISound con adaptador Web Audio API.

Multijugador: añadir un puerto de red.

Persistencia: guardar puntuaciones altas en localStorage o en un servidor.

Créditos
Desarrollado con fines educativos para demostrar el patrón Arquitectura Hexagonal en un proyecto de juego web.

Motor de física: Matter.js
Inspirado en los clásicos pinball de los 80/90.

¡Diviértete mejorándolo!
