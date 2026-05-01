# Step 7 — Run the Deployment

Now we trigger the deployment. GitHub Actions will automatically:
1. Connect to your AWS account
2. Package your Lambda functions
3. Create all the AWS services
4. Upload your website to S3
5. Give you a live URL

This takes about 3–5 minutes.

---

## Before You Start This Step

- [ ] You have completed Step 6 (all secrets and variables added to GitHub)
- [ ] The `API_KEY` value you set in Step 6.6 is written down somewhere — you will need it in Step 7.5
- [ ] You are signed in to the AWS Console in a separate browser tab (needed for Steps 7.5 and 7.6)

---

## 7.1 Trigger the Deployment

1. In your GitHub repository, click the **Actions** tab
2. In the left sidebar, click your workflow name (e.g. "Deploy Health Tracker")
3. Click the **Run workflow** button on the right side

   > 📸 **Screenshot:** Right side shows a "Run workflow" dropdown button.
   > Click it, then click the green "Run workflow" button in the popup.

4. Click the green **Run workflow** button in the popup

---

## 7.2 Watch the Deployment Run

1. Refresh the page — a new row appears with a yellow spinning circle
2. Click that row, then click the **deploy** job

   > 📸 **Screenshot:** The job detail page shows steps checking off one by one:
   > ✅ Checkout → ✅ Configure AWS credentials → ✅ Setup SAM CLI →
   > 🔄 SAM Build → ⬜ SAM Deploy → ⬜ Upload frontend → ⬜ Print URLs

3. Wait for all steps to show a green ✅ checkmark

---

## 7.3 What Each Step Does

| Step | What is happening |
|---|---|
| Checkout | Downloads your code files |
| Configure AWS credentials | Connects to your AWS account |
| Setup SAM CLI | Installs the deployment tool |
| SAM Build | Packages your Lambda functions |
| SAM Deploy | Creates Lambda functions, Lambda Function URLs, S3 in AWS |
| Upload frontend | Puts your website on S3 with the correct API URL |
| Print URLs | Shows you the live links |

---

## 7.4 If Something Goes Wrong

Click the failed step to expand it and read the error. Common fixes:

| Error message | Fix |
|---|---|
| `Credentials could not be loaded` | Check your secrets — no spaces in the values |
| `Bucket does not exist` | Check `SAM_DEPLOY_BUCKET` matches your S3 bucket name exactly |
| `Access denied` | Path A: confirm all 6 IAM policies are attached to `github-deployer` |
| `Model not found` | Go back to Step 3 and confirm Bedrock model access is granted |
| `Stack already exists` | Fine — the stack may have still deployed successfully |
| `ExpiredTokenException` | Path B: lab session expired — start a new session and update all 3 credential secrets |
| `AccessDenied: iam:CreateRole` | Path B: use `template-sandbox.yaml` with LabRole — ask Kiro to generate it |
| `CAPABILITY_IAM` error | Ensure deploy.yml has `--capabilities CAPABILITY_IAM CAPABILITY_NAMED_IAM` (both flags) |
| `samconfig.toml` version error | Delete `samconfig.toml` — it causes version key errors in CI. All config goes in `deploy.yml`. |
| `No changes to deploy` | Fine — your stack is already up to date |

---

## 7.5 After Deployment — Set Up API Key Protection and Cost Controls

**What this does:** Right now your API is open — anyone who finds the URL can call it.
An API key is like a password your frontend sends with every request. API Gateway checks
for it and blocks anyone who does not have it.

We also set throttling limits (from Step 2.11) and Lambda concurrency limits (from Step 2.12)
here, now that your API exists.

### Step 1 — Find your API in API Gateway

1. In the AWS Console search bar, type **API Gateway** and click it
2. Click on your API name (e.g. `my-app-api` or `health-tracker-api`)

   > 📸 **Screenshot:** The API Gateway console shows a list of APIs. Click your app's API name.

### Step 2 — Create a Usage Plan

A **usage plan** sets the rate limits and links them to your API key.

3. In the left sidebar, click **Usage Plans** → **Create usage plan**
4. Fill in:
   - **Name:** `my-app-plan`
   - **Throttling — Rate:** `10` (requests per second)
   - **Throttling — Burst:** `20` (maximum short spike)
   - **Quota:** Leave unchecked
5. Click **Next**

   > 📸 **Screenshot:** The usage plan form shows fields for Name, Rate, and Burst.
   > Enter 10 and 20 respectively.

### Step 3 — Add your API stage to the plan

6. Click **Add API Stage**
7. For **API**, select your app's API
8. For **Stage**, select `prod`
9. Click the checkmark to confirm → **Next**

### Step 4 — Create an API Key

10. Click **Create API Key and add to Usage Plan**
11. **Name:** `my-app-key`
12. **API key:** select **Auto Generate**
13. Click **Save**

### Step 5 — Copy the API key value and update GitHub

14. Click on the key name `my-app-key` in the list
15. Click **Show** next to the API key value
16. Copy the key value

    > ⚠️ Go back to GitHub → Settings → Secrets → `API_KEY` and update the value
    > to match this auto-generated key. The frontend needs to send this exact value.

    > 📸 **Screenshot:** The API key detail page shows the key value hidden with a "Show" link.
    > Click Show, then copy the full value.

### Step 6 — Require the API key on your API methods

17. Go back to your API → click a resource (like `/calorie-scanner`)
18. Click on the **POST** method → **Method Request**
19. Find **API Key Required** and set it to `true`
20. Repeat for each POST method in your API
21. Click **Actions** → **Deploy API** → select `prod` stage → **Deploy**

    > 💡 After this, any request without the correct `x-api-key` header gets a 403 Forbidden
    > response. Your frontend already sends this header because Kiro included it.

### Step 7 — Set Lambda Reserved Concurrency

22. In the AWS Console, search for **Lambda** and click it
23. Click on your first Lambda function
24. Click the **Configuration** tab → **Concurrency**
25. Click **Edit** → select **Reserve concurrency** → enter `5`
26. Click **Save**
27. Repeat for each Lambda function in your app

    > 📸 **Screenshot:** The Concurrency settings page shows "Reserve concurrency" selected
    > with value 5. Click Save.

    > 💡 This caps each function at 5 simultaneous executions — more than enough for a
    > personal app, and it prevents runaway costs if something goes wrong.

---

## 7.6 Set Up CloudWatch Alarms

**What this does:** A CloudWatch alarm is like a smoke detector for your app.
You set a threshold (e.g. "more than 5 errors in 5 minutes") and AWS sends you an email
if that threshold is crossed. Without alarms, you only find out something is wrong
when a user complains.

### Step 1 — Open CloudWatch

1. In the AWS Console search bar, type **CloudWatch** and click it

### Step 2 — Create a Lambda error alarm

2. In the left sidebar, click **Alarms** → **All alarms** → **Create alarm**
3. Click **Select metric** → **Lambda** → **By Function Name**
4. Find your Lambda function, check the box next to **Errors** → **Select metric**

   > 📸 **Screenshot:** The metric browser shows Lambda functions. Check the "Errors"
   > metric for your function and click "Select metric".

5. Under **Conditions**, set:
   - **Threshold type:** Static
   - **Whenever Errors is:** Greater than `5`
   - **Datapoints to alarm:** 1 out of 1
6. Click **Next**

### Step 3 — Set up email notification

7. Under **Notification**, click **Create new topic**
8. **Topic name:** `my-app-alerts`
9. **Email endpoints:** enter your email address
10. Click **Create topic** → **Next**

    > ⚠️ AWS will send you a confirmation email. You must click the link in that email
    > to activate the notification. Check your inbox (and spam folder).

### Step 4 — Name and create the alarm

11. **Alarm name:** `my-app-lambda-errors`
12. Click **Next** → **Create alarm**

    > 📸 **Screenshot:** The new alarm appears in the alarms list with status "OK" (green).
    > This means no errors are happening right now — good!

### Step 5 — Create an API Gateway 5xx alarm

13. Repeat steps 2–12, but this time:
    - Click **API Gateway** → **By Stage**
    - Select your API and `prod` stage, check **5XXError**
    - Set threshold to greater than `5`
    - Name it: `my-app-api-errors`

    > 💡 A "5xx error" means your backend crashed while handling a request.
    > Getting an email when this happens means you can fix problems before users notice.

### Step 6 — Confirm your email subscription

14. Check your inbox for a message from `AWS Notifications`
15. Click the **Confirm subscription** link in the email

    > 📸 **Screenshot:** The email has subject "AWS Notification - Subscription Confirmation"
    > and a blue "Confirm subscription" link. Click it.

---

## 7.7 Set Up a Staging Environment (Optional)

**What is a staging environment?** A second copy of your app for testing changes before
they go live. You test on staging first, then deploy to production when confident.

### How it works

Your deployment creates a CloudFormation "stack" — a named collection of all your AWS resources.
To add staging, you deploy with a different stack name.

### Step 1 — Add a staging workflow

1. In your GitHub repository, navigate to `.github/workflows/`
2. Click **Add file** → **Create new file**
3. Name it: `deploy-staging.yml`
4. Copy the contents of your existing `deploy.yml` file and paste it in
5. Find the stack name line:
   ```yaml
   --stack-name my-partyrock-app
   ```
   Change it to:
   ```yaml
   --stack-name my-partyrock-app-staging
   ```
6. Also change the frontend S3 bucket name to something like `my-app-frontend-staging`
7. Commit the new file

   > 💡 Now you have two workflows: one for staging, one for production.
   > Trigger them independently from the Actions tab.

### Step 2 — What to do if a deployment breaks things

If you deploy a change and something breaks, CloudFormation automatically rolls back —
it undoes all the changes and returns your app to the last working state.

You do not need to do anything. You will see the stack status change to `ROLLBACK_COMPLETE`
in the CloudFormation console.

1. In the AWS Console, search for **CloudFormation**
2. Click on your stack name
3. The **Status** column shows the current state

   > 📸 **Screenshot:** The CloudFormation stacks list shows your stack with status
   > "UPDATE_COMPLETE" (green) or "ROLLBACK_COMPLETE" (red).

   > 💡 If you see `ROLLBACK_COMPLETE`, your app is back to the previous working version.
   > Fix the issue in your code, then redeploy.

---

## ✅ Checkpoint

Before moving on, you should have:
- [ ] Deployment triggered from the Actions tab
- [ ] All steps show green ✅ checkmarks
- [ ] No red ✗ errors
- [ ] API Gateway usage plan created (rate: 10, burst: 20)
- [ ] API key created, value copied to GitHub `API_KEY` secret
- [ ] API key required on all API methods and API redeployed
- [ ] Lambda reserved concurrency set to 5 per function
- [ ] CloudWatch alarm created for Lambda errors (email notification confirmed)
- [ ] CloudWatch alarm created for API 5xx errors
- [ ] (Optional) Staging workflow created
