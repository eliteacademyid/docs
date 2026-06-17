# Step 1 — Read Your PartyRock App Widgets

Before we build anything, we need to understand what your PartyRock app does.
Every PartyRock app is made of "widgets." We need to write down each one.

---

## 1.1 Open Your App in Edit Mode

1. Go to [partyrock.aws](https://partyrock.aws) and sign in
2. Click on your app to open it
3. Click the **Edit** button in the top right corner

   > 📸 **Screenshot:** Top right of the screen shows buttons: "Remix", "Edit", "Share".
   > Click the pencil icon labeled **Edit**.

---

## 1.2 Identify Every Widget

Look at your app. You will see boxes (widgets) arranged on the page.
Each widget has a small **edit icon** (pencil ✏️) in its top right corner.

There are 8 types of widgets in PartyRock:

| Edit panel title | What it does |
|---|---|
| Edit Text Input | User types text here |
| Edit Slider | User picks a number or label on a sliding scale |
| Edit Dropdown | User picks from a list of options |
| Edit Static Text | Displays fixed text or instructions (supports markdown) |
| Edit AI Output | AI generates text based on a prompt |
| Edit Image Output | AI generates an image based on a description |
| Edit Chat Box | Back-and-forth conversation with AI |
| Edit File Upload | User uploads a file or image |

---

## 1.3 Fill In This Widget Map

For each widget in your app, click the ✏️ edit icon and fill in this table.
The fields you need depend on the widget type — see the reference below.

```
Widget #1
  Edit panel title: [ Edit Text Input / Edit Slider / Edit Dropdown /
                      Edit Static Text / Edit AI Output / Edit Image Output /
                      Edit Chat Box / Edit File Upload ]
  Widget name:      ________________________________

  --- For Edit Text Input ---
  Hint text:        ________________________________
  Pre-fill value:   ________________________________

  --- For Edit Slider ---
  Type:             [ Ordinal / Numeric ]
  Tickmarks/Range:  ________________________________
  Default:          ________________________________

  --- For Edit Dropdown ---
  Options:          ________________________________
  Pre-selected:     ________________________________

  --- For Edit Static Text ---
  Content:          ________________________________

  --- For Edit AI Output ---
  Prompt:           ________________________________
  References:       ________________________________  (the [Widget Name] tags used)
  Model:            ________________________________  (from AI model tab)
  Temperature:      ____  Top P: ____

  --- For Edit Image Output ---
  Image prompt:     ________________________________
  References:       ________________________________
  Model:            ________________________________  (from Advanced tab)
  Aspect ratio:     ________________________________
  Negative prompt:  ________________________________

  --- For Edit Chat Box ---
  Role prompt:      ________________________________  (from Prompt tab)
  References:       ________________________________
  Opening message:  ________________________________  (from Instructions tab)
  Model:            ________________________________  (from Advanced tab)

  --- For Edit File Upload ---
  Instructions:     ________________________________
  Used by widget:   ________________________________

(copy for each widget)
```

---

## 1.4 Example — Health Tracker App

```
Widget #1
  Edit panel title: Edit Text Input
  Widget name:      Food Description
  Hint text:        Describe the food you ate (e.g. grilled chicken with rice)

Widget #2
  Edit panel title: Edit AI Output
  Widget name:      Calorie Scanner
  Prompt:           Analyze the calories in: [Food Description]
                    Provide total calories, macros, and a health rating.
  References:       Food Description
  Model:            Claude Haiku 4.5

Widget #3
  Edit panel title: Edit Slider
  Widget name:      Current Weight
  Type:             Numeric
  Range:            30 – 300 kg, default 80

Widget #4
  Edit panel title: Edit Dropdown
  Widget name:      Activity Level
  Options:          Sedentary, Lightly Active, Moderately Active, Very Active
  Pre-selected:     Moderately Active

Widget #5
  Edit panel title: Edit Dropdown
  Widget name:      Fitness Goal
  Options:          Maintain Weight, Lose Weight, Gain Muscle
  Pre-selected:     Maintain Weight

Widget #6
  Edit panel title: Edit AI Output
  Widget name:      Personalized Health Tracker
  Prompt:           Based on [Food Description] and weight [Current Weight]
                    with activity [Activity Level] and goal [Fitness Goal]...
  References:       Food Description, Current Weight, Activity Level, Fitness Goal
  Model:            Claude Haiku 4.5

Widget #7
  Edit panel title: Edit File Upload
  Widget name:      Food Image
  Instructions:     Upload a photo of your meal
  Used by widget:   Image Calorie Scanner

Widget #8
  Edit panel title: Edit AI Output
  Widget name:      Image Calorie Scanner
  Prompt:           Analyze the food visible in this image...
  References:       Food Image
  Model:            Claude Haiku 4.5
```

---

## 1.5 Take Screenshots of Each Widget's Settings

1. Click ✏️ on each widget to open its edit panel
2. Take a screenshot of the panel — capture all tabs (Prompt, Instructions, Advanced, AI model)
3. Save them as `widget1.png`, `widget2.png`, etc.

   > 💡 **Tip:** On Mac press `Cmd + Shift + 4`. On Windows press `Windows + Shift + S`.

You will drag these screenshots into Kiro chat alongside your prompt
so Kiro can see the exact layout and settings.

---

## ✅ Checkpoint

Before moving on, you should have:
- [ ] All widgets listed with their edit panel type and name
- [ ] All fields filled in for each widget
- [ ] Screenshots of each widget's edit panel saved
