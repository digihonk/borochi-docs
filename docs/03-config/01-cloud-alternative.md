# Borochi-Cloud (Alternative)

<!-- TODO: Cloud-Angebot konkretisieren wenn die Cloud-Variante live ist -->

Falls du dir den **Bridge-Server-Teil sparen** möchtest, planen wir eine
**Borochi-Cloud**, bei der wir den Bridge-Server für dich betreiben.

## Was Borochi-Cloud erledigt

- Bridge-Service inkl. Updates, Backups, HTTPS
- Frontend immer auf neuester Version
- Hetzner-Hosting in Deutschland (DSGVO-konform)
- Verschlüsselte Speicherung deiner HA-Tokens & Settings
- Auto-Renew der TLS-Zertifikate

## Was du selbst behältst

- **Agent läuft bei dir zuhause** — deine Modbus-Daten verlassen dein
  LAN nur als zusammenfassende Werte (kWh-Zähler etc.), nicht als
  einzelne Register.
- **Hardware** (Waveshare-Bridges) komplett bei dir.
- **Daten-Hoheit** — Export jederzeit als SQLite-DB + JSON.

## Preise (geplant)

<!-- TODO: Preise finalisieren -->

| Tier | Preis | Was drin |
|---|---|---|
| Family | ~5 €/Monat | bis 4 Wechselrichter, bis 5 User |
| Pro | ~15 €/Monat | bis 10 WR, unbegrenzte User, Priority-Support |

## Wie melde ich mich an?

<!-- TODO: Sign-Up-Flow ausarbeiten, wenn Cloud live ist -->

Wenn die Cloud live ist (geplant Q3/2026):
1. Account auf [cloud.borochi.io](https://cloud.borochi.io) anlegen
2. Pairing-Code generieren
3. Auf deinem Agent als `BOROCHI_BRIDGE_URL=wss://cloud.borochi.io/ws/agent` + Token eintragen
4. Fertig — kein eigener Server nötig.

## Cloud-Migration aus Selfhost-Setup

Wenn du erst selfhostest und später wechseln willst:

1. Settings → Export → "Vollständiger Export" (JSON + DB)
2. In der Cloud-Instanz Settings → Import → Datei hochladen
3. Agent umkonfigurieren auf neue Bridge-URL + neuen Token

→ **Weiter mit konkreten Anbindungen: [Wetter](02-wetter.md)**
