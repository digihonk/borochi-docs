# Bridge startet nicht

Wenn `docker compose up` Fehler wirft oder der Container sofort wieder
beendet wird, liegt's meistens an einer dieser Sachen.

## Logs lesen — immer der erste Schritt

```bash
cd /opt/borochi/bridge
docker compose logs --tail=100 borochi-bridge
```

## Häufige Fehler

### `sqlite3.OperationalError: unable to open database file`

**Ursache:** Container läuft als UID 1500, aber das Host-Volume gehört root.

**Fix:**
```bash
sudo chown -R 1500:1500 /opt/borochi/data
docker compose restart borochi-bridge
```

### `OSError: [Errno 13] Permission denied: '/var/lib/borochi-bridge/bridge.db'`

Gleiche Ursache wie oben. Fix identisch.

### `ValueError: BOROCHI_JWT_SECRET must be set`

**Ursache:** `.env`-Datei fehlt oder Variable nicht gesetzt.

**Fix:**
```bash
cat /opt/borochi/bridge/.env
# Muss BOROCHI_JWT_SECRET=... enthalten
```

Falls die Datei fehlt:
```bash
cd /opt/borochi/bridge
cp .env.example .env  # falls vorhanden
# oder neu erstellen wie in 02-bridge-docker.md beschrieben
```

### `ValueError: BOROCHI_MASTER_SECRET must be set`

Wie oben — Master-Secret in `.env` fehlt. Mit `openssl rand -hex 32` einen
generieren.

!!! danger "Master-Secret niemals nachträglich ändern"
    Wenn du `BOROCHI_MASTER_SECRET` änderst, kannst du die verschlüsselten
    HA-Tokens in der DB nicht mehr entschlüsseln. Setze ihn einmal richtig
    und sichere ihn.

### `Address already in use: 8080`

**Ursache:** Anderer Prozess belegt Port 8080.

**Fix:**
```bash
sudo lsof -i :8080
# Den Prozess killen oder in docker-compose.yml einen anderen Port mappen:
# ports: ["127.0.0.1:8090:8080"]
# Dann auch Nginx-Config anpassen
```

### `WARNING: Setup not initialized — first run mode`

Das ist **kein Fehler**! Borochi wartet auf den First-Run-Wizard.
Browser auf `https://home.dein-name.de` → User anlegen.

### Container startet, beendet sich aber nach Sekunden ohne Fehlermeldung

**Ursache:** Health-Check fehlgeschlagen, aber Logs sind leer.

**Fix:**
```bash
# Status prüfen
docker compose ps

# Container manuell ohne Restart starten, um Output zu sehen
docker compose run --rm borochi-bridge
```

Die manuelle Variante zeigt dir alle stdout/stderr, auch die, die der
Restart-Loop normalerweise wegschluckt.

### `gunicorn: command not found` oder Python-ImportError

**Ursache:** Image ist veraltet oder gebrochen gepullt.

**Fix:**
```bash
docker compose pull
docker compose up -d --force-recreate
```

### Bridge läuft, aber `curl http://127.0.0.1:8080/api/health` timeout

- Container läuft? `docker compose ps` zeigt "Up"?
- Port-Mapping korrekt? Sollte `127.0.0.1:8080->8080/tcp` sein.
- Firewall blockiert localhost? Sehr selten, aber: `sudo ufw status`.

## Wenn nichts hilft

```bash
# Volle Diagnose
docker compose ps
docker compose logs --tail=200 borochi-bridge
docker inspect borochi-bridge | grep -i restart
df -h /opt/borochi
ls -la /opt/borochi/data
cat /opt/borochi/bridge/.env  # bitte den Inhalt zensieren wenn du das postest!
```

Posten kannst du das in [GitHub Issues](https://github.com/digihonk/Home-Bridge/issues)
— **bitte Secrets vorher rausnehmen!**

→ Weiter: [Agent verbindet nicht](03-agent-connect.md)
