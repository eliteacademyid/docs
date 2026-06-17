# Step 1 — Generate index.html

You need two values from your deployment:

| Value | Where to find it |
|---|---|
| API URL | GitHub Actions → your deploy run → Print URLs step |
| API Key | The key you created in Step 7.5 of the main tutorial |

---

## Kiro Prompt

Paste this into Kiro (replace the bracketed parts):

```
Build me a single index.html that calls my deployed API.

APP: [Your App Name]
DESCRIPTION: [What it does]

API ENDPOINTS (with streaming Lambda Function URLs):
- [Widget 1]: POST [URL_1]
- [Widget 2]: POST [URL_2]
(list each AI widget and its URL from the Print URLs step)

API KEY: [YOUR_API_KEY]
(sent as x-api-key header on API Gateway endpoints,
 not needed for Lambda Function URLs)

WIDGETS:
- [Input widgets: name, type, placeholder]
- [AI output widgets: name, which inputs they use]

REQUIREMENTS:
1. Single index.html — no build tools, no server needed
2. All CSS and JS inline
3. fetch() with streaming (response.body.getReader())
4. "Run All" button, loading spinners, error handling
5. Mobile-responsive, dark theme
6. Markdown rendering via marked.js CDN
7. Copy button on each output
8. Works by double-clicking the file (file:// protocol)

API KEY CONSTANT (top of script):
const API_KEY = '[YOUR_API_KEY]';
const API_BASE = '[YOUR_API_URL]';
```

Drag your PartyRock screenshots into the same message, then send.

---

## Save the File

Save the generated `index.html` anywhere on your computer (Desktop, Documents, wherever).

That's the only file you need.
