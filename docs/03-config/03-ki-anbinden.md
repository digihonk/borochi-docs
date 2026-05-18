# KI anbinden

Borochi nutzt LLMs für **zwei Dinge**:

1. **Autopilot** — analysiert deine Wetter/Strompreis/Verbrauchs-Daten
   und schlägt vor: "Lade jetzt voll, morgen wird's wolkig" o.ä.
2. **Modbus-Sniffer-Identifier** — wenn du einen unbekannten Register-Bereich
   scannst, klassifiziert die KI die Werte ("das sieht aus wie Batterie-SoC
   in Prozent")

Beides ist **optional** — wenn du keinen Key hinterlegst, funktioniert Borochi
trotzdem, nur die zwei Features sind aus.

## Provider-Optionen

| Provider | Modell | Preis (typisch) | Wo Key holen |
|---|---|---|---|
| **OpenAI** | `gpt-4o-mini` oder `gpt-4o` | ~0,15 € / 1M Token | [platform.openai.com](https://platform.openai.com/api-keys) |
| **Anthropic** | `claude-haiku-4-5` oder `claude-sonnet-4` | ~1 € / 1M Token | [console.anthropic.com](https://console.anthropic.com) |
| **Google Gemini** | `gemini-2.0-flash` | gratis bei niedrigem Volumen | [aistudio.google.com](https://aistudio.google.com) |

Für Borochis Use-Case reichen **kleine Modelle** locker. Erwartete Kosten:
**weniger als 2 €/Monat** bei normalem Polling.

!!! tip "Gemini Free-Tier"
    Google Gemini hat einen großzügigen Free-Tier — perfekt für Borochi.
    Solange du < 1.500 Requests/Tag hast, kostenfrei.

## Konfiguration

### Schritt 1: API-Key holen

→ Bei einem der drei Provider Account anlegen, API-Key generieren.
Key sieht aus wie `sk-...` (OpenAI), `sk-ant-...` (Anthropic), `AIza...` (Google).

### Schritt 2: In Borochi eintragen

Settings → KI → **Provider wählen** + **API-Key einfügen** → Speichern.

<!-- TODO: Screenshot KI-Settings -->

!!! warning "Key wird verschlüsselt gespeichert"
    Borochi speichert den Key in der Bridge-DB **AES-verschlüsselt** mit dem
    `BOROCHI_MASTER_SECRET` aus deiner `.env`. Wer die DB-Datei in die Hand
    bekommt, kommt ohne den `MASTER_SECRET` nicht an den Key. Aber: behandle
    Borochi-Bridge-Backups trotzdem wie ein Secret.

### Schritt 3: Test-Anfrage

Settings → KI → **Test-Anfrage senden** → "Sag Hallo"

Wenn ein vernünftiger Text zurückkommt, ist alles wired up. Wenn ein Fehler
kommt, prüfe:

- Key gültig?
- Genug Guthaben auf dem Konto?
- Provider-API erreichbar? (Curl-Check vom Server aus)

## Modi für Autopilot

Auf der KI-Page kannst du den Autopilot in 4 Modi konfigurieren:

| Modus | Was passiert |
|---|---|
| **Beobachten** | KI macht nur Vorschläge im Notification-Center, keine Aktionen |
| **Empfehlen** | KI schreibt konkrete Anweisungen, du bestätigst per Button |
| **Aktiv** | KI darf Wallbox-Modus + Lade-Strom direkt setzen (vorsichtig!) |
| **Aus** | Komplett deaktiviert |

Empfehlung am Anfang: **Beobachten** für ein paar Wochen, damit du siehst,
was die KI vorschlägt, bevor du sie auf "Aktiv" lässt.

<!-- TODO: Screenshot KI-Page Mode-Picker -->

## Modbus-Sniffer-Identify

Bei einem unbekannten Register-Wert (auf der **Datenpunkte-Page**) kannst du
auf den **🧠 AI-Identify-Button** klicken. Borochi sendet:

```json
{
  "register": 11247,
  "raw": 4521,
  "interpretations": {
    "uint16": 4521,
    "int16": 4521,
    "x_0.1": 452.1,
    "x_0.01": 45.21,
    "uint32_be": 296419328,
    ...
  },
  "context": "Solinteg BHW-10 Hybrid-Inverter"
}
```

→ Antwort kommt zurück wie: *"Das könnte AC-Frequenz × 0.01 (45.21 → 50.21 Hz
mit Offset) oder Active Power Total in W sein. Weitere Werte nötig zur
Disambiguierung."*

## Kosten-Kontrolle

Settings → KI → **Limits:**

| Limit | Beispiel |
|---|---|
| Max-Token pro Request | `2000` |
| Max-Requests pro Tag | `100` |
| Wenn überschritten | Pause bis Mitternacht UTC |

→ **Weiter: [Tibber Strompreise](04-tibber.md)**
