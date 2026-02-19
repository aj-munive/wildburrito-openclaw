# The OpenClaw Journey: From Scratch to Pro 🚀

> A breakdown of my personal OpenClaw configuration and the "Lessons Learned" from my multi-agent setup. Created with love by AJ and his agents.

---

## 🏗️ 1. Infrastructure: My "Always-On" Setup
I run my environment on a dedicated **AWS EC2 instance (Ubuntu 22.04)**, but the file structures and logic are identical if you're running on **macOS**.

### 📥 How I Installed It:
I used the official one-liner which handles Node.js and dependencies automatically:
```bash
curl -fsSL https://openclaw.ai/install.sh | bash
```

### 📂 Directory Structure (The Result):
Once the onboarding wizard finishes, it generates this structure in your Home folder. Here is how mine looks:
```text
~/.openclaw/                # Hidden config directory
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
I use **Discord** for my primary deep-work UI and **Telegram** for quick mobile tasks. Here is exactly how I set them up:

### 🔹 Discord Setup (Step-by-Step)
1.  **Create Application:** Go to the [Discord Developer Portal](https://discord.com/developers/applications) and create a "New Application."
2.  **Generate Token:** Go to the **Bot** tab and click "Reset Token." This is your `botToken`.
3.  **The "Golden Toggle" (Don't skip!):** On the same **Bot** tab, scroll down to **Privileged Gateway Intents**. You **MUST** toggle **ON** "Message Content Intent" or the bot won't be able to see your messages.
4.  **Invite Bot:** Go to **OAuth2** -> **URL Generator**.
    *   Select Scope: `bot`
    *   Select Permissions: `Administrator` (or specifically `Send Messages` and `Read Message History`).
5.  **Authorize:** Copy the URL, paste it in your browser, and add the bot to your server.

### 🔹 Telegram Setup (Step-by-Step)
1.  **Talk to the Father:** Open Telegram and search for **[@BotFather](https://t.me/botfather)**.
2.  **New Bot:** Send `/newbot`.
3.  **Name & Username:** Follow the prompts. The username must end in `_bot` (e.g., `tysam_labs_bot`).
4.  **Get Token:** BotFather will give you an **API Token**. This is your `botToken` for the Telegram account in `openclaw.json`.
5.  **Privacy Settings (Optional):** If you want to use the bot in a group chat, send `/setprivacy` to BotFather and toggle it to **Disabled** so it can see all messages.

---

## 🔑 3. The Model: Gemini 3 Flash (OAuth)
I use **Gemini 3 Flash** via the `google-gemini-cli` provider. It's fast and cheap, but the setup is unique because it uses **OAuth** instead of a static API key.

### How I triggered the Auth Flow:
1. I added the provider to my `openclaw.json`.
2. I stopped my background service (`openclaw gateway stop`).
3. I ran it manually in the foreground: `openclaw gateway start`.
4. The terminal printed a **Google URL**. I opened it, signed in, and pasted the code back into the terminal.
5. OpenClaw saved the token in `~/.config/google-gemini-cli/`, and I was good to go.

---

## 🧠 4. My Core Configuration: `openclaw.json`
Here is a sanitized version of my "Brain" file. It shows how I bind multiple chat accounts to a single "Main" agent.

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
The most important lesson I learned: **Context is everything.** The agent reads these files at the start of every session.

- **[SOUL.md](examples/SOUL.md):** I set "Core Truths" here so my agent doesn't act like a corporate robot.
- **[USER.md](examples/USER.md):** I gave it my timezone and habits. This is how it knows to nudge me if I haven't worked out.
- **[HEARTBEAT.md](examples/HEARTBEAT.md):** This is my "background" brain. The agent checks this file periodically to see if it should take proactive actions.

---

## ⌨️ 6. Management: My Daily Commands
I use these constantly to keep the system healthy:
- `openclaw gateway restart` - Run this after every `openclaw.json` edit.
- `/status` - My most used command. Shows token usage and current model.
- `/compact` - If the conversation gets too long, I use this to "flush" the memory and save on API costs.

---

## 🛡️ 7. Security & Backups
- **Firewall:** I keep ports 22, 80, and 443 restricted to my **Home IP** only.
- **Backups:** I use a cron job to sync everything to S3 every 6 hours.
- **Redundancy:** I push my workspace to both GitLab and GitHub so I never lose my memory files.

---
*A chronicle of Tysam Labs — Feb 19, 2026*
