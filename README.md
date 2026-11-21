# Imagen a QR

Página de una sola pieza (`index.html`) para subir una imagen y obtener un código QR.
Al escanear el QR **solo se ve la imagen**, a pantalla completa y lista para guardar.

## Publicarla

1. `Settings` → `Pages` → Source: `Deploy from a branch`, rama `main`, carpeta `/ (root)`.
2. Queda en `https://ksambiental4-oss.github.io/qr/`.
3. Esa misma dirección va en el campo *Dirección pública de esta página*, porque es la
   que abrirá el QR al escanearlo.

## Los dos modos

| | Sin servidor | Calidad completa |
|---|---|---|
| Dónde vive la imagen | **dentro del propio QR** | en ImgBB |
| Requiere cuenta | no | API key gratis de [api.imgbb.com](https://api.imgbb.com/) |
| Resolución | reducida (ver tabla) | la original |
| Caduca | nunca | según ImgBB |
| QR resultante | denso | limpio, versión 4 |

### Sin servidor
La imagen se reduce y se comprime (WebP, o JPEG si el navegador no lo soporta) hasta
caber en los **2 953 bytes** que admite un QR, y viaja codificada en el propio enlace
(`…/qr/#i=…`). No se sube nada a ningún sitio: el QR *es* la imagen.

El selector de equilibrio decide cuánto cabe. Medido con la librería `qrcode`:

| Nivel | Datos de imagen | Versión QR | Módulos |
|---|---|---|---|
| Fácil de escanear | ~712 B | 22 | 105 × 105 |
| Equilibrado | ~1 125 B | 29 | 133 × 133 |
| Máxima nitidez | ~2 175 B | 40 | 177 × 177 |

A más nitidez, más módulos y más difícil para la cámara: el nivel máximo conviene
mostrarlo en grande o impreso. El resultado muestra siempre los píxeles y los módulos
reales obtenidos.

### Calidad completa
La imagen se sube a ImgBB con tu API key (guardada solo en tu navegador, en
`localStorage`) y el QR contiene la URL directa del archivo. QR limpio y calidad original.

## Notas

- Admite arrastrar y soltar, pegar con `Ctrl+V` y la cámara del celular.
- Los HEIC de iPhone solo funcionan si el navegador puede decodificarlos; si falla,
  conviértelos a JPG.
- El QR se puede descargar como PNG.
- Única dependencia externa: [`qrcode`](https://github.com/soldair/node-qrcode) por CDN.

## Pruebas hechas

Con Node y la librería `jsqr` como escáner: una imagen real se codifica en el enlace, se
genera el QR, se rasteriza y se decodifica. La imagen recuperada es **byte a byte
idéntica** a la original en los tres niveles.
