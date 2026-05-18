# 3. Konfiguration

Hardware steht, Software läuft. Jetzt machen wir Borochi **richtig schön
und smart**, indem wir externe Dienste anbinden — alle **optional**, alle
über die Settings-Page im Dashboard konfigurierbar.

## Übersicht

| Anbindung | Was kommt rein | Was kostet das |
|---|---|---|
| [Wetter (Open-Meteo)](02-wetter.md) | Live-Wetter + 7-Tage-Vorhersage + Wolken/Sonnenstand | gratis |
| [KI-Provider](03-ki-anbinden.md) | Autopilot-Empfehlungen, Modbus-Sniffer-Identifikation | API-Kosten (~1–5 €/Monat) |
| [Tibber](04-tibber.md) | dynamische Strompreise stündlich + Live-Verbrauchsstrom | gratis (Account nötig) |
| [Home Assistant](05-home-assistant.md) | Daten in dein bestehendes HA pushen | gratis, HA muss laufen |

## Reihenfolge — was zuerst?

Nicht zwingend. Aber unsere Empfehlung:

1. **Wetter zuerst** — kein Account nötig, sofort schöner. Macht den Hero-Banner lebendig.
2. **Tibber als zweites** — wenn du Tibber-Kunde bist, bringt dir das den meisten Nutzen (Auto-Charge bei günstigem Strom).
3. **KI als drittes** — wenn du Lust auf Autopilot hast oder Modbus-Sniffer mit KI nutzen willst.
4. **Home Assistant** — wenn du HA für andere Smart-Home-Sachen nutzt.

## Borochi-Cloud als Alternative

Wenn dir das alles zu viel Eigenbetrieb ist, gibt's (geplant) eine
[Borochi-Cloud](01-cloud-alternative.md), die das meiste für dich erledigt.

→ **Empfehlung: starte mit [Wetter](02-wetter.md)**
