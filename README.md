# Borochi-Docs

Bebilderte Anleitung für andere Solinteg-User die das **Borochi Home Command Center**
selbst aufsetzen wollen.

> Privates Reverse-Engineering von Marcel Ebbert. Kein offizielles Borochi-Produkt.

## Wo ist die Doku live?

- **Online lesbar:** <https://digihonk.github.io/borochi-docs/>
  (wird bei jedem Push auf `main` neu deployed)
- **Offline auf dem Pi:** wer das [Pi-Image](https://github.com/digihonk/borochi-pi-image) flasht,
  hat die Doku unter `http://borochi.local:81` automatisch dabei
- **Als Markdown im Repo:** siehe `docs/` unten

## Wie ich das hier bearbeite

Die Doku ist **Markdown**-basiert. Jedes Kapitel ist eine `.md`-Datei unter `docs/`. Fotos kommen nach `docs/assets/images/` (oder `docs/01-hardware/fotos/` für Hardware-spezifische).

### Lokal anschauen (mit Live-Reload)

```bash
# einmalig: MkDocs + Material-Theme installieren
pip install mkdocs mkdocs-material pymdown-extensions

# Live-Server starten
cd ~/Desktop/KI\ Projekte/Borochi/borochi-docs
mkdocs serve
# → http://127.0.0.1:8000
```

### Statische Site bauen

```bash
mkdocs build
# baut die fertige Site nach site/
```

### Bilder einbinden

In Markdown:
```markdown
![Waveshare Pin 13/14](../assets/images/waveshare-pin.jpg){ width=600 }
```

oder bei Hardware-spezifisch:
```markdown
![BHW-10 COM2-Port](fotos/com2-port.jpg){ width=500 }
```

## Struktur

```
borochi-docs/
├── mkdocs.yml          # Site-Config (Material-Theme, Dark-Mode, Navigation)
├── README.md           # diese Datei
├── docs/
│   ├── index.md        # Start-Seite
│   ├── 01-hardware/    # Was kaufen, wie verkabeln
│   ├── 02-software/    # Bridge + Agent installieren
│   ├── 03-config/      # Wetter / KI / Tibber / HA
│   ├── 04-bedienung/   # Wie nutze ich das?
│   ├── 05-troubleshooting/  # Häufige Probleme
│   ├── 06-entwickler/  # API, Architektur, Contributing
│   └── assets/
│       └── images/     # Logo, Screenshots, Hardware-Fotos
```

## Workflow während Hardware-Verkabelung

Marcel: Wirf während der Verkabelung einfach Fotos nach `docs/01-hardware/fotos/` (z.B. `waveshare-rj45.jpg`, `bhw10-com2.jpg`) und Notizen in die jeweiligen `.md`-Files. Die Skelette darin haben TODO-Marker wo Content rein muss.

## Was später noch dazu kommt

- [x] Hosting: GitHub Pages → `https://digihonk.github.io/borochi-docs/`
- [ ] Versions-Switcher (v2.25, v3.0, …)
- [ ] Such-Funktion mit Algolia DocSearch
- [ ] Dark-Mode-Screenshots
- [ ] Video-Embeds (YouTube) für Hardware-Walkthroughs

## TODO-Marker

Alle Stellen wo Content fehlt sind mit `<!-- TODO: ... -->` markiert. `grep -r "TODO:" docs/` listet alle offenen Punkte.
