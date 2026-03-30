# 🚀 Using Claude Code with Qwen (Free Setup Guide)

> Run Claude Code's powerful agentic coding workflow — completely FREE — by routing it through the Qwen API using `claude-code-router`.

---

## 📋 Table of Contents

- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Step 1 — Install Claude Code](#step-1--install-claude-code)
- [Step 2 — Install claude-code-router](#step-2--install-claude-code-router)
- [Step 3 — Get Your Qwen Access Token](#step-3--get-your-qwen-access-token)
- [Step 4 — Configure the Router](#step-4--configure-the-router)
- [Step 5 — Start the Router](#step-5--start-the-router)
- [Step 6 — Launch Claude Code with Qwen](#step-6--launch-claude-code-with-qwen)
- [Platform-Specific Commands](#platform-specific-commands)
- [Troubleshooting](#troubleshooting)
- [Tips & Notes](#tips--notes)

---

## Overview

Claude Code is Anthropic's agentic coding CLI. By default it uses Anthropic's paid models. This guide shows you how to **redirect Claude Code to use Qwen's free API** instead — giving you full agentic coding power at zero cost.

```
You ──► Claude Code CLI ──► claude-code-router (localhost:3456) ──► Qwen API
```

✅ Works on: **Windows** (PowerShell / CMD) · **macOS** · **Linux**

> ⚠️ **Privacy Note:** Qwen is hosted by Alibaba. If you work with sensitive or proprietary code, review your organization's data policies before proceeding.

---

## Prerequisites

Before starting, make sure you have:

| Requirement | Version | Check |
|---|---|---|
| Node.js | v18.0.0 or higher | `node --version` |
| npm | Latest | `npm --version` |
| Git | Any recent version | `git --version` |

If Node.js is not installed, download it from [nodejs.org](https://nodejs.org).

---

## Step 1 — Install Claude Code

Install the Claude Code CLI globally via npm:

```bash
npm install -g @anthropic-ai/claude-code
```

Verify the installation:

```bash
claude --version
```

---

## Step 2 — Install claude-code-router

`claude-code-router` is a local proxy that intercepts Claude Code's API calls and forwards them to your chosen provider (Qwen in this case).

```bash
npm install -g claude-code-router
```



## Step 3 — Get Your Qwen Access Token

1. Open Terminal And Run This commmand

```bash
qwen
```

3. A browser window will open — log in and authorize the CLI
4. After logging in, locate the generated credentials file:

**macOS / Linux:**
```
~/.qwen/oauth_creds.json
```

**Windows:**
```
%USERPROFILE%\.qwen\oauth_creds.json
```

5. Open the file — it will look like this:

```json
{
  "access_token": "eyJhbGciOiJSUzI1NiIs...",
  "token_type": "Bearer",
  "refresh_token": "eyJhbGciOiJSUzI1NiIs...",
  "resource_url": "portal.qwen.ai",
  "expiry_date": 1764876220290
}
```

6. **Copy the entire `access_token` value** — you'll need it in the next step.

---

## Step 4 — Configure the Router

Create the config directory and file for `claude-code-router`.

### macOS / Linux

```bash
mkdir -p ~/.claude-code-router
```

Create the config file:

```bash
cat > ~/.claude-code-router/config.json << 'EOF'
{  
  "LOG": true,  
  "LOG_LEVEL": "info",  
  "HOST": "127.0.0.1",  
  "PORT": 3456,  
  "API_TIMEOUT_MS": 600000,  
  "Providers": [  
    {  
      "name": "qwen",  
      "api_base_url": "https://portal.qwen.ai/v1/chat/completions",  
      "api_key": "YOUR_ACESS_TOKEN_HERE",  
      "models": [  
        "qwen3-coder-plus",  
        "qwen3-coder-plus",  
        "qwen3-coder-plus"  
      ]  
    }  
  ],  
  "Router": {  
    "default": "qwen,qwen3-coder-plus",  
    "background": "qwen,qwen3-coder-plus",  
    "think": "qwen,qwen3-coder-plus",  
    "longContext": "qwen,qwen3-coder-plus",  
    "longContextThreshold": 60000,  
    "webSearch": "qwen,qwen3-coder-plus"  
  }  
}
EOF
```

### Windows (PowerShell)

```powershell
mkdir -Force "$env:USERPROFILE\.claude-code-router"
```

Then open Notepad to create the config:

```powershell
notepad "$env:USERPROFILE\.claude-code-router\config.json"
```

Paste the JSON above into Notepad, replace `YOUR_QWEN_ACCESS_TOKEN_HERE` with your actual token, then save and close.

### Windows (CMD)

```cmd
mkdir %USERPROFILE%\.claude-code-router
notepad %USERPROFILE%\.claude-code-router\config.json
```

---

## Step 5 — Start the Router

Open a **dedicated terminal window** for the router and run:

```bash
ccr start
```

You should see output like:

```
[info] claude-code-router started on http://127.0.0.1:3456
```

**Keep this terminal open** while using Claude Code. The router must stay running.

---

## Step 6 — Launch Claude Code with Qwen

Open a **second terminal window** and set the environment variables, then start Claude Code.

### macOS / Linux

```bash
export ANTHROPIC_AUTH_TOKEN=test
export ANTHROPIC_API_KEY=""
export ANTHROPIC_BASE_URL=http://127.0.0.1:3456
claude
```

### Windows (PowerShell)

```powershell
$env:ANTHROPIC_AUTH_TOKEN = "test"
$env:ANTHROPIC_API_KEY = ""
$env:ANTHROPIC_BASE_URL = "http://127.0.0.1:3456"
claude
```

### Windows (CMD)

```cmd
set ANTHROPIC_AUTH_TOKEN=test
set ANTHROPIC_API_KEY=
set ANTHROPIC_BASE_URL=http://127.0.0.1:3456
claude
```

✅ Claude Code should launch. Type `hi` and press Enter — Qwen should respond through Claude Code's interface!

---

## Platform-Specific Commands

Summary of all environment variable commands by platform:

| Variable | Value | Purpose |
|---|---|---|
| `ANTHROPIC_AUTH_TOKEN` | `test` | Bypasses Anthropic auth check |
| `ANTHROPIC_API_KEY` | *(empty)* | Must be blank |
| `ANTHROPIC_BASE_URL` | `http://127.0.0.1:3456` | Points to your local router |

---

## Troubleshooting

### ❌ `command not found: claude` or `command not found: ccr`

Close your terminal and reopen it. The `PATH` may not have updated after installation.

### ❌ `401 Unauthorized` errors

Your Qwen access token has expired. Re-run:

```bash
qwen login
```

Then update the `api_key` in your `config.json` with the new token.

### ❌ Router not starting / port in use

Try changing the `PORT` in `config.json` to another port (e.g. `3457`) and update `ANTHROPIC_BASE_URL` to match.

### ❌ Claude Code not connecting to router

Make sure the router terminal is still open and the service is running. Restart it with `ccr start` if needed.

---

## Tips & Notes

- **Two terminals are required:** one for the router, one for Claude Code.
- **Token expiry:** Qwen access tokens expire periodically. If you hit auth errors after days of use, re-run `qwen login`.
- **Persistent setup (macOS/Linux):** Add the environment variables to your `~/.zshrc` or `~/.bashrc` to avoid re-entering them each session:

```bash
export ANTHROPIC_AUTH_TOKEN=test
export ANTHROPIC_API_KEY=""
export ANTHROPIC_BASE_URL=http://127.0.0.1:3456
```

- **Context window:** Qwen-Coder supports large context windows, but if you're doing very long sessions, run `/compact` inside Claude Code to keep the context clean.
- **Sensitive code:** Qwen runs on Alibaba's infrastructure. Avoid using this setup with proprietary or confidential codebases.

---

## 📚 Resources

- [Claude Code Official Docs](https://docs.anthropic.com/en/docs/claude-code/overview)
- [claude-code-router on GitHub](https://github.com/musistudio/claude-code-router)
- [QwenLM / qwen-code](https://github.com/QwenLM/qwen-code)
