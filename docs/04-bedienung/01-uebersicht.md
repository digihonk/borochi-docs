# Übersicht-Page

Die **Übersichts-Seite** ist deine erste Anlaufstelle: ein cinematic Hero
oben + Live-Werte aller wichtigen Größen drunter.

<!-- TODO: Großer Screenshot der Übersichts-Page -->

## Layout

```
┌─────────────────────────────────────────────┐
│  WETTER-HERO mit Hausfoto                   │
│  Temp • Sun-Path • Aktuelle PV-Leistung     │
├─────────────────────────────────────────────┤
│ [PV] [BATTERIE] [NETZ] [VERBRAUCH] [AUTARKIE]│
│  Sticky KPI-Topbar                          │
├──────────────┬──────────────────────────────┤
│  ENERGIE-    │  STATUS-CARDS                │
│  FLOW-       │  Wetter | Strompreis |       │
│  ANIMATION   │  Wallbox | Letzte Aktion     │
└──────────────┴──────────────────────────────┘
```

## Hero (oben)

- **Hintergrund:** dein Hausfoto, gefiltert je nach Tageszeit
- **Wetter:** Symbol + Temperatur + Wind + Wolken
- **Sun-Path:** Animation der Sonnen-Bahn am heutigen Tag
- **Top-Status-Ticker:** rotierende Mini-News
  ("Akku 78% • Netz +120W • Tibber 28 ct/kWh • KI: alles stabil")

<!-- TODO: Screenshot Hero close-up -->

## KPI-Topbar (sticky)

Beim Scrollen bleibt diese Bar oben sichtbar. Sie zeigt:

| KPI | Wert | Beispiel |
|---|---|---|
| ☀️ PV | aktuell produzierte Leistung | `4.32 kW` |
| 🔋 BAT | SoC + Lade/Entlade | `78%, +1.2 kW` |
| 🔌 NETZ | + bezogen / − eingespeist | `−420 W` |
| 🏠 VERBR | Hausverbrauch | `1.85 kW` |
| 📈 AUTARKIE | gleitend 24h | `92%` |

Klick auf eine KPI → springt zur entsprechenden Detail-Page.

## Energie-Flow

Eine animierte Sankey-artige Visualisierung: **Sonne → Wechselrichter →
Haus / Batterie / Netz** mit fließenden Partikeln, deren Geschwindigkeit
der echten Energie entspricht.

<!-- TODO: Screenshot Energie-Flow live + im Detail -->

## Status-Cards

Vier Cards in 2×2-Grid:

| Card | Inhalt |
|---|---|
| **Wetter** | Heute-Vorhersage + 3-Tage-Mini |
| **Strompreis** | Aktueller Tibber-Preis + Tageskurve |
| **Wallbox** | Auto-Icon + SoC + aktueller Lade-Status |
| **Letzte Aktion** | KI-Vorschlag oder System-Event |

Jede Card ist **klickbar** → führt zur ausführlichen Page.

## Live oder Demo?

Oben rechts: kleiner Pill mit aktuellem Modus:

- 🟢 **LIVE** — Daten vom Agent
- 🟡 **DEMO** — simulierte Daten (Default wenn noch kein Agent verbunden)
- 🔴 **OFFLINE** — Agent disconnected oder Bridge nicht erreichbar

Klick auf Pill → Detail-Modal mit Diagnose.

## Tipps

- **Refresh:** `Cmd+R` oder Hochziehen-Geste auf Mobile reicht.
- **Vollbild:** `F` taste schaltet Sidebar weg → cinema-Modus.
- **Demo aus:** Settings → Allgemein → "Demo-Modus" → AUS (falls du den nicht willst).

→ **Weiter: [Wallbox steuern](02-wallbox.md)**
