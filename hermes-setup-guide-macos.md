# Hermes Agent Setup Guide — macOS (iMac)

Step-by-step guide to installing Hermes Agent on a Mac. Runs natively — no WSL or special environment needed.

**Time estimate:** 20–30 minutes for the full setup.

---

## What You're Building

- **Hermes Agent** — an AI agent that lives in your terminal and can chat with you via Telegram, Discord, or a web browser
- **Memory system** — persistent memory so the agent remembers who you are across sessions
- **Gateway** — connects Hermes to your messaging apps so you can talk to it from your phone

---

## Part 1: Get Your API Key

Before installing anything, grab an API key. You need one to power the AI. Pick one option:

### Option A: DeepSeek (Recommended — Cheapest)

DeepSeek is a Chinese AI lab with excellent models at rock-bottom prices.

1. Go to https://platform.deepseek.com
2. Sign up (email or Google)
3. Go to **API Keys** in the dashboard
4. Create a new key — copy it somewhere safe
5. Add some credits (minimum $5 — lasts weeks of casual use)

**Model you'll use:** `deepseek/deepseek-v4-flash` (~$0.14/M input tokens — pennies per conversation)

### Option B: Xiaomi MiMo (Budget Option)

MiMo is Xiaomi's AI platform with competitive pricing.

1. Go to https://xiaoai.mi.com or the Xiaomi AI developer portal
2. Sign up and create an API key
3. Copy the key somewhere safe

**Model you'll use:** `mimo-v2.5` (standard tier, 1x credits)

### Option C: OpenRouter (Multi-Model — Most Flexible)

OpenRouter gives you one API key that accesses dozens of models (DeepSeek, Claude, Gemini, GPT, etc.).

1. Go to https://openrouter.ai
2. Sign up (Google/GitHub login works)
3. Go to **Keys** (https://openrouter.ai/keys)
4. Create a new API key — copy it somewhere safe
5. Add credits (pay-per-use across all models)

**Model you'll use:** `deepseek/deepseek-v4-flash` (same model, routed through OpenRouter)

---

**Keep your API key handy — you'll need it in Part 3.**

---

## Part 2: Install Prerequisites

### Step 2.1 — Install Homebrew (if you don't have it)

Open **Terminal** (search for it in Spotlight with `Cmd+Space`):
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Follow the prompts. After installation, add Homebrew to your path:
```bash
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv)"
```

### Step 2.2 — Install Python 3.11+

```bash
brew install python@3.11
```

Verify:
```bash
python3 --version
```

Should say 3.11 or higher.

### Step 2.3 — Install other dependencies

```bash
brew install git curl
```

---

## Part 3: Install Hermes Agent

### Step 3.1 — Install Hermes

```bash
curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash
```

This installs Hermes to `~/.hermes/` and sets up a virtual environment. Takes 1–2 minutes.

### Step 3.2 — Verify installation

```bash
hermes --version
```

You should see a version number (0.15.x or newer).

---

## Part 4: Configure Your API Key + Model

Now add the API key you got in Part 1 and set up your model.

### Step 4.1 — Add your API key

In Terminal, run ONE of these depending on which provider you chose:

**If you got a DeepSeek key:**
```bash
echo 'DEEPSEEK_API_KEY=*** >> ~/.hermes/.env
```

**If you got a MiMo key:**
```bash
echo 'XIAOMI_API_KEY=*** >> ~/.hermes/.env
```

**If you got an OpenRouter key:**
```bash
echo 'OPENROUTER_API_KEY=*** >> ~/.hermes/.env
```

Replace `your-key-here` with your actual key (no quotes around it in the file).

### Step 4.2 — Set your model

```bash
hermes config set model.default deepseek/deepseek-v4-flash
```

If you're using MiMo instead:
```bash
hermes config set model.default mimo-v2.5
```

### Step 4.3 — Set reasoning level

```bash
hermes config set reasoning.main medium
```

Medium reasoning gives the agent enough room to think through multi-step tasks without burning excessive tokens. You can always change this later with `/reasoning high` inside a chat session.

### Step 4.4 — Test it

```bash
hermes chat -q "Hey, can you hear me?"
```

If you get a response, you're connected. Type `/quit` to exit.

---

## Part 5: Set Up Memory

Memory lets Hermes remember things across sessions — your preferences, project details, past conversations.

Run the interactive setup wizard:
```bash
hermes memory setup
```

This will show you the available memory providers and walk you through configuration. The main options:

| Provider | Setup required | Good for |
|----------|---------------|----------|
| **Built-in** | None (already active) | Basic memory, no extra setup |
| **OpenViking** | Install OpenViking server first | Structured knowledge base, hierarchical browsing |
| **Honcho** | Install Docker + Honcho first | AI-powered memory extraction, sophisticated recall |

### If you chose OpenViking

The wizard will ask for the server endpoint. You need to install and start OpenViking first:

```bash
pip3 install openviking --upgrade
openviking-server init    # interactive wizard sets up Ollama + models
openviking-server         # start the server (leave running)
```

Then run `hermes memory setup` again and select OpenViking. It will auto-detect the server at `localhost:1933`.

### If you chose Honcho

You need Docker and Honcho running first. See the **Honcho Setup (Advanced)** section below, then run `hermes memory setup` and select Honcho.

### If you chose Built-in (no extra setup)

Just select "Built-in" in the wizard. Hermes uses local markdown files for memory. Works fine for getting started — you can always upgrade to OpenViking or Honcho later.

---

## Part 6: Set Up the Gateway (Messaging)

The gateway connects Hermes to Telegram, Discord, or other messaging apps so you can talk to it from your phone.

### Step 6.1 — Start the gateway

```bash
hermes gateway run
```

This runs the gateway in the foreground (you'll see logs). To run it in the background:
```bash
hermes gateway install
hermes gateway start
```

The `install` command sets up a launchd service that auto-starts on login.

### Step 6.2 — Connect a platform

In a **separate terminal tab** (while the gateway is running), run:
```bash
hermes gateway setup
```

This walks you through connecting Telegram, Discord, Slack, etc.

#### Telegram (Easiest to start with)

1. Message [@BotFather](https://t.me/BotFather) on Telegram
2. Send `/newbot` and follow the prompts
3. Copy the bot token
4. Run `hermes gateway setup` and select Telegram
5. Paste the bot token when prompted
6. Start a chat with your new bot on Telegram

#### Discord

1. Go to https://discord.com/developers/applications
2. Create a new application → Bot → Copy token
3. Enable **Message Content Intent** under Bot → Privileged Gateway Intents
4. Run `hermes gateway setup` and select Discord
5. Paste the bot token

### Step 6.3 — Verify

```bash
hermes gateway status
```

Should show "active" with your platform connected. Send a message to your bot — you should get a response.

---

## Part 7: Web Interface (Optional)

If you want a browser-based chat interface:

### Step 7.1 — Start the dashboard

```bash
hermes dashboard
```

Opens automatically at http://localhost:9119.

### Step 7.2 — Install the WebUI (optional, nicer interface)

```bash
cd ~
git clone https://github.com/nesquena/hermes-webui.git
cd hermes-webui
python3 bootstrap.py
```

Opens at http://localhost:8787.

---

## Honcho Setup (Advanced)

Only needed if you chose Honcho as your memory provider. Requires Docker.

### Install Docker Desktop

1. Download Docker Desktop for Mac: https://www.docker.com/products/docker-desktop/
2. Open the `.dmg` file and drag Docker to Applications
3. Launch Docker Desktop from Applications
4. Follow the setup prompts (grant the permissions it asks for)

Verify Docker is running:
```bash
docker --version
docker compose version
```

### Start Honcho

```bash
cd ~
git clone https://github.com/plastic-labs/honcho.git
cd honcho
cp docker-compose.yml.example docker-compose.yml
cp .env.template .env
```

Edit `.env` — set your API key for the LLM:
```
LLM_OPENAI_API_KEY=your-o...Then start:
```bash
docker compose up -d --build
```

Verify:
```bash
curl http://localhost:8000/health
```

Should return `{"status":"ok"}`. Then run `hermes memory setup` and select Honcho.

---

## Quick Reference

| Command | What it does |
|---------|--------------|
| `hermes` | Start interactive chat |
| `hermes chat -q "question"` | Ask a single question |
| `hermes model` | Change your AI model |
| `hermes config set reasoning.main medium` | Set reasoning level |
| `hermes memory setup` | Configure memory provider |
| `hermes gateway start` | Start the messaging gateway |
| `hermes gateway stop` | Stop the gateway |
| `hermes gateway status` | Check if gateway is running |
| `hermes doctor` | Diagnose issues |
| `hermes update` | Update to latest version |
| `/quit` | Exit chat |
| `/reset` | Start a fresh session |
| `/reasoning high` | Increase reasoning mid-session |
| `/help` | See all commands |

---

## Troubleshooting

**"command not found: hermes"**
- Close and reopen Terminal, or run `source ~/.zshrc`

**Gateway won't start**
- Check if port 8080 is in use: `lsof -i :8080`
- Check logs: `hermes gateway status`

**Docker Desktop not starting**
- Make sure you're on macOS 12+ (Monterey or later)
- Check if virtualization is enabled in System Settings → Privacy & Security

**OpenViking won't start**
- Check if port 1933 is in use: `lsof -i :1933`
- Make sure Ollama is running: `ollama list`

**Model not responding**
- Check your API key: `hermes doctor`
- If using DeepSeek: check balance at https://platform.deepseek.com
- If using OpenRouter: check balance at https://openrouter.ai/credits

**Python version issues**
- Make sure you're using the Homebrew Python, not the system one: `which python3`
- Should show `/opt/homebrew/bin/python3`

---

*Guide written for Hermes Agent v0.15.x — May 2026*
