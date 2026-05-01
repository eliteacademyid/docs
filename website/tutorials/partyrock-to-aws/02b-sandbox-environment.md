# Deploying in an AWS Lab / Sandbox Environment

This guide is for users who are working inside a **restricted AWS lab environment**
and do not need to create their own AWS account.

Common sandbox platforms:
- AWS Skill Builder (Jam labs, guided labs)
- Vocareum (used by AWS Academy)
- AWS Workshop Studio
- Event Engine (AWS re:Invent, summits)
- Qwiklabs / Google-hosted AWS labs

---

## How Sandbox Environments Work

When you start a lab session, the platform:
1. Creates a temporary AWS account behind the scenes
2. Gives you pre-provisioned credentials (Access Key ID + Secret + Session Token)
3. Restricts what services and actions you can use
4. Automatically deletes everything when the session ends

You access the AWS Console by clicking a button in the lab panel — no sign-up needed.

---

## What Is Different From a Personal Account

| | Personal Account | Lab Sandbox |
|---|---|---|
| Account creation | Required | Not needed |
| Credit card | Required | Not needed |
| App stays live | Permanently | Only during session |
| IAM full control | Yes | No — restricted to LabRole |
| Session token | Not needed | Required (4th secret) |
| Bedrock models | Any | Only pre-enabled ones |
| Region choice | Any | Lab region only |

---

## Step 1 — Get Your Sandbox Credentials

In your lab environment, find the credentials panel.
It is usually a button labeled **AWS Details**, **Account Details**,
or **Get CLI credentials**.

Copy these three values:

| Value | Format |
|---|---|
| AWS Access Key ID | Starts with `ASIA` (not `AKIA` like personal accounts) |
| AWS Secret Access Key | Long random string |
| AWS Session Token | Very long string — copy the entire thing |

> ⚠️ Session tokens are very long. Make sure you copy the entire value.
> Missing even one character will cause authentication to fail.

---

## Step 2 — Check What Is Available in Your Lab

In the AWS Console (opened from your lab panel):

1. Note the **region** shown in the top right (e.g. `us-east-1`)
2. Go to **Amazon Bedrock → Model access** — check which models show **Access granted**
3. Go to **S3** — check if you can create buckets or if one is pre-created
4. Go to **IAM → Roles** — find a role named `LabRole` and copy its ARN

The LabRole ARN looks like:
```
arn:aws:iam::123456789012:role/LabRole
```

---

## Step 3 — Check Bedrock Model Availability

If Claude 3 Sonnet is not pre-enabled, look for these alternatives:

| Model | Bedrock Model ID |
|---|---|
| Amazon Titan Text Express | `amazon.titan-text-express-v1` |
| Claude Instant | `anthropic.claude-instant-v1` |
| Claude 3 Haiku | `anthropic.claude-3-haiku-20240307-v1:0` |

Update the `modelId` in your Lambda functions to match what is available.

---

## Step 4 — Create an S3 Bucket for SAM Artifacts

In AWS Console → S3 → Create bucket:
- Name: `lab-sam-deploy-[your-initials]-[4 random digits]`
- Region: same as your lab region
- Leave all other settings as default

If bucket creation is blocked, check for any pre-existing buckets in S3 and use one of those.

---

## Step 5 — Use the Pre-Generated Sandbox Template

**What is template-sandbox.yaml?** Sandbox environments block SAM from creating IAM roles.
Instead of modifying the main `template.yaml` yourself (which requires editing YAML code),
Kiro can generate a ready-to-use `template-sandbox.yaml` that already has the LabRole
substituted in — you just need to replace one placeholder with your account ID.

### Ask Kiro to generate the sandbox template

1. Open Kiro and share your `infra/template.yaml`
2. Ask: *"Generate a template-sandbox.yaml version of this template that uses LabRole
   instead of creating an IAM role. Use `YOUR_ACCOUNT_ID` as a placeholder for the account ID.
   Also remove CAPABILITY_NAMED_IAM from the deploy command since we are not creating IAM resources."*

3. Kiro produces a file where each Lambda function has:
   ```yaml
   Role: arn:aws:iam::YOUR_ACCOUNT_ID:role/LabRole
   ```
   instead of the IAM role resource block.

4. Replace `YOUR_ACCOUNT_ID` with your actual account ID:
   - Find your account ID: click your account name in the top right of the AWS Console
   - It is a 12-digit number like `123456789012`

5. Upload `template-sandbox.yaml` to your GitHub repository in the `infra/` folder

### Update the deploy workflow to use the sandbox template

6. In your GitHub repository, open `.github/workflows/deploy.yml`
7. Find the SAM build and deploy commands and update them:

   ```yaml
   - name: SAM Build
     run: sam build --template infra/template-sandbox.yaml

   - name: SAM Deploy
     run: |
       sam deploy \
         --template infra/template-sandbox.yaml \
         --stack-name my-partyrock-app \
         --s3-bucket ${{ secrets.SAM_DEPLOY_BUCKET }} \
         --region ${{ vars.AWS_REGION }} \
         --no-confirm-changeset \
         --no-fail-on-empty-changeset
   ```

   > 💡 Notice `CAPABILITY_NAMED_IAM` is removed — you no longer need it because
   > you are not creating IAM resources.

---

## Step 6 — Set Up GitHub and Add 4 Secrets + 1 Variable

Follow Step 4 of the main tutorial to create a GitHub account and repository,
and Step 5 to upload your files.

Then in GitHub → Settings → Secrets and variables → Actions, add **4 secrets**:

| Secret Name | Value |
|---|---|
| `AWS_ACCESS_KEY_ID` | Access Key ID from your lab panel |
| `AWS_SECRET_ACCESS_KEY` | Secret Access Key from your lab panel |
| `AWS_SESSION_TOKEN` | Session Token from your lab panel — full long string |
| `SAM_DEPLOY_BUCKET` | S3 bucket name from Step 4 |

Also click the **Variables** tab and add **1 variable**:

| Variable Name | Value |
|---|---|
| `AWS_REGION` | Your lab's region (e.g. `us-east-1`) |

> ⚠️ Session tokens expire when your lab session ends.
> To redeploy after expiry, start a new lab session and update all 3 credential secrets.

---

## Step 7 — Update the GitHub Actions Workflow

Add the session token to the credentials step in `.github/workflows/deploy.yml`:

```yaml
- name: Configure AWS credentials
  uses: aws-actions/configure-aws-credentials@v4
  with:
    aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
    aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
    aws-session-token: ${{ secrets.AWS_SESSION_TOKEN }}    # ← add this line
    aws-region: ${{ vars.AWS_REGION }}                     # ← use variable, not hardcoded
```

---

## Step 8 — Run the Deployment

Go to the Actions tab in GitHub → click your workflow → click Run workflow.

### Sandbox-specific errors and fixes

| Error | Fix |
|---|---|
| `ExpiredTokenException` | Lab session expired. Start a new session, update all 3 credential secrets. |
| `AccessDenied: iam:CreateRole` | Remove IAM role from template.yaml, use LabRole ARN instead (Step 5). |
| `AccessDenied: bedrock:InvokeModel` | LabRole may not have Bedrock permissions. Ask your instructor or switch to a pre-enabled model. |
| `BucketAlreadyExists` | Add more random characters to your bucket name. |
| `No changes to deploy` | Fine — stack is already up to date. |
| Bedrock model not found | Switch to `amazon.titan-text-express-v1` in your Lambda functions. |

---

## Step 9 — Visit Your Live App

Find the Frontend URL in the GitHub Actions **Print URLs** step and open it.

> ⚠️ Your app will stop working when the lab session expires because
> Lambda functions use the temporary session credentials.
> To keep the app running permanently, use a personal AWS account.

---

## Step 10 — Add a Session Expiry Warning to Your Frontend

Lab sessions expire after a set time (usually 1–4 hours). A visible countdown helps
users know when the session will end and the app will stop working.

### Ask Kiro to add a countdown timer

1. Open Kiro and share your `frontend/index.html`
2. Ask: *"Add a session expiry countdown banner to my app. It should show a yellow warning
   bar at the top of the page that counts down from [X] hours (the length of your lab session).
   When it reaches zero, show a red message saying the session has expired and the app
   is no longer available."*

3. Kiro adds a small JavaScript timer and a banner element to your HTML
4. Update the countdown duration to match your lab's session length
   (check your lab panel — it usually shows the remaining time)

   > 📸 **Screenshot:** A yellow banner at the top of the app shows:
   > "⏱ Lab session expires in: 1:45:32" — counting down in real time.

> 💡 This is especially useful if you are demonstrating the app to others during a lab session.
> They can see at a glance how much time is left.

---

## Step 11 — Teardown: Delete Your Stack When Done

When your lab session ends, AWS deletes everything automatically. But if you want to
clean up manually (or if you are using a personal account for sandbox testing), here is
how to delete all the resources your app created.

> ⚠️ Deleting the stack is permanent. All your Lambda functions, API Gateway, and
> app-specific S3 buckets will be deleted. Your GitHub repository and code are not affected.

### Delete via CloudFormation

1. In the AWS Console, search for **CloudFormation** and click it
2. Find your stack in the list (e.g. `my-partyrock-app`)
3. Check the box next to it → click **Delete**
4. A confirmation dialog appears — click **Delete stack**

   > 📸 **Screenshot:** The CloudFormation stacks list shows your stack selected.
   > The "Delete" button appears in the top right. After clicking, a dialog asks
   > you to confirm. Click "Delete stack".

5. The stack status changes to `DELETE_IN_PROGRESS` — wait a few minutes
6. When complete, the stack disappears from the list

### What is NOT deleted automatically

These resources must be deleted manually if you want a complete cleanup:

- The **SAM artifacts S3 bucket** you created in Step 4 (S3 → select bucket → Delete)
- The **CloudWatch log groups** for your Lambda functions (CloudWatch → Log groups → select and delete)

> 💡 For a lab environment, you usually do not need to clean up manually — the lab platform
> handles it when the session ends. Manual teardown is mainly useful when testing on a
> personal account.

---

## ✅ Checkpoint

- [ ] Sandbox credentials (Key ID + Secret + Session Token) copied
- [ ] Lab region noted
- [ ] LabRole ARN copied
- [ ] Bedrock model availability checked
- [ ] S3 bucket created for SAM artifacts
- [ ] `template-sandbox.yaml` generated by Kiro with LabRole
- [ ] `YOUR_ACCOUNT_ID` replaced with actual account ID in template-sandbox.yaml
- [ ] Deploy workflow updated to use `template-sandbox.yaml` and `vars.AWS_REGION`
- [ ] `CAPABILITY_NAMED_IAM` removed from deploy command
- [ ] 4 GitHub secrets added (including `AWS_SESSION_TOKEN`)
- [ ] `AWS_REGION` added as a GitHub variable
- [ ] `aws-session-token` line added to deploy.yml
- [ ] Deployment ran successfully
- [ ] Live app URL found and tested
- [ ] (Optional) Session expiry countdown added to frontend
- [ ] (Optional) Stack deleted when done
