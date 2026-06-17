# Step 2 — Run

Double-click `index.html` to open it in your browser. Done.

---

## If Streaming Doesn't Work on file://

Some browsers block streaming on `file://` protocol. Use any tiny server:

```bash
npx serve .                      # Node
python3 -m http.server 5000      # Python
php -S localhost:5000             # PHP
```

Then open `http://localhost:5000`

---

## Troubleshooting

| Problem | Fix |
|---|---|
| CORS error | Lambda Function URLs need CORS headers. Ask Kiro: "Add CORS headers to my Lambda" |
| 403 Forbidden | Wrong API key — check the constant in index.html |
| Network error | Check API URL is correct, backend is deployed |
| Empty response | Open DevTools (F12) → Network tab → read the error |

---

## Making Changes

Edit `index.html` in any text editor → save → refresh browser.

Or ask Kiro: *"Change the theme"* / *"Add a reset button"* / *"Make fonts larger"*

---

## Sharing

| Method | How |
|---|---|
| Send the file | Email index.html — recipient opens it and it works |
| Same WiFi | `npx serve .` → share your IP:port |
| Temporary public | `ngrok http 5000` |
| Permanent | Upload to S3 (already done in main tutorial) |
