# Einkaufsliste

Was du brauchst, **zusätzlich** zu deiner Solinteg-Anlage.

## Pflicht

### 1× oder 2× Waveshare RS485-to-ETH

Eine pro Wechselrichter. Bei **2 BHW-10** brauchst du **2 Waveshares**, weil
beide WR die gleiche Modbus-Adresse `1` haben und über separate IPs angesprochen
werden müssen.

| Produkt | Preis | Link |
|---|---|---|
| Waveshare RS485 to ETH (B) | ~30 € | <!-- TODO: Amazon-Link --> |
| Stromversorgung 5V/2A USB-C oder Hohlstecker | meist dabei | — |

!!! tip "Welches Modell genau?"
    Es muss das **RS485-zu-Ethernet-Gateway** sein, **nicht** das WLAN-Modell
    (das ist langsamer und stört). Suche nach "Waveshare RS485 ETH" oder
    "Waveshare 18441" als Artikelnummer.

<!-- TODO: Foto vom Waveshare-Karton + Inhalt -->

### RS485-Kabel (verdrillte Doppelader)

Pro Waveshare → BHW-10 brauchst du **2 Adern**:
- A (data+)
- B (data−)

Empfohlen: **abgeschirmte verdrillte Leitung** (CAT5e geht zur Not, besser
Steuerleitung wie LIYCY 2×0,5mm² oder einfach 2 Adern aus einem CAT-Kabel).

Länge: so kurz wie möglich, max. 5m bei diesem Setup. Bei längeren Strecken
musst du dich um Terminierung kümmern (120Ω-Widerstand am Ende).

<!-- TODO: Foto vom verwendeten Kabel -->

## Optional / wenn du's komfortabler willst

### Hutschienen-Klemmen

Wenn du das Ganze ordentlich in einen Verteilerschrank baust, hilfreich:

- 4× Wago-Klemmen 221 (2-fach) für A/B-Verteilung
- 1× Hutschiene 35mm mit Endklammern
- 1× kleines Hutschienen-Netzteil 5V/2A für Waveshare (statt USB-Steckernetzteil)

### Patchkabel + Switch-Port

Du brauchst pro Waveshare **einen freien Ethernet-Port** im Heimnetz.
Wenn dein Router nahe genug am Wechselrichter ist → Patchkabel direkt.
Sonst: kleiner unmanaged Switch (5-Port, ~15 €).

### Beschriftung

Drucke dir ein paar Mini-Labels:
- `WR1-A`, `WR1-B`, `WR2-A`, `WR2-B` für die Adern
- `Waveshare-1 → 192.168.178.180`, `Waveshare-2 → 192.168.178.181`

In 6 Monaten weißt du sonst nicht mehr, welche Ader wo hin gehört.

## Lieferzeit

- Waveshare: 2-5 Tage (Amazon Prime) oder direkt von Waveshare (China, 2 Wochen)
- Kabel: aus dem Baumarkt oder über Amazon

→ **Weiter: [Waveshare-Setup](02-waveshare-setup.md)**
