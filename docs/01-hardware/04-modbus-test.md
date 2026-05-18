# Modbus-TCP-Test

Bevor wir den Borochi-Agent starten, testen wir die Verbindung **roh** mit
einem Standard-Tool. Wenn das klappt, ist alle Hardware korrekt — Software-
Probleme sind dann garantiert in der Software, nicht im Kabel.

## Tool deiner Wahl

=== "modpoll (CLI, Linux/macOS)"
    ```bash
    # Mit Homebrew
    brew install modpoll
    # oder von www.modbusdriver.com/modpoll.html laden
    ```

=== "QModMaster (GUI, alle OS)"
    Free, plattformübergreifend, Java-basiert.
    [Download](https://sourceforge.net/projects/qmodmaster/)

=== "Modbus Doctor (Windows GUI)"
    Kostenlos, einfach.
    [Download](https://www.modbusdoctor.com/)

## Der Test

Wir lesen **Register 11000** vom BHW-10. Das ist laut Solinteg-Modbus-Map
**"Active Power Total"** — die aktuelle Solar-Leistung in W.

### Mit modpoll

```bash
# WR1
modpoll -m tcp -a 1 -r 11000 -c 5 -t 4 192.168.178.180

# WR2 (falls vorhanden)
modpoll -m tcp -a 1 -r 11000 -c 5 -t 4 192.168.178.181
```

Erklärung:
- `-m tcp` — Modbus-TCP
- `-a 1` — Slave-ID 1
- `-r 11000` — Start-Register
- `-c 5` — 5 Register lesen
- `-t 4` — Funktionscode 4 (Input Register) — bei Solinteg meist `4`. Wenn leer, mit `-t 3` (Holding) probieren.
- IP am Ende

### Erfolgreiche Ausgabe

```
modpoll 3.4 - FieldTalk(tm) Modbus(R) Master Simulator

Protocol configuration: MODBUS/TCP
Slave configuration...: address = 1, start ref = 11000, count = 5
Communication.........: 192.168.178.180, port 502, t/o 1.00 s, poll rate 1000 ms
Data type.............: 16-bit register, input register table

-- Polling slave... (Ctrl-C to stop)
[11000]: 1247    ← 1247W Solar gerade
[11001]: 0
[11002]: 12
[11003]: 0
[11004]: 4521
```

→ **Wenn du Zahlen siehst, hat alles geklappt!** Solinteg redet.

<!-- TODO: Screenshot vom funktionierenden modpoll-Output -->

## Wenn nichts kommt

| Symptom | Ursache | Lösung |
|---|---|---|
| Connection refused | Waveshare nicht im Netz | Ping `.180` — antwortet? Power LED? |
| Connection timeout | Falsche IP oder Port nicht 502 | Web-UI Waveshare prüfen |
| Connection ok, Polling failed | A/B vertauscht ODER Slave-ID falsch | A/B tauschen, dann Slave-ID prüfen |
| Connection ok, alle Werte `0` | Modbus_TCP_to_RTU nicht aktiviert | Waveshare Socket-Tab |
| Werte unsinnig (riesige Zahlen) | Funktionscode falsch | `-t 3` statt `-t 4` |

### Debug-Reihenfolge

1. **Ping die Waveshare** — geht das nicht, ist's das Netz.
2. **Waveshare-Web-UI öffnen** — geht das, ist die Box live.
3. **Waveshare-Settings prüfen** — ist Port `502`, Modbus-TCP-to-RTU an, Baud 9600?
4. **A/B tauschen** — wenn 1-3 ok, ist das der nächste Test.
5. **Slave-ID 1, 2, 3 durchprobieren** — falls jemand den BHW-10 mal umgestellt hat.
6. **GND ergänzen** — bei sehr langen Strecken oder elektrisch unsauberen Umgebungen.

<!-- TODO: Foto vom Multimeter-Test der A/B-Verbindung -->

## Mehrere Register-Bereiche scannen

Spannender wird's, wenn du mal andere Bereiche pollst:

```bash
# Live-Daten Bereich
modpoll -m tcp -a 1 -r 11000 -c 50 -t 4 192.168.178.180

# Batterie-Status (laut Solinteg-Map)
modpoll -m tcp -a 1 -r 33000 -c 30 -t 4 192.168.178.180

# Konfigurations-Holding-Register
modpoll -m tcp -a 1 -r 30000 -c 20 -t 3 192.168.178.180
```

!!! tip "Du brauchst das nicht zu wissen"
    Der Borochi-Agent macht das automatisch. Aber für dein Verständnis: jedes
    Register hat eine **Adresse** (eine Zahl wie 11000) und liefert einen
    **uint16** (0–65535). Skaliert wird in der Software (z.B. `÷10` für W).

## Wenn alles funktioniert

✅ Du siehst Modbus-Werte → Hardware ist fertig konfiguriert.

→ **Weiter mit Kapitel [2. Software](../02-software/index.md)** — Bridge und Agent installieren.
