# KI Football Intelligence — Wissenschaftliche Fußballanalyse- & Prognoseplattform

Eine modulare, datengetriebene Plattform für globale Fußballanalyse, Spielerleistungsbewertung, taktische Matchup-Auswertung, unabhängige Internetrecherche mit 4-Stufen-Quellenverifizierung sowie kalibrierte Out-of-Sample-Spielprognosen.

## Architekturüberblick
- **Backend (`backend/`)**: Python 3.11, FastAPI, Pydantic, PostgreSQL (`backend/db/schema.sql`), Redis-Caching mit In-Memory-Fallback (`backend/db/cache.py`), SSRF- & Rate-Limit-Schutz (`backend/api/security.py`).
- **Mathematische Prognose-Engine (`backend/engine/models.py`)**:
  - **Modell A**: Zeitgewichtetes Elo-Rating mit Heimvorteil und Tordifferenz-Multiplikator
  - **Modell B**: Bivariate Poisson-Torverteilung ($7 \times 7$ Ergebnismatrix, Over/Under `0.5–4.5`, BTTS, Clean Sheet, Win to Nil, Halbzeitmodell)
  - **Modell C**: Dixon-Coles-Korrektur $\tau_{\lambda,\mu}(x,y)$ für niedrige Torergebnisse $(0,0), (1,0), (0,1), (1,1)$
  - **Modell D**: Expected-Goals (`xG` / `xGA`) & Chancenqualitäts-Ratenmodell
  - **Modell E**: Machine Learning (*Multinomial Logistic Regression*, *Random Forest*, *Gradient Boosted Decision Trees / XGBoost*)
  - **Modell F**: Out-of-Sample Log-Loss-optimiertes Ensemble mit Platt-/Temperature-Kalibrierung
- **Walk-Forward Backtesting (`backend/backtesting/evaluator.py`)**: Verifiziert auf **15.005 realen Spielen** über 18 europäische Ligen (`2024/25–2026/27`, **8.643 Out-of-Sample-Testspiele** ohne Data Leakage).
- **Mobile App (`mobile/`)**: React Native + Expo + TypeScript für iPhone, iPad, Android-Smartphones und Android-Tablets (Dunkles & Helles Design, responsives Tablet-Grid).
- **Interactive Web Platform (`src/`)**: Vollständige 15-Bereiche-Analyseplattform mit Live-Synchronisation (`OpenLigaDB`, `openfootball/football.json`, `StatsBomb Open Data`, `TheSportsDB`, `Wikimedia Research`) und clientseitiger Echtzeit-Berechnung aller mathematischen Modelle.

## Schnellstart

### 1. Python-Backend & Tests
```bash
pip install -r backend/requirements.txt
python3 -m unittest discover -s backend/tests -v
python3 backend/ingest_and_backtest.py
uvicorn backend.api.main:app --host 0.0.0.0 --port 8000
```

### 2. Web-Plattform
```bash
npm install
npm run dev
npm run build
```

### 3. Mobile App (iOS & Android via Expo)
```bash
cd mobile
npm install
npx expo start
```
