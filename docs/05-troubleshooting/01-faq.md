# FAQ

Die häufigsten Probleme + ihre Lösungen.

## Login / Auth

### "Wrong password" obwohl Passwort korrekt

- Caps-Lock?
- Im richtigen Borochi? (manchmal Tab mit anderer Bridge offen)
- Nach v2.14 Cookie-Auth: alte Browser-Sessions clearen (Cmd+Shift+Delete → letzte 1h)

### TOTP-Codes funktionieren nicht

- Uhrzeit auf TOTP-Gerät korrekt? TOTP braucht synchrone Zeit (±30s).
- Falsches Konto im Authenticator?
- Recovery-Code zum Reset nutzen

### "30 Tage angemeldet bleiben" funktioniert nicht

- Browser blockiert Third-Party-Cookies? → für `home.dein-name.de` erlauben.
- HTTPS-Cookie auf HTTP-Seite ist Browser-blockiert — du brauchst HTTPS.
- Private-Mode? Da werden Cookies beim Tab-Schließen gelöscht.

## Daten / Live-Werte

### Alle Werte zeigen `— —` oder `0`

→ Top-rechts Pill checken: 🔴 OFFLINE? Dann hat der Agent keine Verbindung.

→ 🟡 DEMO? Dann läuft Borochi auf Demo-Daten — Agent disconnected oder nicht eingerichtet.

→ 🟢 LIVE, aber trotzdem Nullen? Modbus liefert nichts — siehe [Modbus antwortet nicht](04-modbus-no-response.md).

### Werte sind unsinnig (z.B. 65535 oder riesige Zahlen)

→ Skalierungsfaktor falsch oder int16/uint16 verwechselt. Modbus-Sniffer
verwenden um die echte Bedeutung zu klären. Siehe [Modbus-Sniffer](../04-bedienung/03-modbus-sniffer.md).

### Daten ruckeln / aktualisieren spät

- Bridge → Agent WebSocket evtl. unterbrochen. Agent-Logs prüfen.
- Browser-Tab in Background? Browser pausieren Updates dort manchmal. Klick aufs Tab → live wieder.
- Polling-Intervall zu hoch? Settings → Agents → Intervall (Default 5s).

## Wallbox

### Wallbox lädt nicht im PV-Überschuss-Modus

- Überschuss < 1.4 kW Min-Threshold? Settings → Wallbox → PV-Logik anpassen.
- Auto-Ladegrenze im Fahrzeug erreicht?
- Hysterese-Pause läuft noch (30s nach letztem Switch)?

### Wallbox switcht ständig 1-↔3-phasig

- Hysterese zu klein. Settings → Wallbox → Hysterese auf `60s`.
- PV-Erzeugung unstabil (Wolken): das ist normal. Mit höherer Hysterese ruhiger.

### Wallbox reagiert gar nicht auf Modus-Wechsel

- Wallbox-Verbindung getrennt? System-Page → Wallbox-Status checken.
- Modbus-Adresse der Wallbox korrekt? Default ist meist `1`, kann aber kollidieren mit WR.

## Wetter / Hero

### Hausfoto fehlt im Hero

- Foto hochgeladen? Settings → Anlagen → Hausfoto.
- Datei < 5 MB?
- Format jpg/webp/png — gif/heic gehen nicht direkt.

### Wetter-Werte fehlen

- Standort eingegeben? Settings → Allgemein → PLZ.
- Open-Meteo erreichbar? Server-Logs prüfen.
- Internet auf der Bridge da? `ping api.open-meteo.com` von dort.

### Sun-Path-Visualisierung falsch

- Standort grob falsch (PLZ aus anderem Land)?
- Server-Zeitzone richtig? `timedatectl` auf dem Server.

## Performance

### Dashboard ist langsam beim Laden

- Erste Ladung enthält 233 Icon-PNGs + alle Sprites → ~3-5 MB initial.
- Sollte mit HTTP/2 + Gzip in 1-2 Sek erledigt sein.
- Wenn länger: Nginx-Config prüfen (gzip an? http2 an?).

### Smartphone-Browser stockt

- Animationen reduzieren: Settings → Erweitert → "Reduced Motion" → an.
- Wetterpartikel ausschalten: Settings → Hero → "Partikel" → aus.

## Sonstiges

### Wo finde ich Logs?

- **Bridge:** `docker compose logs borochi-bridge` auf dem Server
- **Agent:** Container Manager → borochi-agent → Logs (NAS)
- **Frontend:** Browser DevTools → Console
- **Nginx:** `/var/log/nginx/access.log` + `error.log`

### Wie mache ich ein Backup?

- **Bridge-DB:** `/opt/borochi/data/bridge.db` täglich kopieren (siehe [Bridge installieren](../02-software/02-bridge-docker.md#backup-strategie))
- **Settings-Export:** Settings → Erweitert → "JSON-Export" → speichert alle Configs
- **Agent-Token-Liste:** in Settings → Agents kannst du Token-Hashes sehen, der Klartext kommt aus der `.env` deines Agent-Containers

### Update auf neue Borochi-Version

```bash
# Bridge
cd /opt/borochi/bridge
docker compose pull
docker compose up -d

# Agent (auf NAS)
Container Manager → borochi-agent → Image neuziehen → Container neu starten

# Frontend
cd /opt/borochi/frontend
git pull
```

### Borochi löschen / komplett deinstallieren

```bash
# Bridge
cd /opt/borochi/bridge
docker compose down
sudo rm -rf /opt/borochi

# Agent
Container Manager → borochi-agent → entfernen
# + ggf. /volume1/docker/borochi-agent löschen

# Nginx-Config
sudo rm /etc/nginx/sites-enabled/borochi.conf
sudo systemctl reload nginx
```

→ Weitere Themen:
- [Bridge startet nicht](02-bridge-startup.md)
- [Agent verbindet nicht](03-agent-connect.md)
- [Modbus antwortet nicht](04-modbus-no-response.md)
