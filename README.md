# Imagen → QR

Página de una sola pieza (`index.html`) para subir una imagen y obtener un código QR.
Al escanear el QR **solo se ve la imagen**, a pantalla completa y lista para descargar.

## Cómo publicarla

1. Sube este repositorio a GitHub.
2. `Settings` → `Pages` → Source: `Deploy from a branch`, rama `main`, carpeta `/ (root)`.
3. Queda en `https://<usuario>.github.io/qr/`.

## Los dos modos

| | Sin servidor | Calidad completa |
|---|---|---|
| Dónde vive la imagen | **dentro del propio QR** | en ImgBB |
| Requiere cuenta | no | API key gratis de [api.imgbb.com](https://api.imgbb.com/) |
| Resolución | ~200–700 px, comprimida | la original |
| Caduca | nunca | según ImgBB |

### Sin servidor
La imagen se reduce y se comprime (WebP, o JPEG si el navegador no lo soporta) hasta
caber en los **2 953 bytes** que admite un QR, y viaja codificada en el propio enlace
(`…/qr/#i=…`). No se sube nada a ningún sitio: el QR es la imagen. A cambio la
resolución es baja y el QR queda denso, así que conviene imprimirlo o mostrarlo grande.

En este modo el enlace apunta a la dirección pública de la página, que se escribe una
vez en el campo correspondiente y se recuerda en el navegador.

### Calidad completa
La imagen se sube a ImgBB con tu API key (guardada solo en tu navegador, en
`localStorage`) y el QR contiene la URL directa del archivo. El QR queda limpio y la
imagen conserva su calidad original.

## Notas

- Admite arrastrar y soltar, pegar con `Ctrl+V` y la cámara del celular.
- Los HEIC de iPhone solo funcionan si el navegador puede decodificarlos; si falla,
  conviértelos a JPG.
- El QR se puede descargar como PNG.
- Única dependencia externa: [`qrcode`](https://github.com/soldair/node-qrcode) por CDN.
