# The Ultimate OpenClaw Setup Guide 🚀

> A production-ready blueprint for a single-agent, multi-channel OpenClaw environment.

---

## 1. 🏗️ Infrastructure & File Structure
OpenClaw is a Node.js-based autonomous agent system. It is highly portable and runs identically on **Ubuntu Linux** (e.g., AWS EC2) and **macOS**.

### 💻 OS Compatibility:
Whether you are on Linux or Mac, the installation and file structure remain the same. The primary difference is that on Linux you'll typically use `systemd` for background management, whereas on Mac you'll run it via terminal or `launchd`.

### 📂 Core Directory Structure:
The directory structure is identical across platforms:
```text
~/.openclaw/                # Hidden config directory in your Home folder
├── openclaw.json           # THE BRAIN: API keys, bots, and agent logic
├── workspace/              # THE HEART: Root agent personality and data
│   ├── SOUL.md             # Defining WHO the AI is
│   ├── USER.md             # Defining WHO you are
│   ├── MEMORY.md           # Long-term curated memories
│   ├── memory/             # Daily session logs (YYYY-MM-DD.md)
│   └── skills/             # Custom scripts/tools (e.g., weather, obsidian)
└── backup-to-s3.sh         # Redundancy script (Linux/Mac compatible)
```

---

## 2. 🧠 The Configuration: `openclaw.json`
This file is the single source of truth for your ecosystem. Note that the Google Gemini CLI provider does **not** require an API key in the JSON, as it uses OAuth.

```json
{
  "models": {
    "providers": {
      "google-gemini-cli": {
        "api": "google-gemini-cli"
      }
    }
  },
  "channels": {
    "discord": {
      "accounts": {
        "primary": {
          "botToken": "DISCORD_TOKEN",
          "enabled": true
        }
      }
    },
    "telegram": {
      "accounts": {
        "mobile": {
          "botToken": "TELEGRAM_TOKEN",
          "enabled": true
        }
      }
    }
  },
  "agents": {
    "list": [
      {
        "id": "main",
        "default": true,
        "model": { 
          "primary": "google-gemini-cli/gemini-3-flash-preview" 
        }
      }
    ]
  },
  "bindings": [
    {
      "agentId": "main",
      "match": { "channel": "discord", "accountId": "primary" }
    },
    {
      "agentId": "main",
      "match": { "channel": "telegram", "accountId": "mobile" }
    }
  ]
}
```

---

## 3. 💬 Channel Setup (The Front Ends)

### 🔹 Discord Setup
1.  Go to the **[Discord Developer Portal](https://discord.com/developers/applications)**.
2.  Click **New Application** and give it a name.
3.  Go to the **Bot** tab on the left sidebar.
4.  Click **Reset Token** (or "Copy Token") to get your `botToken`. Paste this into your `openclaw.json`.
5.  **CRITICAL:** Scroll down to the **Privileged Gateway Intents** section and toggle **ON** the "Message Content Intent." Without this, the agent won't be able to read your messages.
6.  Go to the **OAuth2** tab -> **URL Generator**.
    *   Select Scope: `bot`
    *   Select Bot Permissions: `Administrator` (or specifically `Send Messages`, `Read Messages`, etc.)
7.  Copy the generated URL and paste it into your browser to invite the bot to your Discord server.

### 🔹 Telegram Setup
1.  Open Telegram and search for **[@BotFather](https://t.me/botfather)**.
2.  Send the command `/newbot`.
3.  Follow the prompts to give your bot a Name and a Username (must end in `_bot`).
4.  BotFather will reply with your **API Token** (e.g., `123456789:ABCDefGhIjkl...`).
5.  Paste this token into the `botToken` field under the Telegram account in your `openclaw.json`.
6.  Message your new bot on Telegram to start the first session!

---

## 4. 🔑 Google Gemini (OAuth) Setup
Since we are using **Gemini 3 Flash**, you'll use the `google-gemini-cli` provider. Since this uses your actual Google Account, you must complete an OAuth flow in your terminal.

### The Flow:
1.  **Configure:** Add the `"google-gemini-cli"` block to your `openclaw.json`.
2.  **Set the Model:** Ensure your agent is using `google-gemini-cli/gemini-3-flash-preview`.
3.  **Trigger Auth:** Run `openclaw gateway stop` and then start it manually in your terminal so you can see the prompts:
    ```bash
    openclaw gateway start
    ```
4.  **Authenticate:** 
    *   The terminal will print a **URL**. Copy and paste this into your browser.
    *   Sign in with your Google account.
    *   Google will give you a **Verification Code**.
    *   Paste that code back into the terminal and hit Enter.
5.  **Persistence:** OpenClaw saves this token in `~/.config/google-gemini-cli/`. Once done, you can stop the process and resume running it in the background.

---

## 5. ⌨️ Essential OpenClaw CLI Commands
Once installed, you interact with the system using the `openclaw` command.

### Gateway Management (The Daemon)
- `openclaw gateway status`  - Check if the system is running.
- `openclaw gateway start`   - Start the process.
- `openclaw gateway stop`    - Kill the process.
- `openclaw gateway restart` - Apply changes made to `openclaw.json`.

### Session Control (Inside the Chat)
- `/status`    - See current token usage, model, and context window size.
- `/reasoning` - Toggle "Thinking" mode on/off (if the model supports it).
- `/compact`   - Manually flush the context window to save tokens.

---

## 6. 🧬 Personalizing the Agent
The agent reads these files at the start of **every** session to determine its personality and understand its user.

- **[SOUL.md](examples/SOUL.md):** Define the "vibe." Use this to set "Core Truths" and boundaries.
- **[USER.md](examples/USER.md):** Give the agent your context—timezone, goals, and habits.
- **[IDENTITY.md](examples/IDENTITY.md):** Set the agent's name, emoji, and avatar.
- **[HEARTBEAT.md](examples/HEARTBEAT.md):** A checklist for proactive, recurring background tasks.

---

## 7. 🛡️ Security & Redundancy
- **Firewall:** Keep ports 22 (SSH), 80, and 443 restricted to your **Home IP** only in your AWS Security Group or Router settings.
- **S3 Sync:** Set up a crontab to run a sync script every 6 hours (compatible with both macOS and Linux):
  ```bash
  aws s3 sync ~/.openclaw/workspace s3://your-bucket-name/backup/
  ```
- **Git Mirroring:** Use `git remote add mirror <url>` to push your workspace to both GitLab and GitHub for total redundancy.

---
*Blueprint generated by tysam — Feb 19, 2026*
