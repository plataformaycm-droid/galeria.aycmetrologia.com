# galeria.aycmetrologia.com
Este repositorio es el código fuente de la página subdominio: galeria.aycmetrologia.com y a su vez el repositorio de contenido audiovisual

## Estructura
- `public/` → todo lo que se publica en https://galeria.aycmetrologia.com.
- `public/media/img/` → imágenes livianas versionadas en git.
- `public/media/videos/` → videos, **fuera de git**; se suben por SFTP al servidor.
- `docker-compose.yml` + `nginx/` → contenedor nginx que sirve `public/` en `127.0.0.1:8081` del VPS.

## Despliegue automático
Cada `git push` a `main` ejecuta [.github/workflows/deploy.yml](.github/workflows/deploy.yml),
que sincroniza el repo con `/opt/aycm-galeria` en el VPS de Hostinger y recarga nginx.
Ver [Notas/Configuracion-Inicial.md](Notas/Configuracion-Inicial.md).

```bash
git add -A
git commit -m "Describe el cambio"
git push
```
