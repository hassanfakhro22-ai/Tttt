# MODEL_DOCUMENTATION.md — Mathematische Prognose-Engine

Implementiert in `backend/engine/models.py` und `backend/backtesting/evaluator.py`.

## 1. Modell A — Zeitgewichtetes Elo-Rating
Für Heimteam $H$ und Auswärtsteam $A$ mit aktuellen Ratings $R_H, R_A$ und Heimvorteil $\gamma = 55.0$:
$$E_H = \frac{1}{1 + 10^{-(R_H + \gamma - R_A)/400}}$$
Die empirische Remis-Wahrscheinlichkeit $P(D)$ wird über eine Gauß-Glocke um $\Delta R = 0$ modelliert ($P_{\max}(D) = 0.285$):
$$P(D) = \text{clip}\left(0.285 \cdot \exp\left(-\left(\frac{R_H + \gamma - R_A}{220}\right)^2\right),\; 0.08,\; 0.34\right)$$
$$P(H) = E_H (1 - P(D)), \qquad P(A) = (1 - E_H)(1 - P(D))$$
Nach Spielende erfolgt das Rating-Update mit Tordifferenz-Multiplikator $\ln(|g_H - g_A| + 1)$ und exponentiellem Zeitabklingen ($t_{1/2} = 180$ Tage).

## 2. Modell B & C — Bivariate Poisson-Verteilung & Dixon-Coles-Korrektur
Für erwartete Heim- und Auswärtstore $(\lambda, \mu)$ berechnet das System die vollständige $8 \times 8$-Ergebnismatrix $x, y \in \{0,\dots,7\}$:
$$P(X=x, Y=y) = \frac{1}{Z} \cdot \frac{e^{-\lambda}\lambda^x}{x!} \cdot \frac{e^{-\mu}\mu^y}{y!} \cdot \tau_{\lambda,\mu}(x, y)$$
mit der Dixon-Coles-Korrektur ($\rho = -0.065$) für niedrige Torstände:
$$\tau_{\lambda,\mu}(x,y) = \begin{cases}
1 - \lambda\mu\rho & (x=0, y=0) \\
1 + \lambda\rho & (x=0, y=1) \\
1 + \mu\rho & (x=1, y=0) \\
1 - \rho & (x=1, y=1) \\
1 & \text{sonst}
\end{cases}$$
Daraus ergeben sich exakt und summenkonsistent ($\sum_{x,y} P(X=x,Y=y) = 1$):
- **Heimsieg**: $\sum_{x > y} P(X=x, Y=y)$
- **Unentschieden**: $\sum_{x = y} P(X=x, Y=y)$
- **Auswärtssieg**: $\sum_{x < y} P(X=x, Y=y)$
- **Over/Under $k \in \{0.5, 1.5, 2.5, 3.5, 4.5\}$**: $\sum_{x+y > k} P(X=x, Y=y)$
- **Beide Teams treffen (BTTS)**: $\sum_{x \ge 1, y \ge 1} P(X=x, Y=y)$
- **Clean Sheet / Sieg ohne Gegentor / Halbzeitergebnis**

## 3. Modell D — Expected Goals (`xG` / `xGA`) & Chancenqualitätsmodell
Berechnet Angriffs- und Defensivstärken aus den rollierenden 10-Spiele-`xG`- und `xGA`-Werten relativ zum Ligenmittelwert mit Stichproben-Shrinkage für Saisonbeginne.

## 4. Modell E — Machine Learning (10 Pre-Match-Features ohne Data Leakage)
Features (ausschließlich aus Spielen $t' < t$): Elo-Differenz, rollierende Tor- & Gegentordifferenzen (10 Spiele), Heim-/Auswärtstor-Splits (8 Spiele), rollierende `xG`/`xGA`-Differenzen (10 Spiele), Punkteschnitt (5 Spiele), Schüsse aufs Tor (5 Spiele) und Regenerationszeit-Differenz.
1. **Multinomial Logistic Regression** (L2-regularisierter Gradientenabstieg mit Z-Score-Standardisierung)
2. **Random Forest Classifier** (Bootstrap-Entscheidungsbäume mit Laplace-geglätteten Blättern)
3. **Gradient Boosted Decision Trees / XGBoost** (Newton-Raphson-Boosting 2. Ordnung mit L2-Blattregularisierung)

## 5. Modell F — Out-of-Sample Ensemble & Kalibrierung
Die Kombinationsgewichte und der Platt-/Temperature-Skalierungsparameter $T$ werden **ausschließlich auf dem zeitlich getrennten Validierungssplit** durch Minimierung des Multi-Class Log-Loss bestimmt.

## 6. Positionsabhängiger Spielerleistungsindex (`0–100`)
Gewichtet Offensiv-, Aufbau-, Defensiv- und Zuverlässigkeitsmetriken spezifisch nach 8 Positionen (`GK`, `CB`, `FB`, `DM`, `CM`, `AM`, `W`, `ST`) inklusive Bayes'scher Stichproben-Shrinkage bei geringen Einsatzminuten. Strikt getrennt von fehlender GPS-/Herzfrequenz-Telemetrie.
