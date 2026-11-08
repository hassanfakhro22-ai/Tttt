# SECURITY.md — Sicherheits-, Datenschutz- & Prompt-Injection-Konzept

## 1. Serverseitige Schlüsselverwaltung
- Sämtliche optionalen API-Keys (`FOOTBALL_DATA_ORG_KEY`, `API_FOOTBALL_KEY`, `SPORTMONKS_TOKEN`, `TAVILY_API_KEY`, `EXA_API_KEY`, `GOOGLE_CSE_KEY`) werden ausschließlich in serverseitigen Umgebungsvariablen gespeichert (`.env`) und niemals an das Web- oder Mobile-Frontend ausgeliefert.

## 2. SSRF-Schutz (`backend/api/security.py`)
- `validate_outbound_url` erzwingt `https://`, prüft den Zielhost gegen eine strikte Allowlist (`ALLOWED_EXTERNAL_HOSTS`) und löst DNS-Einträge vorab auf, um private IPv4/IPv6-Netze (`10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`), Loopback (`127.0.0.0/8`) und Cloud-Metadaten-Endpunkte (`169.254.169.254`) zu blockieren.

## 3. Schutz vor Prompt-Injection durch externe Webseiten
- Alle aus dem Internet abgerufenen Texte (Nachrichten, Verletzungsmeldungen, Pressekonferenzen) durchlaufen `sanitize_external_web_text` (`backend/research/internet_research.py`).
- HTML-Tags, Steuerzeichen sowie typische Prompt-Injection-Muster (`ignore previous instructions`, `<|im_start|>`, `override probability`) werden entfernt bzw. neutralisiert.
- Externe Webinhalte werden dem KI-Erklärungsmodul niemals als Systemanweisungen übergeben, sondern ausschließlich als strukturierte, sanitierte Faktenobjekte.

## 4. API-Härtung, Rate-Limiting & SQL-Injection-Schutz
- Sliding-Window-Rate-Limiter (`90 Req/Min` pro Client-IP) in `backend/api/main.py`.
- Strikte Typ- und Längenvalidierung aller Eingaben über Pydantic v2 (`Field(min_length=..., max_length=..., ge=..., le=...)`).
- Sicherheits-Header (`X-Content-Type-Options: nosniff`, `X-Frame-Options: DENY`).
- Ausschließliche Verwendung parametrisierter SQL-Abfragen im PostgreSQL-Layer.

## 5. Datenschutz & Urheberrecht
- Es werden keine personenbezogenen Nutzerdaten ohne Einwilligung gespeichert.
- Für Datenquellen mit restriktivem Urheberrecht oder Scraping-Verbot (`SofaScore`, `FBref`, `Understat`, `Transfermarkt`, `DataMB`) erfolgt kein automatisiertes Auslesen; stattdessen werden ausschließlich offizielle APIs und CC0-/Open-Data-Quellen unter Einhaltung der Lizenzbedingungen genutzt.
