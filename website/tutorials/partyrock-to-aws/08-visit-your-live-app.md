# Step 8 — Visit Your Live App

Your app is now live on the internet. Let's find the URL, test it thoroughly,
and make sure everything is working the way it should.

---

## 8.1 Find Your Live URL

1. Go back to the **Actions** tab in GitHub
2. Click on your most recent successful deployment run
3. Click on the **deploy** job
4. Scroll to the very last step: **Print URLs** and expand it

   > 📸 **Screenshot:** The "Print URLs" step shows two lines:
   > ```
   > ✅ API URL:      https://abc123.execute-api.us-east-1.amazonaws.com/prod
   > ✅ Frontend URL: http://my-app-frontend-123456789.s3-website-us-east-1.amazonaws.com
   > ```

5. Copy the **Frontend URL** — this is your live app

> 💡 Notice the URL starts with `http://` — not `https://`. This means it is not yet
> secure. Step 9 shows you how to add HTTPS with CloudFront (recommended).

---

## 8.2 Open Your App

1. Paste the Frontend URL into a new browser tab and press Enter
2. Your app should load and look similar to your PartyRock app

   > 📸 **Screenshot:** Browser shows your app with the same widgets as PartyRock —
   > input fields, dropdowns, and AI output areas.

---

## 8.3 Test Your App End to End

1. Fill in the input fields
2. Click the **Run All** button
3. Wait 5–10 seconds for the AI to respond
4. The AI output areas should fill with generated content

   > 📸 **Screenshot:** After clicking "Run All", the AI output boxes show a spinning
   > loader, then fill with text responses from Claude.

> 💡 The first request after a period of inactivity may take 10–15 seconds.
> This is called a "cold start" — Lambda is waking up. Subsequent requests are faster.

---

## 8.4 Test the Copy Buttons and Reset Button

Your app should have two convenience features that Kiro added to the frontend:

### Copy button
Each AI output area has a **Copy** button that copies the response text to your clipboard.

1. Run your app and wait for an AI response to appear
2. Click the **Copy** button next to any output area
3. Open a text editor and paste — the text should appear

   > 📸 **Screenshot:** Each output area has a small "Copy" button in the top-right corner.
   > After clicking, it briefly changes to "Copied!" to confirm.

   > ⚠️ If you do not see copy buttons, ask Kiro:
   > *"Add a copy-to-clipboard button to each AI output area in my index.html"*

   > ⚠️ Copy buttons only work on HTTPS pages. If they do not work, add CloudFront first
   > (Step 9.1) then test again.

### Reset button
The **Reset** button clears all inputs and outputs so you can start fresh.

4. Fill in your inputs and run the app
5. Click the **Reset** button — all fields should clear back to empty

   > ⚠️ If you do not see a reset button, ask Kiro:
   > *"Add a Reset button to my index.html that clears all inputs and outputs"*

---

## 8.5 Test on Mobile

Your app should work on phones and tablets. Here is what to check:

1. Open your app URL on your phone (text it to yourself or scan a QR code)
2. Check these things:

   | What to check | What good looks like |
   |---|---|
   | Page loads | App appears without errors |
   | Text is readable | Not too small to read |
   | Input fields work | Keyboard pops up when you tap a field |
   | Keyboard does not cover inputs | You can still see what you are typing |
   | Run All button is tappable | Large enough to tap with a finger |
   | Output text is readable | Responses display properly |

3. If the keyboard covers your input fields when typing on mobile, ask Kiro to fix it:
   *"Add a CSS fix to my index.html so the keyboard does not cover input fields on mobile"*

   > 💡 Kiro will add a small CSS rule that scrolls the page up when the keyboard appears,
   > keeping your input field visible.

---

## 8.6 Check CloudWatch — Find Your App's Logs

Every time someone uses your app, AWS records what happened. These records are called **logs**.
Checking logs is how you diagnose problems when something goes wrong.

1. In the AWS Console, search for **CloudWatch** and click it
2. In the left sidebar, click **Log groups**
3. Look for a log group named `/aws/lambda/your-function-name`
4. Click on a log group

   > 📸 **Screenshot:** The log groups list shows entries starting with "/aws/lambda/"
   > followed by your function names. Click one to open it.

5. Click on the most recent **log stream** (the top row in the list)
6. You will see log entries — each one is a record of one request to your app

   > 📸 **Screenshot:** Log entries show timestamps and messages like
   > "START RequestId: abc123" and "END RequestId: abc123 Duration: 2345ms"

> 💡 If your app returned an error, you will see a line starting with `ERROR` in the logs.
> Copy that line and paste it into Kiro — it will help diagnose the problem.

> 💡 You can also check the CloudWatch dashboard you created in Step 3.6 to see
> graphs of errors and invocations over time.

---

## 8.7 Set a Billing Alarm So You Never Get Surprised

AWS charges based on usage. For a personal app with low traffic, costs are near zero —
but it is good practice to set a billing alarm so you get an email if costs ever spike.

1. In the AWS Console, click your **account name** in the top right
2. Click **Billing and Cost Management**

   > 📸 **Screenshot:** Clicking your account name opens a dropdown menu.
   > "Billing and Cost Management" appears near the bottom.

3. In the left sidebar, click **Budgets** → **Create budget**
4. Select **Use a template** → **Zero spend budget**
   (sends you an email the moment any charges appear above the free tier)
5. Enter your email address → **Create budget**

   > 📸 **Screenshot:** The budget template selection shows "Zero spend budget" as an option.
   > Select it, enter your email, and click Create budget.

   > 💡 Alternatively, choose **Monthly cost budget** and set a threshold of $5.
   > This gives you a warning before costs get significant.

> ⚠️ If you ever see unexpected charges, the most common cause is forgetting to delete
> a test resource. Check the AWS Cost Explorer to see which service is charging you.

---

## 8.8 What to Do If the App Is Slow

### Cold starts (first request is slow)
Lambda "sleeps" when not in use. The first request wakes it up, which takes 5–15 seconds.
Subsequent requests are fast (under 2 seconds). This is normal behavior.

**Fix:** Add a loading message like "Warming up... this may take a moment on first use."

### Large responses taking a long time
If Claude is generating a very long response, the user sees nothing until it is fully done.

**Fix:** Ask Kiro about enabling **Lambda response streaming**:
*"Can my Lambda functions stream responses back to the frontend instead of waiting for the full response?"*

Streaming shows text to the user word-by-word as Claude generates it — just like ChatGPT.
This requires changes to both the Lambda function and the frontend JavaScript.

### One widget failing and blocking others
If your app has multiple AI widgets and one fails, it should not stop the others from running.

**Fix:** Ask Kiro to add per-widget error handling:
*"Update my Run All button logic so each widget runs independently — if one fails, the others still complete"*

---

## 8.9 What You Just Built

| | PartyRock | Your App |
|---|---|---|
| Requires PartyRock login | ✅ Yes | ❌ No |
| Custom look and feel | ❌ No | ✅ Yes |
| You own the infrastructure | ❌ No | ✅ Yes |
| Works on mobile | ✅ Yes | ✅ Yes |
| Share with anyone | Limited | ✅ Yes |
| Custom domain possible | ❌ No | ✅ Yes |
| HTTPS (secure padlock) | ✅ Yes | ✅ Yes (after Step 9) |
| API key protection | N/A | ✅ Yes (set up in Step 7.5) |
| Cost controls | N/A | ✅ Yes (throttling + alarms) |
| Cost | Free (PartyRock credits) | ~$0 (AWS free tier) |

---

## ✅ You Did It!

Your PartyRock app is now a real, published web application running on AWS.

**What you built:**
- A website hosted on Amazon S3
- AI-powered backend running on AWS Lambda
- Connected to Amazon Bedrock (Claude AI)
- Deployed automatically via GitHub Actions
- Protected by an API key
- Monitored with CloudWatch alarms and a billing alarm

**Next steps:**
- See Step 9 to add HTTPS via CloudFront (recommended) and optional extras
- Update your app by editing files in GitHub — it redeploys automatically
- Share the URL with friends and family
