# Multi-User

Borochi unterstützt **mehrere Benutzer mit Rollen** — praktisch wenn deine
Familie Zugriff haben soll, aber nicht alle alles dürfen.

## Rollen

| Rolle | Was darf sie? |
|---|---|
| **Admin** | Alles. User anlegen, Settings ändern, Wallbox-Modus steuern, KI-Konfig ändern |
| **Member** | Wallbox + Lade-Modi nutzen, Benachrichtigungen sehen. Keine Settings. |
| **Viewer** | Nur lesen. Sieht alle Pages, kann nichts ändern. |

## User anlegen

Settings → Benutzer → **+ Neuer Benutzer**:

| Feld | |
|---|---|
| Username | klein, eindeutig |
| Anzeige-Name | wie's im Dashboard erscheint |
| Rolle | Admin / Member / Viewer |
| TOTP | Pflicht für Admin, optional für andere |
| Recovery-Code | wird beim Anlegen einmal angezeigt |

<!-- TODO: Screenshot User-Anlage-Dialog -->

## Login + 30-Tage-Sessions

Beim Login:
1. Username + Passwort
2. TOTP-Code (6 Stellen)
3. Optional: **"30 Tage angemeldet bleiben"** Checkbox

Wenn aktiviert, setzt Borochi einen HttpOnly+Secure-Cookie mit 30 Tagen TTL.
Nach Ablauf normaler Login wieder nötig.

!!! info "Multi-Device"
    Du kannst dich auf beliebig vielen Geräten parallel einloggen — jedes hat
    seine eigene Session. Settings → Sicherheit → "Alle Sessions abmelden"
    haut alle weg, wenn du z.B. ein Handy verloren hast.

## Passwort/TOTP ändern

Klick aufs Avatar oben rechts → **Profil**:

- Passwort ändern (altes Passwort + neues 2× eingeben)
- TOTP-Gerät resetten (neuer QR-Code, dein alter Authenticator-Eintrag wird ungültig)
- Recovery-Code neu generieren

<!-- TODO: Screenshot Profil-Page -->

## Pairing für externe Agents

Settings → Agents → Liste aller verbundenen Agents + **+ Pairing-Code**:

1. Klick "+ Pairing-Code generieren" → bekommst Code wie `K7-D9X-22`
2. Code in den Agent eintragen (env-Variable `BOROCHI_AGENT_TOKEN`)
3. Agent connectet beim ersten Mal → tauscht Code gegen permanenten Token
4. Code ist danach **verbraucht** und kann nicht wiederverwendet werden

<!-- TODO: Screenshot Pairing-Page mit Code-Anzeige -->

### Agent revoken

Wenn du einen Agent loswerden willst (z.B. defekte NAS):

Settings → Agents → 🗑 Revoke → Token wird invalidiert, der Agent kann sich
nicht mehr verbinden. Du brauchst neuen Pairing-Code für ein neues Gerät.

## Audit-Log

Settings → Sicherheit → **Audit-Log** zeigt:

- Login-Events (User, IP, User-Agent, Zeit)
- Settings-Änderungen (was, von wem, vorher/nachher)
- Wallbox-Mode-Switches
- Failed-Login-Versuche

→ Praktisch wenn du nachprüfen willst, wer wann was gemacht hat.

<!-- TODO: Screenshot Audit-Log -->

## Beispiel-Setup für Familie

```
Marcel (Admin):     username "marcel",   TOTP via Authy
Anna  (Member):     username "anna",     TOTP via iOS-Passwörter
Kind   (Viewer):    username "kind",     ohne TOTP, nur Tablet-Modus
```

Anna kann die Wallbox umschalten, wenn sie das Auto später nutzt. Das Kind
sieht das Dashboard nur als Anzeige.

→ **Kapitel komplett. Weiter zu [Troubleshooting](../05-troubleshooting/01-faq.md)**
