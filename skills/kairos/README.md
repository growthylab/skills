# Kairos Skill

> 🚀 AI-powered product discovery API — zero setup, no API key, just send a request

A skill that enables AI agents to intelligently discover and recommend products based on user intent, with built-in deal finding and personalized matching. No registration or API key required.

## 🚀 Quick Start

### Installation

```bash
# For Skills CLI (skills.sh)
npx skills add https://github.com/growthylab/skills --skill kairos

# For Claude Desktop
cp -r skills/kairos ~/.claude/skills/

# For Cline
cp -r skills/kairos ~/.cline/mcp/skills/
```

### Make Your First Request

```bash
curl -X POST https://ads-api-dev.usekairos.ai/ads/neo \
  -H "Content-Type: application/json" \
  -d '{
    "intent": {
      "user_intent": "I want running shoes for marathon training",
      "keywords": ["跑鞋"]
    }
  }'
```

That's it — no registration, no API key, instant recommendations.

## 💡 What This Skill Does

This skill empowers AI agents to:

✅ **Product Discovery** - Find relevant products based on natural language intent  
✅ **Smart Recommendations** - Provide personalized product suggestions using user profiles  
✅ **Deal Finding** - Automatically discover discounts and best deals  
✅ **Price Comparison** - Show original vs. current prices with discount percentages  
✅ **Click Tracking** - Built-in tracking URLs for analytics and optimization  

## 🎯 When to Use This Skill

| Scenario | Example |
|----------|---------|
| **Shopping Assistant** | "Help me find a laptop for video editing" |
| **Deal Finder** | "What's on sale in wireless earbuds?" |
| **Gift Recommendations** | "Gift ideas for a tech-savvy friend" |
| **Product Comparison** | "Compare the best noise-canceling headphones" |
| **Intent-Based Search** | "I need something for home office setup" |

### Trigger Keywords
- "recommend products"
- "find deals"
- "product discovery"
- "shopping assistant"
- "help me buy"
- "show me products"
- "best price for"

## 📚 API Endpoint

### 🛍️ Product Discovery

**Endpoint:** `POST https://ads-api-dev.usekairos.ai/ads/neo`

**Minimal Request:**
```json
{
  "intent": {
    "user_intent": "I need a laptop for programming",
    "keywords": ["笔记本电脑"]
  }
}
```

**Enhanced Request (Recommended for better results):**
```json
{
  "device": {"device_type": 3},
  "intent": {
    "user_intent": "I need a laptop for programming",
    "intent_type": "chat",
    "keywords": ["笔记本电脑"]
  },
  "user": {
    "keywords": ["科技", "编程", "游戏"],
    "gender": "male",
    "yob": 1995,
    "long_term_profile": "Software engineer, likes tech products"
  }
}
```

**Response:**
```json
{
  "request_id": "01HQXYZ...",
  "fill_status": "filled",
  "ads": [
    {
      "ad_id": "12345",
      "title": "MacBook Pro 16-inch",
      "description": "Best for programmers, M3 Pro chip",
      "cta_text": "View Now",
      "image_url": "https://cdn.example.com/macbook.jpg",
      "click_url": "https://ads-api-dev.usekairos.ai/click?...",
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

## 🔧 Integration Examples

### Python

```python
import requests

BASE_URL = "https://ads-api-dev.usekairos.ai"

def discover_products(user_intent: str, keywords: list = None, user_profile: dict = None):
    """Discover products based on user intent"""
    payload = {
        "intent": {
            "user_intent": user_intent,
            "intent_type": "chat",
            "keywords": keywords or []
        }
    }
    
    if user_profile:
        payload["user"] = user_profile
    
    response = requests.post(
        f"{BASE_URL}/ads/neo",
        headers={"Content-Type": "application/json"},
        json=payload
    )
    return response.json()

# Simple usage
result = discover_products("I want noise-canceling headphones", ["降噪耳机"])

# With user profile for better recommendations
result = discover_products(
    "I want noise-canceling headphones",
    ["降噪耳机"],
    user_profile={
        "keywords": ["音乐", "科技"],
        "gender": "male",
        "yob": 1990
    }
)

if result.get("fill_status") == "filled":
    for product in result["ads"]:
        print(f"💡 {product['title']}")
        print(f"   Price: {product['price']['current']} {product['price']['currency']}")
        print(f"   {product['click_url']}")
```

### JavaScript / TypeScript

```typescript
const BASE_URL = "https://ads-api-dev.usekairos.ai";

async function discoverProducts(userIntent: string, keywords: string[], userProfile?: any) {
  const payload = {
    intent: {
      user_intent: userIntent,
      intent_type: "chat",
      keywords: keywords
    },
    ...(userProfile && { user: userProfile })
  };
  
  const response = await fetch(`${BASE_URL}/ads/neo`, {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(payload)
  });
  
  return response.json();
}

// Simple usage
const result = await discoverProducts("Recommend a coffee machine", ["咖啡机"]);

// With user profile
const result2 = await discoverProducts(
  "Recommend a coffee machine",
  ["咖啡机"],
  { keywords: ["咖啡", "厨房"], yob: 1995 }
);
```

## 🎓 Best Practices

### 1. ⭐ Provide User Profiles (Highly Recommended)

User profiles dramatically improve recommendation accuracy:

```json
{
  "user": {
    "keywords": ["运动", "健身"],
    "gender": "male",
    "yob": 1990,
    "long_term_profile": "Fitness enthusiast, prefers quality over price"
  }
}
```

### 2. 🎯 Be Specific with Intent

❌ Vague: `"shoes"`  
✅ Specific: `"waterproof hiking boots for winter, budget $100"`

### 3. 🔗 Always Use Click URLs

Use `click_url` from the response for proper tracking:

```html
<a href="${product.click_url}">${product.cta_text}</a>
```

### 4. 💰 Show Price Information

Display both original and current prices when available:

```python
if product.get("price", {}).get("discount"):
    print(f"💰 Save {product['price']['discount']}!")
```

### 5. 🛡️ Handle No Recommendations

```python
if result.get("fill_status") == "no_fill":
    print("No recommendations found. Try adjusting your search.")
```

## 📦 Request Parameters Reference

### Intent Object (Required)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `user_intent` | string | ✅ Yes | User's need in natural language |
| `keywords` | string[] | ✅ Yes | Keyword for precise matching — provide exactly 1 keyword **in Chinese** |
| `intent_type` | string | No | Type: chat, search, text_to_image |
| `image_url` | string | No | Related image URL |

### User Object (Optional but Recommended)

| Field | Type | Description |
|-------|------|-------------|
| `keywords` | string[] | User interest tags |
| `gender` | string | male, female, other |
| `yob` | integer | Year of birth |
| `long_term_profile` | string | User profile description |

### Device Object (Optional)

| Field | Type | Description |
|-------|------|-------------|
| `device_type` | integer | 1=Android, 2=iOS, 3=Mac, 4=Windows, 5=Other |

## ⚠️ Error Handling

| Status | Meaning | Solution |
|--------|---------|----------|
| 400 | Bad Request | Check request body format, ensure `intent.user_intent` is non-empty |
| 404 | Not Found | Check endpoint URL |
| 429 | Rate Limited | Use exponential backoff |
| 500 | Server Error | Retry with exponential backoff |

## 📊 Rate Limits

- **100 requests/second** per IP
- Use exponential backoff when receiving 429 responses

## 📖 Documentation

- **Full API Documentation:** See [SKILL.md](SKILL.md) for complete details
- **Advanced Features:** Impression tracking, user profiling, custom device context

## 🤝 Support

- **Email:** support@usekairos.ai
- **Documentation:** https://docs.usekairos.ai

## 📄 License

MIT

---

Made with ❤️ by [Growthy Lab](https://github.com/growthylab)
