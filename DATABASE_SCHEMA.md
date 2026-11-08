# DATABASE_SCHEMA.md — PostgreSQL & Redis Datenbankarchitektur

Die vollständige SQL-DDL befindet sich in `backend/db/schema.sql`.

## Kern-Tabellen & Provenance-Metadaten
Jeder Datensatz in den Tabellen `competitions`, `teams`, `players`, `matches` und `research_claims` speichert verpflichtend:
- `source_provider` (`VARCHAR(64)`): Name des liefernden Adapters (z. B. `openligadb`, `openfootball`, `football_data_uk`, `statsbomb_open`, `thesportsdb`).
- `source_url` (`TEXT`): Ursprüngliche API- oder Dokument-URL.
- `fetched_at` (`TIMESTAMPTZ`): Exakter UTC-Abrufzeitpunkt.
- `published_at` (`TIMESTAMPTZ`): Ursprünglicher Veröffentlichungszeitpunkt.
- `season` & `competition_id`: Eindeutige Zuordnung zu Saison und Wettbewerb.
- `data_quality_score` (`0–100`), `freshness_status` und `verification_status`.

## Unveränderlichkeit von Prognosen (`prediction_versions`)
- Die Tabelle `prediction_versions` speichert für jedes Spiel fortlaufend nummerierte Prognoseversionen (`version_number`, `trigger_reason`, `model_version`, `prob_home`, `prob_draw`, `prob_away`, `expected_home_goals`, `expected_away_goals`, `data_quality_index`, `model_breakdown`, `score_matrix`, `ai_explanation`, `created_at`).
- Ein PostgreSQL `BEFORE UPDATE OR DELETE` Trigger (`trg_immutable_predictions`) verhindert technisch jede rückwirkende Manipulation historischer Prognosen.
- Ein `CHECK`-Constraint (`chk_prob_sum`) erzwingt `ABS((prob_home + prob_draw + prob_away) - 1.0) < 0.002`.

## Redis-Caching-Strategie (`backend/db/cache.py`)
- **Ebene A (Live-Spiele)**: `TTL = 45s`
- **Ebene B (Internet-Research & Verletzungen)**: `TTL = 900s` (15 Minuten)
- **Tabellen & Spielpläne**: `TTL = 3600s` (1 Stunde)
- **Historische Saisons & StatsBomb-Events**: `TTL = 86400s` (24 Stunden)
