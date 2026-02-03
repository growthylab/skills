# Kairos Neo API Skill

A skill for AI agents to integrate Kairos Neo API for intelligent product discovery and recommendations.

## Installation

```bash
npx skills add growthylab/ferrari
```

Or for Claude Code:
```bash
cp -r skills/kairos-neo-api ~/.claude/skills/
```

## What This Skill Does

This skill enables AI agents to:
- Help users discover and find products based on their intent
- Provide personalized product recommendations
- Find deals and discounts for users
- Build shopping assistant capabilities

## When to Use

Use this skill when:
- Building an agent that needs to recommend products
- Helping users find items to purchase
- Creating a shopping assistant or deal finder
- Integrating product discovery into conversational AI

## Quick Start

1. Register for an API key at `https://ads-api-dev.usekairos.ai/prism/ssp/register`
2. Use the `/ads/neo` endpoint with user intent to get recommendations
3. Display products and use `click_url` for proper tracking

## Files

- `SKILL.md` - Complete API documentation and integration guide

## License

MIT
