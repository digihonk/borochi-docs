# Agent verbindet nicht

Wenn die System-Page sagt **🔴 Agent offline** oder die Live-Pille auf 🟡 DEMO
hängenbleibt.

## Logs

Auf der NAS:

1. Container Manager → **borochi-agent** → Aktion → **Protokolle anzeigen**
2. Letzte 50 Zeilen scrollen — meistens steht der Fehler ganz unten

Häufige Patterns:

### `Connection refused` zur Bridge

```
[ERROR] Connection refused: wss://home.dein-name.de/ws/agent
```

**Ursache & Fix:**

- Bridge nicht erreichbar? `ping home.dein-name.de` von der NAS aus
- Falsche URL in env? `BOROCHI_BRIDGE_URL` muss mit `wss://` (mit `s` für TLS) anfangen
- Bridge läuft, aber Nginx leitet `/ws/agent` nicht weiter? Nginx-Config siehe [04-nginx-config.md](../02-software/04-nginx-config.md), die `proxy_set_header Upgrade $http_upgrade;` Zeilen sind kritisch

### `401 Unauthorized` beim Connect

```
[ERROR] Authentication failed: 401 Unauthorized
```

**Ursache & Fix:**

- Token ungültig oder revoked?
- Im Borochi-Dashboard: Settings → Agents → Status des Agents prüfen
- Wenn `revoked`: in Borochi neuen Pairing-Code generieren + im Agent eintragen
- Wenn `unknown`: Pairing-Code-Eingabe fehlgeschlagen — Token nochmal frisch erzeugen

### `SSL: CERTIFICATE_VERIFY_FAILED`

```
[ERROR] SSL handshake failed
```

**Ursache & Fix:**

- Let's-Encrypt-Cert abgelaufen? Auf Server: `sudo certbot renew`
- NAS hat alte Root-CA-Bundle? Synology DSM update durchführen
- Du nutzt selbstsigniertes Cert ohne CA in Trust-Store? Über `BOROCHI_TLS_INSECURE=1` overriden (nur für Tests!)

### `WebSocket handshake failed: 502 Bad Gateway`

Bridge antwortet nicht. Nginx hat den Upstream nicht. Auf dem Bridge-Server:

```bash
docker compose ps  # Bridge up?
curl http://127.0.0.1:8080/api/health  # Bridge healthy?
```

### `Connection reset` nach ~30 Sekunden

WS-Idle-Timeout in Nginx zu kurz. In der nginx-Config:

```nginx
proxy_read_timeout 86400s;
proxy_send_timeout 86400s;
```

Reload nginx: `sudo systemctl reload nginx`

### Agent verbindet, aber pollt keine Werte

```
[INFO] Authenticated as agent-id=42
[ERROR] Modbus connection failed: 192.168.178.180:502 — Connection refused
```

→ Modbus-Seite kaputt. Siehe [Modbus antwortet nicht](04-modbus-no-response.md).

### Agent connectet, aber die System-Page sagt trotzdem "offline"

- Pairing-Code wurde 2× verwendet? Codes sind Single-Use. Wenn der Agent
  schon mal connected hat und du gibst denselben Code in einen anderen Agent,
  klappt das nicht.
- Settings → Agents: zeigt der Agent eine grüne "last seen"-Zeit? Wenn dort
  vor 2 Sekunden steht aber System-Page rot: Frontend-Browser-Cache leeren
  (Cmd+Shift+R).

## Diagnose-Befehle

### Auf der NAS

```bash
# In den Agent-Container ssh'en (über Docker)
ssh Borochi@nas-ip
sudo docker exec -it borochi-agent /bin/sh

# Innen drin:
env | grep BOROCHI
# Prüfen: BRIDGE_URL, AGENT_TOKEN, WAVESHARE_HOSTS
```

### Auf dem Bridge-Server

```bash
# Welche Agents sind verbunden?
docker compose exec borochi-bridge sqlite3 /var/lib/borochi-bridge/bridge.db \
  "SELECT id, name, last_seen, revoked FROM agents;"
```

→ Weiter: [Modbus antwortet nicht](04-modbus-no-response.md)
