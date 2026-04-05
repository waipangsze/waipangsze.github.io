---
layout: post
title: "AI | ollama + OpenClaw + gemma4"
categories: [AI]
tags: [AI, ollama, OpenClaw, gemma4]
author: wpsze
date: 2026-04-04 17:03:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: 
banner_img: 
---

# Build

- old computer, rebuild OS

```console
sudo apt update && sudo apt install -y build-essential python3 make cmake
sudo apt install openssh-server
ip addr
sudo systemctl status ssh
lscpu
free -h
df -h 
```

```console
sudo apt install curl
sudo apt-get install zstd
curl -fsSL https://ollama.com/install.sh | sh
ollama --help
ollama list
ollama pull gemma4:e2b
ollama list
ollama run gemma4:e2b
```

```console
sudo apt install git
sudo apt install npm
sudo apt install npmcurl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
sudo apt install -y nodejs

node -v        # >= 22.x
npm -v         # >= 8.x
git --version  # >= 2.30

ollama launch openclaw --model gemma4:e2b
```

```log
Installing OpenClaw...
npm warn deprecated node-domexception@1.0.0: Use your platform's native DOMException instead

added 482 packages in 59s

88 packages are looking for funding
  run `npm fund` for details
OpenClaw installed successfully

Launching OpenClaw with gemma4:e2b...

Security

  OpenClaw can read files and run actions when tools are enabled.
  A bad prompt can trick it into doing unsafe things.

  Learn more: https://docs.openclaw.ai/gateway/security

Setting up OpenClaw with Ollama...
  Model: gemma4:e2b

🦞 OpenClaw 2026.4.2 (d74a122) — Self-hosted, self-updating, self-aware (just kidding... unless?).

Default Ollama model: gemma4:e2b
Config overwrite: /home/wpsze/.openclaw/openclaw.json (sha256 b0dfb4731dc2bcf034025d7e87563893d4aa18362a8af239a396b913d4ba8b84 -> 5ee8a4f4ba8d3050c42caa2069f2957a180f9182207f849c509e86fd3abf0e17, backup=/home/wpsze/.openclaw/openclaw.json.bak)
Config write anomaly: /home/wpsze/.openclaw/openclaw.json (missing-meta-before-write)
Updated ~/.openclaw/openclaw.json
Workspace OK: ~/.openclaw/workspace
Sessions OK: ~/.openclaw/agents/main/sessions

Installed systemd service: /home/wpsze/.config/systemd/user/openclaw-gateway.service
Systemd lingering is disabled for wpsze. Run: sudo loginctl enable-linger wpsze

Agents: main (default)
Heartbeat interval: 30m (main)
Session store (main): /home/wpsze/.openclaw/agents/main/sessions/sessions.json (0 entries)
Tip: run `openclaw configure --section web` to store your Brave API key for web_search. Docs: https://docs.openclaw.ai/tools/web
  ✓ Installed web search plugin

Starting your assistant — this may take a moment...

Starting gateway...

✓ OpenClaw is running

  Open the Web UI:
    http://localhost:18789/#token=ce2cbf5d6ccc9f420af727c53ffb213563802bb128013d0a

  Quick start:
    /help             see all commands
    openclaw configure --section channels   connect WhatsApp, Telegram, etc.
    openclaw skills                         browse and install skills
  The OpenClaw gateway is running in the background.
  Stop it with: openclaw gateway stop
🦞 OpenClaw 2026.4.2 (d74a122) — If you're lost, run doctor; if you're brave, run prod; if you're wise, run tests.
 openclaw tui - ws://127.0.0.1:18789 - agent main - session main                                                         
 Wake up, my friend!                           
 run error: 500 {"error":"model requires more system memory (9.8 GiB) than is available (8.1 GiB)"}                                                                                                       
 gateway connected | error                             
 agent main | session main (openclaw-tui) | ollama/gemma4:e2b | tokens ?/131k 
 ```

# Error

## error EACCES: permission denied

The error EACCES: permission denied when running ollama launch openclaw typically occurs because the command tries to install or write to directories (like /usr/lib/node_modules or /home/node/.openclaw) without the necessary user permissions.

1. Fix Global NPM Permissions (Recommended)This is the most common cause on macOS and Linux. If Ollama attempts to install OpenClaw globally using npm, it may lack write access to the system folder.

Solution: Change the ownership of the npm directory to your current user so you don't need sudo.

```console
sudo chown -R $(whoami) $(npm config get prefix)/{lib/node_modules,bin,share}
```

# For example

- local (computer) fails to run any local model
- apply `kimi-k2.5:cloud`
  - **ollama has free cloud usage** for session and weekly

## Build WRF/WPS

```Prompt
under /home/wpsze/Documents/WRF,
1. build WRF from https://github.com/wrf-model/WRF/tree/v4.7.0
2. use micromamba to build env for WRF env
```

{% gi 5 2-3 %}
![](https://i.imgur.com/kEbqW9C.png)
![](https://i.imgur.com/IYG1La0.png)
![](https://i.imgur.com/4g4EERZ.png)
![](https://i.imgur.com/iQaUiFs.png)
![](https://i.imgur.com/G9XjuzA.png)
{% endgi %}

```Prompt
1. microamamba activate /home/wpsze/micromamba/envs/wrf-build
2. use /home/wpsze/Documents/WRF/WRF
3. build WPS from https://github.com/wrf-model/WPS/tree/v4.5 on folder /home/wpsze/Documents/WPS
```

{% gi 4 2-2 %}
![](https://i.imgur.com/Ou0hFAO.png)
![](https://i.imgur.com/GVfhn4F.png)
![](https://i.imgur.com/94HYU6C.png)
![](https://i.imgur.com/YXpcB7M.png)
{% endgi %}