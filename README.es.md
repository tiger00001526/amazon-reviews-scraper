# xdata-fly · API Amazon Reviews

**Extracción de reseñas de productos de Amazon en tiempo real.** Obtén reseñas de clientes estructuradas por ASIN desde Amazon EE. UU., Reino Unido, Alemania, Francia, Italia y España — mediante una sencilla API REST o una herramienta MCP para agentes de IA.

**Idiomas:** [English](README.md) · [简体中文](README.zh.md) · [Deutsch](README.de.md) · [Français](README.fr.md) · [Italiano](README.it.md) · **Español** · [日本語](README.ja.md)

[Sitio web](https://www.xdatafly.com) · [Doc de la API](https://www.xdatafly.com/en/services/amazon-reviews-api) · [Doc MCP](https://www.xdatafly.com/en/mcp) · [Obtener una clave API](https://www.xdatafly.com/en/register) · [Contacto](mailto:tiger00001526@gmail.com)

---

## Índice

- [¿Qué es la API Amazon Reviews de xdata-fly?](#qué-es-la-api-amazon-reviews-de-xdata-fly)
- [Funciones clave](#funciones-clave)
- [Marketplaces de Amazon compatibles](#marketplaces-de-amazon-compatibles)
- [Inicio rápido](#inicio-rápido)
- [Parámetros](#parámetros)
- [Ejemplo de solicitud](#ejemplo-de-solicitud)
- [Ejemplo de respuesta](#ejemplo-de-respuesta)
- [Campos de la respuesta](#campos-de-la-respuesta)
- [Notas de implementación](#notas-de-implementación)
- [Uso desde MCP (agentes de IA)](#uso-desde-mcp-agentes-de-ia)
- [Precios](#precios)
- [FAQ](#faq)
- [Enlaces y contacto](#enlaces-y-contacto)

---

## ¿Qué es la API Amazon Reviews de xdata-fly?

La **API Amazon Reviews de xdata-fly** es un **scraper de reseñas de productos de Amazon** en tiempo real, expuesto como un endpoint REST limpio. Proporciona un ASIN de Amazon y recibe JSON estructurado: ID de reseña, valoración en estrellas, título, cuerpo de la reseña, autor enmascarado, indicador de compra verificada, votos útiles, fecha de la reseña y país del reseñador.

Cada solicitud se extrae de Amazon **bajo demanda, en tiempo real** — nunca desde una caché desactualizada — por lo que siempre obtienes las reseñas más recientes, exactamente como las ven los compradores en el marketplace. Está pensada para analítica de e-commerce, monitorización de precios y sentimiento, estudios de mercado, agregación de reseñas, validación de dropshipping/private-label y pipelines de agentes de IA.

- **Endpoint:** `GET https://api.xdatafly.com/v1/amazon/reviews`
- **Autenticación:** cabecera `secret-key`
- **Precio:** 1 crédito por página
- **También disponible como herramienta MCP** `amazon_reviews` — consulta [la integración MCP](#uso-desde-mcp-agentes-de-ia).

## Funciones clave

- **Extracción en tiempo real** — las reseñas se obtienen en directo de Amazon en cada solicitud, los datos se mantienen frescos.
- **Búsqueda por ASIN** — apunta a cualquier ASIN de Amazon de 10 caracteres y obtén sus reseñas.
- **Filtro por estrellas** — `all_stars`, `one_star` … `five_star`, o los grupos ya preparados `positive` / `critical`.
- **Ordenación** — más recientes primero (`recent`) o más útiles primero (`helpful`).
- **Paginación** — `page_count` de 1 a 10, facturado por página.
- **Filtros de reseñador y medios** — solo compras verificadas, solo reseñas con medios, formato actual y URLs de imágenes/vídeos opcionales.
- **Multi-marketplace** — Amazon EE. UU., Reino Unido, Alemania, Francia, Italia y España (más bajo petición).
- **Salida conforme** — los nombres de autor se enmascaran (p. ej. `I***o`) y no se devuelven URLs de avatar.
- **JSON rico y estructurado** — un esquema coherente en todos los marketplaces.
- **Nativo para MCP** — conecta los mismos datos a agentes compatibles con MCP como Codex y WorkBuddy.

## Marketplaces de Amazon compatibles

| Marketplace | Código `site` | Dominio | Idioma de las reseñas |
|---|---|---|---|
| 🇺🇸 Estados Unidos | `amz_us` | amazon.com | Inglés |
| 🇬🇧 Reino Unido | `amz_uk` | amazon.co.uk | Inglés |
| 🇩🇪 Alemania | `amz_de` | amazon.de | Alemán |
| 🇫🇷 Francia | `amz_fr` | amazon.fr | Francés |
| 🇮🇹 Italia | `amz_it` | amazon.it | Italiano |
| 🇪🇸 España | `amz_es` | amazon.es | Español |

¿Necesitas otro marketplace (Canadá, Japón, Australia, México, Países Bajos y más)? **Contacta con el desarrollador en [tiger00001526@gmail.com](mailto:tiger00001526@gmail.com)** para habilitar sitios de Amazon adicionales.

## Inicio rápido

### Endpoint y autenticación

```
GET https://api.xdatafly.com/v1/amazon/reviews
secret-key: sk_live_xxxxxxxxxxxxxxxx
```

Crea tu clave API en la [consola](https://www.xdatafly.com/en/register). La clave solo se muestra una vez — guárdala con cuidado.

### Parámetros

| Nombre | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `asin` | string | **obligatorio** | ASIN de Amazon de 10 caracteres |
| `page_count` | integer | opcional | Número de páginas de reseñas a obtener, 1–10, por defecto `1`, facturado por página |
| `sort` | string | opcional | Ordenación: `recent` / `helpful` |
| `star` | string | opcional | Filtro de estrellas: `all_stars` / `one_star` … `five_star` / `positive` / `critical` |
| `reviewer_type` | string | opcional | Ámbito del reseñador: `all_reviews` / `avp_only_reviews` |
| `media_type` | string | opcional | Tipo de medio: `all_contents` / `media_reviews_only` |
| `format_type` | string | opcional | Ámbito de formato: `all_formats` / `current_format` |
| `site` | string | opcional | Marketplace: `amz_us` / `amz_uk` / `amz_de` / `amz_fr` / `amz_it` / `amz_es`, por defecto `amz_us` |
| `include_media` | boolean | opcional | Incluir URLs de imágenes/vídeos de las reseñas, por defecto `false` |

### Ejemplo de solicitud

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
  signal: AbortSignal.timeout(120_000), // una página puede tardar 5–40s; define timeout ≥60s
});
const { data, usage } = await res.json();
console.log(data);  // reseñas estructuradas
console.log(usage); // créditos facturados y saldo restante
```

**Python**

```python
import requests

res = requests.get(
    "https://api.xdatafly.com/v1/amazon/reviews",
    params={"asin": "B0DXK317FN", "page_count": 1, "sort": "recent", "star": "all_stars", "site": "amz_us"},
    headers={"secret-key": "sk_live_xxxxxxxxxxxxxxxx"},
    timeout=120,  # una página puede tardar 5–40s; define timeout ≥60s
)
payload = res.json()
print(payload["data"])
print(payload["usage"])
```

### Ejemplo de respuesta

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

### Campos de la respuesta

| Campo | Tipo | Descripción |
|---|---|---|
| `review_id` | string | ID único de la reseña de Amazon |
| `asin` | string | ASIN del producto reseñado |
| `rating` | integer | Valoración en estrellas, 1–5 |
| `title` | string | Título de la reseña |
| `body` | string | Texto completo de la reseña |
| `author` | string | Nombre del reseñador enmascarado (p. ej. `I***o`) |
| `country` | string | País del reseñador |
| `reviewed_at` | string | Fecha de la reseña |
| `verified_purchase` | boolean | Si es una compra verificada |
| `vine_voice` | boolean | Si es una reseña de Vine |
| `helpful_votes` | integer | Número de votos útiles |
| `has_avatar` | boolean | Si existía un avatar en origen (URL no devuelta) |
| `total_reviews` | integer | Total de reseñas del ASIN en el marketplace |
| `pages_returned` | integer | Número de páginas devueltas en esta llamada |
| `usage.credits` | integer | Créditos facturados en esta llamada |
| `usage.balance` | integer | Saldo de créditos restante |

## Notas de implementación

- Una sola página suele tardar **5–40 segundos** (extraída de Amazon en tiempo real). Define el timeout de tu cliente en **≥60s** — el valor por defecto habitual de 30s provocará timeouts falsos.
- **Facturación por página:** 1 crédito × `page_count`. Una página fallida no se factura; los timeouts y fallos en origen liberan los créditos reservados.
- `author` está **enmascarado** (por ejemplo `I***o`) para mantener la respuesta conforme.
- `has_avatar` indica si existía un avatar en origen — la URL de la imagen en sí no se devuelve.
- `include_media=true` añade URLs de imágenes y vídeos de las reseñas y aumenta notablemente el tamaño de la respuesta.
- `total_reviews` es el recuento a nivel de marketplace para el ASIN, no solo las páginas obtenidas.
- Solo se facturan las llamadas con estado **SUCCESS**. Cada respuesta informa de los créditos usados y tu saldo restante, para conciliar el gasto sin conjeturas.

## Uso desde MCP (agentes de IA)

Los mismos datos de reseñas de Amazon se exponen mediante **MCP** (Model Context Protocol), de modo que los agentes compatibles con MCP como Codex y WorkBuddy pueden llamarlos directamente. Ambas vías comparten la misma facturación y el mismo rate-limit — una llamada a una herramienta vía MCP cuesta exactamente lo mismo que una solicitud API equivalente.

### Endpoint y autenticación

El endpoint MCP usa el transporte estándar **Streamable HTTP** y la misma autenticación que la API REST:

```
Endpoint:    https://api.xdatafly.com/mcp
Auth header: secret-key: sk_live_xxxxxxxxxxxxxxxx
```

### Herramientas disponibles

| Herramienta | Descripción | Estado |
|---|---|---|
| `amazon_reviews` | Obtén reseñas de Amazon por ASIN: valoración, título, cuerpo, autor enmascarado, compra verificada, votos útiles, fecha y país. | Disponible |
| `google_search` | Datos SERP de Google estructurados. | Próximamente |

### Cliente MCP genérico

Para clientes compatibles con MCP remoto (Cursor, VS Code, Cherry Studio y similares), añade lo siguiente al archivo de configuración MCP:

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

### Conectar Codex

Codex CLI lee los servidores MCP desde `~/.codex/config.toml`. Añade el bloque de abajo y reinicia Codex:

```toml
[mcp_servers.xdata-fly]
url = "https://api.xdatafly.com/mcp"
http_headers = { "secret-key" = "sk_live_xxxxxxxxxxxxxxxx" }
```

O añádelo con un solo comando:

```bash
codex mcp add xdata-fly --url https://api.xdatafly.com/mcp --header "secret-key=sk_live_xxxxxxxxxxxxxxxx"
```

### Conectar WorkBuddy

WorkBuddy lee los conectores desde `~/.workbuddy/mcp.json` (en Windows: `C:\Users\<you>\.workbuddy\mcp.json`). Añade el servidor a `mcpServers`, guarda, y WorkBuddy podrá llamar a la herramienta `amazon_reviews` en el chat:

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

### Clientes stdio (puente)

Para clientes solo stdio, conecta el endpoint remoto a un comando local con `mcp-remote` (requiere Node.js):

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

### Facturación MCP

Las llamadas a herramientas MCP siguen las mismas reglas de créditos que la API REST: solo se facturan las llamadas exitosas, y cualquier fallo (parámetros incorrectos, saldo insuficiente, error en origen o de análisis) libera automáticamente los créditos reservados. Cada respuesta informa de los créditos facturados y tu saldo restante.

## Precios

- **1 crédito por página** de reseñas de Amazon.
- Los paquetes van desde un pequeño paquete de prueba hasta un paquete enterprise — consulta [precios](https://www.xdatafly.com/en/pricing).
- Facturación por uso; solo se facturan las llamadas exitosas.

## FAQ

**¿Qué es la API Amazon Reviews de xdata-fly?**
Una API en tiempo real que extrae reseñas de productos de Amazon por ASIN y devuelve JSON estructurado, utilizable vía REST o MCP.

**¿Los datos de reseñas son en tiempo real?**
Sí. Las reseñas se extraen de Amazon bajo demanda en cada solicitud — nada proviene de una caché desactualizada.

**¿Qué marketplaces de Amazon son compatibles?**
Amazon EE. UU., Reino Unido, Alemania, Francia, Italia y España (`amz_us`, `amz_uk`, `amz_de`, `amz_fr`, `amz_it`, `amz_es`). Para otros marketplaces, contacta con [tiger00001526@gmail.com](mailto:tiger00001526@gmail.com).

**¿Cómo se facturan las solicitudes?**
Por página: 1 crédito por página obtenida. Las páginas fallidas no se facturan y solo se facturan las llamadas exitosas.

**¿Puedo usarla con agentes de IA / herramientas LLM?**
Sí — está disponible como la herramienta MCP `amazon_reviews`, compatible con Codex, WorkBuddy y cualquier cliente MCP remoto.

**¿Devolvéis nombres completos de clientes o avatares?**
No. Los nombres de autor se enmascaran (p. ej. `I***o`) y las URLs de avatar no se devuelven, para mantener la conformidad.

## Enlaces y contacto

- **Sitio web:** https://www.xdatafly.com
- **Doc de la API Amazon Reviews:** https://www.xdatafly.com/en/services/amazon-reviews-api
- **Doc MCP:** https://www.xdatafly.com/en/mcp
- **Obtener una clave API:** https://www.xdatafly.com/en/register
- **Precios:** https://www.xdatafly.com/en/pricing
- **Contacto / marketplaces personalizados:** [tiger00001526@gmail.com](mailto:tiger00001526@gmail.com)

---

Palabras clave: API reseñas Amazon, scraper reseñas productos Amazon, reseñas Amazon tiempo real, obtener reseñas por ASIN, reseñas Amazon EE. UU., reseñas Amazon Reino Unido, reseñas Amazon Alemania, reseñas Amazon Francia, reseñas Amazon Italia, reseñas Amazon España, compra verificada, MCP reseñas Amazon, analítica de reseñas de e-commerce.
