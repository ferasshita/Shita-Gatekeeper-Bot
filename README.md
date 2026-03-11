# 🛡️ Shita-Gatekeeper-Bot

> A powerful GitHub App bot that automates pull request greetings and issue management — built and maintained by **Feras Shita**.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Node.js](https://img.shields.io/badge/Node.js-20%2B-brightgreen.svg)](https://nodejs.org/)
[![GitHub App](https://img.shields.io/badge/GitHub-App-black.svg)](https://docs.github.com/en/developers/apps)

---

## 📖 Table of Contents

- [About](#-about)
- [Features](#-features)
- [Requirements](#-requirements)
- [Setup](#️-setup)
- [Configuration](#-configuration)
- [Usage](#-usage)
- [How It Works](#-how-it-works)
- [Security Considerations](#-security-considerations)
- [Contributing](#-contributing)
- [License](#-license)

---

## 🤖 About

**Shita-Gatekeeper-Bot** is a GitHub App powered by [Octokit.js](https://github.com/octokit/octokit.js) that acts as an automated gatekeeper for your repositories. It listens for GitHub webhook events and responds automatically to:

- **New pull requests** — posts a welcoming, informative comment to guide contributors.
- **Issue comments** — closes issues on demand when a maintainer types `/close`.

This bot is ideal for maintainers who want to enforce contribution standards, reduce manual triage work, and keep their repositories organized.

---

## ✨ Features

| Feature | Description |
|---|---|
| 🔔 PR Welcome Message | Automatically posts a customizable greeting comment when a new PR is opened |
| 🔒 Issue Auto-Close | Closes issues immediately when a maintainer comments `/close` |
| ⚙️ Fully Configurable | Customize the welcome message via `message.md` |
| 🏢 GitHub Enterprise Support | Works with both GitHub.com and GitHub Enterprise Server |
| 🔐 Secure Webhook Handling | Validates all incoming webhook payloads using a shared secret |

---

## 📋 Requirements

- **Node.js** 20 or higher
- A **GitHub App** with the following configuration:
  - Subscribed to events: `Pull requests`, `Issue comments`
  - Permissions:
    - Pull requests: **Read & write**
    - Issues: **Read & write**
    - Metadata: **Read-only**
- For local development: a tunnel to expose your local server to the internet
  - [smee.io](https://smee.io/) (recommended for development)
  - [ngrok](https://ngrok.com/)
  - [cloudflared](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup/tunnel-guide/local/)
- Your GitHub App Webhook URL must be publicly accessible.

---

## 🛠️ Setup

### 1. Clone the Repository

```bash
git clone https://github.com/ferasshita/Shita-Gatekeeper-Bot.git
cd Shita-Gatekeeper-Bot
```

### 2. Install Dependencies

```bash
npm install
```

### 3. Create Your GitHub App

1. Go to **GitHub Settings → Developer settings → GitHub Apps → New GitHub App**.
2. Set the Webhook URL to your server's URL (e.g., `https://your-domain.com/api/webhook`).
3. Configure permissions and events as listed in [Requirements](#-requirements).
4. Generate and download a **private key** for your app.
5. Note your **App ID**.

### 4. Configure Environment Variables

Create a `.env` file in the root of the project:

```env
# Your GitHub App's App ID (found in your App settings)
APP_ID=your_app_id

# Path to the private key file you downloaded from GitHub
PRIVATE_KEY_PATH=./your-private-key.pem

# A random secret string used to validate webhook payloads
WEBHOOK_SECRET=your_webhook_secret

# (Optional) Only needed for GitHub Enterprise Server
# ENTERPRISE_HOSTNAME=your-github-enterprise-hostname.com

# (Optional) The port the server listens on (default: 3000)
# PORT=3000
```

### 5. Start the Server

```bash
npm run server
```

### 6. (Local Development) Set Up a Webhook Tunnel

If testing locally, use [smee](https://smee.io/) to forward webhooks:

```bash
npx smee -u https://smee.io/<your-channel-id> -t http://localhost:3000/api/webhook
```

### 7. Install the App on a Repository

In your GitHub App settings, install the app on one or more repositories to start receiving events.

---

## ⚙️ Configuration

### Customizing the PR Welcome Message

Edit the `message.md` file to customize the comment that is automatically posted when a new pull request is opened:

```markdown
👋 Hi there!

Thanks for opening a new PR. Please consider following this guide...
```

Any valid Markdown is supported.

---

## 🚀 Usage

Once the server is running and the app is installed:

### Pull Request Auto-Comment

Open a new pull request on any repository where the app is installed. The bot will automatically post the contents of `message.md` as a comment on the PR.

### Issue Auto-Close

Any maintainer or collaborator can close an issue by posting a comment that **starts with** `/close`. The bot will:
1. Close the issue.
2. Post a confirmation comment: _"Issue closed by Shita-Gatekeeper-Bot 🛡️"_

---

## 🔍 How It Works

```
GitHub Event (PR opened / Issue commented)
        │
        ▼
  Webhook Payload ──► Shita-Gatekeeper-Bot Server
        │                        │
        │              Validates webhook secret
        │                        │
        ├── pull_request.opened ─► Posts welcome comment from message.md
        │
        └── issue_comment.created
                   │
                   └── starts with "/close"? ──► Closes issue + posts confirmation
```

The bot is built using:
- **[Octokit.js](https://github.com/octokit/octokit.js)** — Official GitHub SDK for JavaScript
- **[@octokit/webhooks](https://github.com/octokit/webhooks.js/)** — Webhook event handling middleware
- **[dotenv](https://github.com/motdotla/dotenv)** — Environment variable management

---

## 🔐 Security Considerations

- **Webhook Secret**: Always set a strong, random `WEBHOOK_SECRET`. This ensures that incoming webhook payloads are genuinely from GitHub.
- **Private Key**: The `PRIVATE_KEY_PATH` points to your GitHub App's private key. Keep this file **out of version control** (it is listed in `.gitignore`).
- **Secrets Management**: For production deployments, store secrets using a dedicated secrets management solution:
  - [HashiCorp Vault](https://www.vaultproject.io/use-cases/key-management)
  - [Azure Key Vault](https://learn.microsoft.com/en-us/azure/key-vault/secrets/quick-create-node?tabs=windows)
  - [AWS Secrets Manager](https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/clients/client-secrets-manager/)
  - [Google Secret Manager](https://cloud.google.com/nodejs/docs/reference/secret-manager/latest)

---

## 🤝 Contributing

Contributions are welcome! Please read [CONTRIBUTING.md](CONTRIBUTING.md) and the [Code of Conduct](CODE_OF_CONDUCT.md) before submitting a pull request.

---

## 📄 License

This project is licensed under the [MIT License](LICENSE).

