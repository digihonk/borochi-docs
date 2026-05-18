# Bridge-Server bereitstellen

Die Borochi-Bridge ist ein **Python-Service (FastAPI + SQLite)** in einem
Docker-Container. Sie braucht **wenig Ressourcen** und läuft auf praktisch
jedem Linux-Server.

## Mindestanforderungen

| | Empfehlung |
|---|---|
| CPU | 1 Kern x86_64 oder ARM |
| RAM | 512 MB |
| Storage | 10 GB (DB wächst ~30 MB/Monat bei 5-Sek-Polling) |
| OS | Debian / Ubuntu / RaspberryPi-OS |
| Internet | öffentlich erreichbar (oder via Cloudflare Tunnel) |

## Optionen

=== "🏆 Empfohlen: Hetzner Cloud"
    - **CX11** für 4,15 € / Monat
    - 1 vCPU, 2 GB RAM, 20 GB SSD
    - In Falkenstein oder Nürnberg — DSGVO-konform, deutsche Server
    - [hetzner.com/cloud](https://hetzner.com/cloud)

    Reicht für Borochi locker.

=== "Eigener Raspberry Pi"
    - Raspi 4 mit 2 GB RAM oder besser
    - SD-Karte 32 GB (besser: USB-SSD für mehr DB-Lebensdauer)
    - Per Cloudflare Tunnel ins Internet, falls du dyn-IP hast

    Funktioniert, aber: dein WAN muss stabil sein, weil das auch das ist,
    was du außer Haus erreichst.

=== "Synology NAS (alle 3 Komponenten)"
    - DSM 7 mit Container Manager
    - Bridge + Agent + Reverse Proxy alles auf einer Maschine
    - Vorteil: ein Gerät weniger, läuft eh
    - Nachteil: bei NAS-Reboot ist alles weg, kein redundantes Backend

=== "VPS deiner Wahl"
    Funktioniert mit jedem Anbieter — Netcup, OVH, DigitalOcean, Vultr.
    Achtung: USA-Hoster heißt USA-Datenstrom = ggf. unschön für DSGVO.

## Was du brauchst

- **SSH-Zugriff** zum Server
- **Sudo-Rechte** (zum Docker-Installieren)
- **Eine Domain oder Subdomain** (z.B. `borochi.dein-name.de`) — du kannst auch erstmal mit IP starten und später umziehen
- **Port 443 (HTTPS)** vom Internet erreichbar (Firewall offen)

## Vorbereitung: Docker installieren

Auf Debian/Ubuntu:

```bash
# Docker via offiziellem Script
curl -fsSL https://get.docker.com | sh

# User in docker-Gruppe (damit du nicht sudo brauchst)
sudo usermod -aG docker $USER
# kurz aus- und neu einloggen oder: newgrp docker

# Check
docker --version
docker compose version
```

<!-- TODO: Screenshot vom erfolgreichen Docker-Check -->

## Wo legen wir die Dateien an?

Empfohlene Struktur auf dem Server:

```
/opt/borochi/
├── bridge/              # docker-compose.yml + .env
├── data/                # SQLite-DB landet hier (persistent)
└── frontend/            # ggf. statische Frontend-Files
```

```bash
sudo mkdir -p /opt/borochi/{bridge,data,frontend}
sudo chown -R $USER:$USER /opt/borochi
```

→ **Weiter: [Bridge installieren](02-bridge-docker.md)** — wir holen das Docker-Image und starten.
