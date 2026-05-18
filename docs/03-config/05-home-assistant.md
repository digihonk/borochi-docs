# Home Assistant

Wenn du **Home Assistant** für andere Smart-Home-Sachen nutzt, kann Borochi
seine Daten dorthin **pushen**, damit du in HA-Dashboards/Automationen auch
PV/Batterie-Werte verwenden kannst.

!!! info "Wichtig: Daten-Richtung"
    Borochi **pusht** in HA — du **liest** in HA. Das ist die richtige
    Richtung, weil Borochi der Single-Source-of-Truth für Modbus-Daten ist.
    Wenn du HA-Solinteg-Integration parallel betreibst, könnten sich
    beide um die Modbus-Verbindung streiten. Also: **nur eines von beiden**.

## Was Borochi nach HA pusht

Standardmäßig folgende Sensoren:

| HA-Entity | Wert | Update |
|---|---|---|
| `sensor.borochi_pv_power` | aktuelle PV-Leistung in W | 5s |
| `sensor.borochi_battery_soc` | Batterie-Füllstand in % | 5s |
| `sensor.borochi_battery_power` | Lade/Entladeleistung in W | 5s |
| `sensor.borochi_grid_power` | Netz +/− in W | 5s |
| `sensor.borochi_grid_today_in` | heute eingespeist in kWh | 1min |
| `sensor.borochi_grid_today_out` | heute bezogen in kWh | 1min |
| `sensor.borochi_consumption` | Hausverbrauch in W | 5s |
| `sensor.borochi_autarky` | Autarkie-Quote in % (gleitend) | 1min |
| `sensor.borochi_pv_forecast_today` | PV-Prognose in kWh | 10min |

<!-- TODO: Screenshot HA mit Borochi-Sensoren -->

## Schritt 1: HA-Token erzeugen

In Home Assistant:

1. Profil (unten links Avatar klicken) → **Sicherheit** → **Long-Lived
   Access Tokens** → "Token erstellen"
2. Name: `Borochi-Push`
3. Token kopieren — **du siehst ihn nur einmal!**

<!-- TODO: Screenshot HA-Token-Erzeugung -->

## Schritt 2: In Borochi konfigurieren

Settings → Integrationen → **Home Assistant**:

| Feld | Beispiel |
|---|---|
| HA-URL | `https://homeassistant.local:8123` oder `http://192.168.178.158:8123` |
| Long-Lived Token | aus Schritt 1 |
| Push-Intervall | `5` (Sekunden) — schneller = mehr Traffic |
| Entity-Prefix | `borochi_` (Default) |

→ **Speichern** + **Push-Test** klicken.

<!-- TODO: Screenshot HA-Settings -->

Bei Erfolg siehst du in HA → Entwicklertools → Zustand:
`sensor.borochi_pv_power` mit aktuellem Wert.

## Schritt 3: Lovelace-Karte

So sieht's in HA aus:

```yaml
type: vertical-stack
cards:
  - type: gauge
    entity: sensor.borochi_battery_soc
    min: 0
    max: 100
    severity:
      green: 50
      yellow: 20
      red: 0

  - type: history-graph
    title: PV vs Verbrauch (24h)
    entities:
      - sensor.borochi_pv_power
      - sensor.borochi_consumption

  - type: glance
    entities:
      - entity: sensor.borochi_grid_today_in
        name: Eingespeist
      - entity: sensor.borochi_grid_today_out
        name: Bezogen
      - entity: sensor.borochi_autarky
        name: Autarkie
```

<!-- TODO: Screenshot Lovelace-Karte -->

## Verschlüsselung

Borochi speichert deinen HA-Token **AES-verschlüsselt** in der Bridge-DB.
Wer Bridge-Backups hat, kann den Token ohne `BOROCHI_MASTER_SECRET` nicht lesen.

## Wenn HA-Push nicht funktioniert

| Symptom | Lösung |
|---|---|
| `401 Unauthorized` | Token abgelaufen oder falsch → in HA neuen erzeugen |
| `connection refused` | HA-URL falsch oder HA nicht erreichbar |
| `entity_id invalid` | Prefix-Feld leer oder Sonderzeichen drin? |
| Werte kommen in HA an, aber dauerhaft `unknown` | Push-Intervall < 1s? → höher |

## HA-Lebenslauf-Statistiken aktivieren

In HA → Konfiguration → Statistiken → Borochi-Sensoren auswählen → "Mittelwert"
oder "Summen" aktivieren. Dann erscheinen sie auch in der HA-Energie-Übersicht.

→ **Konfiguration komplett. Weiter zu [Bedienung](../04-bedienung/01-uebersicht.md)**
