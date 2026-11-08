# API_DOCUMENTATION.md — FastAPI REST-Schnittstellen

Basis-URL (lokal): `http://localhost:8000`

## Endpunkte

### 1. `GET /api/v1/health`
Prüft Systemstatus, aktives Cache-Backend (`redis` oder `in-memory-lru`) und UTC-Zeitstempel.

### 2. `GET /api/v1/sources/audit`
Liefert den vollständigen Prüfbericht aller 14 Datenquellen inkl. Tarif, Rate-Limits, Datenfeldern und rechtlichem Status.

### 3. `GET /api/v1/backtesting/report`
Liefert den unverfälschten Out-of-Sample Walk-Forward-Backtesting-Bericht (Brier Score, Log Loss, ECE, ROC-AUC, Kalibrierungskurve, Ligen-/Saison-/Favoriten-/Außenseiter-Aufschlüsselung und erlernte Ensemble-Gewichte).

### 4. `GET /api/v1/teams/{team_name}`
Liefert das kanonische Vereinsprofil inkl. aktuellem Elo-Rating, 2026/27 Saisonbilanz, `xG`, `xGA`, Schüssen, Schüssen aufs Tor, Heim-/Auswärtsbilanz und den letzten 15 Spielen.

### 5. `GET /api/v1/players?source={squad|statsbomb}&limit={1..140}`
Liefert Spielerprofile inkl. positionsabhängigem Leistungsindex (`0–100`), Stichprobenkonfidenz, Unsicherheitsmarge und explizitem Hinweis auf fehlende GPS-/Herzfrequenz-Telemetrie.

### 6. `GET /api/v1/research/search?q={query}`
Führt eine live Internetrecherche über konfigurierte Suchanbieter + Wikimedia Search API durch, sanitisiert alle Webinhalte gegen Prompt-Injection und klassifiziert Treffer in die 4 Verifizierungsstufen.

### 7. `POST /api/v1/matches/analyze`
Berechnet für ein konkretes Spiel (`home_team`, `away_team`, `league_code`, `confirmed_lineup`, `verified_home_absence_impact`, `verified_away_absence_impact`) die `1X2`-Wahrscheinlichkeiten, erwarteten Tore, Over/Under-Grenzen (`0.5–4.5`), BTTS, exakten Ergebniswahrscheinlichkeiten und den getrennt ausgewiesenen Datenqualitätsindex (`0–100`).
