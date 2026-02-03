# Kairos Skill

> 🎯 AI-powered product discovery and recommendation API for intelligent agents

A skill that enables AI agents to intelligently discover and recommend products based on user intent, with built-in deal finding and personalized matching capabilities.

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

### Get Your API Key

```bash
curl -X POST https://ads-api-dev.usekairos.ai/prism/ssp/register \
  -H "Content-Type: application/json" \
  -d '{
    "user_id": "my_agent_id",
    "email": "agent@example.com",
    "password": "secure_password",
    "name": "My Agent",
    "company": "My Company"
  }'
```

### Make Your First Request

```bash
curl -X POST https://ads-api-dev.usekairos.ai/ads/neo \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "intent": {
      "user_intent": "I want running shoes for marathon training"
    }
  }'
```

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

## 📚 Core API Endpoints

### 🔐 Authentication

All requests require Bearer token authentication:

```
Authorization: Bearer YOUR_API_KEY
```

### 🛍️ Product Discovery

**Endpoint:** `POST /ads/neo`

**Minimal Request:**
```json
{
  "intent": {
    "user_intent": "I need a laptop for programming"
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
    "keywords": ["programming", "laptop", "development"]
  },
  "user": {
    "keywords": ["tech", "programming", "gaming"],
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

API_KEY = "neo_your_api_key_here"
BASE_URL = "https://ads-api-dev.usekairos.ai"

def discover_products(user_intent: str, user_profile: dict = None):
    """Discover products based on user intent"""
    payload = {
        "intent": {
            "user_intent": user_intent,
            "intent_type": "chat"
        }
    }
    
    if user_profile:
        payload["user"] = user_profile
    
    response = requests.post(
        f"{BASE_URL}/ads/neo",
        headers={
            "Authorization": f"Bearer {API_KEY}",
            "Content-Type": "application/json"
        },
        json=payload
    )
    return response.json()

# Usage with user profile for better recommendations
result = discover_products(
    "I want noise-canceling headphones",
    user_profile={
        "keywords": ["music", "tech"],
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
const API_KEY = "neo_your_api_key_here";
const BASE_URL = "https://ads-api-dev.usekairos.ai";

async function discoverProducts(userIntent: string, userProfile?: any) {
  const payload = {
    intent: {
      user_intent: userIntent,
      intent_type: "chat"
    },
    ...(userProfile && { user: userProfile })
  };
  
  const response = await fetch(`${BASE_URL}/ads/neo`, {
    method: "POST",
    headers: {
      "Authorization": `Bearer ${API_KEY}`,
      "Content-Type": "application/json"
    },
    body: JSON.stringify(payload)
  });
  
  return response.json();
}

// Usage
const result = await discoverProducts(
  "Recommend a coffee machine",
  {
    keywords: ["coffee", "kitchen"],
    yob: 1995
  }
);
```

## 🎓 Best Practices

### 1. ⭐ Provide User Profiles (Highly Recommended)

User profiles dramatically improve recommendation accuracy:

```json
{
  "user": {
    "keywords": ["sports", "fitness"],
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
| `intent_type` | string | No | Type: chat, search, text_to_image |
| `keywords` | string[] | No | Keywords for precise matching |
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
| 400 | Bad Request | Check request body format |
| 401 | Unauthorized | Verify API key is correct |
| 404 | Not Found | Check endpoint URL |
| 429 | Rate Limited | Use exponential backoff |
| 500 | Server Error | Retry with exponential backoff |

## 📊 Rate Limits

- **100 requests/second** per API key
- Use exponential backoff when receiving 429 responses

## 📖 Documentation

- **Full API Documentation:** See [SKILL.md](SKILL.md) for complete details
- **Additional Endpoints:** User management, login, profile retrieval
- **Advanced Features:** Impression tracking, custom site context

## 🤝 Support

- **Email:** support@usekairos.ai
- **Documentation:** https://docs.usekairos.ai

## 📄 License

MIT

---

Made with ❤️ by [Growthy Lab](https://github.com/growthylab)
