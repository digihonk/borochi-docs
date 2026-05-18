# Wallbox steuern

Die **Wallbox-Page** ist Borochis Live-Charging-Cockpit. Hier siehst du
auf einen Blick, was dein Auto gerade tut, und kannst zwischen 5 Lademodi
wechseln.

<!-- TODO: Screenshot Wallbox-Page Cockpit -->

## Cockpit-Aufbau

```
┌─────────────────────────────────────┐
│  STATUS-PILL  (Bereit / Lädt / ... )│
├─────────────────────────────────────┤
│  ┌───────────┐    ┌──────────────┐  │
│  │   SoC     │    │  POWER-RING  │  │
│  │   65%     │    │  ⚡ 7.4 kW   │  │
│  │ ████░░░   │    │              │  │
│  └───────────┘    └──────────────┘  │
│  3 × Phasen-Strips (L1 / L2 / L3)   │
│  60min-Live-Spark-Verlauf           │
├─────────────────────────────────────┤
│  MODUS-PICKER (5 Tiles)             │
└─────────────────────────────────────┘
```

### SoC-Bar

Animierte Füllung mit Shimmer-Effekt. Wert kommt vom Auto via ISO15118 (falls
deine Wallbox + Auto das unterstützen) oder aus der gefahrenen kWh-Schätzung.

### Power-Ring

Aktuelle Lade-Leistung als Donut mit animiertem ⚡-Symbol. Skala 0–22 kW.

### 3-Phasen-Strips

Drei horizontale Bars für L1/L2/L3 — du siehst sofort, ob 1-phasig (max 4,2 kW)
oder 3-phasig (max 11/22 kW) geladen wird.

### Live-Spark

Die letzten 60 Minuten Lade-Leistung als kleine Sparkline. Praktisch wenn
KI gerade dynamisch hoch-/runterregelt.

## Die 5 Lade-Modi

| Modus | Icon | Was passiert |
|---|---|---|
| **PV-Überschuss** | ☀️ | Lädt nur mit Strom, der gerade ins Netz gehen würde. 3-phasig ab 4,2 kW Überschuss. |
| **Sofortladen** | ⚡ | Volle Power (11 kW oder was die Wallbox kann) — sofort. |
| **Preisoptimiert** | 💶 | Lädt nur wenn Strom günstig ist (Tibber). Plant über die Nacht. |
| **Zeitplan** | 📅 | Lädt zu festgelegten Zeiten (z.B. 23–07 Uhr) mit definierter Leistung. |
| **Manuell** | 🎚 | Du wählst Leistung selbst per Slider (1–11 kW). |

<!-- TODO: Screenshot Mode-Picker mit allen 5 Tiles -->

## Quick-Action-Buttons

Unter dem Mode-Picker hast du **3 Schnell-Aktionen**:

- **Pause** — sofort stoppen
- **Boost 30min** — kurzzeitig auf Maximum, danach zurück zum gewählten Modus
- **Zielzeit setzen** — "fertig bis 07:00" → Borochi rechnet zurück

## PV-Überschuss-Details

PV-Überschuss-Modus ist der häufigste. Die Logik:

1. PV-Erzeugung > Hausverbrauch → Überschuss positiv
2. Überschuss > 1,4 kW → 1-phasig laden mit (Überschuss − 200W Puffer)
3. Überschuss > 4,2 kW → auf 3-phasig hoch
4. Überschuss < 1,4 kW für > 1 Min → pausieren (nicht abreissen)
5. Hysterese: 30 Sek warten bevor neuer Switch (verhindert Flattern)

Parameter dafür: Settings → Wallbox → PV-Logik

| Parameter | Default |
|---|---|
| Min. Leistung 1-phasig | `1.4 kW` |
| Min. Leistung 3-phasig | `4.2 kW` |
| Puffer | `200 W` |
| Hysterese | `30 s` |
| Pause-Threshold | `60 s` |

<!-- TODO: Screenshot PV-Überschuss-Settings -->

## Preisoptimiert-Logik

Wenn Tibber angeschlossen ist:

1. Du gibst **Tagesziel** an: z.B. "20 kWh bis 07:00"
2. Borochi holt die Preiskurve für die nächsten 24h
3. Wählt die **billigsten Stunden** aus, die zusammen ≥ 20 kWh ergeben
4. Plant Boost-Slots: voll laden in günstigen Stunden, pausieren sonst
5. Wenn PV-Überschuss verfügbar → zusätzlich laden (gratis Strom)

## Wallbox-Marken

<!-- TODO: Welche Wallboxen wir konkret unterstützen (Go-eCharger, Wallbe, KEBA, ...) -->

Borochi nutzt Modbus-TCP zur Wallbox oder die jeweilige HTTP-API. Settings →
Wallbox → **Wallbox-Modell** wählen und Verbindungsdaten eingeben.

## Wenn nichts lädt

| Symptom | Lösung |
|---|---|
| Wallbox zeigt "Bereit", lädt nicht | Auto-Side: Ladekabel mit Fehlerschutz? Auto-Setup auf "Eco/Schedule"? |
| PV-Überschuss-Modus: Bar bleibt grau | Überschuss < 1.4 kW oder Pause-Threshold noch nicht überschritten |
| Lädt mit voller Power obwohl PV-Modus | KI-Override aktiv? Notification-Center checken |

→ **Weiter: [Modbus-Sniffer](03-modbus-sniffer.md)**
