# DATA_SOURCES.md — Vollständige Prüfung aller Fußball- & Recherche-Datenquellen (Phase 1)

## 1. Prüfung der 9 angeforderten Fußball-Datenquellen

| Nr. | Quelle | Offizielle API? | Kostenloser Tarif? | Kostenlose Limits | Unterstützte Ligen | Spieler- & Verletzungsdaten | Historisch / Live | Rechtliche Bewertung & Integrationsstatus |
|---|---|---|---|---|---|---|---|---|
| 1 | **Sportmonks** | Ja (REST v3) | Ja (Free Plan) | Unbegrenzt auf **2 Ligen** (Dänemark Superliga & Schottland Premiership); bezahlt ab €29/Monat | 2 im Free Plan (2.200+ kostenpflichtig) | Basisdaten in 2 Free-Ligen; xG/Pressure nur kostenpflichtig | Ja / Ja | **Optionaler Free-Key-Adapter** (`SPORTMONKS_TOKEN`), für Top-5-Ligen im Free Plan ungeeignet |
| 2 | **API-Football** | Ja (REST v3) | Ja (Free Tier) | **100 Anfragen/Tag** (10 Req/Min), alle Endpunkte ohne Kreditkarte | 1.200+ Ligen weltweit | Ja: Saison-/Spielstats, Transfers, `/injuries` & `/sidelined` | Ja / Ja (15s) | **Aktiver Free-Key-Adapter** (`API_FOOTBALL_KEY`) mit striktem Redis-Caching |
| 3 | **StatsBomb** | Ja (GitHub Open Data JSON) | Ja (100% kostenlos) | Unbegrenzt (GitHub Open Data mit Quellenangabe) | Ausgewählte Saisons: Bundesliga, PL, LaLiga, Serie A, Ligue 1, UCL, WM, EM | Ja: Event-Level xG, xA, Progressive Passes, Key Passes, Pressing, Tackles, Blocks | Ja / Nein | **Aktiv integriert (keyless)** für granulare Spieler- & Taktikmetriken |
| 4 | **Football API (`football-data.org`)** | Ja (REST v4) | Ja (Free Forever) | **10 Anfragen/Min.** dauerhaft kostenlos | 12 Wettbewerbe (BL1, PL, PD, SA, FL1, CL, DED, PPL, ELC, BSA, WC, EC) | Nur Top-Torjäger im Free Tier; Verletzungen kostenpflichtig | Aktuelle Saison / ~1 Min. | **Aktiver Free-Key-Adapter** (`FOOTBALL_DATA_ORG_KEY`) |
| 5 | **SofaScore** | Nein (nur interne Web-API) | Nein | Keine öffentliche API | Global | Ja (nur Web-Frontend) | Ja / Ja | **Nicht integriert (Adapter-Stub)**: AGB untersagen automatisiertes Scraping & kommerzielle Nutzung |
| 6 | **FBref** | Nein | Nein | Bot-Policy blockiert >10 Req/Min; Opta-Daten seit Jan 2026 eingeschränkt | Global | Historische Tabellen | Ja / Nein | **Nicht integriert (Adapter-Stub)**: Sports-Reference-Nutzungsbedingungen untersagen konkurrierende Web-Tools |
| 7 | **Understat** | Nein | Nein | Keine offizielle API | Top-6-Ligen Europas | Schuss-xG | Ja / Nein | **Nicht integriert (Adapter-Stub)**: Kein offizieller API-Zugang; xG stattdessen rechtmäßig über `football-data.co.uk` & `StatsBomb` |
| 8 | **Transfermarkt** | Nein | Nein | Keine kostenlose öffentliche API | 1.300+ Ligen | Marktwerte, Verträge, Verletzungshistorie | Ja / Nein | **Nicht integriert (Adapter-Stub)**: AGB untersagen automatisiertes Auslesen ohne Lizenz |
| 9 | **DataMB** | Nein | Freemium Web-Tool (Pro €9,99/Monat) | Keine öffentliche REST-API | Top-7-Ligen (Web) / 40+ (Pro) | Per-90-Perzentile | Ja / Nein | **Nicht integriert (Adapter-Stub)**: Proprietäres Visualisierungstool ohne offene API |

---

## 2. Verifizierte kostenlose & rechtmäßig nutzbare Schnittstellen (Aktiv im Produktivbetrieb)

1. **OpenLigaDB (`https://api.openligadb.de`)** — *100% kostenlos, ohne API-Key, CORS aktiviert*
   - Liefert Live-Spielstände, Spieltage, Anstoßzeiten, Torschützen, Halbzeit-/Endergebnisse, Tabellen und Vereinslogos für **Bundesliga (`bl1`), 2. Bundesliga (`bl2`), 3. Liga (`bl3`), DFB-Pokal (`dfb`), UEFA Champions League (`ucl`), LaLiga (`la1`), Nations League (`unl`) und FIFA WM (`wm26`)**.
2. **openfootball (`https://raw.githubusercontent.com/openfootball/football.json` & `worldcup.json`)** — *CC0 1.0 Public Domain, ohne API-Key, CORS aktiviert*
   - Liefert vollständige Spielpläne, Ergebnisse, Startelf-Aufstellungen, Auswechselbänke, Wechselminuten, Torschützen, Gelbe/Rote Karten und Schiedsrichter über **20+ Ligen** (`2010/11` bis `2026/27`) sowie die **FIFA WM 2026**.
3. **football-data.co.uk (`https://www.football-data.co.uk/mmz4281/{season}/{div}.csv`)** — *Kostenloser öffentlicher Forschungs-Download*
   - Liefert für **18 europäische Ligen** (`2024/25`, `2025/26` und aktuelle Saison `2026/27`) vollständige Spielstatistiken inkl. **Expected Goals (`HxG`, `AxG`)**, **Schüssen (`HS`, `AS`)**, **Schüssen aufs Tor (`HST`, `AST`)**, **Ecken (`HC`, `AC`)**, **Fouls (`HF`, `AF`)**, **Karten (`HY`, `AY`, `HR`, `AR`)**, Halbzeitständen und Marktquoten.
4. **StatsBomb Open Data (`https://github.com/statsbomb/open-data`)** — *Kostenlos mit Quellenangabe, CORS aktiviert*
   - Liefert granulare Event-Daten auf Profi-Niveau (xG, xA, Progressive Passes, Key Passes, Vertikale Pässe, Lange Pässe, Flanken, Ballverluste, Ballgewinne, Pressing-Aktionen, Tackles, Interceptions, Blocks, Klärungsaktionen, Luftzweikämpfe).
5. **TheSportsDB (`https://www.thesportsdb.com/api/v1/json/123/`)** — *Offizieller kostenloser Test-Key `123` (30 Req/Min), CORS aktiviert*
   - Liefert globale Ligen, Vereinswappen, Stadien, Kaderlisten, Positionen, Geburtsdaten und Spielerfotos.
6. **ClubElo (`http://api.clubelo.com/{date}`)** — *Kostenlose Forschungs-API mit automatischem internem Elo-Fallback*
   - Liefert historische und tägliche Klub-Elo-Ratings. Bei HTTP-502-Ausfällen berechnet die Engine automatisch das exakte zeitgewichtete Elo-Rating über alle 15.005 verifizierten Spiele.

---

## 3. Prüfung der Internet-Suchdienste (Ebene B)

| Suchanbieter | Kostenloser Tarif (2026) | Rate Limit | Integration |
|---|---|---|---|
| **Google Programmable Search (CSE)** | **100 Suchanfragen/Tag** dauerhaft kostenlos | 10 Req/Sek. | Optional über `GOOGLE_CSE_KEY` + `GOOGLE_CSE_CX` |
| **Tavily Search API** | **1.000 Credits/Monat** kostenlos | 20 Req/Sek. | Optional über `TAVILY_API_KEY` (`backend/research/internet_research.py`) |
| **Exa Search API** | **1.000 Suchen/Monat** kostenlos | 10–20 Req/Sek. | Optional über `EXA_API_KEY` |
| **Brave Search API** | Eingeschränktes Free-Kontingent / $5 Mindestguthaben | 1 Req/Sek. | Optionaler Adapter vorbereitet |
| **Wikimedia / Wikipedia Search API** | **Unbegrenzt kostenlos, ohne API-Key**, CORS `*` | Fair-Use | **Aktiv als primärer keyless Research-Provider** |

---

## 4. Realistische Einschätzung der Grenzen kostenloser Daten
1. **Keine physische Tracking-Telemetrie**: GPS-Laufdistanzen, Sprintgeschwindigkeiten (`km/h`) und Herzfrequenzdaten sind proprietäre Klub-/Ligadaten und in keiner kostenlosen API enthalten. Die App weist dies transparent aus und nutzt stattdessen objektive **indirekte Belastungsindikatoren** (Einsatzminuten, Startelfquote, Wechsel, Spielabstände).
2. **Verzögerte Startelf-Verfügbarkeit**: Offizielle Startaufstellungen werden erst ca. 60 Minuten vor Anpfiff veröffentlicht. Vorher basiert die Prognose auf der jüngsten Einsatzhistorie und dem Datenqualitätsindex wird ein entsprechender Abzug (`-5 Punkte`) ausgewiesen.
3. **Verletzungs- & Sperrendaten**: Strukturierte Verletzungs-Endpunkte existieren kostenlos nur bei `API-Football` (100 Req/Tag). Daher kombiniert die Plattform strukturierte Disziplinardaten (Rote/Gelbe Karten aus Spielberichten) mit verifizierter Internetrecherche (Stufe 1 & 2) und schließt unbestätigte Gerüchte strikt aus der Berechnung aus.
4. **Globale Ligen-Abdeckung**: Europäische Top- und Zweitligen verfügen über **vollständige Daten** (inkl. xG, Schüssen, Ecken, Elo). Außereuropäische Ligen (MLS, Brasilien, Argentinien, Japan, Südkorea, CAF/AFC) sind im globalen Wettbewerbskatalog erfasst, werden aber je nach verfügbarer Quellentiefe ehrlich als **Eingeschränkt** oder **Unzureichend** gekennzeichnet.
