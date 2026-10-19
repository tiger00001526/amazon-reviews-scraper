# xdata-fly · Amazon レビュー API

**Amazon 商品レビューをリアルタイムで収集。** ASIN を指定して、Amazon 米国・英国・ドイツ・フランス・イタリア・スペインの顧客レビューを構造化データで取得 —— シンプルな REST API、または AI エージェント向けの MCP ツールで利用できます。

**言語:** [English](README.md) · [简体中文](README.zh.md) · [Deutsch](README.de.md) · [Français](README.fr.md) · [Italiano](README.it.md) · [Español](README.es.md) · **日本語**

[公式サイト](https://www.xdatafly.com) · [API ドキュメント](https://www.xdatafly.com/en/services/amazon-reviews-api) · [MCP ドキュメント](https://www.xdatafly.com/en/mcp) · [API キーを取得](https://www.xdatafly.com/en/register) · [お問い合わせ](mailto:tiger00001526@gmail.com)

---

## 目次

- [xdata-fly Amazon レビュー API とは](#xdata-fly-amazon-レビュー-api-とは)
- [主な機能](#主な機能)
- [対応 Amazon マーケットプレイス](#対応-amazon-マーケットプレイス)
- [クイックスタート](#クイックスタート)
- [パラメータ](#パラメータ)
- [リクエスト例](#リクエスト例)
- [レスポンス例](#レスポンス例)
- [レスポンスフィールド](#レスポンスフィールド)
- [実装上の注意](#実装上の注意)
- [MCP から利用する（AI エージェント）](#mcp-から利用するai-エージェント)
- [料金](#料金)
- [よくある質問](#よくある質問)
- [リンク・お問い合わせ](#リンクお問い合わせ)

---

## xdata-fly Amazon レビュー API とは

**xdata-fly Amazon レビュー API** は、リアルタイムの **Amazon 商品レビュー収集（スクレイピング）** サービスをクリーンな REST エンドポイントとして提供します。Amazon の ASIN を渡すと、構造化 JSON を返します：レビュー ID、星評価、タイトル、本文、マスク済みの投稿者、Verified Purchase（購入済み）フラグ、参考になった票数、レビュー日、投稿者の国。

すべてのリクエストは Amazon から **オンデマンドでリアルタイムに** 取得され、古いキャッシュから返されることはありません。そのため、買い物客がマーケットプレイスで目にする最新のレビューを常に取得できます。EC 分析、価格・センチメント監視、市場調査、レビュー集約、ドロップシッピング／自社ブランド商品の検証、AI エージェントのパイプラインに最適です。

- **エンドポイント:** `GET https://api.xdatafly.com/v1/amazon/reviews`
- **認証:** `secret-key` ヘッダー
- **価格:** 1 ページあたり 1 クレジット
- **MCP ツール** `amazon_reviews` としても利用可能 —— [MCP 連携](#mcp-から利用するai-エージェント) を参照。

## 主な機能

- **リアルタイム収集** —— リクエストごとに Amazon からライブで取得し、データは常に最新です。
- **ASIN で検索** —— 任意の 10 桁 Amazon ASIN を指定してレビューを取得。
- **星フィルター** —— `all_stars`、`one_star` … `five_star`、または `positive` / `critical` のプリセット。
- **並べ替え** —— 新しい順（`recent`）または参考になった順（`helpful`）。
- **ページネーション** —— `page_count` は 1〜10、ページ単位で課金。
- **投稿者・メディアフィルター** —— 購入済みのみ、メディア付きレビューのみ、現在のフォーマット、レビュー画像／動画 URL の取得（任意）。
- **複数マーケットプレイス対応** —— Amazon 米国・英国・ドイツ・フランス・イタリア・スペイン（追加は応相談）。
- **コンプライアンスに配慮した出力** —— 投稿者名はマスク（例：`I***o`）し、アバター画像 URL は返しません。
- **豊富で構造化された JSON** —— すべてのマーケットプレイスで一貫したスキーマ。
- **MCP ネイティブ** —— 同じデータを Codex や WorkBuddy など MCP 対応エージェントに直接接続。

## 対応 Amazon マーケットプレイス

| マーケットプレイス | `site` コード | ドメイン | レビューの言語 |
|---|---|---|---|
| 🇺🇸 アメリカ | `amz_us` | amazon.com | 英語 |
| 🇬🇧 イギリス | `amz_uk` | amazon.co.uk | 英語 |
| 🇩🇪 ドイツ | `amz_de` | amazon.de | ドイツ語 |
| 🇫🇷 フランス | `amz_fr` | amazon.fr | フランス語 |
| 🇮🇹 イタリア | `amz_it` | amazon.it | イタリア語 |
| 🇪🇸 スペイン | `amz_es` | amazon.es | スペイン語 |

その他のマーケットプレイス（カナダ、日本、オーストラリア、メキシコ、オランダなど）が必要ですか？**開発者 [tiger00001526@gmail.com](mailto:tiger00001526@gmail.com) までご連絡ください**。追加の Amazon サイトを有効化できます。

## クイックスタート

### エンドポイントと認証

```
GET https://api.xdatafly.com/v1/amazon/reviews
secret-key: sk_live_xxxxxxxxxxxxxxxx
```

[コンソール](https://www.xdatafly.com/en/register) で API キーを作成してください。キーは一度しか表示されないため、大切に保管してください。

### パラメータ

| 名前 | 型 | 必須 | 説明 |
|---|---|---|---|
| `asin` | string | **必須** | 10 桁の Amazon ASIN |
| `page_count` | integer | 任意 | 取得するレビューページ数、1〜10、既定値 `1`、ページ単位で課金 |
| `sort` | string | 任意 | 並べ替え：`recent` / `helpful` |
| `star` | string | 任意 | 星フィルター：`all_stars` / `one_star` … `five_star` / `positive` / `critical` |
| `reviewer_type` | string | 任意 | 投稿者の範囲：`all_reviews` / `avp_only_reviews` |
| `media_type` | string | 任意 | メディア種別：`all_contents` / `media_reviews_only` |
| `format_type` | string | 任意 | フォーマット範囲：`all_formats` / `current_format` |
| `site` | string | 任意 | マーケットプレイス：`amz_us` / `amz_uk` / `amz_de` / `amz_fr` / `amz_it` / `amz_es`、既定値 `amz_us` |
| `include_media` | boolean | 任意 | レビュー画像／動画 URL を含める、既定値 `false` |

### リクエスト例

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
  signal: AbortSignal.timeout(120_000), // 1 ページに 5〜40 秒かかることがあります。タイムアウトは 60 秒以上に設定してください
});
const { data, usage } = await res.json();
console.log(data);  // 構造化されたレビュー
console.log(usage); // 今回の課金クレジットと残高
```

**Python**

```python
import requests

res = requests.get(
    "https://api.xdatafly.com/v1/amazon/reviews",
    params={"asin": "B0DXK317FN", "page_count": 1, "sort": "recent", "star": "all_stars", "site": "amz_us"},
    headers={"secret-key": "sk_live_xxxxxxxxxxxxxxxx"},
    timeout=120,  # 1 ページに 5〜40 秒かかることがあります。タイムアウトは 60 秒以上に設定してください
)
payload = res.json()
print(payload["data"])
print(payload["usage"])
```

### レスポンス例

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

### レスポンスフィールド

| フィールド | 型 | 説明 |
|---|---|---|
| `review_id` | string | Amazon レビューの一意 ID |
| `asin` | string | レビュー対象商品の ASIN |
| `rating` | integer | 星評価（1〜5） |
| `title` | string | レビューのタイトル |
| `body` | string | レビュー本文 |
| `author` | string | マスク済みの投稿者名（例：`I***o`） |
| `country` | string | 投稿者の国 |
| `reviewed_at` | string | レビュー日 |
| `verified_purchase` | boolean | 購入済み（Verified Purchase）かどうか |
| `vine_voice` | boolean | Vine レビューかどうか |
| `helpful_votes` | integer | 参考になった票数 |
| `has_avatar` | boolean | 上流にアバターが存在したか（URL は返しません） |
| `total_reviews` | integer | その ASIN のマーケットプレイス全体のレビュー総数 |
| `pages_returned` | integer | 今回返したページ数 |
| `usage.credits` | integer | 今回の課金クレジット |
| `usage.balance` | integer | 残りのクレジット残高 |

## 実装上の注意

- 1 ページの取得には通常 **5〜40 秒** かかります（Amazon からリアルタイムに取得）。クライアントのタイムアウトは **60 秒以上** に設定してください —— よくある既定値の 30 秒では誤ってタイムアウトします。
- **ページ単位の課金：** 1 クレジット × `page_count`。取得に失敗したページは課金されません。タイムアウトや上流の失敗時は、予約されたクレジットが解放されます。
- `author` はコンプライアンスのため **マスク** されています（例：`I***o`）。
- `has_avatar` は上流にアバターが存在したかどうかを示します —— 画像 URL 自体は返されません。
- `include_media=true` を指定すると、レビューの画像・動画 URL が付加され、レスポンスサイズが明らかに大きくなります。
- `total_reviews` はその ASIN のマーケットプレイス全体の件数であり、今回取得したページ数ではありません。
- ステータスが **SUCCESS** の呼び出しのみ課金されます。各レスポンスには使用クレジットと残高が含まれるため、消費を正確に照合できます。

## MCP から利用する（AI エージェント）

同じ Amazon レビューデータは **MCP**（Model Context Protocol）経由でも提供されるため、Codex や WorkBuddy など MCP 対応エージェントから直接呼び出せます。両者は同じ課金・レート制限のパイプラインを共有しており、MCP 経由のツール呼び出しは同等の API リクエストとまったく同じコストです。

### エンドポイントと認証

MCP エンドポイントは標準の **Streamable HTTP** トランスポートを使用し、REST API と同じ認証方式です：

```
Endpoint:    https://api.xdatafly.com/mcp
Auth header: secret-key: sk_live_xxxxxxxxxxxxxxxx
```

### 利用可能なツール

| ツール | 説明 | ステータス |
|---|---|---|
| `amazon_reviews` | ASIN で Amazon レビューを取得：評価、タイトル、本文、マスク済み投稿者、購入済み、参考になった票数、日付、国。 | 利用可能 |
| `google_search` | 構造化された Google SERP データ。 | 近日公開 |

### 汎用 MCP クライアント

リモート MCP に対応したクライアント（Cursor、VS Code、Cherry Studio など）では、MCP 設定ファイルに以下を追加します：

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

### Codex への接続

Codex CLI は `~/.codex/config.toml` から MCP サーバーを読み込みます。以下のブロックを追加して Codex を再起動してください：

```toml
[mcp_servers.xdata-fly]
url = "https://api.xdatafly.com/mcp"
http_headers = { "secret-key" = "sk_live_xxxxxxxxxxxxxxxx" }
```

または 1 つのコマンドで追加できます：

```bash
codex mcp add xdata-fly --url https://api.xdatafly.com/mcp --header "secret-key=sk_live_xxxxxxxxxxxxxxxx"
```

### WorkBuddy への接続

WorkBuddy は `~/.workbuddy/mcp.json` からコネクタを読み込みます（Windows の場合：`C:\Users\<you>\.workbuddy\mcp.json`）。`mcpServers` にサーバーを追加して保存すると、WorkBuddy のチャットで `amazon_reviews` ツールを呼び出せます：

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

### stdio クライアント（ブリッジ）

stdio 専用クライアントでは、`mcp-remote` を使ってリモートエンドポイントをローカルコマンドにブリッジします（Node.js が必要）：

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

### MCP の課金

MCP ツール呼び出しは REST API と同じクレジット規則に従います：成功した呼び出しのみ課金され、失敗（パラメータ不正、残高不足、上流または解析エラー）は予約されたクレジットを自動的に解放します。各レスポンスには課金クレジットと残高が含まれます。

## 料金

- Amazon レビューは **1 ページあたり 1 クレジット**。
- パッケージは小規模なトライアルパックからエンタープライズパックまで —— [料金](https://www.xdatafly.com/en/pricing) を参照。
- 従量課金制で、成功した呼び出しのみ課金されます。

## よくある質問

**xdata-fly Amazon レビュー API とは何ですか？**
ASIN で Amazon 商品レビューを収集し、構造化 JSON を返すリアルタイム API です。REST または MCP で利用できます。

**レビューデータはリアルタイムですか？**
はい。リクエストごとに Amazon からオンデマンドで取得し、古いキャッシュから返すことはありません。

**どの Amazon マーケットプレイスに対応していますか？**
Amazon 米国・英国・ドイツ・フランス・イタリア・スペイン（`amz_us`、`amz_uk`、`amz_de`、`amz_fr`、`amz_it`、`amz_es`）です。その他のマーケットプレイスについては [tiger00001526@gmail.com](mailto:tiger00001526@gmail.com) までご連絡ください。

**課金はどのように行われますか？**
ページ単位で、取得した 1 ページにつき 1 クレジットです。失敗したページは課金されず、成功した呼び出しのみ課金されます。

**AI エージェント／LLM ツールで使えますか？**
はい —— MCP ツール `amazon_reviews` として利用でき、Codex、WorkBuddy、および任意のリモート MCP クライアントと互換性があります。

**顧客の氏名やアバターは返しますか？**
いいえ。投稿者名はマスクされ（例：`I***o`）、アバター画像 URL も返しません。出力のコンプライアンスを保つためです。

## リンク・お問い合わせ

- **公式サイト:** https://www.xdatafly.com
- **Amazon レビュー API ドキュメント:** https://www.xdatafly.com/en/services/amazon-reviews-api
- **MCP ドキュメント:** https://www.xdatafly.com/en/mcp
- **API キーを取得:** https://www.xdatafly.com/en/register
- **料金:** https://www.xdatafly.com/en/pricing
- **お問い合わせ / マーケットプレイス追加:** [tiger00001526@gmail.com](mailto:tiger00001526@gmail.com)

---

キーワード: Amazon レビュー API, Amazon 商品レビュー スクレイピング, Amazon レビュー リアルタイム取得, ASIN レビュー取得, Amazon 米国 レビュー, Amazon 英国 レビュー, Amazon ドイツ レビュー, Amazon フランス レビュー, Amazon イタリア レビュー, Amazon スペイン レビュー, 購入済みレビュー, MCP Amazon レビュー, EC レビュー分析。
