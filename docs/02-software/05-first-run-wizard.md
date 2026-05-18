# First-Run-Wizard

Beim allerersten Aufruf von `https://home.dein-name.de` merkt Borochi:
"Da ist noch kein User in der DB" und führt dich durch ein **First-Run-Setup**.

## Schritte

### 1. Admin-Account anlegen

<!-- TODO: Screenshot First-Run-Step-1 -->

| Feld | Hinweis |
|---|---|
| **Username** | klein, ohne Sonderzeichen — z.B. `marcel` |
| **Anzeige-Name** | wie's im Dashboard erscheint, z.B. `Marcel E.` |
| **Passwort** | mindestens 12 Zeichen, gerne länger |
| **TOTP** | empfohlen — du scannst einen QR-Code mit Authy / Google Authenticator / iOS-Passwörter |

!!! tip "TOTP nicht überspringen"
    Selbst wenn das System nur im LAN läuft: TOTP kostet 5 Sekunden bei jedem
    Login und schützt dich gegen den Fall, dass dein Passwort irgendwann mal
    leakt (Browser-Sync, Manager-Bug, etc.).

### 2. Recovery-Code

Borochi zeigt dir **einen Recovery-Code** an. Wenn du dein TOTP-Gerät verlierst,
ist das deine einzige Möglichkeit wieder reinzukommen.

→ **Ausdrucken oder im Passwort-Manager speichern**.

<!-- TODO: Screenshot Recovery-Code-Screen -->

### 3. Erstes Agent-Pairing

Wenn du jetzt schon einen Agent vorbereitet hast (siehe vorheriges Kapitel),
zeigt Borochi:

> "Bisher hat sich kein Agent gemeldet. Möchtest du einen Pairing-Code generieren?"

Klicke **Pairing-Code erzeugen** → du bekommst einen 8-Zeichen-Code wie
`K7-D9X-22`. Den trägst du beim Agent als `BOROCHI_AGENT_TOKEN` ein.

Beim ersten Connect tauscht der Agent den Pairing-Code gegen einen permanenten
Token → ab dann ist er **fest gekoppelt** an deine Bridge.

<!-- TODO: Screenshot Pairing-Code-Anzeige -->

### 4. Anlagen-Konfiguration

| Feld | Erklärung |
|---|---|
| **Anlagen-Name** | dein Haus / dein Standort, z.B. `Eichenweg 12` |
| **Anzahl Wechselrichter** | 1 oder 2 |
| **PV-Peak (kWp)** | aus deinem Anlagen-Datenblatt |
| **Batterie-Kapazität (kWh)** | total über alle BES-H2 |

→ Wird nur für Statistiken / Anzeigen genutzt, kann später angepasst werden.

<!-- TODO: Screenshot Anlagen-Config -->

### 5. Fertig

Du landest direkt im Dashboard. Wenn schon ein Agent verbunden ist und Modbus
funktioniert, siehst du **echte Werte** auf der Übersichts-Seite.

Falls noch nicht (z.B. Hardware noch nicht verbunden), läuft Borochi im
**Demo-Modus** und zeigt simulierte Daten. Sobald der Agent online geht,
schaltet's automatisch auf Live.

<!-- TODO: Screenshot Dashboard-Übersicht direkt nach First-Run -->

## Wann startet der Wizard erneut?

- Niemals automatisch — nach Schritt 5 ist Borochi "produktiv".
- Wenn du die DB löschst (`/opt/borochi/data/bridge.db`), startet der Wizard wieder.

→ **Weiter mit Kapitel [3. Konfiguration](../03-config/index.md)** — Wetter, KI, Strompreise einrichten.
