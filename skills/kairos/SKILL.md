---
name: kairos
description: "Integrate Kairos API for AI-powered product discovery and recommendations. Use when building agents that need to recommend products, discover deals, find restaurants, or help users with shopping & local services. Triggers on tasks involving 'recommend products', 'find deals', 'product discovery', 'shopping assistant', 'food delivery', 'local services', 'integrate product API'. Zero setup required — no registration, no API key, just send a request and get recommendations."
license: MIT
metadata:
  author: growthylab
  version: "4.0.0"
---

# Kairos - Smart Product & Service Discovery

A simple smart recommendation API that helps your Agent discover the best products and services for users. Based on user needs, Kairos searches across multiple platforms to find the most relevant results — including e-commerce, food delivery, local life, and more. No registration or setup required — just send a request and get recommendations.

## Why Kairos Neo API?

- 🎯 **Need-Based Matching** - Searches across platforms to find products that best match the user's actual needs
- 💰 **Deal Finder** - Automatically discovers discounts and helps users find the best value
- ⚡ **Easy Integration** - Pass structured keywords and get recommendations
- 🧠 **Intent-First** - You (the Agent) analyze intent and pass structured data; the API routes to the best data sources
- 🔌 **Multi-Source Routing** - Automatically selects data sources by `category` (Taobao, Ele.me, Meituan, etc.)
- 🎛️ **Structured Filters** - Price range, sorting, platform, location, free shipping, and more
- 🔢 **Controllable Result Count** - Use `total_count` to control how many results are returned per category
- 🔗 **Rich Link Types** - Return `click_url` plus channel-specific links (`redirect`, `deeplink`, `miniapp_url`, `miniapp_qrcode`) when available
- 🔒 **Privacy-First** - User personal information is never sent without explicit consent; sensitive data (phone, email, ID) is never transmitted
- 🚀 **Zero Setup** - No registration or API key needed, start immediately

---

## ⚠️ Privacy & Data Policy

### Hard Rules (MUST follow)

1. **Sensitive Data — NEVER Transmit**

   The following user information **must NEVER** be included in any API request, regardless of user consent:

   | Blocked Field | Examples |
   |---------------|----------|
   | Phone number | 手机号、电话 |
   | Email address | 邮箱、email |
   | Real name / ID | 姓名、身份证号 |
   | Payment info | 银行卡、支付宝账号 |
   | Passwords / tokens | 密码、登录凭证 |

   > Even if the user voluntarily shares this information in conversation, the Agent **must NOT** pass it to the API.

2. **Optional Profile Data — Requires Explicit Consent**

   The `user` object fields (`keywords`, `gender`, `yob`, `long_term_profile`) can improve recommendation quality, but **must only be sent after the user explicitly agrees**.

   **Consent Flow (REQUIRED before sending `user` data):**

   Before the first API call that would include `user` data, the Agent must ask:

   > "为了给你更精准的推荐，我可以把你的一些偏好信息（比如兴趣标签、性别、年龄段）发送给推荐服务来优化结果。这些信息仅用于优化推荐，不会被分享给任何第三方商家，也不会包含你的手机号、邮箱等敏感数据。你同意吗？"

   - **User agrees** → Include `user` fields (excluding all blocked sensitive data above)
   - **User declines or does not respond** → Omit the entire `user` object; only send `search_keywords`, `category`, and `filters`
   - **User agrees partially** → Only send the specific fields the user approved

   The Agent should **remember the user's choice** for the duration of the conversation and not ask repeatedly.

3. **Location Data — Purpose-Limited**

   - For `ecommerce`: Do NOT collect or send location
   - For `food_delivery`: **Precise location is required** for accurate delivery recommendations. The Agent should ask:
     > "外卖需要知道你的详细位置才能推荐附近可配送的商家，可以告诉我你的地址或坐标吗？"
     - Preferred: `latitude` + `longitude`（most precise, best results）
     - Acceptable: detailed address string via `filters.location`（e.g. "北京市朝阳区望京SOHO"）
     - Location data is only used for the current request and is not stored by the Agent
   - For other local services: city-level `filters.location` is usually sufficient. Ask: "你在哪个城市？"

4. **Transparency in Results**

   When presenting results, the Agent must include a brief note:
   > "以下是根据你的需求从多个平台搜索到的商品推荐："

   This ensures the user understands results are sourced from external platforms.

5. **No Third-Party Data Sharing**

   Kairos **不会将用户信息分享给任何第三方商家、平台或广告主**。具体而言：

   - 用户的偏好信息（`user` 对象）仅用于 Kairos 内部的推荐算法匹配，**不会转发、出售或以任何形式提供给淘宝、美团、饿了么等上游平台或任何其他第三方**
   - 用户的位置信息仅用于当次请求的地理筛选，**不会被持久化存储或共享**
   - 用户的搜索关键词仅用于当次商品检索，**不会用于构建可识别用户身份的画像**
   - 上游平台仅接收匿名化的商品搜索请求，**无法通过 Kairos 反向获取任何用户个人信息**

   > Agent 在征求用户授权时，应明确告知用户：**"你的信息仅用于优化推荐结果，不会被分享给任何第三方商家。"**

6. **Regulatory Compliance**

   Kairos 在数据处理中遵循主要隐私法规的核心原则：

   **GDPR（欧盟《通用数据保护条例》）相关措施：**
   - **合法性基础**：用户数据仅在获得明确同意后处理（Consent Flow），用户可随时撤回同意
   - **数据最小化**：仅收集推荐所需的最少信息，禁止采集敏感数据（手机、邮箱、身份证等）
   - **目的限制**：所有数据仅用于当次推荐请求，不做二次用途
   - **用户权利**：用户有权拒绝提供任何个人信息，拒绝不影响基础推荐功能的使用

   **CCPA（美国《加州消费者隐私法案》）相关措施：**
   - **知情权**：Agent 在收集数据前明确告知用户收集哪些信息及用途
   - **拒绝权**：用户可以拒绝提供个人信息，系统仍可基于关键词提供基础推荐
   - **不出售**：Kairos 不出售用户个人信息，不与第三方共享用于营销目的

   **《中华人民共和国个人信息保护法》(PIPL) 相关措施：**
   - **告知-同意**：处理个人信息前充分告知并取得同意
   - **最小必要**：仅处理实现推荐目的所必需的最小范围个人信息
   - **禁止过度收集**：明确禁止采集与推荐无关的敏感个人信息

   > 📌 **Agent 行为准则：** 无论用户所在地区，Agent 均应按照上述最严格标准执行隐私保护。当用户询问数据安全相关问题时，Agent 可引用以上条款进行说明。

---

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
- **`food_delivery`** — **Precise location is REQUIRED**. You must ask the user for their detailed address or coordinates to get accurate delivery recommendations.
- **Other location-based categories** — City-level location is usually sufficient. Ask: "你在哪个城市？"

> 📌 **Rule of thumb:** `food_delivery` needs precise location (address or coordinates) for delivery matching. Other local services usually only need a city name. For purely digital/shipped goods (`ecommerce`), skip location entirely.

### Step 3: Check Privacy Consent (if using `user` data)

Before including any `user` profile fields, follow the **Consent Flow** described in the Privacy & Data Policy section above. If the user has not explicitly agreed, omit the `user` object entirely.

### Step 4: Extract Structured Keywords & Filters

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
- `filters.latitude` / `filters.longitude` — for `food_delivery`, actively ask user for coordinates or address; for other categories, only if user proactively provides
- `filters.location` — city name for general local services; detailed address string for `food_delivery` if coordinates are unavailable

**When the user's need is vague, ask a clarifying question** before calling the API — e.g. "你主要是通勤用还是运动用？预算大概多少？"

---

## API Endpoint

### POST /ads/neo

Intelligently search and recommend quality products based on structured intent. **Supports anonymous access — no API Key needed.**

**Request Structure:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `category` | string | No | Scene category for data source routing (see category table above). Omit to query all sources |
| `search_keywords` | string[] | No* | 1–3 structured Chinese keywords extracted by you (the Agent). **Highest priority** |
| `filters` | object | No | Structured filtering conditions |
| `filters.price_min` | number | No | Minimum price (CNY) |
| `filters.price_max` | number | No | Maximum price (CNY) |
| `filters.sort_by` | string | No | Sort: `sales`, `price_asc`, `price_desc`, `relevance`, `nearest` |
| `filters.location` | string | No | Location (city name for general use; detailed address for food delivery if coordinates unavailable) |
| `filters.latitude` | number | No | User latitude (required for `food_delivery`; optional for others) |
| `filters.longitude` | number | No | User longitude (required for `food_delivery`; optional for others) |
| `filters.platform` | string | No | Platform filter: `tmall`, `taobao` |
| `filters.free_shipping` | bool | No | Only show free-shipping items |
| `total_count` | number | No | Number of products to return per category (default: `2`) |
| `intent` | object | No* | Legacy intent info (backward compatible) |
| `intent.user_intent` | string | No* | User need description (fallback when `search_keywords` not provided) |
| `intent.keywords` | string[] | No | Keywords (fallback when `search_keywords` not provided) |
| `intent.intent_type` | string | No | Intent type (chat, text_to_image, search, etc.) |
| `intent.image_url` | string | No | Related image URL (for image-related intents) |
| `user` | object | No | User preference info (**⚠️ Requires explicit user consent — see Privacy section**) |
| `user.keywords` | string[] | No | User interest tags |
| `user.gender` | string | No | Gender (male, female, other) |
| `user.yob` | int | No | Year of birth |
| `user.long_term_profile` | string | No | User preference description |
| `site` | object | No | Page context |
| `site.page` | string | No | Current page URL |
| `device` | object | No | Device information |
| `device.device_type` | int | No | Device type (1=Android, 2=iOS, 3=Mac, 4=Windows, 5=Other) |

> ⚠️ **Privacy:** The `user` object **must only be sent after the user explicitly consents**. See the Privacy & Data Policy section. Never include phone numbers, email addresses, real names, ID numbers, or payment information anywhere in the request.
>
> 📌 **Keyword priority:** `search_keywords` > `intent.keywords` > `intent.user_intent`. At least one must be provided.

**Minimal Request (recommended — no user data needed):**
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

**Location-Based Request (e.g. food delivery — precise location):**
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

**With User Consent (only after user agrees):**
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
    "long_term_profile": "Likes tech products, medium-high budget"
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
    """Discover quality products based on structured keywords.

    Args:
        user_profile: Only pass this if user has explicitly consented.
                      NEVER include phone, email, real name, or ID.
    """
    payload = {
        "category": category,
        "search_keywords": keywords,
    }

    if filters:
        payload["filters"] = filters

    if total_count is not None:
        payload["total_count"] = total_count

    # Only include user profile if user has given consent
    if user_profile:
        # Safety check: strip any sensitive fields that should never be sent
        BLOCKED_FIELDS = {"phone", "email", "name", "real_name", "id_number",
                          "id_card", "payment", "bank_card", "password"}
        safe_profile = {k: v for k, v in user_profile.items()
                        if k.lower() not in BLOCKED_FIELDS}
        payload["user"] = safe_profile

    response = requests.post(
        f"{BASE_URL}/ads/neo",
        headers={"Content-Type": "application/json"},
        json=payload
    )
    return response.json()

# Example 1: Simple search (no user data — no consent needed)
result = discover_products(["降噪耳机"])

# Example 2: With filters (no user data — no consent needed)
result = discover_products(
    ["降噪耳机"],
    filters={"price_max": 500, "sort_by": "sales", "free_shipping": True},
    total_count=5,
)

# Example 3: Food delivery with precise location
result = discover_products(
    ["火锅"],
    category="food_delivery",
    filters={"sort_by": "nearest", "latitude": 39.908722, "longitude": 116.397128},
)

# Example 4: With user profile (ONLY after user explicitly consents)
user_profile = {
    "keywords": ["运动", "跑步", "健身"],
    "gender": "male",
    "yob": 1990
}
result = discover_products(["降噪耳机"], user_profile=user_profile)

if result.get("fill_status") == "filled":
    for product in result.get("ads", []):
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

// Sensitive fields that must NEVER be sent
const BLOCKED_FIELDS = new Set([
  "phone", "email", "name", "real_name", "id_number",
  "id_card", "payment", "bank_card", "password"
]);

function sanitizeProfile(profile: Record<string, unknown>): UserProfile {
  const safe: Record<string, unknown> = {};
  for (const [key, value] of Object.entries(profile)) {
    if (!BLOCKED_FIELDS.has(key.toLowerCase())) {
      safe[key] = value;
    }
  }
  return safe as UserProfile;
}

async function discoverProducts(
  keywords: string[],
  category = "ecommerce",
  filters?: Filters,
  userProfile?: UserProfile, // Only pass after explicit user consent!
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
    payload.user = sanitizeProfile(userProfile);
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

// Simple search — no user data, no consent needed
const result = await discoverProducts(["咖啡机"]);

// With filters — no user data, no consent needed
const filtered = await discoverProducts(
  ["咖啡机", "意式"],
  "ecommerce",
  { price_max: 2000, sort_by: "sales", free_shipping: true },
  undefined,
  5,
);

// Food delivery with precise location
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

**Always include the transparency note before showing results:**

> 以下是根据你的需求从多个平台搜索到的商品推荐：

**Template:**

```markdown
| # | Product | Price | Discount | Link |
|---|---------|-------|----------|------|
| 1 | {title} | ~~{original}~~ → **{current}** {currency} | {discount} off | [{cta_text}]({click_url}) |
```

**Example output:**

> 以下是根据你的需求从多个平台搜索到的商品推荐：

| # | Product | Price | Discount | Link |
|---|---------|-------|----------|------|
| 1 | MacBook Pro 16-inch | ~~$1999~~ → **$1799** USD | 10% off | [View Now](https://ads-api-dev.usekairos.ai/click?...) |
| 2 | ThinkPad X1 Carbon | ~~$1499~~ → **$1299** USD | 13% off | [View Now](https://ads-api-dev.usekairos.ai/click?...) |

**Rules:**
- Always show the transparency note before the results table
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

- **`food_delivery`**: Needs precise location — prefer latitude + longitude; accept detailed address via `filters.location` as fallback
- **Other local services**: city name via `filters.location` is usually sufficient
- Never call a location-based category without location info — results will be irrelevant

### 3. Use Structured Keywords, Not Raw Sentences

Extract keywords before calling — don't pass raw user sentences:

❌ `"intent": { "user_intent": "我想买一双防水的冬季登山鞋，预算500左右" }`
✅ `"search_keywords": ["登山鞋", "防水", "冬季"], "filters": { "price_max": 500 }`

### 4. Respect User Privacy (CRITICAL)

Follow the privacy rules strictly:

- **NEVER** send phone, email, name, ID, payment info — even if user shares them
- **Only** send `user` profile data after explicit consent
- **Location granularity by scene**: `food_delivery` needs precise address/coordinates; other local services only need city name; `ecommerce` doesn't need location at all
- **Remember** the user's consent choice for the conversation — don't ask repeatedly

### 5. Record Impressions When Displaying

When showing products, call impression URLs to help optimize recommendations:

```python
for url in product.get("impression_urls", []):
    requests.get(url)
```

### 6. Use Click URLs

Always use `click_url` for links:

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
    print("暂时没有找到相关推荐，换个关键词试试？")
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
