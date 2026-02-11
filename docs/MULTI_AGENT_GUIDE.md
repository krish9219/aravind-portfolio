# 🤖 OpenClaw Multi-Agent System Guide

## Table of Contents
1. [Overview](#overview)
2. [Architecture](#architecture)
3. [Agent Configuration](#agent-configuration)
4. [Model Routing](#model-routing)
5. [Creating Custom Agents](#creating-custom-agents)
6. [Inter-Agent Communication](#inter-agent-communication)
7. [Practical Examples](#practical-examples)
8. [Best Practices](#best-practices)

---

## 1. Overview

OpenClaw (formerly Clawdbot) supports a sophisticated multi-agent architecture where specialized AI agents handle different types of tasks. Each agent can:
- Use different LLM models
- Have unique system prompts
- Access specific tools
- Maintain separate conversation contexts

### Current Agent Setup

| Agent | Model | Specialty |
|-------|-------|----------|
| **main (Heyansh)** | Kimi K2.5 Cloud | General assistant, family AI |
| **the-engineer** | Qwen3 Coder Next | Coding, DevOps, Full-stack |
| **quantum-scientist** | DeepSeek V3.2 | AI/ML, Physics, Math |
| **market-analyst** | Kimi K2.5 | Crypto, Trading, Business |
| **content-mogul** | Kimi K2.5 | Social media, Content |
| **social-chameleon** | MiniMax M2 | Personas, Social guidance |

---

## 2. Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     OpenClaw Gateway                         │
│                    (Port 18789)                              │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐    │
│  │ WhatsApp │  │ Telegram │  │  Web UI  │  │  Copilot │    │
│  └────┬─────┘  └────┬─────┘  └────┬─────┘  └────┬─────┘    │
│       │             │             │             │           │
│       └─────────────┴─────────────┴─────────────┘           │
│                          │                                   │
│                    ┌─────▼─────┐                            │
│                    │  Router   │                            │
│                    └─────┬─────┘                            │
│                          │                                   │
│    ┌─────────────────────┼─────────────────────┐            │
│    │                     │                     │            │
│    ▼                     ▼                     ▼            │
│ ┌──────┐            ┌──────┐            ┌──────┐           │
│ │Agent1│            │Agent2│            │Agent3│           │
│ │(Main)│            │(Coder)│           │(Analyst)│        │
│ └──┬───┘            └──┬───┘            └──┬───┘           │
│    │                   │                   │                │
│    ▼                   ▼                   ▼                │
│ ┌──────┐            ┌──────┐            ┌──────┐           │
│ │ Kimi │            │ Qwen │            │DeepSk│           │
│ │Cloud │            │Coder │            │ V3.2 │           │
│ └──────┘            └──────┘            └──────┘           │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

---

## 3. Agent Configuration

### Directory Structure

```
~/.clawdbot/
├── clawdbot.json          # Main configuration
├── agents/
│   ├── main/              # Default agent (Heyansh)
│   │   ├── agent/
│   │   │   ├── AGENT.md   # System prompt
│   │   │   └── memory/    # Agent memory
│   │   └── sessions/      # Conversation history
│   ├── the-engineer/
│   │   └── agent/
│   │       └── AGENT.md
│   ├── quantum-scientist/
│   ├── market-analyst/
│   ├── content-mogul/
│   └── social-chameleon/
└── gateway.env            # Environment variables
```

### Configuration File (clawdbot.json)

```json
{
  "agents": {
    "defaults": {
      "model": {
        "primary": "ollama/kimi-k2.5:cloud",
        "fallbacks": [
          "ollama/qwen3-coder-next:cloud",
          "openrouter/xiaomi/mimo-v2-flash"
        ]
      },
      "models": {
        "ollama/kimi-k2.5:cloud": {"alias": "kimi"},
        "ollama/qwen3-coder-next:cloud": {"alias": "coder"},
        "ollama/deepseek-v3.2:cloud": {"alias": "quantum"},
        "ollama/minimax-m2:cloud": {"alias": "social"},
        "perplexity/sonar-pro": {"alias": "pro"}
      },
      "workspace": "/root/clawd",
      "sandbox": {
        "workspaceAccess": "rw"
      }
    }
  }
}
```

---

## 4. Model Routing

### Using Model Aliases

Switch models mid-conversation using `@alias`:

```
User: @coder Write a Python async web scraper
Agent: [Uses Qwen3-Coder-Next]

User: @quantum Explain quantum entanglement
Agent: [Uses DeepSeek V3.2]

User: @pro Search for latest AI news
Agent: [Uses Perplexity Sonar Pro]

User: @social Help me with this dating profile
Agent: [Uses MiniMax M2]
```

### Programmatic Model Switching

```bash
# Via CLI
clawdbot agent --local --agent main --model ollama/qwen3-coder-next:cloud \
  --message "Write a React component"

# Via message prefix
"@coder Create a REST API in Flask"
```

---

## 5. Creating Custom Agents

### Step 1: Create Agent Directory

```bash
mkdir -p ~/.clawdbot/agents/my-agent/agent
```

### Step 2: Create AGENT.md (System Prompt)

```markdown
# My Custom Agent

## Identity
**Name:** SpecialistBot
**Model:** ollama/deepseek-v3.2:cloud
**Specialization:** [Your specialty]

## Core Capabilities
- Capability 1
- Capability 2

## Response Style
- How to respond
- Tone and format

## Tools Available
- exec: Run shell commands
- web_fetch: Fetch web content
- file operations: Read/write files
```

### Step 3: Register Agent (Optional)

```bash
clawdbot agents add my-agent \
  --workspace /root/clawd/my-agent \
  --model ollama/deepseek-v3.2:cloud
```

### Step 4: Test Agent

```bash
clawdbot agent --local --agent my-agent \
  --message "Hello, introduce yourself"
```

---

## 6. Inter-Agent Communication

### Delegating to Specialized Agents

The main agent can delegate tasks:

```
User: I need help with a complex trading algorithm

Heyansh: I'll delegate this to the market-analyst for optimal results.
         @market-analyst Please analyze and create a trading algorithm for...
```

### Subagent Spawning

```json
// In clawdbot.json
"agents": {
  "defaults": {
    "subagents": {
      "maxConcurrent": 8
    }
  }
}
```

### Context Sharing

Agents can share context via:
1. **Workspace files** - Shared `/root/clawd/` directory
2. **Memory files** - MEMORY.md, USER.md
3. **Session handoff** - Explicit context passing

---

## 7. Practical Examples

### Example 1: Coding Task with Review

```
User: Create a REST API and have it reviewed

# Step 1: Coding agent creates
@coder Create a FastAPI REST API for user management

# Step 2: Main agent reviews
@kimi Review the code for security issues

# Step 3: Quantum agent optimizes
@quantum Optimize the algorithm complexity
```

### Example 2: Research + Content Creation

```
User: Research crypto trends and create a LinkedIn post

# Step 1: Pro search for research
@pro Latest cryptocurrency market trends 2026

# Step 2: Content creation
@content-mogul Create a viral LinkedIn post about these trends
```

### Example 3: Multi-Domain Analysis

```
User: Analyze this stock from technical and quantum computing perspectives

# Market analysis
@market-analyst Technical analysis of NVDA stock

# Quantum computing angle  
@quantum How will quantum computing affect NVIDIA's AI chips?
```

---

## 8. Best Practices

### DO ✅

1. **Use specific agents for specific tasks**
   - Coding → @coder
   - Research → @pro
   - Social → @social

2. **Leverage model strengths**
   - Reasoning tasks → DeepSeek V3.2
   - Fast responses → MIMO Flash
   - Code generation → Qwen3 Coder

3. **Maintain clean context**
   - Clear conversation when switching domains
   - Use `clawdbot agents prune` periodically

4. **Use fallbacks wisely**
   - Configure fallback chains for reliability
   - Free models as last resort

### DON'T ❌

1. **Don't overload single agent**
   - Distribute complex tasks across specialists

2. **Don't ignore model limits**
   - Respect context windows
   - Break large tasks into chunks

3. **Don't skip system prompts**
   - Well-crafted AGENT.md = better results

---

## Quick Reference Commands

```bash
# List all agents
clawdbot agents list

# Check agent status
clawdbot channels status

# Run with specific agent
clawdbot agent --local --agent the-engineer --message "..."

# Switch model mid-chat
@coder [your message]
@quantum [your message]
@pro [your message]

# View agent config
clawdbot config get agents.defaults

# Prune old sessions
clawdbot agents prune
```

---

## Model Aliases Quick Reference

| Alias | Model | Best For |
|-------|-------|----------|
| `@kimi` | Kimi K2.5 Cloud | General, default |
| `@coder` | Qwen3 Coder Next | Programming |
| `@quantum` | DeepSeek V3.2 | Science, Math, ML |
| `@social` | MiniMax M2 | Social, Creative |
| `@pro` | Perplexity Sonar | Web research |

---

*Happy multi-agent orchestrating! 🚀*
