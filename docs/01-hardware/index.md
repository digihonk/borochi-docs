# 1. Hardware

In diesem Kapitel geht's um alles, was du physisch in der Hand hältst:
**Waveshare-Bridges**, **RS485-Kabel**, **deine Solinteg-Wechselrichter**.

## Was du am Ende dieses Kapitels hast

- 1× Waveshare RS485-to-ETH **pro Wechselrichter** (also 1 oder 2 Stück)
- Jede Waveshare via RJ45 im Heimnetz, mit fester IP
- RS485-Verkabelung von Waveshare-Pin 13/14 zu **COM2** am BHW-10
- Erfolgreicher **Modbus-TCP-Ping** auf Port 502 → du siehst echte Register-Werte

## Werkzeug

| Was | Wofür |
|---|---|
| Schraubendreher Schlitz 2mm | Waveshare-Klemmen + WR-COM-Port |
| Cuttermesser oder Abisolierzange | Ader-Enden |
| Multimeter (optional) | Verbindung A/B testen |
| Laptop im gleichen Netz | Waveshare-Web-UI öffnen |
| Patchkabel CAT5e/6 | Waveshare → Switch |

## Was du wissen solltest, bevor du loslegst

!!! warning "Spannung am Wechselrichter"
    Der BHW-10 ist ein Hybrid-Wechselrichter mit **230V-Drehstrom**.
    Die **COM-Ports sind potentialfrei** (low-voltage), aber das Gehäuse
    drumherum ist es nicht. Wenn du das WR-Gehäuse aufmachst,
    **vorher per LSS-Schalter abklemmen**.

    Für das Borochi-Setup öffnest du das Gehäuse **nicht** —
    du nutzt nur die externen COM-Ports.

## Reihenfolge

1. [Einkaufsliste](01-bom.md) — was du noch brauchst (mit Amazon-Links)
2. [Waveshare-Setup](02-waveshare-setup.md) — Web-UI, IP, TCP-Server-Modus
3. [Verkabelung BHW-10](03-verkabelung.md) — Pin 13/14 → COM2
4. [Modbus-TCP-Test](04-modbus-test.md) — Verifikation mit modpoll/Modbus-Tester

→ **Los geht's mit der [Einkaufsliste](01-bom.md)**
