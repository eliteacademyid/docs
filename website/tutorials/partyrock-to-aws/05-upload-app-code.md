# Step 5 — Upload Your App Code to GitHub

Now we upload the app files that Kiro generated for you.
You do not need to understand the code — just upload the files.

---

## Before You Start This Step

- [ ] You have completed Step 4 (GitHub account and repository created)
- [ ] Kiro has generated your app files (from the App Planner prompt in Step 0)
- [ ] You have your app folder on your computer with `frontend/`, `backend/`, `infra/`, and `.github/` subfolders

---

## 5.1 Get Your App Files from Kiro

If you used Kiro to generate your app, your files are in a folder structure like this:

```
your-app/
├── frontend/
│   └── index.html
├── backend/
│   └── lambda/
│       └── (your Python files)
├── infra/
│   └── template.yaml
└── .github/
    └── workflows/
        └── deploy.yml
```

> 💡 If you haven't generated your app code yet, go back to Kiro, use the App Planner
> prompt from Step 0, and Kiro will generate all the files for you.

---

## 5.2 Download Your Files

In Kiro's file explorer (left sidebar), right-click your app folder
and select **Download** or **Reveal in Finder/Explorer**.

---

## 5.3 Upload Files to GitHub — Using the Web Interface

GitHub lets you upload files directly from your browser. No Git knowledge needed.

> ⚠️ **Important:** GitHub's web interface does not support dragging an entire folder.
> You need to upload the **files inside each folder**, not the folder itself.
> Follow the steps below carefully — each section tells you exactly which files to upload.

### Upload the frontend files

1. In your GitHub repository, click **Add file** → **Upload files**

   > 📸 **Screenshot:** Above the file list, "Add file" button with dropdown. Click "Upload files".

2. On your computer, open the `frontend` folder
3. Drag the **files inside** the `frontend` folder into the GitHub upload area
   (e.g. `index.html` — do not drag the folder itself)

   > 📸 **Screenshot:** The upload page shows a large dashed box. Open your `frontend` folder
   > on your computer and drag `index.html` directly into the dashed box.

4. In the **Commit changes** section at the bottom, type: `Add frontend files`
5. Click **Commit changes**

### Upload the backend Lambda files

1. Click **Add file** → **Upload files** again
2. In the filename area at the top, you need to create the folder path first.
   Click inside the large text area and type: `backend/lambda/`

   > 📸 **Screenshot:** A text input appears at the top of the upload area. Type
   > `backend/lambda/` — GitHub creates the folder structure automatically.

3. Now drag your Lambda `.py` files (e.g. `calorie_scanner.py`, `health_tracker.py`)
   into the upload area
4. Commit message: `Add backend Lambda functions` → **Commit changes**

   > 💡 Alternatively: click **Add file** → **Create new file**, type the full path
   > like `backend/lambda/calorie_scanner.py`, paste the file contents, and commit.
   > Repeat for each Lambda file.

### Upload the infrastructure template

1. Click **Add file** → **Create new file**
2. In the filename box, type: `infra/template.yaml`
3. Open your `infra/template.yaml` file on your computer, copy all the contents
4. Paste into the large text area
5. Commit message: `Add infrastructure template` → **Commit new file**

### Upload the GitHub Actions workflow

1. Click **Add file** → **Create new file**
2. In the filename box, type exactly: `.github/workflows/deploy.yml`

   > 📸 **Screenshot:** The filename input shows `.github/workflows/deploy.yml`.
   > GitHub automatically creates the folder structure as you type the slashes.

3. Copy the entire contents of the `deploy.yml` file Kiro generated
4. Paste it into the large text area below the filename
5. Click **Commit new file**

---

## 5.4 Verify Your Files Are Uploaded

> 📸 **Screenshot:** Repository file list shows folders: `.github/`, `backend/`, `frontend/`, `infra/`
> and a `README.md` file. Click into each folder to confirm the files are there.

---

## 5.5 Ask Kiro to Add Input Protection to Your Lambda Functions

**What is input sanitization?** When a user types something into your app and clicks Run,
that text goes directly to Claude. Without any checks, someone could type an extremely long
message (costing you money) or try a "prompt injection" attack — typing something like
"ignore your previous instructions and do X instead."

Kiro can add two simple protections:
1. **Input length limits** — reject inputs longer than 2,000 characters
2. **Prompt injection detection** — block inputs containing phrases like "ignore previous instructions"

### Ask Kiro to add these protections

1. Open Kiro and share your Lambda function files (the `.py` files in `backend/lambda/`)
2. Ask: *"Add input validation to each Lambda function: reject inputs longer than 2000 characters
   with a 400 error, and add basic prompt injection detection that blocks inputs containing
   phrases like 'ignore previous instructions' or 'disregard your system prompt'."*
3. Kiro adds a validation block near the top of each handler, before the Bedrock call
4. Upload the updated Lambda files to GitHub (replace the existing ones)

   > 💡 You do not need to understand the code Kiro adds. The key idea: bad input gets
   > rejected early, before it ever reaches the AI — saving you money and keeping your
   > app behaving as intended.

---

## 5.6 Add a requirements.txt File to Each Lambda Folder

**What is requirements.txt?** A text file that lists the Python libraries your Lambda function needs. Kiro generates this automatically with the correct versions, but if you need to create it manually:

- For **text generation and chatbot** Lambdas: `flask>=3.0.0` and `boto3>=1.34.0`
- For **image generation** Lambdas: `boto3>=1.34.0` only

1. In your GitHub repository, navigate into `backend/lambda/`
2. Click **Add file** → **Create new file**
3. In the filename box, type: `requirements.txt`
4. If Kiro already generated this file, copy its contents. Otherwise add the appropriate libraries above.
5. Commit message: `Add requirements.txt` → **Commit new file**

   > 💡 Text and chatbot Lambdas use Flask for streaming. Image Lambdas use a standard
   > handler and only need boto3. Kiro generates the correct requirements.txt for each.

---

## 5.7 Add a Favicon and Page Title to Your App

A **favicon** is the small icon in the browser tab. A **page title** is the text in that tab.
Both make your app feel like a real product.

1. In your GitHub repository, navigate to `frontend/index.html`
2. Click the **pencil icon** (Edit this file) in the top right

   > 📸 **Screenshot:** The file view shows a pencil icon in the top-right area. Click it.

3. Find the `<head>` section near the top. Add or update the `<title>` tag:
   ```html
   <title>My App Name</title>
   ```

4. Add a favicon using an emoji (no image file needed):
   ```html
   <link rel="icon" href="data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'><text y='.9em' font-size='90'>🤖</text></svg>">
   ```
   Replace `🤖` with any emoji that fits your app (🏥 for health, 🍕 for food, etc.)

5. Commit message: `Add favicon and page title` → **Commit changes**

   > 📸 **Screenshot:** After saving, the browser tab shows your emoji favicon and app name.

---

## 5.8 Add Markdown Rendering to Your App

**What is markdown?** Claude uses formatting symbols when it responds — like `**bold**`
or `- bullet points`. Without a renderer, users see those raw symbols instead of
nicely formatted text.

Adding **marked.js** (a free, tiny library) makes Claude's responses display with proper
bold text, bullet points, and headers — just like ChatGPT.

1. In your GitHub repository, navigate to `frontend/index.html` and click the pencil icon
2. Find the closing `</head>` tag and add this line **just before** it:
   ```html
   <script src="https://cdn.jsdelivr.net/npm/marked/marked.min.js"></script>
   ```
3. Now find where AI responses are displayed in the JavaScript. Look for lines like:
   ```javascript
   outputElement.textContent = responseText;
   ```
   Change `textContent` to use marked:
   ```javascript
   outputElement.innerHTML = marked.parse(responseText);
   ```

   > 💡 If you are not sure where to find this, ask Kiro:
   > *"Update my index.html to render AI responses as markdown using marked.js"*

4. Commit message: `Add markdown rendering with marked.js` → **Commit changes**

   > 📸 **Screenshot:** After redeploying, AI responses show formatted text —
   > bold headings, bullet point lists — instead of raw symbols.

---

## 5.9 Add a Character Counter to Text Input Fields

A character counter shows users how many characters they have typed (e.g. `47 / 500`).
It helps users know when they are approaching the input limit from Step 5.5.

1. Open Kiro and share your `index.html`, then ask:
   *"Add a character counter below each text input field in my app. Show the current
   character count and the maximum (e.g. '47 / 2000'). Make the counter turn red when
   the user is within 50 characters of the limit."*

2. Kiro adds a `<small>` element below each input and a few lines of JavaScript
3. Commit the updated file with message: `Add character counters to inputs`

   > 📸 **Screenshot:** Below each text input, a small grey counter shows "0 / 2000".
   > As the user types, the number updates in real time. Near the limit it turns red.

   > 💡 Make sure the maximum in the counter matches the limit in your Lambda validation
   > (Step 5.5). If Lambda rejects inputs over 2,000 characters, the counter shows `X / 2000`.

---

## ✅ Checkpoint

Before moving on, you should have:
- [ ] `frontend/index.html` uploaded
- [ ] `backend/lambda/` folder with your Python files uploaded
- [ ] `infra/template.yaml` uploaded
- [ ] `.github/workflows/deploy.yml` uploaded
- [ ] All 4 folders visible in your repository
- [ ] Lambda functions updated with input length limits and prompt injection protection
- [ ] `requirements.txt` added to the Lambda folder (can be empty)
- [ ] Page title and emoji favicon added to `index.html`
- [ ] `marked.js` script tag added and AI outputs updated to use `marked.parse()`
- [ ] Character counters added to text input fields
