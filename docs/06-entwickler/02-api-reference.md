# API-Reference

Alle Bridge-Endpoints. Basis-URL: `https://home.dein-name.de/api`.

!!! info "Auth"
    Alle Endpoints (außer `/health`, `/setup/status`, `/login`, `/totp/verify`)
    brauchen entweder den Cookie `borochi_session` oder einen
    `Authorization: Bearer <jwt>` Header.

## Health & Setup

| Methode | Pfad | Zweck |
|---|---|---|
| `GET` | `/api/health` | `{status:"ok", version:"2.25"}` |
| `GET` | `/api/setup/status` | `{needs_setup: bool}` |
| `POST` | `/api/setup/initial` | First-Run: User + TOTP setzen |

## Auth

| Methode | Pfad | Body | Antwort |
|---|---|---|---|
| `POST` | `/api/login` | `{username, password}` | Cookie + `{token}` (dual-mode) |
| `POST` | `/api/totp/verify` | `{code, remember}` | Cookie + `{token, expires_in}` |
| `POST` | `/api/logout` | — | Cookie cleared |
| `GET` | `/api/me` | — | `{username, role, display_name}` |
| `POST` | `/api/me/password` | `{old, new}` | 204 |
| `POST` | `/api/me/totp/reset` | `{password}` | `{otpauth_uri, qr_svg}` |

Rate-Limits: 5 Login-Versuche / 60 Sekunden / IP.

## User-Management (Admin only)

| Methode | Pfad |
|---|---|
| `GET` | `/api/users` |
| `POST` | `/api/users` |
| `PATCH` | `/api/users/{id}` |
| `DELETE` | `/api/users/{id}` |

## Agents

| Methode | Pfad | Zweck |
|---|---|---|
| `POST` | `/api/agents/pair` | Generiert Pairing-Code |
| `POST` | `/api/agents/claim` | Agent-Side: Code → Token |
| `GET` | `/api/agents` | Liste verbundener Agents |
| `POST` | `/api/agents/{id}/revoke` | Token invalidieren |

## Modbus / Sniffer

| Methode | Pfad | Body |
|---|---|---|
| `POST` | `/api/modbus/scan` | `{host, slave, start, count, fc}` |
| `POST` | `/api/modbus/read` | `{host, slave, addr, count, fc}` |
| `GET` | `/api/modbus/scans` | Letzte 100 Scans |
| `GET` | `/api/modbus/scans/{id}` | Detail eines Scans |
| `POST` | `/api/modbus/register` | Bekannten Register-Eintrag speichern |
| `GET` | `/api/modbus/registers` | Liste bekannter Register |

## KI

| Methode | Pfad | Body |
|---|---|---|
| `POST` | `/api/ai/identify-register` | `{register, raw, interpretations, context}` |
| `POST` | `/api/ai/autopilot-suggest` | (intern) |
| `GET` | `/api/ai/config` | Provider + Limit-Status |
| `POST` | `/api/ai/config` | Provider + API-Key setzen |

## Tibber

| Methode | Pfad |
|---|---|
| `GET` | `/api/tibber/prices` |
| `GET` | `/api/tibber/consumption` |
| `POST` | `/api/tibber/config` |

## Wetter

| Methode | Pfad |
|---|---|
| `GET` | `/api/weather/current` |
| `GET` | `/api/weather/forecast` |
| `POST` | `/api/weather/location` |

## Home Assistant

| Methode | Pfad |
|---|---|
| `GET` | `/api/ha/config` |
| `POST` | `/api/ha/config` (token wird verschlüsselt) |
| `POST` | `/api/ha/test` |

## Settings / Exports

| Methode | Pfad | Zweck |
|---|---|---|
| `GET` | `/api/settings` | Bündel aller Configs (ohne Secrets) |
| `POST` | `/api/settings/export` | Vollständiger JSON-Export |
| `POST` | `/api/settings/import` | Re-Import |

## WebSocket-Endpoints

### `/ws/live` — Frontend → Bridge

**Auth:** Cookie (Origin-Check), kein Bearer.

**Frames Server → Client:**

```json
{ "type": "metrics", "data": { ... } }
{ "type": "event",   "data": { "level": "info", "msg": "..." } }
{ "type": "ai_suggestion", "data": { ... } }
```

**Frames Client → Server:**

```json
{ "type": "subscribe", "channels": ["metrics", "events"] }
{ "type": "wallbox_mode", "mode": "pv_surplus", "limits": {} }
```

### `/ws/agent` — Agent → Bridge

**Auth:** `Authorization: Bearer <agent_token>` Header.

**Frames Agent → Bridge:**

```json
{ "type": "hello", "agent_name": "nas-borochi", "version": "2.25" }
{ "type": "metrics", "ts": "...", "data": { ... } }
{ "type": "command_result", "command_id": "...", "ok": true, "data": {...} }
```

**Frames Bridge → Agent:**

```json
{ "type": "command", "command_id": "...", "action": "modbus_scan", "params": {...} }
{ "type": "config", "poll_interval": 5, "hosts": ["192.168.178.180"] }
```

## Errors

Borochi nutzt Standard-HTTP-Codes:

| Code | Bedeutung |
|---|---|
| 400 | Bad Request (Validation) |
| 401 | Auth fehlt oder ungültig |
| 403 | Auth ok, aber keine Berechtigung (Rolle) |
| 404 | Resource nicht gefunden |
| 429 | Rate-Limit überschritten |
| 500 | Bridge-Fehler — Logs prüfen |

Response-Body bei Fehlern:

```json
{ "error": "code_name", "message": "Human-readable description" }
```

→ Weiter: [Contributing](03-contributing.md)
