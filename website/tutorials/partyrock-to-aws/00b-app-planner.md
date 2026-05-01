# App Planner — Generate Your Kiro Prompt & Spec

Before you touch any AWS service, use the App Planner to turn your
PartyRock app details into ready-to-paste Kiro outputs.
This is the fastest way to get your app code generated.

---

## What the App Planner Produces

Fill in the form — both outputs update live as you type. No AI call, no button to click.

| Output | What it is | Where to use it |
|---|---|---|
| Kiro Chat Prompt | A complete natural-language request with all widget details | Paste into Kiro chat + drag screenshots |
| Kiro Spec Plan | A structured spec with requirements, architecture, Lambda details, and tasks | Paste into Kiro Spec panel |

---

## How to Use It

Open the tutorial website and click **✨ App Planner** in the top navigation bar.

The page is split into two columns:
- **Left** — the form you fill in
- **Right** — the live preview that updates as you type, with two tabs (Chat Prompt / Spec Plan)

---

## Section 1 — App Info

| Field | What to enter |
|---|---|
| App Name | The name shown at the top of your PartyRock app |
| Who is this app for? | Describe the target user in plain English |
| What does your app do? | 2–4 sentences describing the app |
| How does a user use it? | Step-by-step flow, numbered |

---

## Section 2 — Widgets

Click **+ Add Widget** for each widget in your PartyRock app.
Open each widget's ✏️ edit panel in PartyRock and copy the details here.

### Widget type reference

| PartyRock edit panel title | Select in the form |
|---|---|
| Edit Text Input | 📝 Edit Text Input |
| Edit Slider | 🔢 Edit Slider |
| Edit Dropdown | 📋 Edit Dropdown |
| Edit Static Text | ℹ️ Edit Static Text |
| Edit AI Output | ✨ Edit AI Output |
| Edit Image Output | 🖼️ Edit Image Output |
| Edit Chat Box | 💬 Edit Chat Box |
| Edit File Upload | 📄 Edit File Upload |

### Fields per widget type

**Edit Text Input** (from PartyRock's "Edit Text Input" panel)
- Helpful hint text — the grey placeholder shown when the box is empty
- Pre-fill value — optional default value users can change
- Optional toggle — whether users must fill this to run the app

**Edit Slider** (from PartyRock's "Edit Slider" panel)
- Slider type: Ordinal (labels like Low/Medium/High) or Numeric (numbers)
- Ordinal: tickmarks list (comma separated)
- Numeric: minimum, maximum, default value, unit label
- Optional toggle

**Edit Dropdown** (from PartyRock's "Edit Dropdown" panel)
- Options — one per line or comma separated
- Optional toggle
- Pre-selected option
- Helpful hint text shown when nothing is selected

**Edit Static Text** (from PartyRock's "Edit Static Text" panel)
- Content — supports markdown (## headings, **bold**, bullet lists)

**Edit AI Output** (from PartyRock's "Edit AI Output" panel)
- Prompt tab: "What should AI generate?" — copy exactly, use [Widget Name] for references
- Which widgets does this reference?
- AI model tab: model selector (Claude Sonnet 4.6 default)
- Temperature (0 = Factual → 1 = Creative)
- Top P (0 = Factual → 1 = Creative)
- Web search toggle
- Deep thinking toggle

**Edit Image Output** (from PartyRock's "Edit Image Output" panel)
- Prompt tab: "Describe the image you want AI to create" — use [Widget Name] for references
- Which widgets does this reference?
- Advanced tab: model (Amazon Nova Canvas default), aspect ratio, negative prompt, CFG scale, random seed toggle

**Edit Chat Box** (from PartyRock's "Edit Chat Box" panel)
- Prompt tab: "What role should the chatbot be?" — the system/role prompt, use [Widget Name]
- Which widgets does the role prompt reference?
- Instructions tab: hint text for the chat input box
- Instructions tab: opening message
- Advanced tab: model, Temperature, Top P

**Edit File Upload** (from PartyRock's "Edit File Upload" panel)
- Instructions for users (optional)
- Optional toggle (checked by default)
- Accepted file types: Images, PDF, DOCX, PPTX, CSV, JSON, HTML
- Which AI widget uses this uploaded file?

> 💡 File uploads are handled directly via base64 encoding — no extra setup needed. The generated app reads the file in the browser, converts it to base64, and sends it in the same request as the linked AI widget. This works for files up to ~15MB. For larger files, see Step 9.5 of the full tutorial.

---

## Section 3 — Extra Notes

Anything special Kiro should know — dark theme, reset button, conversation history, etc.

---

## Using the Outputs

### Kiro Chat Prompt tab
1. Fill in all fields until the preview looks complete
2. Click **Copy** (top right of the preview panel)
3. Open Kiro → start a new chat
4. Paste the prompt
5. Drag your PartyRock widget screenshots into the same chat message
6. Send — Kiro generates all your app files

### Kiro Spec Plan tab
1. Switch to the **Kiro Spec Plan** tab in the preview
2. Click **Copy**
3. In Kiro, open the left sidebar → **Specs** → **New Spec**
4. Paste the spec content
5. Kiro breaks it into tasks and implements them one by one with your approval

---

## After Kiro Generates Your Code

Once Kiro has generated your app files, continue with:

- **Path A (personal account):** Steps 2 → 3 → 4 → 5 → 6 → 7 → 8
- **Path B (sandbox):** Steps 02b → 3 → 4 → 5 → 6 → 7 → 8
