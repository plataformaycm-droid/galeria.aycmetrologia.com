# media/

Carpeta para imágenes y videos publicados en https://galeria.aycmetrologia.com/media/...

- Imágenes livianas (logos, fotos optimizadas, < 5 MB) van en git dentro de `media/img/`.
- Videos y archivos pesados **no van en git** (GitHub rechaza archivos > 100 MB y el repo se vuelve lento).
  Súbelos directo al servidor por SFTP (WinSCP/FileZilla) a `/opt/aycm-galeria/public/media/videos/`.
  El despliegue automático nunca borra lo que esté en `media/videos/`.

Ejemplo de URL final: `https://galeria.aycmetrologia.com/media/img/logo-acm.png`
