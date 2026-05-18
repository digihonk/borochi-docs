# Modbus-Sniffer

Solinteg ist ein **chinesischer OEM** — die offizielle Modbus-Map ist begrenzt
dokumentiert und kann zwischen Firmware-Versionen variieren. Der **Modbus-Sniffer**
ist Borochis Werkzeug, um **selbst auf Entdeckungsreise** zu gehen.

<!-- TODO: Screenshot Datenpunkte-Page mit Sniffer-UI -->

## Wann brauche ich den?

- Standard-Register (PV, Battery, Grid) zeigen `0` oder unsinnige Werte
- Du willst einen **neuen Wert** finden (z.B. die LED-Status-Bits, Fehler-Codes)
- Du hilfst der Community, die Map für **neue Firmware-Versionen** zu vervollständigen
- Du hast einen **anderen Hersteller** als BHW-10 und willst Borochi anpassen

## Aufbau der UI

Datenpunkte-Page → unten findest du den **Sniffer-Card-Bereich**:

```
┌─────────────────────────────────────────────────────────┐
│  MODBUS-SNIFFER                                         │
├─────────────────────────────────────────────────────────┤
│  Start: [11000]  End: [11050]                           │
│  Typ:   ⚪ Holding (FC03)  ⚫ Input (FC04)                │
│  Slave: [1]                                              │
│  Quick-Ranges: [PV] [Battery] [Grid] [Inverter Status]  │
│  [⏵ Scannen]                                             │
├─────────────────────────────────────────────────────────┤
│  ERGEBNIS (in 0.34s):                                   │
│  Addr   Hex    uint16 int16  x0.1   x0.01  Bekannt?  AI │
│  11000  04DF   1247   1247   124.7  12.47  PV-Power  -- │
│  11001  0000   0      0      0.0    0.00   --        -- │
│  11002  000C   12     12     1.2    0.12   ?         🧠  │
│  ...                                                    │
└─────────────────────────────────────────────────────────┘
```

## Schritt-für-Schritt

### 1. Range wählen

Bekannte Solinteg-Bereiche:

| Range | Was üblicherweise drin ist |
|---|---|
| `11000–11050` | PV-Live (Power, Voltage, Current pro String) |
| `11200–11250` | Wechselrichter Status, Temperaturen |
| `33000–33050` | BES-H2 Batterie Live (SoC, Power, Cell-Voltage) |
| `30200–30230` | Backup-Port / Notstrom-Status |
| `12000–12100` | Tageszähler (Energy Today, Energy Total) |

Klick auf einen **Quick-Range-Button** → fertig vorausgefüllt.

### 2. FC wählen

- **Input Register (FC04)** für die meisten Live-Daten — Standard
- **Holding Register (FC03)** für Konfigurations-Register (z.B. Lade-Limits)

Bei Unbekanntem: erst FC04 versuchen, dann FC03.

### 3. Scan starten

Klick auf **⏵ Scannen** → Bridge schickt Anweisung an den Agent → Agent
liest in Blöcken von 50 Registern (max 125 pro Modbus-Call) → Ergebnis kommt
in einer Tabelle zurück.

### 4. Werte interpretieren

Jede Register-Zeile zeigt **mehrere Interpretationen**, weil Modbus-Werte
nackte uint16 sind und der Sinn vom Skalierungsfaktor abhängt:

| Spalte | Wozu |
|---|---|
| **Hex** | Bit-Pattern — gut für Status-Flags |
| **uint16** | 0–65535, einfacher Zähler oder kleiner Wert |
| **int16** | −32768 bis +32767 — wenn negative Werte sinnvoll sind (z.B. Netz-Bezug vs Einspeisung) |
| **× 0.1** | typisch für Spannungen (227 → 22,7V) oder Frequenz |
| **× 0.01** | typisch für Strom-Werte oder kleine % |
| **× 10** | typisch für große Energie-Zähler in kWh |
| **uint32_be** | wenn Register-Paar zusammen ist (Big-Endian) — typisch für Total-Counter |
| **uint32_le** | dito Little-Endian |
| **int32_be/le** | wie oben, signed |
| **raw_hex** | Bit-Decoder für Status-Register |

### 5. AI-Identify

Bei einem unklaren Register: 🧠-Button klicken → KI bekommt die Interpretationen
+ Solinteg-Kontext zurück und sagt: *"Das könnte AC-Spannung Phase 1 sein
(228.5 V wäre plausibel) oder Frequenz × 0.01 (45.21 Hz mit unbekanntem Offset)."*

→ Hilft beim Reverse Engineering.

<!-- TODO: Screenshot AI-Identify-Result -->

## Werte zur Map hinzufügen

Wenn du sicher bist, was Register X bedeutet:

1. **Rechtsklick (oder ⋯-Menü)** auf die Register-Zeile → "Als bekannt eintragen"
2. Name (z.B. `pv1_voltage`), Skalierung (`× 0.1`), Einheit (`V`), Kategorie (`PV`)
3. Speichern → ab jetzt erscheint dieser Wert im "Bekannt?"-Feld bei jedem zukünftigen Scan
   und kann auf den Live-Pages eingebunden werden.

<!-- TODO: Screenshot Register-Anlegen-Dialog -->

## Single-Read

Wenn du nur **einen einzelnen Wert** lesen willst, ohne ganzen Range:

Sniffer → **Single-Read** Tab → Adresse + Anzahl Register + FC → Lesen.

Praktisch wenn du z.B. einen bestimmten Konfigurations-Wert nachgucken willst.

## Scan-Verlauf

Borochi speichert deine letzten **100 Scans** in der DB. Sniffer-Page → Tab
**Verlauf** zeigt alle vorigen mit Zeitstempel + Range + Anzahl gelesener Werte.

Klick auf einen Eintrag → die Tabelle wird wieder eingeblendet, ohne neu zu scannen.

→ **Weiter: [Multi-User](04-multi-user.md)**
