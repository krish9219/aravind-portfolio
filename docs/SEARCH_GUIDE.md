# 🔍 Web Search Configuration Guide

## Available Search Providers

### 1. Perplexity Sonar Pro (Pro Search)

**Use for:**
- Deep research queries
- Real-time information
- Fact-checking
- Complex multi-part questions
- Academic research

**API Key:** [YOUR_PERPLEXITY_API_KEY]

**How to trigger:**
```
@pro [your search query]

Examples:
- @pro Latest breakthroughs in quantum computing 2026
- @pro Compare Tesla vs Rivian stock performance
- @pro What's the current state of AI regulation in EU?
```

---

### 2. Exa.ai (Normal Web Search)

**Use for:**
- Quick web lookups
- Finding specific URLs
- General information
- Link discovery

**API Key:** [YOUR_EXA_API_KEY]

**How to trigger:**
```
Search for [topic]
Find links about [topic]
Look up [topic]
```

---

## Search Examples

### Pro Search (Perplexity)
```
User: @pro What are the top 5 AI startups to watch in 2026?
User: @pro Explain the latest developments in nuclear fusion research
User: @pro Compare cryptocurrency regulations across G20 countries
```

### Normal Search (Exa.ai)
```
User: Search for React 19 documentation
User: Find Python asyncio tutorials
User: Look up best practices for Kubernetes deployment
```

---

## API Configuration

### In clawdbot.json:
```json
{
  "models": {
    "providers": {
      "perplexity": {
        "baseUrl": "https://api.perplexity.ai/chat/completions",
        "apiKey": "[YOUR_PERPLEXITY_API_KEY]",
        "api": "openai-completions",
        "models": [{
          "id": "sonar-pro",
          "name": "Sonar Pro",
          "reasoning": false
        }]
      }
    }
  },
  "agents": {
    "defaults": {
      "models": {
        "perplexity/sonar-pro": {"alias": "pro"}
      }
    }
  }
}
```

### Environment Variables:
```bash
export PERPLEXITY_API_KEY="[YOUR_PERPLEXITY_API_KEY]"
export EXA_API_KEY="[YOUR_EXA_API_KEY]"
```

---

## Best Practices

1. **Use @pro for:**
   - Current events
   - Market data
   - Research papers
   - Fact verification

2. **Use normal search for:**
   - Documentation links
   - Tutorial discovery
   - General lookups

3. **Combine with agents:**
   - `@pro` + `@market-analyst` for financial research
   - `@pro` + `@quantum` for scientific papers
   - `@pro` + `@coder` for latest library docs

---

*Pro tip: Start queries with "@pro" for Perplexity's real-time search capabilities!*
