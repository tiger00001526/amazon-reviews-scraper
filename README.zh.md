# xdata-fly · 亚马逊评论接口

**实时爬取亚马逊商品评论。** 按 ASIN 从亚马逊美国、英国、德国、法国、意大利、西班牙站点获取结构化的买家评论 —— 既提供简洁的 REST API，也为 AI Agent 提供 MCP 工具。

**语言：** [English](README.md) · **简体中文** · [Deutsch](README.de.md) · [Français](README.fr.md) · [Italiano](README.it.md) · [Español](README.es.md) · [日本語](README.ja.md)

[官网](https://www.xdatafly.com) · [接口文档](https://www.xdatafly.com/en/services/amazon-reviews-api) · [MCP 文档](https://www.xdatafly.com/en/mcp) · [获取 API Key](https://www.xdatafly.com/en/register) · [联系邮箱](mailto:tiger00001526@gmail.com)

---

## 目录

- [什么是 xdata-fly 亚马逊评论接口](#什么是-xdata-fly-亚马逊评论接口)
- [核心能力](#核心能力)
- [支持的亚马逊站点](#支持的亚马逊站点)
- [快速开始](#快速开始)
- [请求参数](#请求参数)
- [请求示例](#请求示例)
- [响应示例](#响应示例)
- [响应字段](#响应字段)
- [实现说明](#实现说明)
- [在 MCP 中使用（AI Agent）](#在-mcp-中使用ai-agent)
- [计费](#计费)
- [常见问题](#常见问题)
- [链接与联系方式](#链接与联系方式)

---

## 什么是 xdata-fly 亚马逊评论接口

**xdata-fly 亚马逊评论接口** 是一套实时的 **亚马逊商品评论采集** 服务，以简洁的 REST 接口对外提供能力。传入一个亚马逊 ASIN，即可返回结构化 JSON：评论 ID、评分星级、标题、评论正文、脱敏作者、是否 Verified Purchase、有用票数、评论日期与评论者国家。

每一次请求都是从亚马逊 **实时按需抓取**，而非读取过期缓存 —— 因此你拿到的永远是最新评论，与消费者在该站点上看到的完全一致。它适用于电商数据分析、价格与舆情监控、市场调研、评论聚合、铺货/精品选品验证，以及 AI Agent 数据管道。

- **接口地址：** `GET https://api.xdatafly.com/v1/amazon/reviews`
- **鉴权方式：** `secret-key` 请求头
- **价格：** 每页 1 积分
- **同时提供 MCP 工具** `amazon_reviews` —— 见 [在 MCP 中使用](#在-mcp-中使用ai-agent)。

## 核心能力

- **实时采集** —— 每次请求都从亚马逊现场抓取，数据始终新鲜。
- **按 ASIN 查询** —— 输入任意 10 位亚马逊 ASIN 即可拉取该商品评论。
- **星级筛选** —— `all_stars`、`one_star` … `five_star`，或直接使用 `positive` / `critical` 预设分组。
- **排序方式** —— 按最新（`recent`）或按最有帮助（`helpful`）。
- **分页能力** —— `page_count` 取值 1–10，按页计费。
- **评审与媒体筛选** —— 仅已验证购买、仅带图/视频评论、当前格式，以及可选的评论图片/视频 URL。
- **多站点支持** —— 亚马逊美国、英国、德国、法国、意大利、西班牙（更多站点可申请开通）。
- **合规输出** —— 作者名已脱敏（例如 `I***o`），且不返回头像原图 URL。
- **结构化 JSON** —— 各站点使用统一一致的数据结构。
- **原生支持 MCP** —— 同一份数据可直接接入 Codex、WorkBuddy 等支持 MCP 的智能体。

## 支持的亚马逊站点

| 站点 | `site` 取值 | 域名 | 评论语言 |
|---|---|---|---|
| 🇺🇸 美国 | `amz_us` | amazon.com | 英语 |
| 🇬🇧 英国 | `amz_uk` | amazon.co.uk | 英语 |
| 🇩🇪 德国 | `amz_de` | amazon.de | 德语 |
| 🇫🇷 法国 | `amz_fr` | amazon.fr | 法语 |
| 🇮🇹 意大利 | `amz_it` | amazon.it | 意大利语 |
| 🇪🇸 西班牙 | `amz_es` | amazon.es | 西班牙语 |

需要更多站点（加拿大、日本、澳大利亚、墨西哥、荷兰等）？**请联系开发者 [tiger00001526@gmail.com](mailto:tiger00001526@gmail.com)** 开通更多亚马逊站点。

## 快速开始

### 接口地址与鉴权

```
GET https://api.xdatafly.com/v1/amazon/reviews
secret-key: sk_live_xxxxxxxxxxxxxxxx
```

在 [控制台](https://www.xdatafly.com/en/register) 创建 API Key。Key 只显示一次，请妥善保存。

### 请求参数

| 参数 | 类型 | 必填 | 说明 |
|---|---|---|---|
| `asin` | string | **必填** | 10 位亚马逊商品 ASIN |
| `page_count` | integer | 可选 | 需要抓取的评论页数，1–10，默认 `1`，按页计费 |
| `sort` | string | 可选 | 排序：`recent`（最新）/ `helpful`（最有帮助） |
| `star` | string | 可选 | 星级筛选：`all_stars` / `one_star` … `five_star` / `positive` / `critical` |
| `reviewer_type` | string | 可选 | 评审范围：`all_reviews` / `avp_only_reviews` |
| `media_type` | string | 可选 | 媒体类型：`all_contents` / `media_reviews_only` |
| `format_type` | string | 可选 | 格式范围：`all_formats` / `current_format` |
| `site` | string | 可选 | 站点：`amz_us` / `amz_uk` / `amz_de` / `amz_fr` / `amz_it` / `amz_es`，默认 `amz_us` |
| `include_media` | boolean | 可选 | 是否返回评论图片/视频 URL，默认 `false` |

### 请求示例

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
  signal: AbortSignal.timeout(120_000), // 单页可能耗时 5–40s，超时建议 ≥60s
});
const { data, usage } = await res.json();
console.log(data);  // 结构化评论
console.log(usage); // 本次扣费与余额
```

**Python**

```python
import requests

res = requests.get(
    "https://api.xdatafly.com/v1/amazon/reviews",
    params={"asin": "B0DXK317FN", "page_count": 1, "sort": "recent", "star": "all_stars", "site": "amz_us"},
    headers={"secret-key": "sk_live_xxxxxxxxxxxxxxxx"},
    timeout=120,  # 单页可能耗时 5–40s，超时建议 ≥60s
)
payload = res.json()
print(payload["data"])
print(payload["usage"])
```

### 响应示例

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

### 响应字段

| 字段 | 类型 | 说明 |
|---|---|---|
| `review_id` | string | 亚马逊评论唯一 ID |
| `asin` | string | 被评论商品的 ASIN |
| `rating` | integer | 星级评分，1–5 |
| `title` | string | 评论标题 |
| `body` | string | 评论正文 |
| `author` | string | 已脱敏的评论者名称（例如 `I***o`） |
| `country` | string | 评论者所在国家 |
| `reviewed_at` | string | 评论日期 |
| `verified_purchase` | boolean | 是否为已验证购买 |
| `vine_voice` | boolean | 是否为 Vine 评论 |
| `helpful_votes` | integer | 有用票数 |
| `has_avatar` | boolean | 上游是否存在头像（不返回头像 URL） |
| `total_reviews` | integer | 该 ASIN 在整站的评论总数 |
| `pages_returned` | integer | 本次返回的页数 |
| `usage.credits` | integer | 本次扣费积分 |
| `usage.balance` | integer | 剩余积分余额 |

## 实现说明

- 单页通常需要 **5–40 秒**（上游实时抓取亚马逊）。请把客户端超时设为 **≥60 秒** —— 常见的默认 30 秒会被误判为超时。
- **按页计费：** 1 积分 × `page_count`。抓取失败的那一页不计费；超时或上游失败会全额解冻积分。
- `author` 已做 **脱敏** 处理（例如 `I***o`），以保证响应合规。
- `has_avatar` 表示上游是否存在头像，但头像原图 URL 不返回。
- `include_media=true` 会附带评论图片与视频 URL，响应体积明显增大。
- `total_reviews` 是该 ASIN 在整个站点的评论总数，不只是本次抓取的页数。
- 仅 **成功（SUCCESS）** 的调用才会计费。每次响应都会回传本次消耗的积分与剩余余额，便于对账。

## 在 MCP 中使用（AI Agent）

同一份亚马逊评论数据也通过 **MCP**（Model Context Protocol）对外提供，Codex、WorkBuddy 等支持 MCP 的智能体可直接调用。两种方式的计费与限流完全一致 —— 通过 MCP 调用工具的费用与等价的 API 请求完全相同。

### 接口地址与鉴权

MCP 端点采用标准的 **Streamable HTTP** 传输，并与 REST API 使用相同的鉴权方式：

```
Endpoint:    https://api.xdatafly.com/mcp
Auth header: secret-key: sk_live_xxxxxxxxxxxxxxxx
```

### 可用工具

| 工具 | 说明 | 状态 |
|---|---|---|
| `amazon_reviews` | 按 ASIN 获取亚马逊评论：评分、标题、正文、脱敏作者、是否已验证购买、有用票数、日期与国家。 | 可用 |
| `google_search` | 结构化 Google SERP 数据。 | 即将上线 |

### 通用 MCP 客户端

对于支持远程 MCP 的客户端（Cursor、VS Code、Cherry Studio 等），在 MCP 配置文件中加入以下内容：

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

### 接入 Codex

Codex CLI 从 `~/.codex/config.toml` 读取 MCP 服务。加入以下配置并重启 Codex：

```toml
[mcp_servers.xdata-fly]
url = "https://api.xdatafly.com/mcp"
http_headers = { "secret-key" = "sk_live_xxxxxxxxxxxxxxxx" }
```

或用一条命令添加：

```bash
codex mcp add xdata-fly --url https://api.xdatafly.com/mcp --header "secret-key=sk_live_xxxxxxxxxxxxxxxx"
```

### 接入 WorkBuddy

WorkBuddy 从 `~/.workbuddy/mcp.json` 读取连接器（Windows 路径：`C:\Users\<you>\.workbuddy\mcp.json`）。把该服务加入 `mcpServers` 并保存，之后即可在对话中调用 `amazon_reviews` 工具：

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

### stdio 客户端（桥接）

对于仅支持 stdio 的客户端，可用 `mcp-remote` 把远程端点桥接为本地命令（需要 Node.js）：

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

### MCP 计费

MCP 工具调用的积分规则与 REST API 完全相同：仅成功的调用才计费；任何失败（参数错误、余额不足、上游或解析错误）都会自动解冻预留积分。每次响应都会回传本次扣费与剩余余额。

## 计费

- 亚马逊评论 **每页 1 积分**。
- 套餐从体验包到企业包不等 —— 详见 [价格](https://www.xdatafly.com/en/pricing)。
- 按量计费，仅成功的调用才会计费。

## 常见问题

**xdata-fly 亚马逊评论接口是什么？**
一套实时 API，按 ASIN 采集亚马逊商品评论并返回结构化 JSON，可通过 REST 或 MCP 使用。

**评论数据是实时的吗？**
是。每次请求都从亚马逊实时按需抓取，不读取任何过期缓存。

**支持哪些亚马逊站点？**
亚马逊美国、英国、德国、法国、意大利、西班牙（`amz_us`、`amz_uk`、`amz_de`、`amz_fr`、`amz_it`、`amz_es`）。如需更多站点，请联系 [tiger00001526@gmail.com](mailto:tiger00001526@gmail.com)。

**如何计费？**
按页计费：每抓取一页 1 积分。失败的页面不计费，只有成功的调用才会计费。

**可以配合 AI Agent / 大模型工具使用吗？**
可以 —— 提供 MCP 工具 `amazon_reviews`，兼容 Codex、WorkBuddy 以及任意支持远程 MCP 的客户端。

**会返回完整的用户姓名或头像吗？**
不会。作者名已脱敏（例如 `I***o`），头像原图 URL 也不返回，以保证输出合规。

## 链接与联系方式

- **官网：** https://www.xdatafly.com
- **亚马逊评论接口文档：** https://www.xdatafly.com/en/services/amazon-reviews-api
- **MCP 文档：** https://www.xdatafly.com/en/mcp
- **获取 API Key：** https://www.xdatafly.com/en/register
- **价格：** https://www.xdatafly.com/en/pricing
- **联系 / 定制站点：** [tiger00001526@gmail.com](mailto:tiger00001526@gmail.com)

---

关键词：亚马逊评论接口, 亚马逊评论采集, 亚马逊商品评论 API, 实时评论抓取, ASIN 评论, 亚马逊美国评论, 亚马逊德国评论, 亚马逊英国评论, 亚马逊法国评论, 亚马逊意大利评论, 亚马逊西班牙评论, Verified Purchase, MCP 亚马逊评论, 电商评论分析。
