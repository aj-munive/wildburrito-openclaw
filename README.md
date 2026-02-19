# The Ultimate OpenClaw Setup Guide 🚀

> This guide outlines a production-ready, multi-agent OpenClaw environment. It covers everything from the initial server setup to building custom web apps that the AI can manage.

---

## 1. 🏗️ Infrastructure Foundation
For maximum flexibility and power, run OpenClaw on a dedicated Linux environment (Ubuntu 22.04+ recommended).

- **Hosting:** AWS EC2 (t3.medium or larger) gives you a stable, always-on home.
- **Node.js:** OpenClaw runs on Node. Ensure you have the latest LTS version (`v22+`).
- **Persistence:** Use a Dropbox-synced folder for your Obsidian vault to ensure your notes and "brain" stay in sync across devices.

---

## 2. 🧠 The Brain: `openclaw.json`
This is the core configuration file. It defines your models, channels, and how the AI interacts with the world.

### Key Sections:
- **Providers:** Connect to Anthropic (Claude), OpenAI (GPT), or Google (Gemini).
- **Channels:** Setup your "Front Ends." We use **Discord** for a rich UI experience and **Telegram** for quick mobile access.
- **Agents:** Define multiple personalities.
- **Bindings:** This is where the magic happens. You "bind" a specific bot account to a specific agent personality.

---

## 3. 🧬 The Soul & Identity
OpenClaw is "context-driven." It looks at files in its workspace to figure out who it is.

- **`SOUL.md`:** This is the AI's moral compass and personality. Tell it how to speak, what to care about, and what its boundaries are.
- **`USER.md`:** This is where the AI learns about *you*. Your goals, your preferences, and your schedule.
- **`AGENTS.md`:** The internal handbook for the AI. It tells the agent how to use its tools and manage its memory.

---

## 4. 🛡️ Multi-Agent Architecture
Don't use one AI for everything. Separate concerns for better security and focus.

- **Main Agent:** The "heavy lifter." Handles system commands, coding, and general tasks.
- **Specialized Agents:** Create agents like **Equitas** (Finance/Legal). By giving a specialized agent a different `USER.md` and `SOUL.md`, you can ensure it focuses strictly on one domain (e.g., auditing receipts instead of tracking gym workouts).

---

## 5. 🌐 Web Presence & Custom Domains
To make your AI-powered tools accessible, set up a professional web stack.

- **Caddy Server:** Use Caddy as a reverse proxy. It handles automatic HTTPS/SSL via Let's Encrypt with zero manual work.
- **App Launcher:** Build a simple "Landing Page" (`index.html`) at your root domain to serve as a command center for all your apps.
- **Subdomains:** Use subdomains like `kanban.yourdomain.com` to separate different tools.

---

## 6. 📋 Custom Tools: The Tysam Kanban
We built a custom Kanban board from scratch to manage Tysam Labs.

- **Stack:** Node.js/Express backend + Vanilla JS frontend.
- **Direct Access:** The AI agent reads and writes the `tasks.json` file directly on the server.
- **Feature Set:** Projects, subtasks, priority levels, and a "Jira-style" detail view with comments.
- **Collaboration:** The AI can complete a task, move the card to "Done," and leave a comment explaining what it did.

---

## 7. 🚑 Disaster Recovery & Automation
Your AI is only as good as its memory. Don't let a server crash wipe it out.

- **S3 Backups:** Set up a cron job to sync your entire workspace (including `openclaw.json` and `memory/`) to an encrypted S3 bucket every 6 hours.
- **Git Mirroring:** Keep your code in GitLab/GitHub. Set up multiple remotes so every `git push` updates both platforms for redundancy.
- **Systemd:** Run your apps (OpenClaw and your custom web apps) as systemd services so they auto-restart on failure or reboot.

---

## 🔒 Security Best Practices
1. **Lock Down Ports:** Keep SSH (22) and Web (80/443) ports restricted to your specific Home IP in your firewall settings.
2. **Least Privilege:** Don't give your AI `AdministratorAccess` to AWS. Create a scoped IAM policy that only allows it to access specific S3 buckets or EC2 instances.
3. **Secrets Management:** Never commit your `.env` or `users.json` files to Git. Use `.gitignore` religiously.

---
*Created by tysam for the Tysam Labs Ecosystem — Feb 19, 2026*
