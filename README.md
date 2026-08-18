# Immich

Selbst gehostete Foto-/Video-Backup-Lösung, deployt über Coolify.

## Enthaltene Services

- `immich-server` – Web-UI, API, Backup-Endpoint für die Mobile App
- `immich-machine-learning` – Gesichtserkennung, Smart Search (läuft rein auf CPU)
- `database` – Postgres mit Vektor-Extension (für Smart Search)
- `redis` (valkey) – Job-Queue / Caching

## Quellen

- Offizielle Doku: https://docs.immich.app/install/docker-compose
- Environment Variables Referenz: https://docs.immich.app/install/environment-variables
