# Imagen a QR

Página de una sola pieza (`index.html`): subes una imagen, el código QR se genera
solo, lo guardas, y al escanearlo desde **cualquier dispositivo** se abre a pantalla
completa **solo la imagen**, lista para descargar.

En línea: <https://ksambiental4-oss.github.io/qr/>

## Cómo funciona

El modo por defecto (**sin servidor**) no guarda la imagen en ninguna parte: la reduce y
la comprime en WebP hasta caber en los **2 953 bytes** que admite un código QR, y la
mete codificada dentro del propio enlace (`…/qr/?i=…`). El QR *es* la imagen. Por eso el
código funciona en cualquier celular, con wifi o datos móviles, sin cuenta, y no caduca
nunca mientras la página siga publicada.

### Por qué los datos van en `?i=` y no en `#i=`
La cámara de Android convierte el `#` del enlace en `%23` al abrirlo, con lo que el
servidor deja de reconocer la ruta y responde **404**. Comprobado contra GitHub Pages:

| Enlace | Respuesta |
|---|---|
| `/qr/#i=…` (2 943 ch) | 200 OK |
| `/qr/%23i=…` (lo que manda Android) | **404** |
| `/qr/?i=…` (2 943 ch) | 200 OK |

Por eso los enlaces se generan con `?i=`. El visor sigue aceptando `#i=` para que los
códigos impresos antes no dejen de funcionar, y un `404.html` rescata los enlaces que
lleguen rotos con `%23` y redirige al formato bueno.

El modo **calidad completa** sube la imagen a ImgBB con una API key gratuita y pone en
el QR la URL directa del archivo: calidad original y un QR mucho más limpio.

## Niveles

A más datos dentro del QR, más módulos, y más difícil se lo pones a la cámara.
Resultados medidos con una foto de 1600 × 1200:

| Nivel | Datos | Resolución obtenida | Módulos |
|---|---|---|---|
| Fácil de escanear | ~825 B | 120 × 90 px | 113 × 113 |
| Equilibrado | ~1 275 B | 270 × 203 px | 133 × 133 |
| Máxima nitidez | ~2 175 B | 480 × 360 px | 169 × 169 |

Con una imagen de muchísimo detalle las resoluciones bajan y el nivel más pequeño puede
no alcanzar; la página lo avisa y sugiere otro nivel.

### Por qué se comprime así
Midiendo la curva real de WebP en Chrome: a igual peso, **más píxeles con poca calidad
se ve mejor que pocos píxeles con mucha** (a 1 125 B caben 200 × 150 con calidad 0.1, o
solo 96 × 72 con calidad 0.5). Por eso la búsqueda maximiza primero el tamaño —descarta
cada medida con una sola prueba al mínimo— y después sube la calidad mientras siga
cabiendo. WebP además no baja de unos 700 B, así que el nivel más pequeño no puede ser
más ajustado de lo que es.

## Publicarla

`Settings` → `Pages` → Source: `Deploy from a branch`, rama `main`, carpeta `/ (root)`.
La página detecta sola su propia dirección, así que no hay nada que configurar.

## Notas

- Arrastrar y soltar, pegar con `Ctrl+V` y cámara del celular.
- Los HEIC de iPhone solo funcionan si el navegador puede decodificarlos.
- Si el navegador no soporta codificar WebP, cae a JPEG automáticamente.
- El QR se descarga en PNG de ~1 450 px, nítido para imprimir.
- Única dependencia externa:
  [`qrcode-generator`](https://github.com/kazuhikoarase/qrcode-generator) por CDN.

## Pruebas

Automatizadas con Chrome (puppeteer) y `jsqr` como escáner real:

- La app carga sin pantalla negra, con la librería disponible.
- Al subir la imagen el QR se genera solo, sin pulsar nada (~600 ms).
- Los píxeles del canvas se decodifican con `jsqr` y devuelven un **WebP válido**, en los
  tres niveles y con dos imágenes distintas.
- Al abrir el enlace se ve el visor con la imagen cargada, la app oculta y el botón de
  descarga visible.

- Los tres caminos posibles del enlace llegan a mostrar la imagen: el nuevo `?i=`, el
  antiguo `#i=` y el roto por Android `%23` (rescatado por `404.html`).

### Tres fallos que encontraron estas pruebas

1. **Pantalla negra.** `#visor{display:flex}` es un selector de id y le gana al
   `[hidden]{display:none}` del navegador, así que el visor negro tapaba la app siempre.
   Corregido con un `[hidden]{display:none!important}` propio.
2. **El QR nunca se generaba.** `qrcode@1.5.4` no publica la carpeta `build/`, así que el
   `<script>` del CDN daba 404 (`ERR_BLOCKED_BY_ORB`) y `QRCode` quedaba `undefined`.
   Sustituida por `qrcode-generator`, dibujando el canvas a mano.
3. **404 al escanear desde otro dispositivo.** El `#` del enlace llegaba como `%23`.
   Resuelto con `?i=`, compatibilidad con `#i=` y el rescate de `404.html`.
