# Step 10 — Update Your App After It's Live

Your app is deployed and working. Now you want to make a change — update a prompt,
fix a typo, add a new feature, or change the design. This step shows you how.

---

## Before You Start This Step

- [ ] Your app is deployed and the live URL is working (Step 8 complete)
- [ ] You have your GitHub repository open in a browser tab

---

## How Updates Work

Every time you push (save) a change to your GitHub repository, the deployment
workflow runs automatically and updates your live app. You do not need to do
anything in the AWS Console — GitHub handles it.

The process is:
1. Edit a file in GitHub (or ask Kiro to edit it)
2. Commit the change (save it to GitHub)
3. GitHub Actions automatically redeploys
4. Your live app is updated in 3–5 minutes

---

## 10.1 Update a Prompt (Change What the AI Says)

The AI prompts live inside your Lambda function files in `backend/lambda/`.

### Using Kiro (recommended)

1. Open Kiro and share the Lambda file you want to update
   (e.g. `backend/lambda/calorie_scanner.py`)
2. Describe the change you want:
   *"Update the prompt to also include a recommendation for a complementary exercise"*
3. Kiro edits the file
4. The updated file is saved in your workspace

### Upload the updated file to GitHub

5. In your GitHub repository, navigate to `backend/lambda/`
6. Click on the file you want to update (e.g. `calorie_scanner.py`)
7. Click the **pencil icon** (Edit this file) in the top right

   > 📸 **Screenshot:** The file view shows a pencil icon in the top-right area.
   > Click it to enter edit mode.

8. Select all the text in the editor (`Ctrl+A` or `Cmd+A`) and delete it
9. Paste the updated file content from Kiro
10. Scroll down, add a commit message describing your change:
    `Update calorie scanner prompt to include exercise recommendation`
11. Click **Commit changes**

   > 📸 **Screenshot:** After clicking Commit changes, the file is saved and
   > the Actions tab shows a new deployment running automatically.

---

## 10.2 Update the Frontend (Change How It Looks)

The frontend lives in `frontend/index.html`.

### Using Kiro (recommended)

1. Open Kiro and share `frontend/index.html`
2. Describe the change:
   *"Change the background color to dark blue"*
   or *"Add a header with my app's logo"*
   or *"Make the Run All button larger"*
3. Kiro edits the file

### Upload the updated file to GitHub

4. In your GitHub repository, navigate to `frontend/index.html`
5. Click the **pencil icon** to edit
6. Select all, delete, paste the updated content
7. Commit message: `Update frontend design`
8. Click **Commit changes**

---

## 10.3 Add a New Widget / Feature

If you want to add a new AI output, a new input field, or a new feature:

1. Open Kiro and share your current app files
2. Describe what you want to add:
   *"Add a new AI output widget called 'Meal Plan' that generates a 3-day meal plan
   based on the user's fitness goal and current weight"*
3. Kiro will:
   - Add a new Lambda function (`meal_plan.py`)
   - Add a new API route to `template.yaml`
   - Add the new widget to `index.html`
   - Update `deploy.yml` if needed
4. Upload all changed files to GitHub
5. The deployment runs automatically

---

## 10.4 Watch the Update Deploy

1. After committing your change, click the **Actions** tab in GitHub
2. You will see a new deployment run starting automatically

   > 📸 **Screenshot:** The Actions tab shows a new row with a yellow spinning circle
   > appearing within a few seconds of your commit.

3. Wait for all steps to show green ✅ checkmarks (3–5 minutes)
4. Refresh your live app URL — the change is live

   > 💡 If you have CloudFront set up (Step 9.1), you may need to wait an extra
   > minute for CloudFront to pick up the new version. Or you can force a refresh
   > with `Ctrl+Shift+R` (Windows) or `Cmd+Shift+R` (Mac).

---

## 10.5 Roll Back a Change That Broke Something

If you deploy a change and something breaks, you have two options:

### Option A — Revert in GitHub (easiest)

1. In your GitHub repository, click on the file you changed
2. Click the **History** button (clock icon) to see previous versions

   > 📸 **Screenshot:** The file view shows a clock icon labeled "History" in the
   > top-right area. Click it to see a list of previous versions.

3. Click on the commit before your broken change
4. Click the **<>** button to view the file at that point in time
5. Click the **pencil icon** to edit, select all, copy the content
6. Go back to the current file, edit it, paste the old content
7. Commit with message: `Revert to previous version`

### Option B — CloudFormation automatic rollback

If the deployment itself failed (not just a logic error), CloudFormation automatically
rolls back to the last working version. You do not need to do anything.

Check the status:
1. In the AWS Console, search for **CloudFormation**
2. Click on your stack name
3. If status shows `ROLLBACK_COMPLETE` — your app is back to the previous working version

   > 💡 Fix the issue in your code, then redeploy.

---

## 10.6 Test Changes Before Going Live (Staging)

If you set up a staging environment in Step 7.7, use it:

1. Make your change and commit it to a branch (not `main`)
2. In GitHub Actions, manually trigger `deploy-staging.yml`
3. Test the staging URL
4. If it works, merge your branch to `main` — the production deployment runs automatically

---

## ✅ Checkpoint

- [ ] You know how to edit a Lambda function and redeploy
- [ ] You know how to edit the frontend and redeploy
- [ ] You know how to roll back a broken change
- [ ] (Optional) You have a staging environment for testing changes safely
