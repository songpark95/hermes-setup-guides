# Hermes Agent Setup Guides

Step-by-step guides to installing and configuring [Hermes Agent](https://github.com/NousResearch/hermes-agent) — an open-source AI agent framework by Nous Research.

## Guides

| Guide | Platform | Link |
|-------|----------|------|
| **Windows PC (WSL2)** | Windows 10/11 with WSL2 + Ubuntu | [hermes-setup-guide-windows.md](hermes-setup-guide-windows.md) |
| **macOS (iMac/MacBook)** | macOS with Homebrew | [hermes-setup-guide-macos.md](hermes-setup-guide-macos.md) |

## What's Covered

Both guides walk through:

1. **Prerequisites** — system setup (WSL2/systemd on Windows, Homebrew on Mac)
2. **Hermes Agent install** — one-liner install script
3. **AI model configuration** — OpenRouter setup with recommended models
4. **Memory system** — pick one:
   - **OpenViking** (simpler, no Docker, self-hosted context database)
   - **Honcho** (more powerful, Docker Compose, AI-powered memory extraction)
5. **Gateway** — connect to Telegram, Discord, or other messaging platforms
6. **Web interface** — dashboard + optional WebUI
7. **Troubleshooting** — common issues and fixes

## Time Estimate

- **Windows (WSL2):** 30–45 minutes
- **macOS:** 20–30 minutes

## Requirements

- An [OpenRouter](https://openrouter.ai) account (free to create, pay-per-use for API calls)
- A Telegram or Discord account (for the messaging gateway, optional)

## Source

These guides are based on Hermes Agent v0.15.x (May 2026). For the latest documentation, visit https://hermes-agent.nousresearch.com/docs/
