# Contributing

Du willst was beitragen? Sehr gut. Kurz die Spielregeln und Workflows.

## Was wir gerne sehen

- **Bug-Fixes** mit Reproduktions-Schritten in der Issue-Beschreibung
- **Neue Modbus-Register** — wenn du was im Sniffer rauskriegst, lass es uns wissen
- **Wallbox-Adapter** für andere Marken (Go-eCharger, KEBA, Wallbe, etc.)
- **Übersetzungen** — Borochi ist deutsch, English/Niederländisch/... wären cool
- **Doku-Verbesserungen** (also: dieses Repo hier)
- **UI-Polish** — Animations, Mobile-Improvements, Accessibility

## Was eher nicht

- Komplette Umbauten ohne vorherige Issue-Diskussion
- Features, die nur für **deine** spezielle Hardware-Kombi Sinn machen (besser als Plugin)
- Cloud-only-Features — Borochi muss immer auch selbst-gehostet laufen

## Workflow

1. **Issue zuerst** öffnen, kurz beschreiben was du planst
2. Maintainer geben grobes Go/No-Go ("ja gerne", "lieber so machen", "passt nicht ins Konzept")
3. **Fork** + **Feature-Branch** (`feat/wallbox-keba` oder `fix/sniffer-overflow`)
4. **Pull Request** mit:
   - Klare Beschreibung was und warum
   - Screenshots bei UI-Änderungen
   - Test-Hinweise wenn nicht offensichtlich

## Code-Style

### Python (Bridge + Agent)

- `black` für Formatting (default 88 Zeichen)
- `ruff` für Linting
- Type-Hints überall wo möglich
- Async-only (`asyncio`) — kein sync-blocking-Code

```bash
cd bridge-repo
pip install black ruff
black src/
ruff check src/
```

### HTML/CSS/JS (Frontend)

- Eingerückt mit **2 Spaces**, nicht Tabs
- Inline-Style nur für dynamische Werte (z.B. Animation-Width)
- Keine externen JS-Frameworks — pure JS reicht (React/Vue blow up das Bundle)
- `let`/`const`, kein `var`

### Markdown (Docs)

- Maximale Zeilen-Länge: **100 Zeichen**
- Bei Code-Blöcken: Sprache angeben für Highlighting
- Bilder unter `docs/assets/images/` mit beschreibendem Filenamen

## Lokale Entwicklung

### Bridge

```bash
cd bridge-repo
python -m venv .venv
source .venv/bin/activate
pip install -e .[dev]

# Bridge starten
BOROCHI_JWT_SECRET=dev BOROCHI_MASTER_SECRET=dev python -m borochi_bridge
# läuft auf :8080
```

### Agent (gegen Mock-Modbus)

Im Repo gibt's `tools/mock_modbus.py` — ein lokaler Modbus-Server mit
Fake-Solinteg-Daten. Praktisch fürs Entwickeln ohne echte Hardware.

```bash
cd agent-repo
python tools/mock_modbus.py &
python -m borochi_agent \
  BOROCHI_BRIDGE_URL=ws://127.0.0.1:8080/ws/agent \
  BOROCHI_AGENT_TOKEN=dev-token \
  BOROCHI_WAVESHARE_HOSTS=127.0.0.1
```

### Frontend

```bash
cd web-repo
python3 -m http.server 8000
# → http://localhost:8000/index.html
```

→ Localstorage-Demo-Mode an, sonst greift's auf die Bridge zu.

## Tests

### Bridge

```bash
cd bridge-repo
pytest
```

Tests müssen alle grün sein bevor PR-Merge.

### Frontend

Visuelles Smoke-Test:
```bash
# Start lokal, dann Smoke-Test
python3 -m http.server 8000 &
# Browser auf localhost:8000 → durch alle 12 Pages klicken → console-fehler?
```

Mehr automatisierte E2E-Tests sind in Arbeit (Playwright).

## Release-Prozess

1. Maintainer mergt PR auf `main`
2. CI baut Docker-Images + pusht zu GHCR
3. CHANGELOG.md aktualisieren mit Versionsnummer + Highlights
4. Tag setzen: `git tag v2.26 && git push --tags`
5. GitHub-Release-Notes mit Migration-Hinweisen
6. Doku-Site rebuilden + deployen

## Kontakt

- **Issues**: [GitHub Issues](https://github.com/digihonk/Home/issues)
- **Discussions**: [GitHub Discussions](https://github.com/digihonk/Home/discussions)
- **Security**: bitte privat per [GitHub Security Advisory](https://github.com/digihonk/borochi-docs/security/advisories/new) statt öffentliches Issue

<!-- TODO: License-Datei + Code-of-Conduct ergänzen -->

Danke für's Mitmachen 💪
