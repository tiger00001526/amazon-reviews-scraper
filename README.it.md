# xdata-fly · API Amazon Reviews

**Scraping delle recensioni prodotto Amazon in tempo reale.** Recupera recensioni clienti strutturate per ASIN da Amazon US, UK, DE, FR, IT ed ES — tramite una semplice API REST o uno strumento MCP per agenti IA.

**Lingue:** [English](README.md) · [简体中文](README.zh.md) · [Deutsch](README.de.md) · [Français](README.fr.md) · **Italiano** · [Español](README.es.md) · [日本語](README.ja.md)

[Sito web](https://www.xdatafly.com) · [Doc API](https://www.xdatafly.com/en/services/amazon-reviews-api) · [Doc MCP](https://www.xdatafly.com/en/mcp) · [Ottieni una chiave API](https://www.xdatafly.com/en/register) · [Contatto](mailto:tiger00001526@gmail.com)

---

## Indice

- [Cos'è l'API Amazon Reviews di xdata-fly?](#cosè-lapi-amazon-reviews-di-xdata-fly)
- [Funzionalità principali](#funzionalità-principali)
- [Marketplace Amazon supportati](#marketplace-amazon-supportati)
- [Avvio rapido](#avvio-rapido)
- [Parametri](#parametri)
- [Esempio di richiesta](#esempio-di-richiesta)
- [Esempio di risposta](#esempio-di-risposta)
- [Campi della risposta](#campi-della-risposta)
- [Note di implementazione](#note-di-implementazione)
- [Uso tramite MCP (agenti IA)](#uso-tramite-mcp-agenti-ia)
- [Prezzi](#prezzi)
- [FAQ](#faq)
- [Link & contatti](#link--contatti)

---

## Cos'è l'API Amazon Reviews di xdata-fly?

L'**API Amazon Reviews di xdata-fly** è uno **scraper di recensioni prodotto Amazon** in tempo reale, esposto come semplice endpoint REST. Fornisci un ASIN Amazon e ricevi JSON strutturato: ID recensione, valutazione in stelle, titolo, testo, autore mascherato, flag di acquisto verificato, voti utili, data della recensione e paese del recensore.

Ogni richiesta viene scrapata da Amazon **su richiesta, in tempo reale** — mai servita da una cache obsoleta — quindi ottieni sempre le recensioni più recenti, esattamente come le vedono gli acquirenti sul marketplace. È pensata per analisi e-commerce, monitoraggio di prezzi e sentiment, ricerche di mercato, aggregazione di recensioni, validazione dropshipping/private-label e pipeline di agenti IA.

- **Endpoint:** `GET https://api.xdatafly.com/v1/amazon/reviews`
- **Autenticazione:** header `secret-key`
- **Prezzo:** 1 credito per pagina
- **Disponibile anche come strumento MCP** `amazon_reviews` — vedi [integrazione MCP](#uso-tramite-mcp-agenti-ia).

## Funzionalità principali

- **Scraping in tempo reale** — le recensioni sono prelevate live da Amazon a ogni richiesta, i dati restano aggiornati.
- **Ricerca per ASIN** — indica qualsiasi ASIN Amazon di 10 caratteri e recupera le sue recensioni.
- **Filtro per stelle** — `all_stars`, `one_star` … `five_star`, oppure i gruppi pronti `positive` / `critical`.
- **Ordinamento** — più recenti prima (`recent`) o più utili prima (`helpful`).
- **Paginazione** — `page_count` da 1 a 10, fatturato per pagina.
- **Filtri recensore & media** — solo acquisti verificati, solo recensioni con media, formato corrente e URL di immagini/video opzionali.
- **Multi-marketplace** — Amazon US, UK, DE, FR, IT ed ES (altri su richiesta).
- **Output conforme** — i nomi degli autori sono mascherati (es. `I***o`) e nessun URL avatar viene restituito.
- **JSON ricco e strutturato** — uno schema coerente su tutti i marketplace.
- **Nativo MCP** — collega gli stessi dati ad agenti compatibili MCP come Codex e WorkBuddy.

## Marketplace Amazon supportati

| Marketplace | Codice `site` | Dominio | Lingua delle recensioni |
|---|---|---|---|
| 🇺🇸 Stati Uniti | `amz_us` | amazon.com | Inglese |
| 🇬🇧 Regno Unito | `amz_uk` | amazon.co.uk | Inglese |
| 🇩🇪 Germania | `amz_de` | amazon.de | Tedesco |
| 🇫🇷 Francia | `amz_fr` | amazon.fr | Francese |
| 🇮🇹 Italia | `amz_it` | amazon.it | Italiano |
| 🇪🇸 Spagna | `amz_es` | amazon.es | Spagnolo |

Ti serve un altro marketplace (Canada, Giappone, Australia, Messico, Paesi Bassi e altri)? **Contatta lo sviluppatore a [tiger00001526@gmail.com](mailto:tiger00001526@gmail.com)** per abilitare altri siti Amazon.

## Avvio rapido

### Endpoint & autenticazione

```
GET https://api.xdatafly.com/v1/amazon/reviews
secret-key: sk_live_xxxxxxxxxxxxxxxx
```

Crea la tua chiave API nella [console](https://www.xdatafly.com/en/register). La chiave viene mostrata una sola volta: conservala con cura.

### Parametri

| Nome | Tipo | Obbligatorio | Descrizione |
|---|---|---|---|
| `asin` | string | **obbligatorio** | ASIN Amazon di 10 caratteri |
| `page_count` | integer | opzionale | Numero di pagine di recensioni da recuperare, 1–10, default `1`, fatturato per pagina |
| `sort` | string | opzionale | Ordinamento: `recent` / `helpful` |
| `star` | string | opzionale | Filtro stelle: `all_stars` / `one_star` … `five_star` / `positive` / `critical` |
| `reviewer_type` | string | opzionale | Ambito recensore: `all_reviews` / `avp_only_reviews` |
| `media_type` | string | opzionale | Tipo di media: `all_contents` / `media_reviews_only` |
| `format_type` | string | opzionale | Ambito formato: `all_formats` / `current_format` |
| `site` | string | opzionale | Marketplace: `amz_us` / `amz_uk` / `amz_de` / `amz_fr` / `amz_it` / `amz_es`, default `amz_us` |
| `include_media` | boolean | opzionale | Includi URL di immagini/video delle recensioni, default `false` |

### Esempio di richiesta

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
  signal: AbortSignal.timeout(120_000), // una pagina può richiedere 5–40s; imposta timeout ≥60s
});
const { data, usage } = await res.json();
console.log(data);  // recensioni strutturate
console.log(usage); // crediti addebitati e saldo residuo
```

**Python**

```python
import requests

res = requests.get(
    "https://api.xdatafly.com/v1/amazon/reviews",
    params={"asin": "B0DXK317FN", "page_count": 1, "sort": "recent", "star": "all_stars", "site": "amz_us"},
    headers={"secret-key": "sk_live_xxxxxxxxxxxxxxxx"},
    timeout=120,  # una pagina può richiedere 5–40s; imposta timeout ≥60s
)
payload = res.json()
print(payload["data"])
print(payload["usage"])
```

### Esempio di risposta

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

### Campi della risposta

| Campo | Tipo | Descrizione |
|---|---|---|
| `review_id` | string | ID univoco della recensione Amazon |
| `asin` | string | ASIN del prodotto recensito |
| `rating` | integer | Valutazione in stelle, 1–5 |
| `title` | string | Titolo della recensione |
| `body` | string | Testo completo della recensione |
| `author` | string | Nome del recensore mascherato (es. `I***o`) |
| `country` | string | Paese del recensore |
| `reviewed_at` | string | Data della recensione |
| `verified_purchase` | boolean | Se è un acquisto verificato |
| `vine_voice` | boolean | Se è una recensione Vine |
| `helpful_votes` | integer | Numero di voti utili |
| `has_avatar` | boolean | Se esisteva un avatar a monte (URL non restituito) |
| `total_reviews` | integer | Totale recensioni per l'ASIN sul marketplace |
| `pages_returned` | integer | Numero di pagine restituite in questa chiamata |
| `usage.credits` | integer | Crediti addebitati per questa chiamata |
| `usage.balance` | integer | Saldo crediti residuo |

## Note di implementazione

- Una singola pagina richiede tipicamente **5–40 secondi** (scrapata da Amazon in tempo reale). Imposta il timeout del client su **≥60s** — il default comune di 30s causerà timeout spuri.
- **Addebito per pagina:** 1 credito × `page_count`. Una pagina non riuscita non viene addebitata; timeout e errori a monte rilasciano i crediti riservati.
- `author` è **mascherato** (ad esempio `I***o`) per mantenere la risposta conforme.
- `has_avatar` indica se esisteva un avatar a monte — l'URL dell'immagine stessa non viene restituito.
- `include_media=true` aggiunge URL di immagini e video delle recensioni e aumenta sensibilmente la dimensione della risposta.
- `total_reviews` è il conteggio a livello di marketplace per l'ASIN, non solo le pagine recuperate.
- Solo le chiamate con stato **SUCCESS** vengono addebitate. Ogni risposta riporta i crediti usati e il saldo residuo, così puoi riconciliare la spesa senza approssimazioni.

## Uso tramite MCP (agenti IA)

Gli stessi dati di recensioni Amazon sono esposti tramite **MCP** (Model Context Protocol), così gli agenti compatibili MCP come Codex e WorkBuddy possono chiamarli direttamente. Entrambi i percorsi condividono la stessa fatturazione e lo stesso rate-limit — una chiamata a uno strumento via MCP costa esattamente quanto una richiesta API equivalente.

### Endpoint & autenticazione

L'endpoint MCP usa il trasporto standard **Streamable HTTP** e la stessa autenticazione dell'API REST:

```
Endpoint:    https://api.xdatafly.com/mcp
Auth header: secret-key: sk_live_xxxxxxxxxxxxxxxx
```

### Strumenti disponibili

| Strumento | Descrizione | Stato |
|---|---|---|
| `amazon_reviews` | Recupera recensioni Amazon per ASIN: valutazione, titolo, testo, autore mascherato, acquisto verificato, voti utili, data e paese. | Disponibile |
| `google_search` | Dati SERP Google strutturati. | Prossimamente |

### Client MCP generico

Per i client che supportano MCP remoto (Cursor, VS Code, Cherry Studio e simili), aggiungi quanto segue al file di configurazione MCP:

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

### Collegare Codex

Codex CLI legge i server MCP da `~/.codex/config.toml`. Aggiungi il blocco sotto e riavvia Codex:

```toml
[mcp_servers.xdata-fly]
url = "https://api.xdatafly.com/mcp"
http_headers = { "secret-key" = "sk_live_xxxxxxxxxxxxxxxx" }
```

Oppure aggiungilo con un solo comando:

```bash
codex mcp add xdata-fly --url https://api.xdatafly.com/mcp --header "secret-key=sk_live_xxxxxxxxxxxxxxxx"
```

### Collegare WorkBuddy

WorkBuddy legge i connettori da `~/.workbuddy/mcp.json` (su Windows: `C:\Users\<you>\.workbuddy\mcp.json`). Aggiungi il server a `mcpServers`, salva, e WorkBuddy potrà chiamare lo strumento `amazon_reviews` in chat:

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

### Client stdio (bridge)

Per i client solo stdio, collega l'endpoint remoto a un comando locale con `mcp-remote` (richiede Node.js):

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

### Fatturazione MCP

Le chiamate agli strumenti MCP seguono le stesse regole sui crediti dell'API REST: solo le chiamate riuscite vengono addebitate, e qualsiasi errore (parametri errati, saldo insufficiente, errore a monte o di parsing) rilascia automaticamente i crediti riservati. Ogni risposta riporta i crediti addebitati e il saldo residuo.

## Prezzi

- **1 credito per pagina** di recensioni Amazon.
- I pacchetti vanno da un piccolo pacchetto di prova a un pacchetto enterprise — vedi [prezzi](https://www.xdatafly.com/en/pricing).
- Fatturazione a consumo; solo le chiamate riuscite vengono addebitate.

## FAQ

**Cos'è l'API Amazon Reviews di xdata-fly?**
Un'API in tempo reale che scrapa le recensioni prodotto Amazon per ASIN e restituisce JSON strutturato, utilizzabile via REST o MCP.

**I dati delle recensioni sono in tempo reale?**
Sì. Le recensioni vengono scrapate da Amazon su richiesta a ogni chiamata — nulla proviene da una cache obsoleta.

**Quali marketplace Amazon sono supportati?**
Amazon US, UK, Germania, Francia, Italia e Spagna (`amz_us`, `amz_uk`, `amz_de`, `amz_fr`, `amz_it`, `amz_es`). Per altri marketplace contatta [tiger00001526@gmail.com](mailto:tiger00001526@gmail.com).

**Come vengono addebitate le richieste?**
Per pagina: 1 credito per pagina recuperata. Le pagine non riuscite non vengono addebitate e solo le chiamate riuscite vengono fatturate.

**Posso usarla con agenti IA / strumenti LLM?**
Sì — è disponibile come strumento MCP `amazon_reviews`, compatibile con Codex, WorkBuddy e qualsiasi client MCP remoto.

**Restituite i nomi completi dei clienti o gli avatar?**
No. I nomi degli autori sono mascherati (es. `I***o`) e gli URL degli avatar non vengono restituiti, per garantire la conformità.

## Link & contatti

- **Sito web:** https://www.xdatafly.com
- **Doc API Amazon Reviews:** https://www.xdatafly.com/en/services/amazon-reviews-api
- **Doc MCP:** https://www.xdatafly.com/en/mcp
- **Ottieni una chiave API:** https://www.xdatafly.com/en/register
- **Prezzi:** https://www.xdatafly.com/en/pricing
- **Contatto / marketplace personalizzati:** [tiger00001526@gmail.com](mailto:tiger00001526@gmail.com)

---

Parole chiave: API recensioni Amazon, scraper recensioni prodotto Amazon, recensioni Amazon tempo reale, recupero recensioni per ASIN, recensioni Amazon US, recensioni Amazon UK, recensioni Amazon Germania, recensioni Amazon Francia, recensioni Amazon Italia, recensioni Amazon Spagna, acquisto verificato, MCP recensioni Amazon, analisi recensioni e-commerce.
