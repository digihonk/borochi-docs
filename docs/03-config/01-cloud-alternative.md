# Hosting-Optionen

Es gibt zwei Wege, Borochi laufen zu lassen — beide selbst-gehostet,
keine Cloud-Anbindung, keine Daten verlassen dein Heimnetz.

## Vergleich

| Aspekt | Pi-Image | Selfhost-Server |
|---|---|---|
| **Aufwand** | SD-Karte flashen, ~5 Min | VPS aufsetzen, ~30 Min |
| **Hardware** | Raspberry Pi (~80 €) | NAS/VM/VPS (eh schon vorhanden oder ~5 €/Monat) |
| **Updates** | `sudo borochi-update` auf dem Pi | `docker compose pull` auf dem Server |
| **Skalierung** | bis ~4 WR komfortabel | unbegrenzt |
| **HTTPS extern** | optional via Cloudflare Tunnel | Let's Encrypt direkt |
| **Backup-Strategie** | SD-Karte stirbt nach 1-2 Jahren | RAID-NAS, Hyper Backup, etc. |

## Empfehlung

- **Pi-Image** wenn du anfängst — schnellster Weg ins Dashboard,
  läuft alles vorkonfiguriert ([Schnellweg-Anleitung](../02-software/00-image-flash.md))
- **Selfhost-Server** wenn dir der Pi zu klein wird, du eh eine NAS
  hast, oder du das Dashboard von außerhalb erreichen willst
  ([Server-Anleitung](../02-software/01-bridge-server.md))

## Wechseln vom Pi zum Server

Wenn du später umsteigen willst:

1. **Auf dem Pi:** Settings → Erweitert → "Vollständiger Export" → JSON-Datei runterladen
2. **Auf dem neuen Server:** Server-Setup gemäß Anleitung
3. **Im neuen Dashboard:** Settings → Erweitert → "Import" → JSON-Datei hochladen
4. Agent neu pairen (Pi pairen erfolgte gegen den alten Bridge-Server)

Deine Daten sind dadurch nicht verloren — die Bridge-DB ist eine simple
SQLite-Datei, du kannst sie auch direkt kopieren wenn du genau weißt was
du tust.

## Borochi-Cloud?

**Nein.** Borochi ist ein **privates Reverse-Engineering-Projekt** von Marcel
Ebbert — es gibt keine kommerzielle Cloud, keine Subscription, keine
zentral gehostete Variante. Du bleibst Herr deiner Daten und deiner
Wechselrichter. Falls dir die Selfhost-Welt zu groß ist, ist das Pi-Image
die einfachste Lösung — flashen, fertig.
