# Nginx + HTTPS

Wir hängen einen **Nginx-Reverse-Proxy** vor die Bridge. Der macht:

- **HTTPS** (mit Let's Encrypt automatisiert)
- **Static-File-Serving** für das Frontend
- **WebSocket-Upgrade** für `/ws/live` und `/ws/agent`

## Voraussetzungen

- Eine **Domain** zeigt auf deine Server-IP (A-Record).
  Bei dynamischer IP: DynDNS-Dienst wie [DuckDNS](https://duckdns.org) oder
  über deinen Domain-Provider.
- **Port 80 + 443** offen im Internet.

## Nginx installieren

```bash
sudo apt update
sudo apt install nginx certbot python3-certbot-nginx
```

## Konfigurationsdatei

```bash
sudo nano /etc/nginx/sites-available/borochi.conf
```

Inhalt:

```nginx
# HTTP → HTTPS Redirect
server {
    listen 80;
    server_name home.dein-name.de;
    return 301 https://$host$request_uri;
}

# HTTPS-Block
server {
    listen 443 ssl http2;
    server_name home.dein-name.de;

    # Let's Encrypt füllt das gleich aus
    ssl_certificate     /etc/letsencrypt/live/home.dein-name.de/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/home.dein-name.de/privkey.pem;

    # Security-Header
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header Referrer-Policy "strict-origin-when-cross-origin" always;

    # Frontend (statische Files)
    root /opt/borochi/frontend;
    index index.html;

    # API + WebSocket an Bridge weiterleiten
    location ~ ^/(api|ws)/ {
        proxy_pass http://127.0.0.1:8080;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        proxy_read_timeout 86400s;
        proxy_send_timeout 86400s;
    }

    # SPA-Fallback (Borochi-Frontend ist statisch + JS-Router)
    location / {
        try_files $uri $uri/ /index.html;
    }
}
```

Aktivieren:

```bash
sudo ln -s /etc/nginx/sites-available/borochi.conf /etc/nginx/sites-enabled/
sudo nginx -t   # Syntax-Check
sudo systemctl reload nginx
```

## Let's Encrypt

```bash
sudo certbot --nginx -d home.dein-name.de
```

Certbot:
- erkennt den Server-Block automatisch
- holt das Zertifikat
- richtet einen Cron-Job für Renewal ein

<!-- TODO: Screenshot vom erfolgreichen Certbot-Lauf -->

## Frontend deployen

Das Frontend ist eine **einzelne `index.html`** plus Assets. Du kannst sie
entweder manuell hochladen oder über Git-Pull deployen.

### Option A: Direkt-Upload

```bash
# Von deinem lokalen Rechner
scp -r ./web-repo/* root@server:/opt/borochi/frontend/
```

### Option B: Git-Pull (so machen wir's bei der Demo-Instanz)

```bash
# Einmalig auf dem Server
cd /opt/borochi
git clone https://github.com/digihonk/Home.git frontend
# oder dein eigener Fork

# Update später
cd /opt/borochi/frontend
git pull
```

### Option C: Auto-Deploy per Cron-Pull

```bash
# Cron-Eintrag
*/2 * * * * cd /opt/borochi/frontend && git pull --ff-only >/dev/null 2>&1
```

Frontend wird alle 2 Minuten von GitHub gezogen. Praktisch, wenn du auf einem
anderen Rechner entwickelst.

## Verifikation

Öffne `https://home.dein-name.de` im Browser. Du solltest den **First-Run-Wizard**
sehen — Borochi merkt, dass noch kein User angelegt ist.

→ **Weiter: [First-Run-Wizard](05-first-run-wizard.md)** — Admin-Account anlegen
