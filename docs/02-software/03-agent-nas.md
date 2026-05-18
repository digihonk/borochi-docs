# Agent auf NAS

Der **Borochi-Agent** ist der Teil, der **im selben LAN wie deine Waveshares**
laufen muss. Er pollt alle paar Sekunden die Wechselrichter via Modbus-TCP
und schickt die Werte über einen verschlüsselten WebSocket an die Bridge.

## Wo lasse ich den Agent laufen?

=== "🏆 Empfohlen: Synology NAS (Container Manager)"
    Wenn du eine DSM-7-NAS hast: Container Manager öffnen, Image ziehen,
    fertig. Kein eigener Pi nötig.

=== "Raspberry Pi"
    Pi 3 reicht. Docker drauf, dasselbe Compose-File.

=== "Linux-VM auf dem Hauptserver"
    Wenn du Proxmox/ESXi hast und eine kleine VM ins LAN-VLAN bringen kannst.

=== "PC, der eh läuft"
    Wenn schon ein Always-On-PC im Netz ist (NUC, alter Desktop), geht das.

!!! warning "Was nicht funktioniert"
    Der Agent **muss im LAN sein**, damit er die Waveshares (`.180`, `.181`)
    erreicht. Er kann **nicht** auf dem Hetzner-VPS laufen — der wäre außen.

## Synology Container Manager — Setup

### Schritt 1: Image ziehen

In DSM:
1. **Container Manager** → Registry
2. Suche `ghcr.io/digihonk/borochi-agent`
3. Tag `latest` → Download

<!-- TODO: Screenshot Container Manager Image-Suche -->

### Schritt 2: Projekt anlegen

In DSM:
1. **Container Manager** → Projekt → **Erstellen**
2. **Name:** `borochi-agent`
3. **Pfad:** `/volume1/docker/borochi-agent`
4. **Quelle:** "compose.yaml erstellen"
5. Folgenden Compose-Inhalt einfügen:

```yaml
services:
  borochi-agent:
    image: ghcr.io/digihonk/borochi-agent:latest
    container_name: borochi-agent
    restart: unless-stopped
    network_mode: bridge
    environment:
      # URL der Bridge — anpassen
      BOROCHI_BRIDGE_URL: "wss://home.dein-name.de/ws/agent"

      # Der Token, den du beim Pairing in der Bridge bekommst
      # (siehe Konfiguration → Pairing-Ceremony)
      BOROCHI_AGENT_TOKEN: "${AGENT_TOKEN}"

      # Welche Waveshares pollen?
      BOROCHI_WAVESHARE_HOSTS: "192.168.178.180,192.168.178.181"

      # Poll-Intervall in Sekunden
      BOROCHI_POLL_INTERVAL: "5"

      # Log-Level
      BOROCHI_LOG_LEVEL: "INFO"
    volumes:
      - /volume1/docker/borochi-agent/data:/var/lib/borochi-agent
    user: "1501:1501"
    read_only: true
    tmpfs:
      - /tmp:size=32M
    security_opt:
      - no-new-privileges:true
    cap_drop:
      - ALL
```

<!-- TODO: Screenshot Compose-Editor mit eingefügtem Content -->

### Schritt 3: Token besorgen

Wir brauchen den `AGENT_TOKEN` aus der Bridge. Den bekommst du beim **First-Run-Wizard**
(siehe nächstes Kapitel) oder über die **Konfiguration → Agents** im Dashboard,
wenn du die Bridge schon eingerichtet hast.

1. Auf der Bridge: Settings → Agents → **+ Neuer Agent**
2. Du bekommst einen **Pairing-Code** (z.B. `K7-D9X-22`)
3. Den im Agent eingeben (oder per Env-Variable, siehe oben)

Detailliert beschrieben in [Bedienung → Multi-User](../04-bedienung/04-multi-user.md).

### Schritt 4: Starten

Im Container Manager: Projekt **borochi-agent** auswählen → **Erstellen**.

Logs öffnen → wenn alles klappt, siehst du:

```
[INFO] borochi-agent v2.25 starting
[INFO] Connecting to bridge wss://home.dein-name.de/ws/agent ...
[INFO] Authenticated as agent-id=42
[INFO] Polling 192.168.178.180 (WR1) ...
[INFO] WR1: pv1=1247W, pv2=890W, bat_soc=78%, grid_in=−420W
```

<!-- TODO: Screenshot vom Agent-Log mit erfolgreichem Polling -->

## Verifikation

In deinem Dashboard solltest du jetzt auf **System-Page → Agents** sehen:

| Agent | Status | Letzte Antwort |
|---|---|---|
| `nas-borochi` | 🟢 online | vor 2s |

→ **Weiter: [Nginx + HTTPS](04-nginx-config.md)** — Reverse-Proxy einrichten für saubere URL
