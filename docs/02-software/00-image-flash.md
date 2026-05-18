# Schnellweg: Borochi-Pi-Image flashen

Wenn du **kein Lust auf Server-Setup** hast: nimm den vorgekochten Weg.
Ein Raspberry Pi, ein Image, fertig.

!!! tip "Wann ist das der richtige Weg?"
    - Du willst's einfach laufen haben, kein eigener Server, kein Cloud-VPS
    - Du hast einen Pi 4 / Pi 5 / Pi Zero 2 W rumliegen (oder bestellst neu)
    - Du willst keinen Docker-Wahnsinn manuell aufsetzen

    Wenn du eh schon eine **NAS oder VM** hast, die rund um die Uhr läuft,
    ist [die Server-Variante](01-bridge-server.md) eleganter, weil ein
    Gerät weniger Strom zieht.

## Was du brauchst

| | Empfehlung |
|---|---|
| Raspberry Pi | **Pi 4 (2GB+)** oder **Pi 5** — Pi Zero 2 W geht auch, ist aber zäh |
| SD-Karte | **32 GB Class A2** oder besser. Noch besser: USB-SSD via USB-3-Adapter |
| Netzteil | offizielles Pi-Netzteil (5V / 3A USB-C bei Pi 4/5) |
| LAN-Kabel | das Pi braucht stabilen Internet/Heimnetz-Zugang |
| Computer mit SD-Reader | zum Flashen |

## Schritt 1: Image runterladen

Lade die aktuelle Image-Datei von GitHub Releases:

<!-- TODO: Direktlink einsetzen wenn das erste Release draußen ist -->

→ **[github.com/digihonk/borochi-pi-image/releases/latest](https://github.com/digihonk/borochi-pi-image/releases/latest)**

Du brauchst die `.img.xz`-Datei (~1.5-2 GB) und ihre `.sha256` daneben.

```bash
# Verifikation auf macOS / Linux
shasum -a 256 -c borochi-pi-vX.Y.Z.img.xz.sha256
# → "borochi-pi-vX.Y.Z.img.xz: OK"
```

## Schritt 2: Raspberry Pi Imager installieren

[**raspberrypi.com/software**](https://www.raspberrypi.com/software/) → Imager für dein OS runterladen.

<!-- TODO: Screenshot Raspberry Pi Imager Hauptbildschirm -->

## Schritt 3: Image flashen

1. Pi-Imager öffnen
2. **Choose Device** → Pi 4 oder Pi 5 (deins)
3. **Choose OS** → ganz unten scrollen → **Use custom** → `borochi-pi-vX.Y.Z.img.xz` wählen
4. **Choose Storage** → SD-Karte oder USB-SSD wählen
5. **NEXT** → "Would you like to apply OS customisation settings?" → **EDIT SETTINGS**

<!-- TODO: Screenshot Custom-OS-Auswahl im Imager -->

### Wichtige Einstellungen im Customisation-Dialog

| Einstellung | Wert |
|---|---|
| **Hostname** | `borochi` (damit mDNS funktioniert) |
| **Username** | `borochi` |
| **Password** | **NICHT** den Default lassen — ein eigenes setzen! |
| **WLAN** | nur ausfüllen wenn kein LAN-Kabel — dann WLAN-Land **DE** |
| **Locale** | Zeitzone `Europe/Berlin`, Tastatur `de` |
| SSH aktivieren | ✓ (mit Passwort-Auth) |

<!-- TODO: Screenshot Customisation-Dialog -->

!!! danger "Default-Passwort ändern"
    Das Image kommt mit `borochi/borochi` als Default. **Im Imager-Dialog
    setzt du dein eigenes Passwort** — sonst kann jeder in deinem WLAN den
    Pi übernehmen.

6. **SAVE** → **WRITE** → SD-Karte wird beschrieben (~3-10 Min)
7. Karte rausnehmen, in den Pi stecken, Strom anstöpseln

## Schritt 4: Boot abwarten (ca. 2-3 Min)

Beim ersten Boot passiert eine Menge:

- SD-Karte wird auf die volle Größe expandiert
- Docker-Container werden gestartet
- Secrets generieren (JWT, Master, Agent-Token)
- Firewall einrichten
- Bridge-Health-Check warten

Du erkennst's daran, dass die grüne LED am Pi nach dem Initial-Blinken
für ~60-90 Sek dauernd flackert.

## Schritt 5: Browser öffnen

```
http://borochi.local
```

→ Du landest direkt im **First-Run-Wizard**: User anlegen, TOTP einrichten,
Recovery-Code notieren, Anlage konfigurieren.

<!-- TODO: Screenshot Boot-Erfolg mit erstem Wizard-Screen -->

Wenn `borochi.local` **nicht** im Browser auflöst (Windows-Problem):

```bash
# IP rausfinden — am Router suchen oder:
ping borochi.local
# oder vom Pi-Display direkt ablesen wenn HDMI dran
```

Dann eben `http://<pi-ip>` aufrufen.

## Schritt 6: Waveshare-IPs eintragen

Nach dem Wizard musst du dem Pi noch sagen, wo deine Waveshare-Bridges
hängen. Per SSH:

```bash
ssh borochi@borochi.local
sudo borochi-setup
```

Das Script:
- Scannt dein LAN nach Waveshare-Bridges (offene Port-80-Geräte)
- Fragt dich, welche IPs deine Wechselrichter haben
- Schreibt's in die Config
- Startet die Container neu

Danach siehst du auf dem Dashboard echte Live-Daten.

<!-- TODO: Screenshot SSH-Session mit borochi-setup-Ablauf -->

## Updates später

Wenn neue Borochi-Versionen rauskommen:

```bash
ssh borochi@borochi.local
sudo borochi-update
```

Zieht neue Docker-Images, sichert die DB, startet neu. Dauert ~30 Sekunden.

Größere OS-Updates (Bookworm → Trixie, Pi-OS-Major-Release) machst du am
einfachsten durch **Neu-Flashen mit aktuellem Image**, vorher per
`Settings → Erweitert → Export` deine Config retten.

## Diagnose

Wenn was nicht stimmt, hilft das CLI-Tool:

```bash
ssh borochi@borochi.local
borochi-doctor
```

→ Zeigt dir Status von Docker, Bridge, Agent, Waveshare-Verbindung,
Disk-Space, RAM. Wenn `Borochi Doctor` rot anzeigt, hast du den Hinweis
worauf du gucken musst.

## Wenn dir der Pi nicht reicht

Der Schnellweg ist genau das — **schnell**. Mit dem Wachstum deiner
Anlage (mehr Wechselrichter, höheres Polling-Volumen, Mehrere User die
gleichzeitig drauf schauen) kommst du irgendwann zu:

- Längere DB-Backups
- Höhere CPU-Last beim Live-WS
- SD-Karte stirbt nach 1-2 Jahren (Cell-Wear)

Spätestens dann ist [die Server-Variante](01-bridge-server.md) mit
Hetzner-VPS oder NAS-VM die bessere Wahl.

→ **Wenn alles läuft, gehst du jetzt zu [First-Run-Wizard](05-first-run-wizard.md)** — Account anlegen.
