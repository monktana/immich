# Immich auf Hetzner (via Coolify)

Selbst gehostete Foto-/Video-Backup-Lösung, deployt über Coolify auf dem
`stuff`-Server (CPX32), analog zum Paperless-Setup.

## Enthaltene Services

- `immich-server` – Web-UI, API, Backup-Endpoint für die Mobile App
- `immich-machine-learning` – Gesichtserkennung, Smart Search (läuft rein auf CPU)
- `database` – Postgres mit Vektor-Extension (für Smart Search)
- `redis` (valkey) – Job-Queue / Caching

## Vorbereitung: Separates Volume für Fotos anlegen

Der `stuff`-Server hat nur 160 GB System-Disk, geteilt mit Paperless und der
Sudoku-App. **Bevor du Immich startest**, leg dir in Hetzner Cloud ein
zusätzliches **Volume** an und häng es auf dem Server ein, z.B. unter
`/mnt/immich-data`. Das hält deine Fotobibliothek vom Root-Volume fern und
lässt sich später unabhängig vergrößern.

```bash
# Beispiel, nachdem das Volume in der Hetzner Console angelegt & attached wurde
sudo mkfs.ext4 /dev/disk/by-id/scsi-0HC_Volume_<ID>
sudo mkdir -p /mnt/immich-data
sudo mount /dev/disk/by-id/scsi-0HC_Volume_<ID> /mnt/immich-data
# und in /etc/fstab eintragen, damit es nach Reboot automatisch mountet
```

## Deployment über Coolify

1. Neue Resource in Coolify: **Docker Compose** → dieses Repo als Quelle angeben.
2. `.env.example` nach `.env` kopieren, Werte anpassen (Passwort, Pfade).
3. Domain für den `immich-server` Service in Coolify hinterlegen (Port `2283`,
   Coolify erzeugt automatisch die Traefik-Labels + Let's Encrypt-Zertifikat –
   genau wie bei Paperless).
4. Deploy anstoßen. Beim ersten Start lädt Immich die ML-Modelle herunter,
   das kann ein paar Minuten dauern.
5. Unter der konfigurierten Domain aufrufen, Admin-Account anlegen.

## Wichtige Hinweise

- **RAM:** Auf dem CPX32 (8 GB) laufen Paperless + Immich zusammen eng, aber
  in der Regel machbar für privaten Gebrauch. Beim ersten großen Import
  (Gesichtserkennung/Smart Search über die ganze Bibliothek) den RAM-Verbrauch
  im Coolify-Dashboard oder mit `docker stats` im Auge behalten.
- **Video-Transcoding** läuft ohne GPU über CPU – bei großen 4K-Video-Mengen
  entsprechend langsam. Für den Anfang unkritisch.
- **Updates:** `IMMICH_VERSION` ist bewusst auf eine feste Version statt
  `release` zu setzen, sobald der erste Import durch ist – Immich hat
  gelegentlich Breaking Changes / DB-Migrationen. Release Notes vorher lesen:
  https://github.com/immich-app/immich/releases
- **Mobile App:** Nach dem Setup in der Immich-App (iOS/Android) die Server-
  URL (deine konfigurierte Domain) eintragen und automatisches Backup
  aktivieren.

## Quellen

- Offizielle Doku: https://docs.immich.app/install/docker-compose
- Environment Variables Referenz: https://docs.immich.app/install/environment-variables
