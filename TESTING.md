# TESTING.md — Test- & Backtesting-Dokumentation

## 1. Automatisierte Python-Testsuite (`backend/tests/test_engine.py`)
Ausführung:
```bash
python3 -m unittest discover -s backend/tests -v
```
Abgedeckte Testbereiche:
- **Mathematische Invarianten**: Normierung der $8 \times 8$ Dixon-Coles-/Poisson-Tormatrix ($\sum P(X=x,Y=y) = 1.0$), Summenkonsistenz $P(\text{Heimsieg}) + P(\text{Remis}) + P(\text{Auswärtssieg}) = 100\%$, Monotonie der Over/Under-Schwellen (`0.5 >= 1.5 >= 2.5 >= 3.5 >= 4.5`), Clean-Sheet-Identität, Nullsummen-Elo-Erhaltung.
- **Datenqualitäts-Entkopplung**: Nachweis, dass der Datenqualitätsindex (`0–100`) unabhängig von der Siegwahrscheinlichkeit berechnet wird und fehlende GPS-Telemetrie ausweist.
- **Positionsabhängiger Spielerindex**: Prüfung der Bayes'schen Stichproben-Shrinkage und Unsicherheitsmarge bei geringen Einsatzminuten.
- **Sicherheit & Research**: Prompt-Injection-Sanitizer, 4-Stufen-Quellenverifizierung, SSRF-Blocker, Rate-Limiter und kanonische Team-Deduplizierung.
- **Walk-Forward Backtesting & Datensatz-Integrität**: Prüfung der zeitlich strikt getrennten Evaluation ohne Data Leakage.

## 2. Frontend TypeScript & Production Build Verification
```bash
npx tsc --noEmit
npm run build
```

## 3. Reproduzierbares Daten- & Backtesting-Pipeline-Skript
```bash
python3 backend/ingest_and_backtest.py
```
Lädt 15.005 Spiele über 18 europäische Ligen, berechnet das Out-of-Sample Backtesting über 8.643 Testspiele und aktualisiert `src/data/verified_platform_data.json`.
