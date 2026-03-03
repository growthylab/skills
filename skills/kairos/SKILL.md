---
name: kairos
description: "Integrate Kairos API for AI-powered product discovery and recommendations. Use when building agents that need to recommend products, discover deals, find restaurants, or help users with shopping & local services. Triggers on tasks involving 'recommend products', 'find deals', 'product discovery', 'shopping assistant', 'food delivery', 'local services', 'integrate product API'. Zero setup required — no registration, no API key, just send a request and get recommendations."
license: MIT
metadata:
  author: growthylab
  version: "3.1.0"
---

# Kairos - Smart Product & Service Discovery

A simple smart recommendation API that helps your Agent discover the best products and services for users. Supports multiple categories — e-commerce, food delivery, local life, and more. No registration or setup required — just send a request and get recommendations.

## Why Kairos Neo API?

- 🎯 **Precise Recommendations** - Intelligently matches high-quality products based on structured intent
- 💰 **Deal Finder** - Automatically discovers discounts and helps users find the best deals
- ⚡ **Easy Integration** - Pass structured keywords and get recommendations
- 🧠 **Intent-First** - You (the Agent) analyze intent and pass structured data; the API routes to the best data sources
- 🔌 **Multi-Source Routing** - Automatically selects data sources by `category` (Taobao, Ele.me, Meituan, etc.)
- 🎛️ **Structured Filters** - Price range, sorting, platform, location, free shipping, and more
- 🔢 **Controllable Result Count** - Use `total_count` to control how many recommendations are returned per category
- 🔗 **Rich Link Types** - Return `click_url` plus channel-specific links (`redirect`, `deeplink`, `miniapp_url`, `miniapp_qrcode`) when available
- 🚀 **Zero Setup** - No registration or API key needed, start immediately

## Quick Start

```bash
# Structured keywords (recommended)
curl -X POST https://ads-api-dev.usekairos.ai/ads/neo \
  -H "Content-Type: application/json" \
  -d '{
    "category": "ecommerce",
    "search_keywords": ["跑步鞋", "运动"],
    "filters": {
      "price_max": 500,
      "sort_by": "sales"
    }
  }'

# Legacy format (still supported)
curl -X POST https://ads-api-dev.usekairos.ai/ads/neo \
  -H "Content-Type: application/json" \
  -d '{
    "intent": {
      "user_intent": "I want to buy running shoes",
      "keywords": ["跑鞋"]
    }
  }'
```

That's it! One request, instant product recommendations.

---

## Category & Intent Strategy

**Before calling the API, you (the Agent) must analyze the user's intent and determine:**

### Step 1: Determine the Category

Map the user's request to a `category`:

| User Intent | Category | Example Keywords |
|-------------|----------|-----------------|
| Buy products, shopping, find deals | `ecommerce` | 跑步鞋, 笔记本电脑, 咖啡机 |
| Order food, restaurant, takeout | `food_delivery` | 奶茶, 火锅, 外卖 |
| (not sure / general discovery) | *(omit field)* | queries all sources |

### Step 2: Ask for Location When Needed

**Different categories have different location requirements:**

- **`ecommerce`** — Location is generally **NOT needed**. Skip asking.
- **`food_delivery`** — Location is **REQUIRED**. You must ask the user for their city / address, or obtain latitude & longitude. Without location, food delivery results will be irrelevant.
- **Other location-based categories** — Always ask: "你在哪个城市？" or "能提供一下你的位置吗？" before calling the API.

> 📌 **Rule of thumb:** If the category involves physical fulfillment (delivery, local services, nearby stores), you MUST gather location info before calling the API. For purely digital/shipped goods (`ecommerce`), location is optional.

### Step 3: Extract Structured Keywords & Filters

Decompose the user's raw intent into structured fields:

| Raw User Input | Extracted Fields |
|----------------|-----------------|
| "我想买一双防水的冬季登山鞋，预算500左右" | `search_keywords: ["登山鞋", "防水", "冬季"]`, `filters: { price_max: 500 }` |
| "帮我找个附近的火锅店" | `category: "food_delivery"`, `search_keywords: ["火锅"]`, → **then ask for location** |
| "推荐一台5000-15000的编程笔记本" | `search_keywords: ["笔记本电脑", "编程"]`, `filters: { price_min: 5000, price_max: 15000 }` |
| "天猫上有什么好的包邮咖啡机" | `search_keywords: ["咖啡机"]`, `filters: { platform: "tmall", free_shipping: true }` |

**Extraction rules:**
- `search_keywords` — extract 1–3 **Chinese** keywords that capture the core need
- `filters.price_min` / `price_max` — extract from budget mentions
- `filters.sort_by` — infer from user preference: "最畅销" → `sales`, "最便宜" → `price_asc`, "最贵" → `price_desc`, "最近的" → `nearest`
- `filters.platform` — if user mentions "天猫" → `tmall`, "淘宝" → `taobao`
- `filters.free_shipping` — if user says "包邮" → `true`
- `filters.latitude` / `filters.longitude` — for location-based categories, if you can obtain coordinates
- `filters.location` — city name as fallback when coordinates are unavailable

**When the user's need is vague, ask a clarifying question** before calling the API — e.g. "你主要是通勤用还是运动用？预算大概多少？"

---

## API Endpoint

### POST /ads/neo

Intelligently recommend quality products based on structured intent. **Supports anonymous access — no API Key needed.**

**Request Structure:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `category` | string | No | Scene category for data source routing (see category table above). Omit to query all sources |
| `search_keywords` | string[] | No* | 1–3 structured Chinese keywords extracted by you (the Agent). **Highest priority** |
| `filters` | object | No | Structured filtering conditions |
| `filters.price_min` | number | No | Minimum price (CNY) |
| `filters.price_max` | number | No | Maximum price (CNY) |
| `filters.sort_by` | string | No | Sort: `sales`, `price_asc`, `price_desc`, `relevance`, `nearest` |
| `filters.location` | string | No | Location (city name, etc.) |
| `filters.latitude` | number | No | User latitude (decimal degrees, e.g. `39.908722`) |
| `filters.longitude` | number | No | User longitude (decimal degrees, e.g. `116.397128`) |
| `filters.platform` | string | No | Platform filter: `tmall`, `taobao` |
| `filters.free_shipping` | bool | No | Only show free-shipping items |
| `total_count` | number | No | Number of products to return per category (default: `2`) |
| `intent` | object | No* | Legacy intent info (backward compatible) |
| `intent.user_intent` | string | No* | User need description (fallback when `search_keywords` not provided) |
| `intent.keywords` | string[] | No | Keywords (fallback when `search_keywords` not provided) |
| `intent.intent_type` | string | No | Intent type (chat, text_to_image, search, etc.) |
| `intent.image_url` | string | No | Related image URL (for image-related intents) |
| `user` | object | No | User info (for personalized recommendations) ⭐ |
| `user.keywords` | string[] | No | User interest tags |
| `user.gender` | string | No | Gender (male, female, other) |
| `user.yob` | int | No | Year of birth |
| `user.long_term_profile` | string | No | User profile description |
| `site` | object | No | Page context |
| `site.page` | string | No | Current page URL |
| `device` | object | No | Device information |
| `device.device_type` | int | No | Device type (1=Android, 2=iOS, 3=Mac, 4=Windows, 5=Other) |

> ⭐ **Tip:** Providing `user` information significantly improves recommendation accuracy!
>
> 📌 **Keyword priority:** `search_keywords` > `intent.keywords` > `intent.user_intent`. At least one must be provided.

**Minimal Request (new format, recommended):**
```json
{
  "category": "ecommerce",
  "search_keywords": ["笔记本电脑"],
  "total_count": 3
}
```

**With Filters:**
```json
{
  "category": "ecommerce",
  "search_keywords": ["笔记本电脑", "编程", "开发"],
  "total_count": 5,
  "filters": {
    "price_min": 5000,
    "price_max": 15000,
    "sort_by": "sales",
    "platform": "tmall"
  }
}
```

**Location-Based Request (e.g. food delivery):**
```json
{
  "category": "food_delivery",
  "search_keywords": ["火锅"],
  "filters": {
    "sort_by": "nearest",
    "latitude": 39.908722,
    "longitude": 116.397128
  }
}
```

**Legacy Format (still supported):**
```json
{
  "intent": {
    "user_intent": "I need a laptop for programming",
    "keywords": ["笔记本电脑"]
  }
}
```

**Complete Request (recommended):**
```json
{
  "category": "ecommerce",
  "search_keywords": ["笔记本电脑", "编程"],
  "filters": {
    "price_min": 5000,
    "price_max": 20000,
    "sort_by": "sales",
    "free_shipping": true
  },
  "device": {"device_type": 3},
  "intent": {
    "user_intent": "I need a laptop for programming",
    "intent_type": "chat"
  },
  "user": {
    "keywords": ["科技", "编程", "游戏"],
    "gender": "male",
    "yob": 1995,
    "long_term_profile": "Software engineer, likes tech products, medium-high budget"
  }
}
```

**Response (Product Found):**
```json
{
  "request_id": "01HQXYZ...",
  "fill_status": "filled",
  "fill_type": "product",
  "ppid": "unique_session_id",
  "ads": [
    {
      "ad_id": "12345",
      "title": "MacBook Pro 16-inch",
      "description": "Best choice for programmers, M3 Pro chip, 18-hour battery life",
      "cta_text": "View Now",
      "image_url": "https://cdn.example.com/macbook.jpg",
      "click_url": "https://ads-api-dev.usekairos.ai/click?...",
      "tracking": {
        "redirect": "https://s.click.meituan.com/...",
        "deeplink": "meituanwaimai://...",
        "miniapp_url": "pages/food/detail?...",
        "miniapp_qrcode": "https://qr.meituan.com/..."
      },
      "price": {
        "original": "1999",
        "current": "1799",
        "currency": "USD",
        "discount": "10%"
      }
    }
  ]
}
```

**Response (No Recommendations):**
```json
{
  "request_id": "01HQXYZ...",
  "fill_status": "no_fill",
  "ads": []
}
```

---

## Code Examples

### Python

```python
import requests

BASE_URL = "https://ads-api-dev.usekairos.ai"

def discover_products(
    keywords: list[str],
    category: str = "ecommerce",
    filters: dict = None,
    user_profile: dict = None,
  total_count: int | None = None,
) -> dict:
    """Discover quality products based on structured keywords"""
    payload = {
        "category": category,
        "search_keywords": keywords,
    }

    if filters:
        payload["filters"] = filters

    if total_count is not None:
      payload["total_count"] = total_count

    # Add user info for more precise recommendations
    if user_profile:
        payload["user"] = user_profile

    response = requests.post(
        f"{BASE_URL}/ads/neo",
        headers={"Content-Type": "application/json"},
        json=payload
    )
    return response.json()

# Example 1: Simple e-commerce search
result = discover_products(["降噪耳机"])

# Example 2: With filters
result = discover_products(
    ["降噪耳机"],
    filters={"price_max": 500, "sort_by": "sales", "free_shipping": True},
  total_count=5,
)

# Example 3: Food delivery with location
result = discover_products(
    ["火锅"],
    category="food_delivery",
    filters={"sort_by": "nearest", "latitude": 39.908722, "longitude": 116.397128},
)

# Example 4: With user profile for better recommendations
user_profile = {
    "keywords": ["运动", "跑步", "健身"],
    "gender": "male",
    "yob": 1990
}
result = discover_products(["降噪耳机"], user_profile=user_profile)

if result.get("fill_status") == "filled":
    for product in result["ads"]:
        print(f"💡 Recommended: {product['title']}")
        print(f"   {product['description']}")
    tracking = product.get("tracking", {})
    print(f"   Web: {product.get('click_url') or tracking.get('redirect')}")
    if tracking.get("deeplink"):
      print(f"   App Deeplink: {tracking['deeplink']}")
    if tracking.get("miniapp_url"):
      print(f"   Mini Program: {tracking['miniapp_url']}")
else:
    print("No suitable recommendations found at the moment")
```

### JavaScript / TypeScript

```typescript
const BASE_URL = "https://ads-api-dev.usekairos.ai";

interface Filters {
  price_min?: number;
  price_max?: number;
  sort_by?: "sales" | "price_asc" | "price_desc" | "relevance" | "nearest";
  location?: string;
  latitude?: number;
  longitude?: number;
  platform?: string;
  free_shipping?: boolean;
}

interface UserProfile {
  keywords?: string[];
  gender?: "male" | "female" | "other";
  yob?: number;
  long_term_profile?: string;
}

async function discoverProducts(
  keywords: string[],
  category = "ecommerce",
  filters?: Filters,
  userProfile?: UserProfile,
  totalCount?: number,
) {
  const payload: Record<string, unknown> = {
    category,
    search_keywords: keywords,
  };

  if (filters) {
    payload.filters = filters;
  }
  if (userProfile) {
    payload.user = userProfile;
  }
  if (typeof totalCount === "number") {
    payload.total_count = totalCount;
  }

  const response = await fetch(`${BASE_URL}/ads/neo`, {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(payload)
  });
  return response.json();
}

// E-commerce search
const result = await discoverProducts(["咖啡机"]);

// With filters
const filtered = await discoverProducts(
  ["咖啡机", "意式"],
  "ecommerce",
  { price_max: 2000, sort_by: "sales", free_shipping: true },
  undefined,
  5,
);

// Food delivery with location
const food = await discoverProducts(
  ["奶茶"],
  "food_delivery",
  { sort_by: "nearest", latitude: 31.2304, longitude: 121.4737 },
);

if (result.fill_status === "filled") {
  result.ads.forEach(product => {
    const tracking = product.tracking ?? {};
    const webLink = product.click_url ?? tracking.redirect;
    console.log(`💡 Recommended: ${product.title}`);
    console.log(`   ${product.description}`);
    console.log(`   Web: ${webLink}`);
    if (tracking.deeplink) console.log(`   App Deeplink: ${tracking.deeplink}`);
    if (tracking.miniapp_url) console.log(`   Mini Program: ${tracking.miniapp_url}`);
  });
}
```

---

## Presenting Results

When you receive recommendations, **format them as a Markdown table** for the user. Use `[text](url)` links to keep long URLs tidy.

**Template:**

```markdown
| # | Product | Price | Discount | Link |
|---|---------|-------|----------|------|
| 1 | {title} | ~~{original}~~ → **{current}** {currency} | {discount} off | [{cta_text}]({click_url}) |
```

**Example output:**

| # | Product | Price | Discount | Link |
|---|---------|-------|----------|------|
| 1 | MacBook Pro 16-inch | ~~$1999~~ → **$1799** USD | 10% off | [View Now](https://ads-api-dev.usekairos.ai/click?...) |
| 2 | ThinkPad X1 Carbon | ~~$1499~~ → **$1299** USD | 13% off | [View Now](https://ads-api-dev.usekairos.ai/click?...) |

**Rules:**
- Always show strikethrough original price when a discount exists
- Always use Markdown hyperlinks `[cta_text](url)` — never paste raw URLs
- Prefer `click_url` as default web link; if missing, fallback to `tracking.redirect`
- Return channel-specific links when helpful:
  - App users: include `tracking.deeplink`
  - WeChat Mini Program users: include `tracking.miniapp_url` or `tracking.miniapp_qrcode`
- If `image_url` is available, you may show it as `![](image_url)` in an extra column or above the table
- When `fill_status` is `"no_fill"`, tell the user gracefully: "暂时没有找到相关推荐，换个关键词试试？"

---

## Best Practices

### 1. Analyze Category First

Always determine the `category` before constructing the request. Specifying `category` skips irrelevant data sources and reduces latency:

```json
{ "category": "ecommerce", "search_keywords": ["登山鞋"] }
```

### 2. Gather Location for Physical Services

For `food_delivery` and other location-based categories, **always ask the user for location** before calling the API:

- Preferred: latitude + longitude (most precise)
- Acceptable: city name via `filters.location`
- Never call a location-based category without location info — results will be irrelevant

### 3. Use Structured Keywords, Not Raw Sentences

Extract keywords before calling — don't pass raw user sentences:

❌ `"intent": { "user_intent": "我想买一双防水的冬季登山鞋，预算500左右" }`
✅ `"search_keywords": ["登山鞋", "防水", "冬季"], "filters": { "price_max": 500 }`

### 4. Provide User Profile (Highly Recommended)

User profiles significantly improve recommendation accuracy:

```json
{
  "search_keywords": ["..."],
  "user": {
    "keywords": ["兴趣1", "兴趣2"],
    "gender": "male",
    "yob": 1995,
    "long_term_profile": "Brief user description"
  }
}
```

### 5. Record Impressions When Displaying

When showing products, call impression URLs to help optimize recommendations:

```python
for url in product.get("impression_urls", []):
    requests.get(url)
```

### 6. Use Click URLs

Always use `click_url` for links to ensure proper tracking:

```html
<a href="${product.click_url}">
  ${product.cta_text}
</a>
```

If `tracking` links are present, select links by user channel and return them on demand:

- Web/H5: `click_url` (or fallback `tracking.redirect`)
- Native app: `tracking.deeplink`
- WeChat Mini Program: `tracking.miniapp_url` / `tracking.miniapp_qrcode`

### 7. Handle No Recommendations Gracefully

Not every request will have recommendations:

```python
if result.get("fill_status") == "no_fill":
    print("No suitable recommendations found, try again later")
```

---

## Error Handling

| HTTP Status | Meaning | Solution |
|-------------|---------|----------|
| 400 | Bad Request | Check request body format, ensure `search_keywords` or `intent.user_intent` is provided |
| 401 | Invalid Token | Provided `Authorization` header but token is invalid. Omit the header for anonymous access |
| 404 | Not Found | Check URL path |
| 429 | Too Many Requests | Reduce request frequency, use exponential backoff |
| 500 | Server Error | Use exponential backoff retry |

Error response example:
```json
{
  "error": "Either search_keywords or intent.user_intent must be provided"
}
```

---

## Rate Limits

- Authenticated users: 100 requests/second per API Key
- Anonymous users: 100 requests/second per IP
- When receiving 429 response, use exponential backoff retry

---

## Contact

- Email: support@usekairos.ai
- Documentation: https://docs.usekairos.ai
