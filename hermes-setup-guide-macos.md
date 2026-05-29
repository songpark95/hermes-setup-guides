# Hermes Agent Setup Guide — macOS (iMac)

Step-by-step guide to installing Hermes Agent on a Mac. Runs natively — no WSL or special environment needed.

**Time estimate:** 20–30 minutes for the full setup.

---

## What You're Building

- **Hermes Agent** — an AI agent that lives in your terminal and can chat with you via Telegram, Discord, or a web browser
- **Memory system** — persistent memory so the agent remembers who you are across sessions (choose between OpenViking or Honcho)
- **Gateway** — connects Hermes to your messaging apps so you can talk to it from your phone

---

## Part 1: Install Prerequisites

### Step 1.1 — Install Homebrew (if you don't have it)

Open **Terminal** (search for it in Spotlight with `Cmd+Space`):
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Follow the prompts. After installation, add Homebrew to your path:
```bash
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv)"
```

### Step 1.2 — Install Python 3.11+

```bash
brew install python@3.11
```

Verify:
```bash
python3 --version
```

Should say 3.11 or higher.

### Step 1.3 — Install other dependencies

```bash
brew install git curl
```

---

## Part 2: Install Hermes Agent

### Step 2.1 — Install Hermes

```bash
curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash
```

This installs Hermes to `~/.hermes/` and sets up a virtual environment. Takes 1–2 minutes.

### Step 2.2 — Verify installation

```bash
hermes --version
```

You should see a version number (0.15.x or newer).

---

## Part 3: Configure Your AI Model

Hermes needs an LLM provider to power the conversations. The easiest option is **OpenRouter** — it gives you access to dozens of models with one API key.

### Step 3.1 — Create an OpenRouter account

1. Go to https://openrouter.ai
2. Sign up (Google/GitHub login works)
3. Go to **Keys** (https://openrouter.ai/keys)
4. Create a new API key — copy it somewhere safe

### Step 3.2 — Add your API key

In Terminal:
```bash
echo 'OPENROUTER_API_KEY=sk-or-...PASTE_YOUR_KEY_HERE' >> ~/.hermes/.env
```

Replace `sk-or-...PASTE_YOUR_KEY_HERE` with your actual key.

### Step 3.3 — Pick a model

Run the interactive model picker:
```bash
hermes model
```

Or set it directly:
```bash
hermes config set model.default deepseek/deepseek-v4-flash
```

**Recommended models for beginners:**
| Model | Cost | Good for |
|-------|------|----------|
| `deepseek/deepseek-v4-flash` | Cheap (~$0.14/M tokens) | Daily use, most tasks |
| `anthropic/claude-sonnet-4` | Medium (~$3/M tokens) | Complex tasks, coding |
| `google/gemini-2.5-flash` | Cheap | Fast responses, good general |

You can switch models anytime with `hermes model`.

### Step 3.4 — Test it

```bash
hermes chat -q "Hey, can you hear me?"
```

If you get a response, you're connected. Type `/quit` to exit.

---

## Part 4: Set Up Memory (Pick One)

Memory lets Hermes remember things across sessions — your preferences, project details, past conversations. You have two options:

### Option A: OpenViking (Recommended — Simpler)

OpenViking is a lightweight memory server. Just Python — no Docker needed.

#### 4A.1 — Install OpenViking

```bash
pip3 install openviking --upgrade
```

#### 4A.2 — Initialize with local models

The easiest setup uses Ollama (runs AI models locally for free):
```bash
openviking-server init
```

The wizard will:
- Detect/install Ollama if needed
- Pull embedding and vision models
- Generate a config file at `~/.openviking/ov.conf`

#### 4A.3 — Start the server

```bash
openviking-server
```

Leave this running in a separate terminal tab, or run it in the background:
```bash
nohup openviking-server > /dev/null 2>&1 &
```

#### 4A.4 — Connect Hermes to OpenViking

```bash
hermes config set memory.provider openviking
echo 'OPENVIKING_ENDPOINT=http://localhost:1933' >> ~/.hermes/.env
```

#### 4A.5 — Verify

```bash
hermes memory status
```

Should show OpenViking as active.

---

### Option B: Honcho (More Powerful — Requires Docker)

Honcho is a more sophisticated memory system with AI-powered memory extraction. It runs as 4 Docker containers.

#### 4B.1 — Install Docker Desktop

1. Download Docker Desktop for Mac: https://www.docker.com/products/docker-desktop/
2. Open the `.dmg` file and drag Docker to Applications
3. Launch Docker Desktop from Applications
4. Follow the setup prompts (grant the permissions it asks for)

Verify Docker is running:
```bash
docker --version
docker compose version
```

#### 4B.2 — Clone and configure Honcho

```bash
cd ~
git clone https://github.com/plastic-labs/honcho.git
cd honcho
cp docker-compose.yml.example docker-compose.yml
cp .env.template .env
```

#### 4B.3 — Edit the .env file

```bash
nano .env
```

Find and set these lines (use your OpenRouter API key):
```
LLM_OPENAI_API_KEY=sk-or-...nFor the model configs, change the defaults to use OpenRouter. Find each `MODEL_CONFIG` section and set:
```
DERIVER_MODEL_CONFIG__TRANSPORT=openai
DERIVER_MODEL_CONFIG__MODEL=openai/gpt-4.1-mini
DERIVER_MODEL_CONFIG__OVERRIDES__BASE_URL=https://openrouter.ai/api/v1
```

Repeat for all model config sections (DIALECTIC_LEVELS, SUMMARY_MODEL_CONFIG, DREAM_*_MODEL_CONFIG, EMBEDDING_MODEL_CONFIG).

Save with `Ctrl+O`, `Enter`, `Ctrl+X`.

#### 4B.4 — Start Honcho

```bash
docker compose up -d --build
```

First build takes a few minutes. Verify:
```bash
docker compose ps
curl http://localhost:8000/health
```

Should return `{"status":"ok"}`.

#### 4B.5 — Configure Hermes

```bash
hermes config set memory.provider honcho
```

Create the Honcho config file:
```bash
cat > ~/.hermes/honcho.json << 'EOF'
{
  "baseUrl": "http://localhost:8000",
  "workspace": "hermes",
  "peerName": "YOUR_NAME",
  "aiPeer": "hermes",
  "enabled": true,
  "hosts": {
    "hermes": {
      "enabled": true,
      "aiPeer": "hermes",
      "peerName": "YOUR_NAME",
      "recallMode": "hybrid",
      "observationMode": "directional",
      "writeFrequency": "async",
      "sessionStrategy": "per-directory",
      "contextCadence": 2,
      "dialecticCadence": 2,
      "dialecticDepth": 2,
      "dialecticReasoningLevel": "low",
      "dialecticMaxChars": 600,
      "saveMessages": true
    }
  }
}
EOF
```

Replace `YOUR_NAME` with your actual name.

#### 4B.6 — Verify

```bash
hermes memory status
```

Should show Honcho as active.

---

## Part 5: Set Up the Gateway (Messaging)

The gateway connects Hermes to Telegram, Discord, or other messaging apps so you can talk to it from your phone.

### Step 5.1 — Start the gateway

```bash
hermes gateway run
```

This runs the gateway in the foreground (you'll see logs). To run it in the background:
```bash
hermes gateway install
hermes gateway start
```

The `install` command sets up a launchd service that auto-starts on login.

### Step 5.2 — Connect a platform

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

### Step 5.3 — Verify

```bash
hermes gateway status
```

Should show "active" with your platform connected. Send a message to your bot — you should get a response.

---

## Part 6: Web Interface (Optional)

If you want a browser-based chat interface:

### Step 6.1 — Start the dashboard

```bash
hermes dashboard
```

Opens automatically at http://localhost:9119.

### Step 6.2 — Install the WebUI (optional, nicer interface)

```bash
cd ~
git clone https://github.com/nesquena/hermes-webui.git
cd hermes-webui
python3 bootstrap.py
```

Opens at http://localhost:8787.

---

## Quick Reference

| Command | What it does |
|---------|--------------|
| `hermes` | Start interactive chat |
| `hermes chat -q "question"` | Ask a single question |
| `hermes model` | Change your AI model |
| `hermes gateway start` | Start the messaging gateway |
| `hermes gateway stop` | Stop the gateway |
| `hermes gateway status` | Check if gateway is running |
| `hermes doctor` | Diagnose issues |
| `hermes update` | Update to latest version |
| `hermes memory status` | Check memory system |
| `/quit` | Exit chat |
| `/reset` | Start a fresh session |
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
- Check OpenRouter balance: https://openrouter.ai/credits

**Python version issues**
- Make sure you're using the Homebrew Python, not the system one: `which python3`
- Should show `/opt/homebrew/bin/python3`

---

*Guide written for Hermes Agent v0.15.x — May 2026*
