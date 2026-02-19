# The Ultimate OpenClaw Setup Guide 🚀

> A production-ready blueprint for a single-agent, multi-channel OpenClaw environment. Created with love by AJ and his agents.

---

## 1. 🏗️ Infrastructure & Installation
OpenClaw is a Node.js-based autonomous agent system. It is highly portable and runs identically on **Ubuntu Linux** (e.g., AWS EC2) and **macOS**.

### 💻 Prerequisites:
- **Node.js:** Ensure you have the latest LTS version (`v22+`).
- **NPM:** Comes bundled with Node.js.

### 📥 Install OpenClaw:
The easiest way to install OpenClaw is to use the official one-liner from **[openclaw.ai](https://openclaw.ai/)**:

```bash
curl -sL https://openclaw.ai/install | bash
```

Alternatively, you can install it via NPM:
```bash
npm install -g openclaw
```

### 📂 Directory Structure (Informational)
Once you install OpenClaw and go through the **Onboarding Wizard** (`openclaw gateway start`), the system will automatically generate the following structure in your Home folder. This is for your reference:

```text
~/.openclaw/                # Generated hidden config directory
├── openclaw.json           # THE BRAIN: API keys, bots, and agent logic
├── workspace/              # THE HEART: Root agent personality and data
│   ├── SOUL.md             # Defining WHO the AI is
│   ├── USER.md             # Defining WHO you are
│   ├── IDENTITY.md         # The Agent's Name & Avatar
│   ├── HEARTBEAT.md        # Proactive checklist
│   ├── MEMORY.md           # Long-term curated memories
│   ├── memory/             # Daily session logs (YYYY-MM-DD.md)
│   └── skills/             # Custom scripts/tools
└── backup-to-s3.sh         # Redundancy script
```

---

## 2. 💬 Create Your Channels (The Front Ends)
Before you configure the brain, you need to create the communication lines.

### 🔹 Discord Setup
1.  Go to the **[Discord Developer Portal](https://discord.com/developers/applications)**.
2.  Click **New Application** -> **Bot** tab.
3.  Click **Reset Token** to get your `botToken`.
4.  **CRITICAL:** Scroll to **Privileged Gateway Intents** and toggle **ON** "Message Content Intent."
5.  Go to **OAuth2** -> **URL Generator** -> Select `bot` scope + `Administrator` permissions.
6.  Copy the URL and invite the bot to your server.

### 🔹 Telegram Setup
1.  Open Telegram and search for **[@BotFather](https://t.me/botfather)**.
2.  Send the command `/newbot` and follow the prompts to get your **API Token**.

---

## 3. 🧬 Personalize the Agent
After onboarding, you can personalize your agent by editing the files in `~/.openclaw/workspace/`. These files are read at the start of **every** session.

- **[SOUL.md](examples/SOUL.md):** Define the "vibe" and "Core Truths."
- **[USER.md](examples/USER.md):** Provide your timezone, goals, and habits.
- **[IDENTITY.md](examples/IDENTITY.md):** Set the agent's name and emoji.
- **[HEARTBEAT.md](examples/HEARTBEAT.md):** Checklist for recurring background tasks.

---

## 4. 🧠 Configure the Brain: `openclaw.json`
Your `openclaw.json` is updated via the Wizard, but you can manually edit it at `~/.openclaw/openclaw.json` to fine-tune your setup.

```json
{
  "models": {
    "providers": {
      "google-gemini-cli": { "api": "google-gemini-cli" }
    }
  },
  "channels": {
    "discord": {
      "accounts": {
        "primary": { "botToken": "YOUR_DISCORD_TOKEN", "enabled": true }
      }
    },
    "telegram": {
      "accounts": {
        "mobile": { "botToken": "YOUR_TELEGRAM_TOKEN", "enabled": true }
      }
    }
  },
  "agents": {
    "list": [
      {
        "id": "main",
        "default": true,
        "model": { "primary": "google-gemini-cli/gemini-3-flash-preview" }
      }
    ]
  },
  "bindings": [
    { "agentId": "main", "match": { "channel": "discord", "accountId": "primary" } },
    { "agentId": "main", "match": { "channel": "telegram", "accountId": "mobile" } }
  ]
}
```

---

## 5. 🚀 Liftoff & Google Gemini Auth
Since Gemini uses your actual Google Account, you must complete an OAuth flow in your terminal on the first run.

1.  **Onboarding Wizard:** Run `openclaw gateway start` to begin the wizard.
2.  **Authenticate:** During the process, the terminal will print a **URL**. Copy and paste this into your browser, sign in, and paste the **Verification Code** back into the terminal.
3.  **Verify:** Message your bot on Discord or Telegram. If it replies, you are live!

---

## 6. ⌨️ Management Commands
- `openclaw gateway status`  - Check if the system is running.
- `openclaw gateway restart` - Apply changes made to `openclaw.json`.
- `/status` (in chat) - Check token usage and current model.
- `/compact` (in chat) - Manual context flush to save tokens.

---

## 7. 🛡️ Security & Redundancy
- **Firewall:** Keep ports 22, 80, and 443 restricted to your **Home IP** only.
- **S3 Sync:** Set up a crontab to run a sync script every 6 hours.
- **Git Mirroring:** Use `git remote add mirror <url>` to push your workspace to both GitLab and GitHub.

---
*Blueprint generated by tysam — Feb 19, 2026*
