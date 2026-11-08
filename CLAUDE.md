# CLAUDE.md — Projektregeln & Architektur-Invarianten

## Projektidentität
**KI Football Intelligence** ist eine wissenschaftliche, mathematisch nachvollziehbare Fußballanalyse-, Spieleranalyse- und Prognoseplattform (Python FastAPI + PostgreSQL + Redis + Expo React Native iOS/Android + Interactive Web Workbench).

## Verbindliche Entwicklungsregeln (Strikte Invarianten)
1. **Keine erfundenen Daten**: Niemals Spielergebnisse, xG-Werte, Verletzungen, Sperren, Startelf-Aufstellungen oder Prognosegenauigkeiten erfinden. Alle Kennzahlen stammen aus verifizierten Open-Data-/API-Quellen oder nachvollziehbaren mathematischen Berechnungen.
2. **Kein Data Leakage**: Bei historischen Trainings-, Validierungs- und Backtesting-Läufen dürfen zum Zeitpunkt $t$ ausschließlich Informationen mit Zeitstempel $t' < t$ verwendet werden.
3. **Keine willkürlichen Modellgewichtungen**: Ensemble-Gewichtungen $(w_{\text{Elo}}, w_{\text{DC}}, w_{\text{xG}}, w_{\text{LR}}, w_{\text{RF}}, w_{\text{XGB}})$ sowie Platt-/Temperature-Kalibrierung werden ausschließlich durch Minimierung des Out-of-Sample Log-Loss auf zeitlich getrennten Validierungsdaten bestimmt.
4. **Strikte Trennung von Siegwahrscheinlichkeit und Datenqualität**: Der Datenqualitätsindex (`0–100`) wird unabhängig von den `1X2`-Wahrscheinlichkeiten berechnet und weist fehlende Felder (z. B. fehlende Startelf-Bestätigung oder fehlende GPS-Telemetrie) transparent aus.
5. **Ehrliche Fitness- & Telemetrie-Kennzeichnung**: Da kostenlose öffentliche Fußball-APIs keine GPS-, Herzfrequenz- oder Sprintdaten bereitstellen, werden ausschließlich indirekte Belastungsindikatoren (Einsatzminuten, Startelfeinsätze, Auswechslungen, Spielabstände) berechnet und fehlende physische Telemetrie explizit ausgewiesen.
6. **Quellenverifizierung & Prompt-Injection-Schutz**: Externe Web-Inhalte werden strikt als untrusted data sanitisiert (`backend/research/internet_research.py`). Nur Stufe 1 (*Offiziell bestätigt*) und Stufe 2 (*Von mehreren unabhängigen Quellen bestätigt*) dürfen Modellparameter beeinflussen.
7. **Rechtskonforme Datenbeschaffung**: Ausschließlich kostenlose, rechtmäßig nutzbare Schnittstellen (`OpenLigaDB`, `openfootball/football.json`, `football-data.co.uk`, `StatsBomb Open Data`, `TheSportsDB`, `ClubElo`, sowie optionale Free-Tier-Keys für `football-data.org`, `API-Football`, `Sportmonks`, `Tavily`, `Exa`, `Google CSE`). Kein unerlaubtes Scraping von Portalen mit entgegenstehenden AGB (`SofaScore`, `FBref`, `Understat`, `Transfermarkt`, `DataMB` liegen ausschließlich als austauschbare Adapter-Stubs vor).
8. **Unveränderliche Prognoseversionen**: Historische Prognosen in `prediction_versions` werden niemals rückwirkend verändert; neue Informationen (z. B. offizielle Startelf 60 Minuten vor Anpfiff) erzeugen eine neue Version mit Zeitstempel.

## Verifizierte Claude-Code-Plugins & MCP-Architektur
- Aktive Marketplace-Integration: `davepoon/buildwithclaude` (`all-agents@buildwithclaude` — 117 spezialisierte Subagenten).
- Empfohlene MCP- & Plugin-Erweiterungen: `Context7` (aktuelle FastAPI/Expo/Scikit-learn-Dokumentation), `GitHub` (Repository- & Open-Data-Synchronisation), `Playwright` (E2E-Tests), `Pyright LSP` & `TypeScript LSP` (statische Typprüfung), `Security Guidance` & `Code Review`.
