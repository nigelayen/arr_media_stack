# Docker Media Stack
A self hosted media automation and streaming platform powered by Docker Compose. This stack provides a complete media management ecosystem including:
- Media requests and approvals
- Automated movie and TV management
- Usenet download automation
- Subtitle management
- Plex media streaming
- Media transcoding and optimisation
- Metadata overlays and collections
- Monitoring and analytics

# Architecture Overview
```
                 ┌───────────────┐
                 │     Seerr     │
                 │ Requests UI   │
                 └──────┬────────┘
                        │
                        ▼
         ┌────────────────────────────┐
         │     Sonarr / Radarr        │
         │ TV & Movie Automation      │
         └───────────┬────────────────┘
                     │
                     ▼
            ┌────────────────┐
            │    Prowlarr    │
            │ Index Manager  │
            └────────┬───────┘
                     │
                     ▼
              ┌────────────┐
              │  SABnzbd   │
              │ Downloader │
              └─────┬──────┘
                    │
                    ▼
          ┌───────────────────┐
          │  Media Libraries  │
          │ Movies / TV       │
          └───────┬───────────┘
                  │
     ┌────────────┼─────────────┐
     ▼            ▼             ▼
┌────────┐  ┌──────────┐  ┌─────────┐
│ Plex   │  │ Bazarr   │  │ Tdarr   │
│ Stream │  │ Subtitles│  │ Transcode
└────────┘  └──────────┘  └─────────┘
                  │
                  ▼
            ┌──────────┐
            │ Kometa   │
            │ Metadata │
            └──────────┘
```

# Included Services

| Service | Purpose |
|---|---|
| Seerr | Media request management for Plex users |
| Sonarr | TV show management and automation |
| Radarr | Movie management and automation |
| Prowlarr | Indexer management for Usenet |
| SABnzbd | Usenet download client |
| Bazarr | Subtitle management |
| Plex | Media streaming server |
| Tdarr | Automated media transcoding and optimisation |
| Tdarr Node | Additional transcoding worker |
| Kometa | Plex metadata, overlays, and collections |
| UMTK | Upcoming media integration for Kometa |
| Tautulli | Plex monitoring and analytics |
| Profilarr | Automated quality profile management |

# Features

## Fully Automated Media Workflow
This stack supports a fully automated workflow:

1. Users request media with Seerr
2. Sonarr/Radarr processes the request
3. Prowlarr manages the indexers
4. SABnzbd downloads content
5. Media is imported into the library
6. Bazarr manages subtitles
7. Tdarr optimizes media files
8. Kometa adds Plex metadata and overlays
9. Plex serves content to clients

# Hardware Acceleration
This stack is optimised for Intel Quick Sync Video (QSV).

## Required Device Mapping
```
devices:
  - /dev/dri:/dev/dri
```

## Intel VAAPI Environment Variable

```
LIBVA_DRIVER_NAME=iHD
```

Services using hardware acceleration:
- Plex
- Tdarr
- Tdarr Node

# Storage Layout
Recommended storage structure:

```
/opt/docker/
├── plex
├── sonarr
├── radarr
├── tdarr
├── bazarr
└── ...

/mnt/media/
├── Movies
├── TV
└── umtk/

/mnt/ssd/
├── downloads
├── transcode_cache
└── plexms
```

---

# Environment Variables
Create a `.env` file in the root of the project.

Example:
```
TZ=America/Toronto
PUID=1000
PGID=1000

CONFIG_PATH=/opt/docker
MEDIA_PATH=/mnt/media
SSD_PATH=/mnt/ssd

PLEX_CLAIM=claim-xxxxxxxxxxxx

GOTIFY_URL=https://gotify.example.com
GOTIFY_TOKEN=your_token_here
```

# Networking
All services communicate using the shared Docker bridge network:
```
networks:
  media-network:
    driver: bridge
```

Plex uses `host` networking for DLNA and local discovery support.

# Resource Optimisation
This stack includes several optimisations for performance and stability.

## OOM Protection
Critical services use custom OOM scores:
```
oom_score_adj: -100
```

Non critical services are deprioritised:
```
oom_score_adj: 500
```

## Memory Limits
Heavy transcoding services include memory reservations and limits.

Example:
```
deploy:
  resources:
    limits:
      memory: 6G
    reservations:
      memory: 2G
```

## SSD Optimisation
High IO workloads are directed to SSD storage:
- SABnzbd downloads
- Tdarr transcoding cache
- Plex metadata

This reduces RAID array fragmentation and improves responsiveness.

# Deployment

## Clone Repository
```
git clone https://github.com/yourusername/media-stack.git
cd media-stack
```

## Create Environment File
It is recommended that you create a .env file in the root folder of you repo folder.

## Start Stack
```
docker compose up -d
```

## View Logs
```
docker compose logs -f
```

## Update Containers
```
docker compose pull
docker compose up -d
```
# Recommended Host Configuration
- Intel iGPU enabled in BIOS
- Docker Engine
- Docker Compose Plugin

## Docker Storage Driver
```
overlay2
```

## Filesystem Recommendations

Recommended mount options:
```
noatime
```

SSD cache strongly recommended for:
- Downloads
- Plex metadata
- Tdarr temporary files

# Security Recommendations
Recommended improvements for production deployments:

- Reverse proxy with Nginx Proxy Manager
- HTTPS using Let's Encrypt
- Cloudflare Tunnel or VPN access
- Disable unused external ports
- Enable authentication on Tdarr
- Restrict Plex remote access where possible

# Monitoring Recommendations
Recommended companion tools:
- Uptime Kuma
- Glances
- Grafana
- Prometheus
- cAdvisor

# Future Enhancements
Potential additions:
- Jellyfin fallback server
- Immich photo management
- n8n automation workflows

# Disclaimer
This project is intended for educational and personal media management purposes only. Users are responsible for complying with local laws and service terms.

# Credits
Projects used in this stack:

- Plex
- LinuxServer.io
- Tdarr
- Kometa
- Seerr
- Sonarr
- Radarr
- Prowlarr
- SABnzbd
- Bazarr
- Tautulli

Please support the original developers of these excellent open source projects.
