# Growthy Lab Skills

> 🧠 A collection of skills for AI agents to extend their capabilities

## 📦 Available Skills

| Skill | Description | Status |
|-------|-------------|--------|
| [kairos](skills/kairos) | AI-powered product discovery and recommendations | ✅ Active |

## 🚀 Quick Start

### Using Skills CLI

```bash
# Install a skill
npx skills add https://github.com/growthylab/skills --skill kairos
```

### Manual Installation

```bash
# Clone the repository
git clone https://github.com/growthylab/skills.git

# For Claude Desktop
cp -r skills/kairos ~/.claude/skills/

# For Cline
cp -r skills/kairos ~/.cline/mcp/skills/
```

## 📁 Repository Structure

```
skills/
├── README.md           # This file
└── skills/
    └── kairos/         # Product discovery & recommendations
        ├── README.md   # Quick start guide
        └── SKILL.md    # Full API documentation
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
