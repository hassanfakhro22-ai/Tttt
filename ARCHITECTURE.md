# ARCHITECTURE.md — Technische Gesamtarchitektur

## 1. Drei-Ebenen-Datenarchitektur
Die Plattform trennt strikt zwischen drei miteinander verknüpften Datenebenen:

1. **Ebene A – Live-Daten (`TTL: 45s`)**
   - Laufende Spielstände, Spielminuten, Tore, Auswechslungen, Gelbe/Rote Karten und offizielle Aufstellungen.
   - Primäre kostenlose Echtzeitquelle: `OpenLigaDB` (`https://api.openligadb.de/getmatchdata/{league}`) sowie optionale Free-Tier-Keys (`API-Football`, `football-data.org`).
2. **Ebene B – Unabhängige Internetrecherche & Quellenverifizierung (`TTL: 15m`)**
   - Aktuelle Verletzungsmeldungen, Spielersperren, Trainer-Pressekonferenzen, voraussichtliche/bestätigte Aufstellungen und taktische Hinweise.
   - Austauschbare Suchanbieter (`Tavily`, `Exa`, `Google Programmable Search`, `Brave Search` + keyless `Wikimedia Search API`).
   - Striktes 4-Stufen-Verifizierungsmodell und Prompt-Injection-Sanitizer (`backend/research/internet_research.py`).
3. **Ebene C – Historische Datenbank & Backtesting-Speicher (`TTL: 24h`)**
   - **15.005 historische & aktuelle Spiele** (`2024/25`, `2025/26`, `2026/27`) über 18 europäische Ligen inkl. `HxG`, `AxG`, Schüssen, Schüssen aufs Tor, Ecken, Fouls, Karten und Marktquoten.
   - Granulare Event-Daten aus `StatsBomb Open Data` (xG, xA, Progressive Passes, Key Passes, Pressing, Tackles, Interceptions, Blocks).
   - Unveränderliche `prediction_versions`-Historie in PostgreSQL (`backend/db/schema.sql`).

## 2. Kanonische Entitätsauflösung (`CanonicalEntityResolver`)
Da unterschiedliche Anbieter abweichende Schreibweisen verwenden (z. B. `FC Bayern München`, `Bayern Munich`, `Bayern`), normalisiert `CanonicalEntityResolver` (`backend/adapters/providers.py`) alle Vereinsnamen per Unicode-NFKD-Zerlegung, Suffix-Bereinigung und Alias-Mapping auf eindeutige kanonische IDs.

## 3. Strikte Modultrennung
```
[Datenbeschaffung: backend/adapters/]
          │
          ▼
[Validierung & Sanitisierung: backend/research/ & backend/api/security.py]
          │
          ▼
[Kanonische Aufbereitung & Caching: backend/db/schema.sql & cache.py]
          │
          ▼
[Mathematische Prognose-Engine: backend/engine/models.py]
   ├── Modell A: Time-Decayed Elo
   ├── Modell B: Bivariate Poisson (7x7 Matrix)
   ├── Modell C: Dixon-Coles Low-Score Tau
   ├── Modell D: xG / xGA Rate Model
   ├── Modell E: Logistic Regression + Random Forest + XGBoost-GBDT
   └── Modell F: Out-of-Sample Calibrated Ensemble + Data Quality Index (0-100)
          │
          ▼
[Objektives KI-Erklärungsmodul & REST-API: backend/api/main.py]
          │
          ├──► [Expo React Native iOS/Android App: mobile/]
          └──► [Interactive Web Analytics Workbench: src/]
```
