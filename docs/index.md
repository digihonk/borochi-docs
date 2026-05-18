# Borochi Home Command Center

**Premium-Dashboard für deine Solinteg-PV-Anlage.**
Tesla Mission Control × SpaceX Cockpit × Apple Pro Display — für deine eigene Solar-Anlage zu Hause.

<!-- TODO: Hero-Screenshot vom Live-Dashboard einfügen — Datei nach docs/assets/images/screenshot-overview.png, dann nächste Zeile uncomment -->
<!-- ![Borochi Dashboard](assets/images/screenshot-overview.png){ width=1200 } -->

## Was ist das?

Borochi ist ein selbstgebautes, cinematic-schönes Energie-Dashboard speziell für **Solinteg BHW-10 Hybrid-Wechselrichter** und **BES-H2 Batteriespeicher**.
Es läuft lokal, ohne Cloud-Zwang, und greift direkt per **Modbus-TCP** auf deine Wechselrichter zu.

**Was du bekommst:**

- 📊 **12 Live-Pages** — Übersicht, Energie, Solar, Batterien, Wallbox, Netz, Markt, KI, Wetter, Verlauf, System, Datenpunkte
- ⚡ **Live-Charging-Cockpit** für deine Wallbox (PV-Überschuss, Sofortladen, Preisoptimiert, Zeitplan, Manuell)
- 🌦️ **Cinematic-Wetter-Hero** mit deinem eigenen Hausfoto, das je nach Tageszeit/Wetter wechselt
- 🔌 **Modbus-Sniffer** mit KI-Identifier — falls deine Solinteg-Variante andere Register hat als unsere Standard-Map
- 🤖 **KI-Autopilot** (OpenAI / Anthropic / Gemini) für automatisches Last-Management
- 💶 **Tibber-Integration** für dynamische Strompreise + Auto-Charge bei günstigem Strom
- 🏠 **Home Assistant Push** — optional, falls du HA für andere Dinge nutzt
- 📱 **Mobile + PWA-fähig** — wird auf dem iPhone wie eine native App
- 👥 **Multi-User** mit TOTP, Rollen (Admin/Member/Viewer), 30-Tage-Sessions

## Was du brauchst

| Komponente | Aufwand |
|---|---|
| **Solinteg BHW-10** (1× oder 2×) | du hast schon |
| **BES-H2 Batteriespeicher** (1× oder 2×) | du hast schon |
| **Waveshare RS485-to-ETH Bridge** (1 pro Wechselrichter) | ~30 € pro Stück |
| **Synology NAS** oder Raspberry Pi für den Agent | du hast schon |
| **Eigener kleiner Server / VPS** für die Bridge | ~5 €/Monat oder Borochi-Cloud |
| **Domain mit HTTPS** (z.B. home.dein-name.de) | optional |
| **Etwas Linux-Mut** | wird belohnt 💪 |

## In welcher Reihenfolge?

1. **[Hardware](01-hardware/index.md)** — Kabel löten, Waveshare konfigurieren
2. **[Software](02-software/index.md)** — Bridge auf einem Server, Agent auf NAS
3. **[Konfiguration](03-config/index.md)** — Wetter, KI, Tibber anbinden
4. **[Bedienung](04-bedienung/01-uebersicht.md)** — Alles was du im Dashboard tun kannst
5. **[Troubleshooting](05-troubleshooting/01-faq.md)** — wenn was nicht läuft

## Demo & Screenshots

<!-- TODO: Demo-Link einfügen (demo.borochi.io read-only) -->

Schau dir das Live-Dashboard mit Demo-Daten an: **<https://demo.borochi.io>** *(geplant)*

## Lizenz & Support

<!-- TODO: License-Modell finalisieren (MIT/AGPL/Proprietär?) -->

- **Quellcode:** [GitHub](https://github.com/digihonk/Home) *(repo-link anpassen)*
- **Issues:** GitHub Issues für Bugs
- **Discussion:** GitHub Discussions für Fragen
- **Community:** *(Discord/Forum kommt noch)*

!!! note "Disclaimer"
    Dieses Dashboard ist ein **privates Reverse-Engineering-Projekt** von
    **Marcel Ebbert**. Der Hersteller Borochi stellt keine offizielle
    Dokumentation, keine API und keine Daten-Logger-Zugänge zur Verfügung —
    daher dieses Eigenbau-Projekt von Solar-Enthusiasten für Solar-Enthusiasten.

    **Kein offizielles Borochi-Produkt.** Keine Verbindung zu Borochi, Solinteg
    oder verbundenen Unternehmen. **Keine Garantie** auf Funktionsfähigkeit.
    Du arbeitest mit 230V-Drehstrom und teurer Hardware — übernimm die
    Verantwortung selbst. Im Zweifel: Elektriker fragen.

---

**Bereit?** → [Los geht's mit der Hardware](01-hardware/index.md)
