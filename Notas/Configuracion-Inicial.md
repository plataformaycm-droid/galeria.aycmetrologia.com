# Configuración inicial — galeria.aycmetrologia.com

VPS Hostinger `2.25.160.230`, el mismo de clientes.aycmetrologia.com, con el mismo esquema:

| | clientes | galeria |
|---|---|---|
| Carpeta en el VPS | `/opt/aycm-portal` | `/opt/aycm-galeria` |
| Contenedor | Flask + MySQL | nginx (archivos estáticos) |
| Puerto interno | `127.0.0.1:8080` | `127.0.0.1:8081` |
| Despliegue | manual (`git pull` + `docker compose`) | automático con GitHub Actions en cada `git push` |

El proxy web del VPS (el que hoy recibe `https://clientes.aycmetrologia.com` y lo envía a `8080`)
también debe enviar `galeria.aycmetrologia.com` a `8081` y emitir su certificado SSL.

## 1. GoDaddy — DNS

Dominio `aycmetrologia.com` > DNS > Agregar registro:

| Tipo | Nombre | Valor | TTL |
|---|---|---|---|
| A | `galeria` | `2.25.160.230` | 1 hora |

Verificar (de minutos a unas horas): `nslookup galeria.aycmetrologia.com` debe responder `2.25.160.230`.

## 2. Proxy web del VPS (hecho el 29-oct-2026)

El VPS usa **nginx en el host + certbot** (igual que clientes):

- Vhost: `/etc/nginx/sites-available/galeria.aycmetrologia.com` (enlazado en `sites-enabled/`), hace `proxy_pass` a `127.0.0.1:8081`.
- SSL: Let's Encrypt emitido con `certbot --nginx -d galeria.aycmetrologia.com --redirect`; HTTP redirige a HTTPS.
  La renovación es automática con `certbot.timer`.
- Contenedor `aycm-galeria-web-1` levantado en `/opt/aycm-galeria`.

Si se cambia el vhost: `nginx -t && systemctl reload nginx`.

## 3. Llave SSH exclusiva para el despliegue (en tu PC)

```bash
ssh-keygen -t ed25519 -f ~/.ssh/galeria_deploy -N "" -C "github-actions-galeria"
```

Autorizarla en el VPS (agrega la llave pública sin tocar las existentes):

```bash
cat ~/.ssh/galeria_deploy.pub | ssh -i ~/.ssh/aycm_vps_hostinger root@2.25.160.230 "cat >> ~/.ssh/authorized_keys"
```

La llave privada (`~/.ssh/galeria_deploy`) va **solo** en GitHub Secrets: nunca en chats ni en el repo.

## 4. GitHub — Secrets

Repo `plataformaycm-droid/galeria.aycmetrologia.com` > Settings > Secrets and variables > Actions > New repository secret:

| Secret | Valor |
|---|---|
| `VPS_HOST` | `2.25.160.230` |
| `VPS_USER` | `root` |
| `VPS_SSH_KEY` | contenido completo de `~/.ssh/galeria_deploy` |
| `DEPLOY_PATH` | `/opt/aycm-galeria` |

## 5. Primer despliegue

```bash
git add -A
git commit -m "Estructura inicial del sitio y despliegue automático"
git push
```

GitHub > Actions: el workflow debe quedar en verde y terminar con `HTTP 200`. Luego abrir https://galeria.aycmetrologia.com.

## Subir videos

Por SFTP (WinSCP/FileZilla) con `root@2.25.160.230` y la llave `aycm_vps_hostinger`, a
`/opt/aycm-galeria/public/media/videos/`. Quedan disponibles al instante, sin commit:
`https://galeria.aycmetrologia.com/media/videos/<archivo>.mp4`.

## Vigilar espacio

Al 29-oct-2026 el VPS tenía ~42 GB libres de 48 GB. Revisar con:

```bash
df -h /
du -sh /opt/aycm-galeria/public/media
```
