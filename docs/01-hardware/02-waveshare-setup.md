# Waveshare-Setup

Wir konfigurieren jede Waveshare-Box so, dass sie als **Modbus-TCP-Server**
auf **Port 502** im Heimnetz erreichbar ist und intern auf der RS485-Seite
mit **9600 Baud / 8N1** spricht (das ist der Standard der Solinteg BHW-10).

## Schritt 1: Auspacken + Stromversorgung

1. Waveshare aus Karton, USB-C oder Hohlstecker mit 5V-Netzteil verbinden.
2. **LAN-Kabel** in den RJ45-Port der Waveshare, anderes Ende in deinen Router/Switch.
3. Power-LED leuchtet → Bridge ist live.

<!-- TODO: Foto: Waveshare ausgepackt mit angeschlossener Stromversorgung + RJ45 -->

## Schritt 2: IP herausfinden

Die Waveshare bekommt **per default eine DHCP-IP**. Du findest sie auf zwei Wegen:

=== "Router-UI"
    Fritzbox → Heimnetz → "Waveshare" oder "RS485-ETH" suchen.

=== "Vendor-Tool"
    [VirCom Tool](https://www.waveshare.com/wiki/RS485_TO_ETH_(B)) von Waveshare laden,
    "Search Device" — findet alle Boxen im Subnet.

=== "Netscan"
    ```bash
    nmap -p 80 192.168.178.0/24 | grep -B 4 open
    ```

<!-- TODO: Screenshot vom Router/VirCom mit erkannter Waveshare -->

## Schritt 3: Web-UI öffnen

Browser auf `http://<waveshare-ip>` → Login:

- **User:** `admin`
- **Pass:** `admin`

!!! warning "Passwort ändern!"
    Erste Aktion: Passwort wechseln. Eine RS485-Bridge mit `admin/admin` im
    LAN ist eine Einladung an alles, was zufällig dein WLAN-Passwort errät.

<!-- TODO: Screenshot vom Login-Bildschirm -->

## Schritt 4: Konfiguration

### Network-Tab

| Feld | Wert |
|---|---|
| IP Mode | **Static** |
| IP | `192.168.178.180` (für WR1) bzw `.181` (WR2) |
| Subnet | `255.255.255.0` |
| Gateway | `192.168.178.1` |
| DNS | `192.168.178.1` |

→ Du willst statische IPs, weil der Borochi-Agent diese als Ziel hat.

<!-- TODO: Screenshot Network-Tab konfiguriert -->

### Serial-Tab (RS485)

| Feld | Wert |
|---|---|
| Baudrate | **9600** |
| Data Bit | 8 |
| Stop Bit | 1 |
| Parity | None |
| Flow Control | None |

<!-- TODO: Screenshot Serial-Tab konfiguriert -->

### Socket-Tab (das wichtige)

| Feld | Wert |
|---|---|
| Work Mode | **TCP Server** |
| Local Port | **502** |
| Reset | Disable |
| Link | Disable |
| Index | Disable |
| ModbusTCP | **Modbus_TCP_to_RTU** ← wichtig! |
| Multi-Connect | optional aktivieren |

!!! danger "Modbus_TCP_to_RTU vergessen"
    Wenn dieser Modus aus ist, redet die Bridge nicht Modbus-RTU mit dem
    Solinteg, sondern leitet die rohen TCP-Frames durch. Das funktioniert
    nicht. Setze ihn auf **Modbus_TCP_to_RTU**, das ist der Übersetzer.

<!-- TODO: Screenshot Socket-Tab mit Modbus_TCP_to_RTU markiert -->

### Save + Reboot

Klicke **Save** → die Box bootet neu. Nach 10 Sekunden ist sie unter der
neuen statischen IP erreichbar.

## Schritt 5: Zweite Waveshare

Falls du **2 BHW-10** hast, wiederhole alle Schritte für Waveshare-2 mit IP
`.181`. Beschrifte die Boxen physisch (Edding-Aufkleber), sonst verwechselst
du sie später.

→ **Weiter: [Verkabelung BHW-10](03-verkabelung.md)**
