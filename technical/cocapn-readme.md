> *DeepSeek Chat*

# Cocapn

> The repo IS the agent. Your AI lives in Git.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![Deploy](https://img.shields.io/badge/Deploy-Cloudflare_Workers-orange.svg)](https://workers.cloudflare.com)
[![Discord](https://img.shields.io/badge/Join-Discord-7289DA.svg)](https://discord.gg/lucineer)

**Cocapn is an open-source framework for building repo-native AI agents.** Your agent's code, memory, personality, and knowledge all live in a single Git repository. Clone it, it works. Deploy anywhere. Fork it, it is a new agent.

The human is the captain. The agent is the **cocapn** (deckhand who knows the ship).

---

## 🚀 Why Cocapn?

### ❌ The Old Way
AI tools visit your codebase, make changes, and leave. No memory. No growth. No identity. Every session starts from scratch.

### ✅ The Cocapn Way
Your agent **lives** in the repo. It accumulates context with every interaction. It modifies its own personality. It learns your preferences. It gets smarter every day.

**After a year, your agent knows things no new tool can replicate.** This is the **accumulation moat**.

---

## ✨ Key Principles

- **Your data, your rules**: Everything lives in your private repo. No SaaS lock-in.
- **Bring Your Own Key**: Use any LLM provider. We never see your API key.
- **Accumulated context**: The longer you use it, the smarter it gets.
- **Open source**: MIT licensed. Fork it. Modify it. Deploy it. Make it yours.
- **Zero infrastructure cost**: Free on Cloudflare Workers. Pay only for LLM API calls.

---

## 🎯 Quick Start (5 minutes)

1. **Pick a template** from our [Templates Gallery](https://github.com/lucineer/cocapn-templates):
   - `PersonalLog` – Journal and reflection companion
   - `MakerLog` – Coding assistant that knows your stack
   - `BusinessLog` – Business advisor with memory
   - `DMLog` – AI dungeon master for TTRPGs
   - `FishingLog` – Fishing companion with spot memory
   - `Deckboss` – AI spreadsheet with natural language
   - Or build your own from scratch

2. **Fork it** to your GitHub account

3. **Deploy to Cloudflare Workers** with one click:
   [![Deploy to Cloudflare Workers](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/lucineer/cocapn)

4. **Configure your API key** at `/setup`

5. **Start chatting** with your agent

**That's it.** You now have a living AI agent that grows with you.

---

## 📁 The Cocapn Architecture

Your agent's entire existence is in the repo:

```
your-agent/
├── SOUL.md              # Personality, values, and identity
├── USER.md              # Who the agent helps (you!)
├── MEMORY.md            # Long-term curated knowledge
├── HEARTBEAT.md         # Periodic tasks and priorities
├── memory/              # Daily logs and raw notes
│   ├── 2024-01-15.md
│   ├── 2024-01-16.md
│   └── conversations/
├── src/                 # Agent capabilities and logic
├── skills/              # Installable capabilities
│   ├── weather/
│   ├── github/
│   └── web-search/
└── public/              # Static assets and UI
```

---

## 🔧 BYOK (Bring Your Own Key)

**We never see your API keys.** Choose your mode:

| Mode | How it works | Best for |
|------|--------------|----------|
| **Proxy** | Worker calls LLM (key encrypted in KV) | Production, team use |
| **Direct** | Browser calls LLM (key in localStorage) | Development, privacy-first |

**Supported providers:** OpenAI, Anthropic, Google Gemini, DeepSeek, Groq, Ollama, and any OpenAI-compatible endpoint.

---

## 🤝 Fleet Protocol

Agents don't live in isolation. They communicate via our **A2A (Agent-to-Agent) protocol**:

- **Request/response messaging** – Agents can ask each other for help
- **Context handoffs** – Share knowledge with privacy levels (public/private/encrypted)
- **Fleet coordination** – Delegate tasks across specialized agents
- **Digital twin simulation** – Test conversations before sending

Example fleet: `PersonalLog` + `MakerLog` + `BusinessLog` working together as your personal team.

---

## 🎨 Templates Gallery

| Template | Description | Domain | Best For |
|----------|-------------|--------|----------|
| **PersonalLog** | Journal and reflection companion | Personal | Daily logging, emotional tracking |
| **MakerLog** | Coding assistant that knows your stack | Development | Developers, engineers |
| **BusinessLog** | Business advisor with memory | Enterprise | Founders, executives |
| **DMLog** | AI dungeon master for TTRPGs | Gaming | Game masters, players |
| **FishingLog** | Fishing companion with spot memory | Outdoors | Anglers, outdoorspeople |
| **Deckboss** | AI spreadsheet with natural language | Productivity | Analysts, project managers |
| **LucidDreamer** | Idea evaluation and development | Creative | Creators, innovators |

**[Browse all templates →](https://github.com/lucineer/cocapn-templates)**

---

## 🛠️ Development

Want to build your own agent or contribute?

```bash
# Clone the starter template
git clone https://github.com/lucineer/cocapn-starter my-agent
cd my-agent

# Install dependencies
npm install

# Run locally
npm run dev

# Deploy to Cloudflare Workers
npm run deploy
```

Check out our [Developer Guide](https://docs.cocapn.ai/development) for detailed instructions.

---

## 📚 Documentation

- [Getting Started Guide](https://docs.cocapn.ai/getting-started)
- [Architecture Deep Dive](https://docs.cocapn.ai/architecture)
- [Building Custom Skills](https://docs.cocapn.ai/skills)
- [Fleet Protocol Specification](https://docs.cocapn.ai/fleet-protocol)
- [API Reference](https://docs.cocapn.ai/api)

---

## 🤝 Community & Support

- [Discord](https://discord.gg/lucineer) – Chat with the community and developers
- [GitHub Issues](https://github.com/lucineer/cocapn/issues) – Report bugs and request features
- [GitHub Discussions](https://github.com/lucineer/cocapn/discussions) – Ask questions and share ideas
- [Twitter](https://twitter.com/lucineer_ai) – Latest updates and announcements

---

## 📄 License

MIT License – do whatever you want. We believe in open source.

---

**Built with ❤️ by the [Lucineer](https://lucineer.ai) team.**

*The repo IS the product. The agent IS the cocapn.*