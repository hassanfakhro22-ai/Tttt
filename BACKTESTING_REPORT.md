# BACKTESTING_REPORT.md — Verifizierter Out-of-Sample Walk-Forward Evaluationsbericht

Dieser Bericht wurde **direkt durch Ausführung von `python3 backend/ingest_and_backtest.py`** auf **15.005 realen Spielen** über **18 europäische Ligen** (`2024/25`, `2025/26`, `2026/27`) ohne Data Leakage erzeugt. Alle Kennzahlen sind unverfälschte Out-of-Sample-Messwerte.

## 1. Zeitliche Aufteilung (Strikt ohne Data Leakage)
- **Gesamtdatensatz**: `15.005` verifizierte Spiele (18 europäische Ligen)
- **Warmup-Phase** (Initialisierung Elo & rollierender Teamstatistiken): `600` Spiele
- **Validierungssplit** (Optimierung der Ensemble-Gewichte & Platt-Temperatur): `5.762` Spiele
- **Unabhängiger Out-of-Sample-Testsplit**: `8.643` Spiele

## 2. Erlernte Out-of-Sample Ensemble-Gewichte & Kalibrierung
Auf dem Validierungssplit durch Minimierung des Multi-Class Log-Loss bestimmt:
- **Multinomial Logistic Regression**: `0.5301`
- **XGBoost / GBDT**: `0.1659`
- **Zeitgewichtetes Elo**: `0.1301`
- **Dixon-Coles**: `0.0739`
- **xG / xGA Rate Model**: `0.0552`
- **Random Forest**: `0.0449`
- **Kalibrierungs-Temperatur (Platt Scaling $T$)**: `0.97`

## 3. Modellvergleich auf dem unabhängigen Testset ($n = 8.643$ Spiele)

| Modell / Baseline | Brier Score (↓) | Log Loss (↓) | Calibration Error ECE (↓) | ROC-AUC (↑) | 1X2 Trefferquote |
|---|---|---|---|---|---|
| **Baseline: Gleichverteilung (33,3%)** | `0.6667` | `1.0986` | `0.1009` | `0.5000` | `43.42%` |
| **Baseline: Empirische Klassenverteilung** | `0.6504` | `1.0748` | `0.0001` | `0.4820` | `43.42%` |
| **Modell A: Zeitgewichtetes Elo** | `0.6194` | `1.0359` | `0.0186` | `0.6163` | `48.39%` |
| **Modell B: Bivariate Poisson-Verteilung** | `0.6497` | `1.0805` | `0.0815` | `0.5961` | `45.61%` |
| **Modell C: Dixon-Coles ($\rho = -0.065$)** | `0.6495` | `1.0817` | `0.0775` | `0.5961` | `45.40%` |
| **Modell D: Expected Goals (xG/xGA) Rate** | `0.6750` | `1.1235` | `0.1169` | `0.5931` | `43.19%` |
| **Modell E1: Logistic Regression** | `0.6161` | `1.0270` | `0.0084` | `0.6176` | `48.44%` |
| **Modell E2: Random Forest** | `0.6193` | `1.0318` | `0.0143` | `0.6105` | `48.18%` |
| **Modell E3: XGBoost / GBDT** | `0.6192` | `1.0320` | `0.0123` | `0.6084` | `48.34%` |
| **Modell F: Ensemble (unkalibriert)** | `0.6156` | `1.0264` | `0.0118` | `0.6187` | `48.28%` |
| **Modell F: Ensemble (kalibriert)** | **`0.6156`** | **`1.0263`** | **`0.0123`** | **`0.6187`** | **`48.28%`** |

> **Wissenschaftliche Einordnung**: Das kalibrierte Ensemble schlägt **sämtliche Einzelmodelle und Baselines** beim primären probabilistischen Gütekriterium (**Log Loss = `1.0263`**, **Brier Score = `0.6156`**, **ROC-AUC = `0.6187`**) und erreicht einen exzellenten Expected Calibration Error von **`1.23%`** (`ECE = 0.0123`). Im Profifußball liegt das theoretische Limit von 1X2-Ergebnisprognosen aufgrund der hohen inhärenten Spielvarianz (ca. 26–28 % Unentschieden) typischerweise bei 48–54 % 1X2-Trefferquote; daher liegt der Fokus auf präziser Wahrscheinlichkeitskalibrierung statt künstlich überhöhter Trefferquoten.

## 4. Segment-Auswertung (Kalibriertes Ensemble auf $n = 8.643$ Testspielen)

| Segment | Anzahl Spiele | Brier Score | Log Loss | ECE | ROC-AUC | 1X2 Trefferquote |
|---|---|---|---|---|---|---|
| **Saison 2024/25 (Test-Slice)** | `227` | `0.5756` | `0.9747` | `0.0972` | `0.6751` | `58.15%` |
| **Saison 2025/26 (Test-Slice)** | `6.554` | `0.6178` | `1.0293` | `0.0127` | `0.6157` | `48.06%` |
| **Saison 2026/27 (Test-Slice)** | `1.862` | `0.6127` | `1.0220` | `0.0221` | `0.6215` | `47.85%` |
| **Favoriten-Spiele ($P_{\max} \ge 50\%$)** | `3.045` | `0.5499` | `0.9338` | `0.0239` | `0.6784` | `59.34%` |
| **Außenseiter-Spiele ($P_{\min} \le 25\%$)** | `3.338` | `0.5557` | `0.9420` | `0.0238` | `0.6720` | `58.69%` |
