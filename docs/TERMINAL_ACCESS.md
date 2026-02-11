# 🖥️ Terminal Access Guide

## Quick Access Details

### 🌐 Web URLs

| Service | URL |
|---------|-----|
| **Main App** | https://silly-allen-1.preview.emergentagent.com |
| **Code Server (Terminal)** | https://silly-allen-1.preview.emergentagent.com/code/ |
| **OpenClaw Web UI** | https://silly-allen-1.preview.emergentagent.com (port 3000) |

### 🔌 OpenClaw Copilot Connection

```
Gateway URL: wss://silly-allen-1.preview.emergentagent.com/api/openclaw/ws
Auth Token: [YOUR_GATEWAY_TOKEN]
Relay URL: (leave empty)
```

### 📱 Messaging Channels

| Channel | Contact |
|---------|---------|
| **WhatsApp** | +916281047229 |
| **Telegram** | @HeyanshBot |

---

## Accessing Terminal from Local System

### Option 1: Code Server (Recommended)
1. Open browser: `https://silly-allen-1.preview.emergentagent.com/code/`
2. Access full VS Code environment with terminal
3. Navigate to `/root/clawd/` for workspace

### Option 2: OpenClaw Copilot Extension
1. Install OpenClaw Copilot Chrome extension
2. Configure with gateway details above
3. Use terminal commands directly from any webpage

### Option 3: Direct API Access
```bash
# Using curl to execute commands
curl -X POST "https://silly-allen-1.preview.emergentagent.com/api/exec" \
  -H "Authorization: Bearer [YOUR_TOKEN]" \
  -H "Content-Type: application/json" \
  -d '{"command": "ls -la /root/clawd/"}'
```

---

## Common Terminal Commands

### Clawdbot Management
```bash
# Set PATH
export NODE_DIR=/root/nodejs
export CLAWDBOT_DIR=/root/.clawdbot-bin
export PATH="$NODE_DIR/bin:$CLAWDBOT_DIR:/usr/local/bin:$PATH"

# Check status
clawdbot channels status

# Restart gateway
sudo supervisorctl restart clawdbot-gateway

# View logs
tail -f /var/log/supervisor/clawdbot-gateway.out.log

# Run agent command
clawdbot agent --local --agent main --message "Hello"
```

### Ollama Commands
```bash
# List models
ollama list

# Run model
ollama run kimi-k2.5:cloud "Your prompt"

# Pull new model
ollama pull model-name:cloud
```

### Service Management
```bash
# Check all services
sudo supervisorctl status

# Restart specific service
sudo supervisorctl restart backend
sudo supervisorctl restart frontend
sudo supervisorctl restart clawdbot-gateway

# View logs
tail -f /var/log/supervisor/backend.err.log
tail -f /var/log/supervisor/frontend.err.log
```

### Homebrew (Linux)
```bash
# Use brew
/home/linuxbrew/.linuxbrew/bin/brew --version

# Install package (as brewuser)
sudo -u brewuser /home/linuxbrew/.linuxbrew/bin/brew install package-name
```

---

## File Locations

| Path | Description |
|------|-------------|
| `/root/.clawdbot/` | Clawdbot config and agents |
| `/root/.clawdbot/clawdbot.json` | Main configuration |
| `/root/.clawdbot/agents/` | Agent definitions |
| `/root/clawd/` | Workspace directory |
| `/root/clawd/SOUL.md` | Agent personality |
| `/root/clawd/MEMORY.md` | Persistent memory |
| `/app/` | Application code |
| `/var/log/supervisor/` | Service logs |

---

## Environment Variables

```bash
# Clawdbot
export CLAWDBOT_GATEWAY_TOKEN="[YOUR_GATEWAY_TOKEN]"

# Gmail (gog)
export GOG_KEYRING_PASSWORD="[YOUR_GOG_PASSWORD]"
export GOG_ACCOUNT="[YOUR_EMAIL]"

# Exa.ai
export EXA_API_KEY="[YOUR_EXA_API_KEY]"

# Perplexity
export PERPLEXITY_API_KEY="[YOUR_PERPLEXITY_API_KEY]"
```

---

*For any issues, check logs first: `tail -50 /var/log/supervisor/clawdbot-gateway.err.log`*
