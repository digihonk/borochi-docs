# Tibber Strompreise

Wenn du **Tibber-Kunde** bist, kann Borochi dynamische Strompreise anzeigen
und die Wallbox automatisch laden, wenn der Strom günstig ist.

## Was du davon hast

- **Live-Spotpreis** im Hero ("aktuell 23,4 ct/kWh")
- **24h-Preiskurve** auf der Markt-Page
- **Günstigste Lade-Slots** für Auto + Hausbatterie
- **Auto-Charge bei günstigem Strom** (wenn aktiviert)
- **Notification** wenn Preis unter eingestellten Threshold fällt

!!! info "Kein Tibber-Kunde?"
    Du kannst trotzdem den Markt-Tab nutzen mit **EPEX-Spot-Daten** (gratis
    via aWATTar API). Tibber gibt dir nur den persönlichen Endpreis dazu.

## Schritt 1: API-Token holen

1. App **Tibber** öffnen oder [tibber.com](https://developer.tibber.com) im Browser
2. Account → Entwickler → **Persönlicher Token**
3. Token kopieren (sieht aus wie `5K4MV...`)

<!-- TODO: Screenshot Tibber-Developer-Page -->

## Schritt 2: In Borochi eintragen

Settings → Integrationen → **Tibber** → Token einfügen → Speichern.

Wenn dein Account mehrere Häuser hat, wirst du nach Save gefragt welches
**Home** Borochi nutzen soll.

<!-- TODO: Screenshot Tibber-Settings + Home-Selector -->

## Schritt 3: Live-Daten verifizieren

Direkt nach Speichern sollte auf der **Markt-Page**:

- Aktueller Spot-Preis sichtbar
- Heute-Kurve mit `low / medium / high`-Färbung
- Wenn nach 13 Uhr deutscher Zeit: morgige Kurve auch

<!-- TODO: Screenshot Markt-Page mit Tibber-Daten -->

## Auto-Charge konfigurieren

Wallbox-Page → **Modus: Preisoptimiert** → Einstellungen:

| Parameter | Beispiel |
|---|---|
| Charge-Threshold | `0.20 €/kWh` — Auto lädt nur unterhalb |
| Tägliches Minimum | `15 kWh` — egal wie teuer, das wird geladen |
| Bis-Wann-Zeitfenster | `bis 07:00` — fertig bevor du zur Arbeit fährst |
| Lade-Modus bei Niedrig | `volle 11 kW` — drauflasten wenn günstig |

Borochi plant dann automatisch die **günstigsten Stunden** in deinem Fenster
und schaltet die Wallbox passend.

<!-- TODO: Screenshot Preisoptimiert-Modus mit Settings -->

## Tibber + Hausbatterie (vorsichtig)

!!! danger "Batterie netzladen ist NICHT förderkonform"
    Wenn du **EEG-gefördert** bist (Einspeisevergütung), darfst du die
    BES-H2 **nicht aus dem Netz laden**. Das ist eine Förderbedingung.

    Borochi hat eine separate Einstellung "Batterie-Netzladen erlauben",
    die **defaultmäßig AUS** ist. Lass sie aus, wenn du Förderung beziehst.

Wenn du **keine Förderung** hast (z.B. ältere Anlage oder Eigenverbrauchs-Modell):

Settings → Batterie → "Tibber-Netzladen erlauben" → ja → Threshold + Limit
festlegen.

## Tibber-Verbrauchsstrom

Tibber liefert dir auch deinen **aktuellen Hausverbrauch** via Pulse, falls
du das Gerät hast. Das ist genauer als die rechnerische Ableitung aus
PV-Erzeugung minus Netz-Einspeisung.

Settings → Tibber → **Pulse aktivieren** → ja → Borochi nutzt diesen Wert
für die Verbrauchs-Kurve, statt zu rechnen.

→ **Weiter: [Home Assistant](05-home-assistant.md)**
