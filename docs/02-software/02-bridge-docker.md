# Bridge installieren

Wir starten die Borochi-Bridge als Docker-Container.

## Quick-Start

```bash
cd /opt/borochi/bridge

# docker-compose.yml runterladen
curl -O https://raw.githubusercontent.com/digihonk/Home-Bridge/main/docker-compose.example.yml
mv docker-compose.example.yml docker-compose.yml

# .env-Datei mit Secrets erstellen
cat > .env <<'EOF'
# Wird genutzt um Sessions zu signieren — generiere dir was Eigenes
BOROCHI_JWT_SECRET=hier_64_zufaellige_zeichen_einfuegen

# Wird genutzt um HA-Tokens zu verschlüsseln
BOROCHI_MASTER_SECRET=hier_64_andere_zufaellige_zeichen

# Welche Origin(s) dürfen das Frontend laden?
BOROCHI_ALLOWED_ORIGINS=https://home.dein-name.de

# Optional: Token für den Agent (bei Pairing kannst du auch leer lassen)
BOROCHI_AGENT_SHARED_TOKEN=
EOF

# Secrets generieren (Ubuntu/Debian/macOS):
openssl rand -hex 32  # für JWT_SECRET
openssl rand -hex 32  # für MASTER_SECRET
```

!!! danger "Secrets nicht teilen"
    Die zwei `BOROCHI_*_SECRET`-Werte sind **dein Familien-Schlüssel**.
    Wer die hat, kann Sessions fälschen und HA-Tokens lesen.
    `.env` niemals ins Git committen, nie in Chat-Apps posten,
    Datei-Rechte `chmod 600 .env`.

## docker-compose.yml — das Beispiel

```yaml
services:
  borochi-bridge:
    image: ghcr.io/digihonk/borochi-bridge:latest
    container_name: borochi-bridge
    restart: unless-stopped
    ports:
      - "127.0.0.1:8080:8080"   # nur lokal, Nginx davorhängen
    volumes:
      - /opt/borochi/data:/var/lib/borochi-bridge
    env_file:
      - .env
    user: "1500:1500"
    read_only: true
    tmpfs:
      - /tmp:size=64M
    security_opt:
      - no-new-privileges:true
    cap_drop:
      - ALL
```

<!-- TODO: docker-compose.example.yml in bridge-repo aktuell halten, hier auf dasselbe Format -->

## Erste Initialisierung der DB

Vor dem ersten Start brauchen wir das Volume mit den richtigen Permissions:

```bash
sudo chown -R 1500:1500 /opt/borochi/data
```

(UID 1500 ist der `bridge`-User innerhalb des Containers — wir laufen ja
nicht als root.)

## Starten

```bash
cd /opt/borochi/bridge
docker compose up -d

# Logs anschauen
docker compose logs -f
```

Wenn alles klappt, siehst du:

```
borochi-bridge  | Uvicorn running on http://0.0.0.0:8080
borochi-bridge  | INFO: Application startup complete.
```

<!-- TODO: Screenshot vom erfolgreichen Bridge-Start -->

## Schnell-Test

```bash
curl http://127.0.0.1:8080/api/health
# → {"status":"ok","version":"2.25"}
```

## Backup-Strategie

Die SQLite-DB liegt unter `/opt/borochi/data/bridge.db`. Backups so:

```bash
# Tägliches Backup per Cron
sudo crontab -e

# Eintrag hinzufügen:
0 3 * * * sqlite3 /opt/borochi/data/bridge.db ".backup /opt/borochi/backups/bridge-$(date +\%F).db"
```

Oder du nutzt **restic / borg / Synology Hyper Backup** je nach Setup.

→ **Weiter: [Agent auf NAS](03-agent-nas.md)** — der Teil, der die Wechselrichter pollt.
