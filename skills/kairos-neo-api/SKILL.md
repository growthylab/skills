---
name: kairos-neo-api
description: Integrate Kairos Neo API for AI-powered product discovery and recommendations. Use when building agents that need to recommend products, discover deals, or help users find items to buy. Triggers on tasks involving "recommend products", "find deals", "product discovery", "shopping assistant", or "integrate product API".
license: MIT
metadata:
  author: growthylab
  version: "1.0.0"
---

# Kairos Neo API - 智能好物发现

A simple smart recommendation API that helps your Agent discover the best and most affordable products for users.

## Why Kairos Neo API?

- 🎯 **Precise Recommendations** - Intelligently matches high-quality products based on user intent
- 💰 **Deal Finder** - Automatically discovers discounts and helps users find the best deals
- ⚡ **Easy Integration** - Just one field needed to get recommendations
- 🔒 **Secure** - API Key authentication ensures data security

## Quick Start

### 1. Register for API Key

```bash
curl -X POST https://ads-api-dev.usekairos.ai/prism/ssp/register \
  -H "Content-Type: application/json" \
  -d '{
    "user_id": "your_unique_id",
    "email": "you@example.com",
    "password": "your_secure_password",
    "name": "Your Name",
    "company": "Your Company"
  }'
```

Response:
```json
{
  "user_id": "your_unique_id",
  "api_key": "neo_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
}
```

**Important:** Keep your `api_key` safe - you'll need it for all subsequent requests.

### 2. Discover Products

```bash
curl -X POST https://ads-api-dev.usekairos.ai/ads/neo \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "intent": {
      "user_intent": "I want to buy running shoes"
    }
  }'
```

That's it! For more precise recommendations, provide additional information (see complete request below).

---

## Authentication

All API requests require Bearer Token authentication:

```
Authorization: Bearer YOUR_API_KEY
```

API Key is obtained during registration. Keep it safe and never expose it in client-side code.

---

## API Endpoints

### POST /prism/ssp/register

Register a new account.

**Request Parameters:**
| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `user_id` | string | Yes | Your unique identifier (letters, numbers, underscores) |
| `email` | string | Yes | Email address |
| `password` | string | Yes | Password (recommend at least 8 characters) |
| `name` | string | No | Your name |
| `company` | string | No | Company name |

**Response:**
```json
{
  "user_id": "your_unique_id",
  "api_key": "neo_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
}
```

### POST /prism/ssp/login

Login to get API Key (if forgotten).

**Request:**
```json
{
  "email": "you@example.com",
  "password": "your_password"
}
```

**Response:**
```json
{
  "user_id": "your_unique_id",
  "api_key": "neo_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
}
```

### GET /prism/ssp/me

Get personal information.

**Headers:**
```
Authorization: Bearer YOUR_API_KEY
```

**Response:**
```json
{
  "user_id": "your_unique_id",
  "email": "you@example.com",
  "name": "Your Name",
  "company": "Your Company",
  "status": "active",
  "timezone": "UTC",
  "created_at": "2024-01-15T10:30:00Z"
}
```

### POST /ads/neo

Intelligently recommend quality products based on user intent.

**Request Structure:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `intent` | object | **Yes** | User intent information |
| `intent.user_intent` | string | **Yes** | User's need description |
| `intent.intent_type` | string | No | Intent type (chat, text_to_image, search, etc.) |
| `intent.image_url` | string | No | Related image URL (for image-related intents) |
| `intent.keywords` | string[] | No | Keywords (for more precise matching) |
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

**Minimal Request:**
```json
{
  "intent": {
    "user_intent": "I need a laptop for programming"
  }
}
```

**Complete Request (Recommended):**
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

API_KEY = "neo_your_api_key_here"
BASE_URL = "https://ads-api-dev.usekairos.ai"

def discover_products(user_intent: str, user_profile: dict = None) -> dict:
    """Discover quality products based on user intent"""
    payload = {
        "intent": {
            "user_intent": user_intent,
            "intent_type": "chat"
        }
    }
    
    # Add user info for more precise recommendations
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

# Usage example with user profile
user_profile = {
    "keywords": ["sports", "running", "fitness"],
    "gender": "male",
    "yob": 1990
}
result = discover_products("I want noise-canceling headphones", user_profile)

if result.get("fill_status") == "filled":
    for product in result["ads"]:
        print(f"💡 Recommended: {product['title']}")
        print(f"   {product['description']}")
        print(f"   Details: {product['click_url']}")
else:
    print("No suitable recommendations found at the moment")
```

### JavaScript / TypeScript

```typescript
const API_KEY = "neo_your_api_key_here";
const BASE_URL = "https://ads-api-dev.usekairos.ai";

interface UserProfile {
  keywords?: string[];
  gender?: "male" | "female" | "other";
  yob?: number;
  long_term_profile?: string;
}

async function discoverProducts(userIntent: string, userProfile?: UserProfile) {
  const payload: any = {
    intent: {
      user_intent: userIntent,
      intent_type: "chat"
    }
  };
  
  if (userProfile) {
    payload.user = userProfile;
  }
  
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

// Usage example with user profile
const userProfile: UserProfile = {
  keywords: ["music", "tech"],
  gender: "female",
  yob: 1995,
  long_term_profile: "Designer, likes minimalist style"
};

const result = await discoverProducts("Recommend a coffee machine", userProfile);
if (result.fill_status === "filled") {
  result.ads.forEach(product => {
    console.log(`💡 Recommended: ${product.title}`);
    console.log(`   ${product.description}`);
  });
}
```

---

## Best Practices

### 1. Provide User Profile (Highly Recommended)

User profiles significantly improve recommendation accuracy:

```json
{
  "intent": { "user_intent": "..." },
  "user": {
    "keywords": ["interest1", "interest2"],
    "gender": "male",
    "yob": 1995,
    "long_term_profile": "Brief user description"
  }
}
```

### 2. Be Specific with Intent

More specific descriptions lead to better recommendations:

❌ Not recommended: `"shoes"`  
✅ Recommended: `"I want waterproof winter hiking boots, budget around $100"`

### 3. Record Impressions When Displaying

When showing products, call impression URLs to help optimize recommendations:

```python
for url in product.get("impression_urls", []):
    requests.get(url)
```

### 4. Use Click URLs

Always use `click_url` for links to ensure proper tracking:

```html
<a href="${product.click_url}">
  ${product.cta_text}
</a>
```

### 5. Handle No Recommendations Gracefully

Not every request will have recommendations:

```python
if result.get("fill_status") == "no_fill":
    print("No suitable recommendations found, try again later")
```

---

## Error Handling

| HTTP Status | Meaning | Solution |
|-------------|---------|----------|
| 400 | Bad Request | Check request body format |
| 401 | Unauthorized | Check if API Key is correct |
| 404 | Not Found | Check URL path |
| 429 | Too Many Requests | Reduce request frequency, use exponential backoff |
| 500 | Server Error | Use exponential backoff retry |

Error response example:
```json
{
  "error": "user_intent field is required"
}
```

---

## Rate Limits

- Maximum 100 requests/second per API Key
- When receiving 429 response, use exponential backoff retry

---

## Contact

- Email: support@usekairos.ai
- Documentation: https://docs.usekairos.ai
