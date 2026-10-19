# xdata-fly · Amazon Reviews API

**Real-time Amazon product review scraping.** Fetch structured customer reviews by ASIN from Amazon US, UK, DE, FR, IT and ES — over a simple REST API or an MCP tool for AI agents.

**Languages:** **English** · [简体中文](README.zh.md) · [Deutsch](README.de.md) · [Français](README.fr.md) · [Italiano](README.it.md) · [Español](README.es.md) · [日本語](README.ja.md)

[Website](https://www.xdatafly.com) · [API docs](https://www.xdatafly.com/en/services/amazon-reviews-api) · [MCP docs](https://www.xdatafly.com/en/mcp) · [Get an API key](https://www.xdatafly.com/en/register) · [Contact](mailto:tiger00001526@gmail.com)

---

## Table of contents

- [What is the xdata-fly Amazon Reviews API?](#what-is-the-xdata-fly-amazon-reviews-api)
- [Key features](#key-features)
- [Supported Amazon marketplaces](#supported-amazon-marketplaces)
- [Quick start](#quick-start)
- [Parameters](#parameters)
- [Request example](#request-example)
- [Response example](#response-example)
- [Response fields](#response-fields)
- [Implementation notes](#implementation-notes)
- [Use it from MCP (AI agents)](#use-it-from-mcp-ai-agents)
- [Pricing](#pricing)
- [FAQ](#faq)
- [Links & contact](#links--contact)

---

## What is the xdata-fly Amazon Reviews API?

The **xdata-fly Amazon Reviews API** is a real-time **Amazon product review scraper** delivered as a clean REST endpoint. Give it an Amazon ASIN and it returns structured JSON: review ID, star rating, title, review body, masked author, verified-purchase flag, helpful votes, review date and reviewer country.

Every request is scraped from Amazon **on demand, in real time** — not served from a stale cache — so you always get the latest reviews exactly as shoppers see them on the marketplace. It is built for e-commerce analytics, price and sentiment monitoring, market research, review aggregation, dropshipping/private-label validation and AI agent pipelines.

- **Endpoint:** `GET https://api.xdatafly.com/v1/amazon/reviews`
- **Auth:** `secret-key` header
- **Price:** 1 credit per page
- **Also available as an MCP tool** named `amazon_reviews` — see [MCP integration](#use-it-from-mcp-ai-agents).

## Key features

- **Real-time scraping** — reviews are pulled live from Amazon per request, so data stays fresh.
- **Search by ASIN** — point at any 10-character Amazon ASIN and pull its reviews.
- **Star filter** — `all_stars`, `one_star` … `five_star`, or ready-made `positive` / `critical` buckets.
- **Sorting** — newest first (`recent`) or most helpful first (`helpful`).
- **Pagination** — `page_count` from 1 to 10, billed per page.
- **Reviewer & media filters** — verified-purchase only, media-only reviews, current format, and optional review image/video URLs.
- **Multi-marketplace** — Amazon US, UK, DE, FR, IT and ES (more on request).
- **Compliance-friendly output** — author names are masked (e.g. `I***o`) and no avatar image URLs are returned.
- **Rich, structured JSON** — one consistent schema across every marketplace.
- **MCP-native** — plug the same data into MCP-ready agents such as Codex and WorkBuddy.

## Supported Amazon marketplaces

| Marketplace | `site` code | Domain | Reviews language |
|---|---|---|---|
| 🇺🇸 United States | `amz_us` | amazon.com | English |
| 🇬🇧 United Kingdom | `amz_uk` | amazon.co.uk | English |
| 🇩🇪 Germany | `amz_de` | amazon.de | German |
| 🇫🇷 France | `amz_fr` | amazon.fr | French |
| 🇮🇹 Italy | `amz_it` | amazon.it | Italian |
| 🇪🇸 Spain | `amz_es` | amazon.es | Spanish |

Need another marketplace (Canada, Japan, Australia, Mexico, the Netherlands, and more)? **Contact the developer at [tiger00001526@gmail.com](mailto:tiger00001526@gmail.com)** to enable additional Amazon sites.

## Quick start

### Endpoint & authentication

```
GET https://api.xdatafly.com/v1/amazon/reviews
secret-key: sk_live_xxxxxxxxxxxxxxxx
```

Create your API key in the [console](https://www.xdatafly.com/en/register). Keys are shown only once, so store yours safely.

### Parameters

| Name | Type | Required | Description |
|---|---|---|---|
| `asin` | string | **required** | 10-character Amazon ASIN |
| `page_count` | integer | optional | Number of review pages to fetch, 1–10, default `1`, charged per page |
| `sort` | string | optional | Sort order: `recent` / `helpful` |
| `star` | string | optional | Star filter: `all_stars` / `one_star` … `five_star` / `positive` / `critical` |
| `reviewer_type` | string | optional | Reviewer scope: `all_reviews` / `avp_only_reviews` |
| `media_type` | string | optional | Media type: `all_contents` / `media_reviews_only` |
| `format_type` | string | optional | Format scope: `all_formats` / `current_format` |
| `site` | string | optional | Marketplace: `amz_us` / `amz_uk` / `amz_de` / `amz_fr` / `amz_it` / `amz_es`, default `amz_us` |
| `include_media` | boolean | optional | Include review image/video URLs, default `false` |

### Request example

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
  signal: AbortSignal.timeout(120_000), // a single page can take 5–40s; set timeout ≥60s
});
const { data, usage } = await res.json();
console.log(data);  // structured reviews
console.log(usage); // credits charged and remaining balance
```

**Python**

```python
import requests

res = requests.get(
    "https://api.xdatafly.com/v1/amazon/reviews",
    params={"asin": "B0DXK317FN", "page_count": 1, "sort": "recent", "star": "all_stars", "site": "amz_us"},
    headers={"secret-key": "sk_live_xxxxxxxxxxxxxxxx"},
    timeout=120,  # a single page can take 5–40s; set timeout ≥60s
)
payload = res.json()
print(payload["data"])
print(payload["usage"])
```

### Response example

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

### Response fields

| Field | Type | Description |
|---|---|---|
| `review_id` | string | Unique Amazon review ID |
| `asin` | string | The reviewed product's ASIN |
| `rating` | integer | Star rating, 1–5 |
| `title` | string | Review headline |
| `body` | string | Full review text |
| `author` | string | Masked reviewer name (e.g. `I***o`) |
| `country` | string | Reviewer's country |
| `reviewed_at` | string | Review date |
| `verified_purchase` | boolean | Whether it is a verified purchase |
| `vine_voice` | boolean | Whether it is a Vine review |
| `helpful_votes` | integer | Number of helpful votes |
| `has_avatar` | boolean | Whether an avatar existed upstream (URL not returned) |
| `total_reviews` | integer | Total reviews for the ASIN on the marketplace |
| `pages_returned` | integer | Number of pages returned in this call |
| `usage.credits` | integer | Credits charged for this call |
| `usage.balance` | integer | Remaining credit balance |

## Implementation notes

- A single page typically takes **5–40 seconds** (scraped from Amazon in real time). Set your client timeout to **≥60s** — the common 30s default will time out spuriously.
- **Charged per page:** 1 credit × `page_count`. A failed page is not charged; timeouts and upstream failures release the reserved credits.
- `author` is **masked** (for example `I***o`) to keep the response compliant.
- `has_avatar` tells you whether an avatar existed upstream — the image URL itself is not returned.
- `include_media=true` adds review image and video URLs and increases the payload size noticeably.
- `total_reviews` is the marketplace-wide count for the ASIN, not just the pages you fetched.
- Only calls that return status **SUCCESS** are charged. Every response reports the credits used and your remaining balance, so you can reconcile spend without guessing.

## Use it from MCP (AI agents)

The same Amazon review data is exposed over **MCP** (Model Context Protocol), so MCP-ready agents such as Codex and WorkBuddy can call it directly. Both paths share the same billing and rate-limit pipeline — a tool call over MCP costs exactly what an equivalent API request costs.

### Endpoint & auth

The MCP endpoint speaks the standard **Streamable HTTP** transport and uses the same auth as the REST API:

```
Endpoint:    https://api.xdatafly.com/mcp
Auth header: secret-key: sk_live_xxxxxxxxxxxxxxxx
```

### Available tools

| Tool | Description | Status |
|---|---|---|
| `amazon_reviews` | Fetch Amazon reviews by ASIN: rating, title, body, masked author, verified purchase, helpful votes, date and country. | Available |
| `google_search` | Structured Google SERP data. | Coming soon |

### Generic MCP client

For clients that support remote MCP (Cursor, VS Code, Cherry Studio and friends), add the following to the MCP config file:

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

### Connect Codex

Codex CLI reads MCP servers from `~/.codex/config.toml`. Add the block below and restart Codex:

```toml
[mcp_servers.xdata-fly]
url = "https://api.xdatafly.com/mcp"
http_headers = { "secret-key" = "sk_live_xxxxxxxxxxxxxxxx" }
```

Or add it with a single command:

```bash
codex mcp add xdata-fly --url https://api.xdatafly.com/mcp --header "secret-key=sk_live_xxxxxxxxxxxxxxxx"
```

### Connect WorkBuddy

WorkBuddy reads connectors from `~/.workbuddy/mcp.json` (on Windows: `C:\Users\<you>\.workbuddy\mcp.json`). Add the server to `mcpServers`, save, and WorkBuddy can call the `amazon_reviews` tool in chat:

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

### stdio clients (bridge)

For stdio-only clients, bridge the remote endpoint into a local command with `mcp-remote` (Node.js required):

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

### MCP billing

MCP tool calls follow the same credit rules as the REST API: only successful calls are charged, and any failure (bad parameters, insufficient balance, upstream or parse error) releases the reserved credits automatically. Each response reports the credits charged and your remaining balance.

## Pricing

- **1 credit per page** of Amazon reviews.
- Package sizes range from a small trial pack to an enterprise pack — see [pricing](https://www.xdatafly.com/en/pricing).
- Billing is usage-based; only successful calls are charged.

## FAQ

**What is the xdata-fly Amazon Reviews API?**
A real-time API that scrapes Amazon product reviews by ASIN and returns structured JSON, usable over REST or MCP.

**Is the review data real-time?**
Yes. Reviews are scraped from Amazon on demand for every request — nothing is served from a stale cache.

**Which Amazon marketplaces are supported?**
Amazon US, UK, Germany, France, Italy and Spain (`amz_us`, `amz_uk`, `amz_de`, `amz_fr`, `amz_it`, `amz_es`). For additional marketplaces, contact [tiger00001526@gmail.com](mailto:tiger00001526@gmail.com).

**How are requests billed?**
By page: 1 credit per page fetched. Failed pages are not charged, and only successful calls are billed.

**Can I use it with AI agents / LLM tools?**
Yes — it is available as the MCP tool `amazon_reviews`, compatible with Codex, WorkBuddy and any remote-MCP client.

**Do you return full customer names or avatars?**
No. Author names are masked (e.g. `I***o`) and avatar image URLs are not returned, keeping output compliant.

## Links & contact

- **Website:** https://www.xdatafly.com
- **Amazon Reviews API docs:** https://www.xdatafly.com/en/services/amazon-reviews-api
- **MCP docs:** https://www.xdatafly.com/en/mcp
- **Get an API key:** https://www.xdatafly.com/en/register
- **Pricing:** https://www.xdatafly.com/en/pricing
- **Contact / custom marketplaces:** [tiger00001526@gmail.com](mailto:tiger00001526@gmail.com)

---

Keywords: Amazon reviews API, Amazon product review scraper, real-time Amazon review data, ASIN review fetch, Amazon US reviews, Amazon UK reviews, Amazon Germany reviews, Amazon France reviews, Amazon Italy reviews, Amazon Spain reviews, verified purchase reviews, MCP Amazon reviews, e-commerce review analytics.
