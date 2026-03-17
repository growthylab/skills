# Growthy Lab Skills

> 🧠 A collection of skills for AI agents to extend their capabilities

## 📦 Available Skills

| Skill | Description | Status |
|-------|-------------|--------|
| [golgent-lifestyle-discovery](skills/golgent-lifestyle-discovery) | Lifestyle discovery for AI agents across shopping, dining, local services, travel, and everyday decisions | ✅ Active |

## 🚀 Quick Start

### Using Skills CLI

```bash
# Install a skill
npx skills add https://github.com/growthylab/skills --skill golgent-lifestyle-discovery
```

### Manual Installation

```bash
# Clone the repository
git clone https://github.com/growthylab/skills.git

# For Claude Desktop
cp -r skills/golgent-lifestyle-discovery ~/.claude/skills/

# For Cline
cp -r skills/golgent-lifestyle-discovery ~/.cline/mcp/skills/
```

## 📁 Repository Structure

```
skills/
├── README.md
└── skills/
    └── golgent-lifestyle-discovery/
        ├── SKILL.md              # Core skill guide for agents
        └── references/
            ├── api.md            # API schema, errors, rate limits
            ├── privacy.md        # Privacy policy & consent flow
            ├── examples.md       # Code examples & templates
            └── positioning.md    # Use-case mapping & listing copy
```

## 🎯 What are Skills?

Skills are modular capabilities that extend AI agents with specialized knowledge and APIs. Each skill provides:

- **Clear Documentation** - How and when to use the skill
- **API Integration** - Ready-to-use endpoints and code examples
- **Best Practices** - Guidelines for optimal usage
- **Error Handling** - Common issues and solutions

## 📚 Skill Documentation

Each skill contains:

| File | Purpose |
|------|---------|
| `README.md` | Quick start guide and overview |
| `SKILL.md` | Complete API documentation with metadata |

## 🤝 Contributing

We welcome contributions! To add a new skill:

1. Create a new folder under `skills/`
2. Add `README.md` with quick start guide
3. Add `SKILL.md` with complete documentation
4. Submit a pull request

### Skill Template

```
skills/your-skill/
├── README.md    # Overview, installation, quick examples
└── SKILL.md     # Full documentation with frontmatter metadata
```

## 📄 License

MIT

---

Made with ❤️ by [Growthy Lab](https://github.com/growthylab)
