# Hermes Agent Setup Guides

Step-by-step guides to installing and configuring [Hermes Agent](https://github.com/NousResearch/hermes-agent) — an open-source AI agent framework by Nous Research.

---

## What is Hermes Agent?

Hermes is a self-improving AI agent that runs on your machine and talks to you through your terminal, Telegram, Discord, or a web browser. It's not just a chatbot — it's a persistent assistant that:

- **Remembers you** across sessions — your preferences, project details, past conversations
- **Learns from experience** — creates reusable "skills" after solving complex tasks, so it gets faster over time
- **Runs autonomously** — scheduled tasks, daily reports, monitoring, all unattended
- **Works where you do** — talk to it from your phone via Telegram while it works on your computer
- **Uses any AI model** — DeepSeek, Claude, Gemini, GPT, or 200+ others through OpenRouter. No vendor lock-in.

Think of it as a terminal-native AI teammate that grows with you. It can research topics, write code, manage files, browse the web, analyze documents, automate repetitive tasks, and connect to the tools you already use.

**Built by [Nous Research](https://nousresearch.com)** — the team behind the Hermes family of open-source language models. 170k+ GitHub stars, MIT licensed, active community.

---

## Guides

| Guide | Platform | Time |
|-------|----------|------|
| [**Windows PC (WSL2)**](hermes-setup-guide-windows.md) | Windows 10/11 with WSL2 + Ubuntu | 30–45 min |
| [**macOS (iMac/MacBook)**](hermes-setup-guide-macos.md) | macOS with Homebrew | 20–30 min |

Both guides cover the same setup — pick the one that matches your machine.

### What's in the guides

1. **Get an API key** — DeepSeek (cheapest), MiMo (budget), or OpenRouter (most flexible)
2. **Install prerequisites** — WSL2 on Windows, Homebrew on Mac
3. **Install Hermes** — one-liner install
4. **Configure your model** — DeepSeek V4 Flash at medium reasoning (the recommended default)
5. **Set up memory** — OpenViking, Honcho, or built-in (Hermes walks you through it)
6. **Connect messaging** — Telegram or Discord bot so you can chat from your phone
7. **Web interface** — optional browser-based chat UI

---

## Resources

| Resource | What it is |
|----------|-----------|
| [**Hermes Agent GitHub**](https://github.com/NousResearch/hermes-agent) | Source code, issues, releases — 170k+ stars |
| [**Official Docs**](https://hermes-agent.nousresearch.com/docs/) | Full documentation — configuration, features, integrations |
| [**User Guide**](https://hermes-agent.nousresearch.com/docs/user-guide/) | Getting started, features, troubleshooting |
| [**Third-Party WebUI**](https://github.com/nesquena/hermes-webui) | Browser-based chat interface (7k+ stars) |
| [**OpenViking**](https://github.com/volcengine/OpenViking) | Self-hosted context database for AI agents (23k+ stars) |
| [**Honcho**](https://github.com/plastic-labs/honcho) | Self-hosted memory with AI-powered extraction |
| [**Hermes Ecosystem**](https://github.com/ksimback/hermes-ecosystem) | Curated list of tools, integrations, and platforms in the Hermes ecosystem |
| [**OpenRouter**](https://openrouter.ai) | Multi-model API gateway — one key for 200+ models |
| [**DeepSeek**](https://platform.deepseek.com) | Cheapest high-quality models for daily use |

### In-Hermes Commands

Once installed, these are useful right away:

| Command | What it does |
|---------|--------------|
| `hermes` | Start chatting |
| `hermes model` | Switch AI models |
| `hermes memory setup` | Configure memory |
| `hermes gateway setup` | Connect Telegram/Discord |
| `hermes doctor` | Diagnose issues |
| `hermes skills browse` | Explore the skills catalog |
| `/help` | See all commands |

---

*Guides written for Hermes Agent v0.15.x — May 2026*
