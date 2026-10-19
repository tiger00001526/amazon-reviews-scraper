# xdata-fly · Amazon Reviews API

**Amazon-Produktbewertungen in Echtzeit scrapen.** Strukturierte Kundenrezensionen per ASIN von Amazon USA, UK, DE, FR, IT und ES abrufen — über eine einfache REST-API oder ein MCP-Tool für KI-Agenten.

**Sprachen:** [English](README.md) · [简体中文](README.zh.md) · **Deutsch** · [Français](README.fr.md) · [Italiano](README.it.md) · [Español](README.es.md) · [日本語](README.ja.md)

[Website](https://www.xdatafly.com) · [API-Doku](https://www.xdatafly.com/en/services/amazon-reviews-api) · [MCP-Doku](https://www.xdatafly.com/en/mcp) · [API-Key erhalten](https://www.xdatafly.com/en/register) · [Kontakt](mailto:tiger00001526@gmail.com)

---

## Inhaltsverzeichnis

- [Was ist die xdata-fly Amazon Reviews API?](#was-ist-die-xdata-fly-amazon-reviews-api)
- [Kernfunktionen](#kernfunktionen)
- [Unterstützte Amazon-Marktplätze](#unterstützte-amazon-marktplätze)
- [Schnellstart](#schnellstart)
- [Parameter](#parameter)
- [Beispielanfrage](#beispielanfrage)
- [Beispielantwort](#beispielantwort)
- [Antwortfelder](#antwortfelder)
- [Hinweise zur Implementierung](#hinweise-zur-implementierung)
- [Nutzung über MCP (KI-Agenten)](#nutzung-über-mcp-ki-agenten)
- [Preise](#preise)
- [FAQ](#faq)
- [Links & Kontakt](#links--kontakt)

---

## Was ist die xdata-fly Amazon Reviews API?

Die **xdata-fly Amazon Reviews API** ist ein Echtzeit-**Amazon-Produktbewertungs-Scraper** in Form eines sauberen REST-Endpunkts. Übergib eine Amazon-ASIN und erhalte strukturiertes JSON: Rezensions-ID, Sternebewertung, Titel, Rezensionstext, maskierter Autor, Verified-Purchase-Kennzeichen, Hilfreich-Stimmen, Rezensionsdatum und Land des Rezensenten.

Jede Anfrage wird **on demand in Echtzeit** von Amazon gescrapt — nicht aus einem veralteten Cache bedient. So erhältst du stets die neuesten Rezensionen, genau so, wie Käufer sie im Marktplatz sehen. Geeignet für E-Commerce-Analysen, Preis- und Sentiment-Monitoring, Marktforschung, Rezensions-Aggregation, Dropshipping-/Private-Label-Validierung und KI-Agent-Pipelines.

- **Endpunkt:** `GET https://api.xdatafly.com/v1/amazon/reviews`
- **Authentifizierung:** Header `secret-key`
- **Preis:** 1 Credit pro Seite
- **Auch als MCP-Tool** `amazon_reviews` verfügbar — siehe [MCP-Integration](#nutzung-über-mcp-ki-agenten).

## Kernfunktionen

- **Echtzeit-Scraping** — Rezensionen werden pro Anfrage live von Amazon geholt, die Daten bleiben aktuell.
- **Suche per ASIN** — jede 10-stellige Amazon-ASIN angeben und ihre Rezensionen abrufen.
- **Sternefilter** — `all_stars`, `one_star` … `five_star` oder fertige Gruppen `positive` / `critical`.
- **Sortierung** — neueste zuerst (`recent`) oder hilfreichste zuerst (`helpful`).
- **Paginierung** — `page_count` von 1 bis 10, Abrechnung pro Seite.
- **Rezensenten- & Medienfilter** — nur verifizierte Käufe, nur Medien-Rezensionen, aktuelles Format sowie optionale Bild-/Video-URLs.
- **Mehrere Marktplätze** — Amazon USA, UK, DE, FR, IT und ES (weitere auf Anfrage).
- **Konforme Ausgabe** — Autorennamen werden maskiert (z. B. `I***o`), Avatar-URLs werden nicht zurückgegeben.
- **Reichhaltiges, strukturiertes JSON** — ein einheitliches Schema über alle Marktplätze.
- **MCP-native** — dieselben Daten direkt in MCP-fähige Agenten wie Codex und WorkBuddy einspeisen.

## Unterstützte Amazon-Marktplätze

| Marktplatz | `site`-Code | Domain | Sprache der Rezensionen |
|---|---|---|---|
| 🇺🇸 USA | `amz_us` | amazon.com | Englisch |
| 🇬🇧 Großbritannien | `amz_uk` | amazon.co.uk | Englisch |
| 🇩🇪 Deutschland | `amz_de` | amazon.de | Deutsch |
| 🇫🇷 Frankreich | `amz_fr` | amazon.fr | Französisch |
| 🇮🇹 Italien | `amz_it` | amazon.it | Italienisch |
| 🇪🇸 Spanien | `amz_es` | amazon.es | Spanisch |

Du brauchst einen weiteren Marktplatz (Kanada, Japan, Australien, Mexiko, Niederlande und mehr)? **Kontaktiere den Entwickler unter [tiger00001526@gmail.com](mailto:tiger00001526@gmail.com)**, um zusätzliche Amazon-Sites freizuschalten.

## Schnellstart

### Endpunkt & Authentifizierung

```
GET https://api.xdatafly.com/v1/amazon/reviews
secret-key: sk_live_xxxxxxxxxxxxxxxx
```

Erstelle deinen API-Key in der [Konsole](https://www.xdatafly.com/en/register). Der Key wird nur einmal angezeigt — sicher aufbewahren.

### Parameter

| Name | Typ | Erforderlich | Beschreibung |
|---|---|---|---|
| `asin` | string | **erforderlich** | 10-stellige Amazon-ASIN |
| `page_count` | integer | optional | Anzahl der abzurufenden Rezensionsseiten, 1–10, Standard `1`, pro Seite abgerechnet |
| `sort` | string | optional | Sortierung: `recent` / `helpful` |
| `star` | string | optional | Sternefilter: `all_stars` / `one_star` … `five_star` / `positive` / `critical` |
| `reviewer_type` | string | optional | Rezensentenbereich: `all_reviews` / `avp_only_reviews` |
| `media_type` | string | optional | Medientyp: `all_contents` / `media_reviews_only` |
| `format_type` | string | optional | Formatbereich: `all_formats` / `current_format` |
| `site` | string | optional | Marktplatz: `amz_us` / `amz_uk` / `amz_de` / `amz_fr` / `amz_it` / `amz_es`, Standard `amz_us` |
| `include_media` | boolean | optional | Bild-/Video-URLs der Rezension einbeziehen, Standard `false` |

### Beispielanfrage

**cURL**

```bash
curl --location 'https://api.xdatafly.com/v1/amazon/reviews?asin=B0DXK317FN&page_count=1&sort=recent&star=all_stars&site=amz_us' \
  --header 'Accept: application/json' \
  --max-time 120 \
  --header 'secret-key: sk_live_xxxxxxxxxxxxxxxx'
```

**Node.js**

```javascript
const url = 'https://api.xdatafly.com/v1/amazon/reviews?asin=B0DXK317FN&page_count=1&sort=recent&star=all_stars&site=amz_us';

const res = await fetch(url, {
  headers: { 'secret-key': 'sk_live_xxxxxxxxxxxxxxxx' },
  signal: AbortSignal.timeout(120_000), // eine Seite kann 5–40s dauern; Timeout ≥60s setzen
});
const { data, usage } = await res.json();
console.log(data);  // strukturierte Rezensionen
console.log(usage); // abgerechnete Credits und Restguthaben
```

**Python**

```python
import requests

res = requests.get(
    "https://api.xdatafly.com/v1/amazon/reviews",
    params={"asin": "B0DXK317FN", "page_count": 1, "sort": "recent", "star": "all_stars", "site": "amz_us"},
    headers={"secret-key": "sk_live_xxxxxxxxxxxxxxxx"},
    timeout=120,  # eine Seite kann 5–40s dauern; Timeout ≥60s setzen
)
payload = res.json()
print(payload["data"])
print(payload["usage"])
```

### Beispielantwort

```json
{
  "code": 0,
  "data": {
    "asin": "B0DXK317FN",
    "site": "amz_us",
    "reviews": [
      {
        "asin": "B0DXK317FN",
        "body": "Battery lasts all day and the fit is comfortable...",
        "title": "Sounds great",
        "author": "I***o",
        "rating": 5,
        "country": "United States",
        "review_id": "R3C2Q5J7C0DA76",
        "has_avatar": true,
        "vine_voice": false,
        "reviewed_at": "2026-05-23",
        "helpful_votes": 12,
        "verified_purchase": true
      }
    ],
    "total_reviews": 24318,
    "pages_returned": 1,
    "parser_version": "amazon.reviews:2026.10.2"
  },
  "usage": { "balance": 975, "credits": 25 },
  "message": "ok",
  "request_id": "req_9b2c41de",
  "duration_ms": 5200
}
```

### Antwortfelder

| Feld | Typ | Beschreibung |
|---|---|---|
| `review_id` | string | Eindeutige Amazon-Rezensions-ID |
| `asin` | string | ASIN des rezensierten Produkts |
| `rating` | integer | Sternebewertung, 1–5 |
| `title` | string | Rezensionstitel |
| `body` | string | Vollständiger Rezensionstext |
| `author` | string | Maskierter Rezensentenname (z. B. `I***o`) |
| `country` | string | Land des Rezensenten |
| `reviewed_at` | string | Rezensionsdatum |
| `verified_purchase` | boolean | Ob ein verifizierter Kauf vorliegt |
| `vine_voice` | boolean | Ob es eine Vine-Rezension ist |
| `helpful_votes` | integer | Anzahl der Hilfreich-Stimmen |
| `has_avatar` | boolean | Ob upstream ein Avatar existierte (URL wird nicht zurückgegeben) |
| `total_reviews` | integer | Gesamtzahl der Rezensionen für die ASIN im Marktplatz |
| `pages_returned` | integer | Anzahl der in diesem Aufruf zurückgegebenen Seiten |
| `usage.credits` | integer | Für diesen Aufruf abgerechnete Credits |
| `usage.balance` | integer | Verbleibendes Credit-Guthaben |

## Hinweise zur Implementierung

- Eine einzelne Seite dauert typischerweise **5–40 Sekunden** (in Echtzeit von Amazon gescrapt). Setze den Client-Timeout auf **≥60s** — der übliche 30s-Standard führt zu scheinbaren Timeouts.
- **Abrechnung pro Seite:** 1 Credit × `page_count`. Eine fehlgeschlagene Seite wird nicht abgerechnet; Timeouts und Upstream-Fehler geben die reservierten Credits frei.
- `author` ist **maskiert** (zum Beispiel `I***o`), um die Ausgabe konform zu halten.
- `has_avatar` zeigt an, ob upstream ein Avatar existierte — die Bild-URL selbst wird nicht zurückgegeben.
- `include_media=true` fügt Bild- und Video-URLs der Rezension hinzu und vergrößert die Antwort deutlich.
- `total_reviews` ist die marktplatzweite Anzahl für die ASIN, nicht nur die abgerufenen Seiten.
- Nur Aufrufe mit Status **SUCCESS** werden abgerechnet. Jede Antwort meldet die verbrauchten Credits und dein Restguthaben — so lässt sich der Verbrauch ohne Rätselraten abstimmen.

## Nutzung über MCP (KI-Agenten)

Dieselben Amazon-Rezensionsdaten werden auch über **MCP** (Model Context Protocol) bereitgestellt, sodass MCP-fähige Agenten wie Codex und WorkBuddy sie direkt aufrufen können. Beide Wege teilen dieselbe Abrechnung und dasselbe Rate-Limit — ein Tool-Aufruf über MCP kostet genau so viel wie eine äquivalente API-Anfrage.

### Endpunkt & Authentifizierung

Der MCP-Endpunkt nutzt den Standard-**Streamable-HTTP**-Transport und dieselbe Authentifizierung wie die REST-API:

```
Endpoint:    https://api.xdatafly.com/mcp
Auth header: secret-key: sk_live_xxxxxxxxxxxxxxxx
```

### Verfügbare Tools

| Tool | Beschreibung | Status |
|---|---|---|
| `amazon_reviews` | Amazon-Rezensionen per ASIN abrufen: Bewertung, Titel, Text, maskierter Autor, verifizierter Kauf, Hilfreich-Stimmen, Datum und Land. | Verfügbar |
| `google_search` | Strukturierte Google-SERP-Daten. | Bald verfügbar |

### Generischer MCP-Client

Für Clients, die Remote-MCP unterstützen (Cursor, VS Code, Cherry Studio und ähnliche), füge Folgendes in die MCP-Konfigurationsdatei ein:

```json
{
  "mcpServers": {
    "xdata-fly": {
      "url": "https://api.xdatafly.com/mcp",
      "headers": { "secret-key": "sk_live_xxxxxxxxxxxxxxxx" }
    }
  }
}
```

### Codex verbinden

Die Codex CLI liest MCP-Server aus `~/.codex/config.toml`. Füge den folgenden Block hinzu und starte Codex neu:

```toml
[mcp_servers.xdata-fly]
url = "https://api.xdatafly.com/mcp"
http_headers = { "secret-key" = "sk_live_xxxxxxxxxxxxxxxx" }
```

Oder mit einem einzigen Befehl hinzufügen:

```bash
codex mcp add xdata-fly --url https://api.xdatafly.com/mcp --header "secret-key=sk_live_xxxxxxxxxxxxxxxx"
```

### WorkBuddy verbinden

WorkBuddy liest Konnektoren aus `~/.workbuddy/mcp.json` (unter Windows: `C:\Users\<you>\.workbuddy\mcp.json`). Füge den Server zu `mcpServers` hinzu, speichere und WorkBuddy kann das Tool `amazon_reviews` im Chat aufrufen:

```json
{
  "mcpServers": {
    "xdata-fly": {
      "url": "https://api.xdatafly.com/mcp",
      "headers": { "secret-key": "sk_live_xxxxxxxxxxxxxxxx" }
    }
  }
}
```

### stdio-Clients (Bridge)

Für reine stdio-Clients lässt sich der Remote-Endpunkt mit `mcp-remote` als lokaler Befehl anbinden (Node.js erforderlich):

```json
{
  "mcpServers": {
    "xdata-fly": {
      "command": "npx",
      "args": [
        "-y",
        "mcp-remote",
        "https://api.xdatafly.com/mcp",
        "--header",
        "secret-key:sk_live_xxxxxxxxxxxxxxxx"
      ]
    }
  }
}
```

### MCP-Abrechnung

MCP-Tool-Aufrufe folgen denselben Credit-Regeln wie die REST-API: Nur erfolgreiche Aufrufe werden abgerechnet, und jeder Fehler (falsche Parameter, unzureichendes Guthaben, Upstream- oder Parse-Fehler) gibt die reservierten Credits automatisch frei. Jede Antwort meldet die abgerechneten Credits und dein Restguthaben.

## Preise

- **1 Credit pro Seite** Amazon-Rezensionen.
- Die Pakete reichen von einem kleinen Testpaket bis zu einem Enterprise-Paket — siehe [Preise](https://www.xdatafly.com/en/pricing).
- Nutzungsbasierte Abrechnung; nur erfolgreiche Aufrufe werden berechnet.

## FAQ

**Was ist die xdata-fly Amazon Reviews API?**
Eine Echtzeit-API, die Amazon-Produktbewertungen per ASIN scrapt und strukturiertes JSON zurückgibt — nutzbar über REST oder MCP.

**Sind die Rezensionsdaten in Echtzeit?**
Ja. Die Rezensionen werden bei jeder Anfrage on demand von Amazon gescrapt — nichts wird aus einem veralteten Cache geliefert.

**Welche Amazon-Marktplätze werden unterstützt?**
Amazon USA, UK, Deutschland, Frankreich, Italien und Spanien (`amz_us`, `amz_uk`, `amz_de`, `amz_fr`, `amz_it`, `amz_es`). Für weitere Marktplätze kontaktiere [tiger00001526@gmail.com](mailto:tiger00001526@gmail.com).

**Wie werden Anfragen abgerechnet?**
Pro Seite: 1 Credit pro abgerufener Seite. Fehlgeschlagene Seiten werden nicht abgerechnet, und nur erfolgreiche Aufrufe werden berechnet.

**Kann ich es mit KI-Agenten / LLM-Tools nutzen?**
Ja — als MCP-Tool `amazon_reviews`, kompatibel mit Codex, WorkBuddy und jedem Remote-MCP-Client.

**Werden vollständige Kundennamen oder Avatare zurückgegeben?**
Nein. Autorennamen werden maskiert (z. B. `I***o`) und Avatar-URLs werden nicht zurückgegeben, damit die Ausgabe konform bleibt.

## Links & Kontakt

- **Website:** https://www.xdatafly.com
- **Amazon Reviews API-Doku:** https://www.xdatafly.com/en/services/amazon-reviews-api
- **MCP-Doku:** https://www.xdatafly.com/en/mcp
- **API-Key erhalten:** https://www.xdatafly.com/en/register
- **Preise:** https://www.xdatafly.com/en/pricing
- **Kontakt / individuelle Marktplätze:** [tiger00001526@gmail.com](mailto:tiger00001526@gmail.com)

---

Schlagwörter: Amazon Reviews API, Amazon Produktbewertungen Scraper, Amazon Rezensionen Echtzeit, Rezensionen per ASIN abrufen, Amazon US Rezensionen, Amazon UK Rezensionen, Amazon Deutschland Rezensionen, Amazon Frankreich Rezensionen, Amazon Italien Rezensionen, Amazon Spanien Rezensionen, verifizierter Kauf, MCP Amazon Reviews, E-Commerce Rezensionsanalyse.
