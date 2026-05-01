# Step 6 — Add Your AWS Credentials to GitHub

GitHub needs permission to deploy to your AWS account.
We do this by adding "secrets" and "variables" — private and configuration values that
GitHub keeps safe and uses automatically during deployment.

> 🔒 Secrets are encrypted and never visible to anyone after you save them.
> They are only used by GitHub Actions during deployment.

---

## Before You Start This Step

- [ ] You have completed Step 5 (all files uploaded to GitHub)
- [ ] You have your **Access Key ID** saved (from Step 2.9)
- [ ] You have your **Secret Access Key** saved (from Step 2.9) — if you lost it, go to IAM → Users → github-deployer → Security credentials → delete the old key → create a new one
- [ ] You have your **S3 bucket name** saved (from Step 2.10)
- [ ] You have decided on an **API key value** — a strong random string you make up (e.g. `MyApp-2024-xK9mP3qR7vL2`). Write it down — you will need it again in Step 7.5.

---

## Which path are you on?

| | Path A — Personal Account | Path B — Lab Sandbox |
|---|---|---|
| Number of secrets | 4 | 5 (session token required) |
| Key ID starts with | `AKIA` | `ASIA` |
| Session token needed | No | Yes |

---

## 6.1 Open Repository Settings

1. In your GitHub repository, click the **Settings** tab (far right in the top tab bar)
2. In the left sidebar, click **Secrets and variables**
3. Click **Actions** in the submenu

   > 📸 **Screenshot:** Left sidebar shows "Secrets and variables" expanding to show
   > "Actions", "Codespaces", "Dependabot". Click "Actions".

---

## 6.2 Add Secrets — Path A (Personal AWS Account)

Click **New repository secret** for each of these 3 secrets:

| Secret Name (type exactly) | Value |
|---|---|
| `AWS_ACCESS_KEY_ID` | Your Access Key ID from Step 2 (starts with `AKIA`) |
| `AWS_SECRET_ACCESS_KEY` | Your Secret Access Key from Step 2 |
| `SAM_DEPLOY_BUCKET` | Your S3 bucket name from Step 2 |

> 📸 **Screenshot:** After adding all 3, the "Repository secrets" section shows
> 3 rows with "Updated X minutes ago". Values are hidden — this is correct.

---

## 6.3 Add Secrets — Path B (Lab Sandbox)

Click **New repository secret** for each of these 4 secrets:

| Secret Name (type exactly) | Value |
|---|---|
| `AWS_ACCESS_KEY_ID` | Access Key ID from your lab panel (starts with `ASIA`) |
| `AWS_SECRET_ACCESS_KEY` | Secret Access Key from your lab panel |
| `AWS_SESSION_TOKEN` | Session Token from your lab panel — copy the full long string |
| `SAM_DEPLOY_BUCKET` | S3 bucket name you created in the sandbox guide |

> ⚠️ Session tokens are very long. Make sure you copy the entire value.
> Missing even one character will cause authentication to fail.

> ⚠️ Session tokens expire when your lab session ends. To redeploy after expiry,
> start a new lab session and update all 3 credential secrets with fresh values.

---

## 6.4 Verify Your Secrets Are Added

After adding all secrets, the page shows them listed with "Updated X minutes ago".
The actual values are always hidden — this is correct and expected.

---

## 6.5 Add AWS_REGION as a GitHub Variable

**What is the difference between a secret and a variable?**
- A **secret** is for sensitive values like passwords and access keys — hidden after saving
- A **variable** is for non-sensitive configuration values like region names — visible and easy to update

Your AWS region (`us-east-1`) is not sensitive, so it goes in a variable.
This makes it easy to change later without touching your code files.

1. You should still be on the **Settings → Secrets and variables → Actions** page
2. Click the **Variables** tab (next to the "Secrets" tab at the top of the page)

   > 📸 **Screenshot:** Near the top of the page, two tabs appear: "Secrets" and "Variables".
   > Click "Variables".

3. Click **New repository variable**
4. **Name:** `AWS_REGION`
5. **Value:** `us-east-1` (or your lab's region if you are on Path B)
6. Click **Add variable**

   > 📸 **Screenshot:** After saving, the Variables tab shows one row:
   > "AWS_REGION" with value "us-east-1" and "Updated X minutes ago".

> 💡 Unlike secrets, variable values are visible on this page. That is fine — region names
> are not sensitive information.

---

## 6.6 Add Your API Gateway Key as a GitHub Secret

Your app will be protected by an **API key** — a secret code that the frontend sends
with every request to prove it is allowed to use your backend. Without this, anyone who
finds your API URL could call it directly and run up your AWS bill.

You will create the actual API key in the AWS Console after deployment (Step 7.5),
but we add the placeholder secret now so the deployment workflow can use it.

1. Click back to the **Secrets** tab
2. Click **New repository secret**
3. **Name:** `API_KEY`
4. **Value:** type a strong random string — at least 20 characters.
   Example: `MyApp-2024-xK9mP3qR7vL2nS8wQ4`

   > ⚠️ Write this value down somewhere safe. You will need to enter this exact same value
   > in the AWS Console in Step 7.5. If they do not match, your app will return errors.

5. Click **Add secret**

   > 📸 **Screenshot:** The Secrets tab now shows "API_KEY" in the list alongside
   > your other secrets, all showing "Updated X minutes ago".

> 💡 Think of the API key like a secret handshake. Your frontend knows the handshake
> (it has the key), so API Gateway lets it through. Anyone else who tries to call your
> API without the key gets blocked.

---

## ✅ Checkpoint

**Path A:**
- [ ] `AWS_ACCESS_KEY_ID` added as a secret
- [ ] `AWS_SECRET_ACCESS_KEY` added as a secret
- [ ] `SAM_DEPLOY_BUCKET` added as a secret
- [ ] `AWS_REGION` added as a variable (value: `us-east-1`)
- [ ] `API_KEY` added as a secret (your chosen key value, written down)

**Path B (sandbox):**
- [ ] `AWS_ACCESS_KEY_ID` added as a secret
- [ ] `AWS_SECRET_ACCESS_KEY` added as a secret
- [ ] `AWS_SESSION_TOKEN` added as a secret (full string)
- [ ] `SAM_DEPLOY_BUCKET` added as a secret
- [ ] `AWS_REGION` added as a variable (your lab's region)
- [ ] `API_KEY` added as a secret (your chosen key value, written down)
