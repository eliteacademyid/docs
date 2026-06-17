# From PartyRock to Local App — Static HTML

## What This Is

A single HTML file that calls your deployed API endpoints.
No Python, no server, no installation. Just open the file in a browser.

```
Browser (index.html on your computer)
      │  fetch() + API key
      ▼
Your API Gateway (already deployed)
      │
      ▼
Lambda → Bedrock (Claude AI)
```

---

## Prerequisites

- [ ] Backend already deployed (from the main tutorial Steps 1–7)
- [ ] Your API URL (from GitHub Actions → Print URLs)
- [ ] Your API key (from Step 7.5 of the main tutorial)

---

## 2 Steps

| # | Step | Time |
|---|---|---|
| 1 | Generate — Kiro builds your index.html | 10 min |
| 2 | Run — Open the file in your browser | 1 min |

**Total: ~10 minutes.**

---

## Cost

$0 for running locally. Your deployed backend still costs ~$0.003 per AI request.
