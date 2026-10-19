# xdata-fly · API Amazon Reviews

**Scraping des avis produits Amazon en temps réel.** Récupérez des avis clients structurés par ASIN depuis Amazon US, UK, DE, FR, IT et ES — via une API REST simple ou un outil MCP pour agents IA.

**Langues :** [English](README.md) · [简体中文](README.zh.md) · [Deutsch](README.de.md) · **Français** · [Italiano](README.it.md) · [Español](README.es.md) · [日本語](README.ja.md)

[Site web](https://www.xdatafly.com) · [Doc API](https://www.xdatafly.com/en/services/amazon-reviews-api) · [Doc MCP](https://www.xdatafly.com/en/mcp) · [Obtenir une clé API](https://www.xdatafly.com/en/register) · [Contact](mailto:tiger00001526@gmail.com)

---

## Sommaire

- [Qu'est-ce que l'API Amazon Reviews de xdata-fly ?](#quest-ce-que-lapi-amazon-reviews-de-xdata-fly-)
- [Fonctionnalités clés](#fonctionnalités-clés)
- [Places de marché Amazon prises en charge](#places-de-marché-amazon-prises-en-charge)
- [Démarrage rapide](#démarrage-rapide)
- [Paramètres](#paramètres)
- [Exemple de requête](#exemple-de-requête)
- [Exemple de réponse](#exemple-de-réponse)
- [Champs de la réponse](#champs-de-la-réponse)
- [Notes d'implémentation](#notes-dimplémentation)
- [Utilisation via MCP (agents IA)](#utilisation-via-mcp-agents-ia)
- [Tarifs](#tarifs)
- [FAQ](#faq)
- [Liens & contact](#liens--contact)

---

## Qu'est-ce que l'API Amazon Reviews de xdata-fly ?

L'**API Amazon Reviews de xdata-fly** est un **scraper d'avis produits Amazon** en temps réel, exposé sous forme d'un point de terminaison REST propre. Fournissez un ASIN Amazon et recevez un JSON structuré : identifiant d'avis, note en étoiles, titre, corps de l'avis, auteur masqué, indicateur d'achat vérifié, votes utiles, date de l'avis et pays du commentateur.

Chaque requête est scrapée depuis Amazon **à la demande, en temps réel** — jamais servie depuis un cache obsolète — vous obtenez donc toujours les avis les plus récents, exactement comme les acheteurs les voient sur la place de marché. Idéal pour l'analyse e-commerce, la surveillance des prix et du sentiment, les études de marché, l'agrégation d'avis, la validation dropshipping/private-label et les pipelines d'agents IA.

- **Point de terminaison :** `GET https://api.xdatafly.com/v1/amazon/reviews`
- **Authentification :** en-tête `secret-key`
- **Prix :** 1 crédit par page
- **Également disponible en tant qu'outil MCP** `amazon_reviews` — voir [l'intégration MCP](#utilisation-via-mcp-agents-ia).

## Fonctionnalités clés

- **Scraping en temps réel** — les avis sont récupérés en direct depuis Amazon à chaque requête, les données restent à jour.
- **Recherche par ASIN** — ciblez n'importe quel ASIN Amazon à 10 caractères et récupérez ses avis.
- **Filtre par étoiles** — `all_stars`, `one_star` … `five_star`, ou les groupes prêts à l'emploi `positive` / `critical`.
- **Tri** — les plus récents d'abord (`recent`) ou les plus utiles d'abord (`helpful`).
- **Pagination** — `page_count` de 1 à 10, facturé par page.
- **Filtres commentateur & média** — achats vérifiés uniquement, avis avec média uniquement, format actuel, et URLs d'images/vidéos en option.
- **Multi-places de marché** — Amazon US, UK, DE, FR, IT et ES (d'autres sur demande).
- **Sortie conforme** — les noms d'auteurs sont masqués (ex. `I***o`) et aucune URL d'avatar n'est renvoyée.
- **JSON riche et structuré** — un schéma cohérent sur toutes les places de marché.
- **Natif MCP** — branchez les mêmes données dans des agents compatibles MCP comme Codex et WorkBuddy.

## Places de marché Amazon prises en charge

| Place de marché | Code `site` | Domaine | Langue des avis |
|---|---|---|---|
| 🇺🇸 États-Unis | `amz_us` | amazon.com | Anglais |
| 🇬🇧 Royaume-Uni | `amz_uk` | amazon.co.uk | Anglais |
| 🇩🇪 Allemagne | `amz_de` | amazon.de | Allemand |
| 🇫🇷 France | `amz_fr` | amazon.fr | Français |
| 🇮🇹 Italie | `amz_it` | amazon.it | Italien |
| 🇪🇸 Espagne | `amz_es` | amazon.es | Espagnol |

Besoin d'une autre place de marché (Canada, Japon, Australie, Mexique, Pays-Bas, etc.) ? **Contactez le développeur à [tiger00001526@gmail.com](mailto:tiger00001526@gmail.com)** pour activer des sites Amazon supplémentaires.

## Démarrage rapide

### Point de terminaison & authentification

```
GET https://api.xdatafly.com/v1/amazon/reviews
secret-key: sk_live_xxxxxxxxxxxxxxxx
```

Créez votre clé API dans la [console](https://www.xdatafly.com/en/register). La clé n'est affichée qu'une seule fois — conservez-la précieusement.

### Paramètres

| Nom | Type | Requis | Description |
|---|---|---|---|
| `asin` | string | **requis** | ASIN Amazon à 10 caractères |
| `page_count` | integer | optionnel | Nombre de pages d'avis à récupérer, 1–10, défaut `1`, facturé par page |
| `sort` | string | optionnel | Tri : `recent` / `helpful` |
| `star` | string | optionnel | Filtre par étoiles : `all_stars` / `one_star` … `five_star` / `positive` / `critical` |
| `reviewer_type` | string | optionnel | Périmètre : `all_reviews` / `avp_only_reviews` |
| `media_type` | string | optionnel | Type de média : `all_contents` / `media_reviews_only` |
| `format_type` | string | optionnel | Périmètre de format : `all_formats` / `current_format` |
| `site` | string | optionnel | Place de marché : `amz_us` / `amz_uk` / `amz_de` / `amz_fr` / `amz_it` / `amz_es`, défaut `amz_us` |
| `include_media` | boolean | optionnel | Inclure les URLs d'images/vidéos des avis, défaut `false` |

### Exemple de requête

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
  signal: AbortSignal.timeout(120_000), // une page peut prendre 5–40s ; régler le timeout ≥60s
});
const { data, usage } = await res.json();
console.log(data);  // avis structurés
console.log(usage); // crédits facturés et solde restant
```

**Python**

```python
import requests

res = requests.get(
    "https://api.xdatafly.com/v1/amazon/reviews",
    params={"asin": "B0DXK317FN", "page_count": 1, "sort": "recent", "star": "all_stars", "site": "amz_us"},
    headers={"secret-key": "sk_live_xxxxxxxxxxxxxxxx"},
    timeout=120,  # une page peut prendre 5–40s ; régler le timeout ≥60s
)
payload = res.json()
print(payload["data"])
print(payload["usage"])
```

### Exemple de réponse

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

### Champs de la réponse

| Champ | Type | Description |
|---|---|---|
| `review_id` | string | Identifiant unique de l'avis Amazon |
| `asin` | string | ASIN du produit commenté |
| `rating` | integer | Note en étoiles, 1–5 |
| `title` | string | Titre de l'avis |
| `body` | string | Texte complet de l'avis |
| `author` | string | Nom masqué du commentateur (ex. `I***o`) |
| `country` | string | Pays du commentateur |
| `reviewed_at` | string | Date de l'avis |
| `verified_purchase` | boolean | S'agit-il d'un achat vérifié |
| `vine_voice` | boolean | S'agit-il d'un avis Vine |
| `helpful_votes` | integer | Nombre de votes utiles |
| `has_avatar` | boolean | Un avatar existait-il en amont (URL non renvoyée) |
| `total_reviews` | integer | Nombre total d'avis pour l'ASIN sur la place de marché |
| `pages_returned` | integer | Nombre de pages renvoyées dans cet appel |
| `usage.credits` | integer | Crédits facturés pour cet appel |
| `usage.balance` | integer | Solde de crédits restant |

## Notes d'implémentation

- Une page prend généralement **5 à 40 secondes** (scrapée depuis Amazon en temps réel). Réglez le timeout de votre client sur **≥60s** — le défaut courant de 30s provoquera des faux timeouts.
- **Facturation par page :** 1 crédit × `page_count`. Une page en échec n'est pas facturée ; les timeouts et échecs en amont libèrent les crédits réservés.
- `author` est **masqué** (par exemple `I***o`) pour rendre la réponse conforme.
- `has_avatar` indique si un avatar existait en amont — l'URL de l'image elle-même n'est pas renvoyée.
- `include_media=true` ajoute les URLs des images et vidéos des avis et augmente sensiblement la taille de la réponse.
- `total_reviews` est le nombre d'avis à l'échelle de la place de marché pour l'ASIN, pas seulement les pages récupérées.
- Seuls les appels renvoyant le statut **SUCCESS** sont facturés. Chaque réponse indique les crédits utilisés et votre solde restant, pour un suivi sans approximation.

## Utilisation via MCP (agents IA)

Les mêmes données d'avis Amazon sont exposées via **MCP** (Model Context Protocol), afin que les agents compatibles MCP comme Codex et WorkBuddy puissent les appeler directement. Les deux voies partagent la même facturation et le même rate-limit — un appel d'outil via MCP coûte exactement le même prix qu'une requête API équivalente.

### Point de terminaison & authentification

Le point de terminaison MCP utilise le transport standard **Streamable HTTP** et la même authentification que l'API REST :

```
Endpoint:    https://api.xdatafly.com/mcp
Auth header: secret-key: sk_live_xxxxxxxxxxxxxxxx
```

### Outils disponibles

| Outil | Description | Statut |
|---|---|---|
| `amazon_reviews` | Récupérer les avis Amazon par ASIN : note, titre, corps, auteur masqué, achat vérifié, votes utiles, date et pays. | Disponible |
| `google_search` | Données SERP Google structurées. | Bientôt disponible |

### Client MCP générique

Pour les clients compatibles MCP distant (Cursor, VS Code, Cherry Studio et autres), ajoutez ce qui suit dans le fichier de configuration MCP :

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

### Connecter Codex

Codex CLI lit les serveurs MCP depuis `~/.codex/config.toml`. Ajoutez le bloc ci-dessous puis redémarrez Codex :

```toml
[mcp_servers.xdata-fly]
url = "https://api.xdatafly.com/mcp"
http_headers = { "secret-key" = "sk_live_xxxxxxxxxxxxxxxx" }
```

Ou ajoutez-le avec une seule commande :

```bash
codex mcp add xdata-fly --url https://api.xdatafly.com/mcp --header "secret-key=sk_live_xxxxxxxxxxxxxxxx"
```

### Connecter WorkBuddy

WorkBuddy lit les connecteurs depuis `~/.workbuddy/mcp.json` (sous Windows : `C:\Users\<you>\.workbuddy\mcp.json`). Ajoutez le serveur dans `mcpServers`, enregistrez, et WorkBuddy pourra appeler l'outil `amazon_reviews` dans le chat :

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

### Clients stdio (pont)

Pour les clients uniquement stdio, ponté le point de terminaison distant dans une commande locale avec `mcp-remote` (Node.js requis) :

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

### Facturation MCP

Les appels d'outils MCP suivent les mêmes règles de crédits que l'API REST : seuls les appels réussis sont facturés, et tout échec (mauvais paramètres, solde insuffisant, erreur en amont ou de parsing) libère automatiquement les crédits réservés. Chaque réponse indique les crédits facturés et votre solde restant.

## Tarifs

- **1 crédit par page** d'avis Amazon.
- Les forfaits vont d'un petit pack d'essai à un pack entreprise — voir [tarifs](https://www.xdatafly.com/en/pricing).
- Facturation à l'usage ; seuls les appels réussis sont facturés.

## FAQ

**Qu'est-ce que l'API Amazon Reviews de xdata-fly ?**
Une API en temps réel qui scrape les avis produits Amazon par ASIN et renvoie un JSON structuré, utilisable via REST ou MCP.

**Les données d'avis sont-elles en temps réel ?**
Oui. Les avis sont scrapés depuis Amazon à la demande à chaque requête — rien ne provient d'un cache obsolète.

**Quelles places de marché Amazon sont prises en charge ?**
Amazon US, UK, Allemagne, France, Italie et Espagne (`amz_us`, `amz_uk`, `amz_de`, `amz_fr`, `amz_it`, `amz_es`). Pour d'autres places de marché, contactez [tiger00001526@gmail.com](mailto:tiger00001526@gmail.com).

**Comment les requêtes sont-elles facturées ?**
Par page : 1 crédit par page récupérée. Les pages en échec ne sont pas facturées, et seuls les appels réussis sont facturés.

**Puis-je l'utiliser avec des agents IA / des outils LLM ?**
Oui — disponible en tant qu'outil MCP `amazon_reviews`, compatible avec Codex, WorkBuddy et tout client MCP distant.

**Renvoyez-vous les noms complets des clients ou les avatars ?**
Non. Les noms d'auteurs sont masqués (ex. `I***o`) et les URLs d'avatars ne sont pas renvoyées, pour garantir la conformité.

## Liens & contact

- **Site web :** https://www.xdatafly.com
- **Doc de l'API Amazon Reviews :** https://www.xdatafly.com/en/services/amazon-reviews-api
- **Doc MCP :** https://www.xdatafly.com/en/mcp
- **Obtenir une clé API :** https://www.xdatafly.com/en/register
- **Tarifs :** https://www.xdatafly.com/en/pricing
- **Contact / places de marché personnalisées :** [tiger00001526@gmail.com](mailto:tiger00001526@gmail.com)

---

Mots-clés : API avis Amazon, scraper avis produits Amazon, avis Amazon temps réel, récupérer avis par ASIN, avis Amazon US, avis Amazon UK, avis Amazon Allemagne, avis Amazon France, avis Amazon Italie, avis Amazon Espagne, achat vérifié, MCP avis Amazon, analyse avis e-commerce.
