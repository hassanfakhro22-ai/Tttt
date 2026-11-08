# ROADMAP.md — 12-Phasen-Entwicklungsplan

| Phase | Titel | Status | Konkrete Ergebnisse & Verifikation |
|---|---|---|---|
| **Phase 1** | Recherche & Quellenprüfung | Abgeschlossen | Vollständige Prüfung aller 9 angeforderten Quellen + kostenlose Open-Data-APIs (`DATA_SOURCES.md`) |
| **Phase 2** | Technische Gesamtarchitektur | Abgeschlossen | 3-Ebenen-Architektur, kanonische DTOs, Entity Resolution & API-Spezifikation (`ARCHITECTURE.md`, `API_DOCUMENTATION.md`) |
| **Phase 3** | Internetzugriff & Datenprovider | Abgeschlossen | Adapter für `OpenLigaDB`, `openfootball`, `football-data.co.uk`, `StatsBomb`, `TheSportsDB`, `ClubElo` + 4-Stufen-Research (`backend/research/`) |
| **Phase 4** | Globale Datenbank | Abgeschlossen | PostgreSQL-Schema (`backend/db/schema.sql`) + Redis-Caching (`backend/db/cache.py`) + 395 Klub- & 15.005 Spieldatensätze |
| **Phase 5** | Mannschafts-, Spieler- & Verletzungsanalyse | Abgeschlossen | Form (5/10/15/Heim/Auswärts), xG/xGA, Pressing/Aufbau, Positionsindex `0–100`, Ausfall-Auswirkungsrechner |
| **Phase 6** | Mathematische Prognose-Engine | Abgeschlossen | Elo, Poisson, Dixon-Coles, xG-Modell, Logistic Regression, Random Forest, XGBoost-GBDT & Kalibriertes Ensemble (`backend/engine/models.py`) |
| **Phase 7** | Walk-Forward Backtesting | Abgeschlossen | Out-of-Sample-Evaluation über 8.643 Testspiele mit Brier Score, Log Loss, ECE, ROC-AUC (`BACKTESTING_REPORT.md`) |
| **Phase 8** | KI-Erklärungsmodul | Abgeschlossen | Objektive, datengebundene Spielanalysen inkl. Gegenargumenten, Unsicherheiten & Startelf-Sensitivität |
| **Phase 9** | Mobile App (iOS & Android) | Abgeschlossen | Expo React Native TypeScript App für iPhone, iPad & Android (`mobile/`) |
| **Phase 10** | Vollständige Systemintegration | Abgeschlossen | Verknüpfung aller 3 Datenebenen in Backend-API & 15-Bereiche-Webplattform (`src/App.tsx`) |
| **Phase 11** | Qualitätssicherung & Sicherheit | Abgeschlossen | 11/11 Python Unit-/Security-/Backtest-Tests + `tsc --noEmit` + Vite Production Build |
| **Phase 12** | Produktionsvorbereitung | Bereit | `.env.example`, `SECURITY.md`, Rate-Limiting, SSRF-Schutz, App-Store/Play-Store Expo-Konfiguration (`mobile/app.json`) |
