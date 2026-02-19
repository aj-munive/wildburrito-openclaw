# The OpenClaw Journey: From Scratch to Fake Pro 🚀

> *"Debí tirar más fotos de cuando te tuve.*
> *Debí darte más beso' y abrazo' las vece' que pude."*
> — **Bad Bunny (DTMF)**

> A breakdown of my personal OpenClaw configuration and the "Lessons Learned" from my multi-agent setup. Created with love by AJ and his agents.

---

## 🏗️ 1. Infrastructure & Installation
I run my environment on a dedicated **AWS EC2 instance (Ubuntu 22.04)**, but the logic is identical if you're on **macOS**.

### 💻 Prerequisites:
- **Node.js:** You need version 22 or newer. Check with `node --version`.

### 📥 How I Installed It:
I used the official one-liner which handles Node.js and dependencies automatically (works on macOS, Linux, and Windows):
```bash
curl -fsSL https://openclaw.ai/install.sh | bash
```

### 🪄 The Onboarding Wizard:
The official way to bootstrap is to run the wizard. This handles auth, gateway settings, and your initial channel setup:
```bash
openclaw onboard --install-daemon
```
*Reference: [Official Getting Started Guide](https://docs.openclaw.ai/start/getting-started)*

### 📂 Directory Structure (Informational):
Once the onboarding finishes, it generates this structure in your Home folder. Here is how mine looks:
```text
~/.openclaw/                # Generated hidden config directory
├── openclaw.json           # THE BRAIN: API keys, bots, and agent logic
├── workspace/              # THE HEART: Root agent personality and data
│   ├── SOUL.md             # WHO the AI is (The Vibe)
│   ├── USER.md             # WHO I am (Preferences/Habits)
│   ├── IDENTITY.md         # Name, Emoji, and Avatar
│   ├── HEARTBEAT.md        # The "Proactive" checklist
│   ├── MEMORY.md           # Long-term curated memories
│   ├── memory/             # Daily session logs (YYYY-MM-DD.md)
│   └── skills/             # Custom tools (Weather, Obsidian, etc.)
└── backup-to-s3.sh         # My custom disaster recovery script
```

---

## 💬 2. Channels: Connecting the Front-Ends
I use **Discord** for my primary deep-work UI and **Telegram** for quick mobile tasks. Here is the detailed breakdown of how to wire them up.

### 🔹 Discord Setup (The High-Fidelity UI)
1.  **Developer Portal:** Log into the **[Discord Developer Portal](https://discord.com/developers/applications)**.
2.  **Create App:** Click **New Application** at the top right. Give it a name (e.g., "Tysam Assistant").
3.  **Create Bot:** Navigate to the **Bot** tab on the left sidebar.
    *   Click **Reset Token** to generate your bot's unique key. **Copy this immediately**; you'll need it for `openclaw.json`.
    *   (Optional) Upload an icon and set a username.
4.  **The "Golden Toggle" (Crucial):** Scroll down on the **Bot** page to the **Privileged Gateway Intents** section.
    *   **Toggle ON:** `Message Content Intent`. 
    *   *Without this, OpenClaw can join your server but will be "deaf" to your messages.*
5.  **Generate Invite Link:** Navigate to **OAuth2** -> **URL Generator**.
    *   **Scopes:** Select `bot`.
    *   **Bot Permissions:** Select `Administrator` (easiest for personal servers) or manually select `Send Messages`, `Read Message History`, and `Attach Files`.
6.  **Invite:** Copy the generated URL at the bottom, paste it into your browser, and select your server to invite the bot.

### 🔹 Telegram Setup (The Mobile Command Line)
1.  **Find the Father:** Open Telegram and search for **[@BotFather](https://t.me/botfather)**.
2.  **Initialize:** Send the `/newbot` command.
3.  **Naming:**
    *   Choose a **Display Name** (e.g., "Tysam Mobile").
    *   Choose a **Username** (must end in `_bot`, e.g., `aj_tysam_bot`).
4.  **Get API Token:** BotFather will send a message containing your **HTTP API Token** (e.g., `123456:ABC-DEF1234...`). Copy this into your `openclaw.json`.
5.  **Group Chat Usage (Optional):**
    *   If you plan to add the bot to a group, send `/setprivacy` to BotFather.
    *   Select your bot and set it to **Disabled**. This allows the bot to see all messages in the group, not just those starting with `/`.
6.  **Start Chat:** Search for your bot's username in Telegram and hit **Start** to begin your first session.

---

## 🔑 3. The Model: Gemini 3 Flash (OAuth)
I use **Gemini 3 Flash** via the `google-gemini-cli` provider. It's fast and cheap, but the setup is unique because it uses **OAuth** instead of a static API key.

### How I triggered the Auth Flow:
1. I added the `"google-gemini-cli"` block to my `openclaw.json`.
2. I stopped my background service: `openclaw gateway stop`.
3. I ran it manually: `openclaw gateway start`.
4. The terminal printed a **Google URL**. I signed in via browser and pasted the **Verification Code** back into the terminal.

---

## 🧠 4. My Core Configuration: `openclaw.json`
Sanitized version showing how I bind everything together:

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

## 🧬 5. Personality & Personalization
The most important lesson I learned: **Context is everything.**

- **[SOUL.md](examples/SOUL.md):** Personality definition.
- **[USER.md](examples/USER.md):** My context (timezone, habits).
- **[HEARTBEAT.md](examples/HEARTBEAT.md):** Background brain for proactive checks.

---

## ⌨️ 6. Management Commands
- `openclaw gateway status`  - Check if system is running.
- `openclaw gateway restart` - Apply `openclaw.json` changes.
- `openclaw dashboard`       - Open the local Control UI (browser-based chat).
- `/status` (in chat)        - Check token usage and model.
- `/compact` (in chat)       - Manual context flush.

---

## 🛡️ 7. Security & Redundancy
- **Firewall:** Keep ports 22, 80, and 443 restricted to your **Home IP** only.
- **S3 Backups:** Use a cron job to sync your workspace to S3 every 6 hours.
- **Git Mirroring:** Use `git remote add mirror <url>` to push your workspace to both GitLab and GitHub.

---
*A chronicle of Tysam Labs — Feb 19, 2026*
