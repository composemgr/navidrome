## 👋 Welcome to navidrome 🚀

Modern music server and streamer compatible with Subsonic/Airsonic

## 📋 Description

Navidrome is a music server and streamer that speaks the Subsonic API, so
existing Subsonic or Airsonic clients work unmodified. This stack runs
Navidrome with a read-only mount of your music library and a writable data
directory for the database, config, and cover art cache, behind the host
nginx proxy.

## 🚀 Services

- **app**: deluan/navidrome:latest

## 📦 Installation

### Option 1: Quick Install
```bash
curl -q -LSsf "https://raw.githubusercontent.com/composemgr/navidrome/main/docker-compose.yaml" -o compose.yml
```

### Option 2: Git Clone
```bash
git clone "https://github.com/composemgr/navidrome" ~/.local/srv/docker/navidrome
cd ~/.local/srv/docker/navidrome
cp default.env .env
docker compose --env-file .env up -d
```

### Option 3: Using composemgr
```bash
composemgr install navidrome
```

## 🔧 Configuration

### Environment Variables

```shell
TZ=America/New_York
NAVIDROME_LOG_LEVEL=info
NAVIDROME_SCAN_SCHEDULE=1h
NAVIDROME_SCANNER_WATCHER_WAIT=300s
NAVIDROME_SESSION_TIMEOUT=24h
NAVIDROME_DEFAULT_THEME=Dark
NAVIDROME_IMAGE_CACHE_SIZE=100MB
NAVIDROME_BACKUP_PATH=/data/backups
NAVIDROME_BACKUP_COUNT=1
```

See `docker-compose.yaml` for complete list of configurable options.

### Environment Files

`default.env.sample` holds the full set of variables and is copied to `.env`
before starting the stack:

```bash
cp default.env .env
docker compose --env-file .env up -d
```

`app.env.sample` holds the app-specific overrides and is copied to `app.env`:

```bash
cp app.env.sample app.env
```

`composemgr up` applies `app.env` and `default.env` automatically. Raw
`docker compose` commands need `--env-file .env` to pick up the same values.

### Reverse Proxy

Three standalone compose files ship in this repo:

| File | Use |
|------|-----|
| `docker-compose.yaml` | Default, published on `172.17.0.1:64265` |
| `docker-compose.traefik.yaml` | Behind an existing external `traefik` network |
| `docker-compose.tunnel.yaml` | Behind an existing external `cloudflare` tunnel network |

```bash
docker compose -f docker-compose.traefik.yaml --env-file .env up -d
```

Only the `app` service carries the proxy labels and joins the external
network, proxying the web UI on port `4533`. Navidrome serves both the web UI
and the Subsonic API on that one port, so streaming clients work through the
same host.

## 🌐 Access

- **Web Interface**: http://172.17.0.1:64265
- **Subsonic API**: http://172.17.0.1:64265/rest

The first visit asks you to create the admin account, there is no preseeded
user.

## 📂 Volumes

- `./volumes/config/navidrome` - Navidrome database, config, and cover art
  cache
- `./volumes/data/media/music` - Music library, mounted read-only

## 🔐 Security

- Create the admin account on the first visit, Navidrome has no default user
- Sharing is off by default, turn it on only if you need public links
- The music library is mounted read-only, Navidrome cannot modify your files
- Use HTTPS via the reverse proxy in production, stream URLs and the session
  cookie are sent in clear over plain HTTP
- The Subsonic API accepts credentials on every request, do not expose it to
  untrusted networks without the proxy in front
- Regularly update Docker images for security patches
- Backup `./volumes/config/navidrome` regularly, it holds the user database
  and cover art cache

## 🔍 Logging

```shell
docker compose --env-file .env logs -f app
```

## 🛠️ Management

```bash
# Start services
docker compose --env-file .env up -d

# Stop services
docker compose --env-file .env down

# Update to latest images
docker compose --env-file .env pull && docker compose --env-file .env up -d

# View logs
docker compose --env-file .env logs -f

# Restart services
docker compose --env-file .env restart
```

## 📋 Requirements

- Docker Engine 20.10+
- Docker Compose V2+
- A music library in `./volumes/data/media/music`

## 🤝 Author

🤖 casjay: [Github](https://github.com/casjay) 🤖  
🦄 composemgr: [Github](https://github.com/composemgr) 🦄
