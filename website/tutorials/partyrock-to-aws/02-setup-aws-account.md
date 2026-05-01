# Step 2 — Set Up Your AWS Account

AWS (Amazon Web Services) is the cloud platform that will host your app.
You need an account to use it.

> ⚠️ **Cost note:** Everything in this tutorial stays within the AWS Free Tier.
> You will need a credit card to sign up, but charges will be near $0 for a personal app
> with low traffic. AWS charges only for what you use.

---

## 2.1 Create an AWS Account

1. Go to [aws.amazon.com](https://aws.amazon.com)
2. Click the orange **"Create an AWS Account"** button in the top right

   > 📸 **Screenshot:** Orange button top-right of the homepage that says "Create an AWS Account"

3. Enter your **email address** and choose an **AWS account name** (e.g. "my-ai-apps")
4. Click **Verify email address**, enter the code from your email, click **Verify**
5. Create a **root user password** — make it strong and save it somewhere safe
6. Click **Continue**

---

## 2.2 Choose Account Type

1. Select **Personal** account type
2. Fill in your name, phone number, and address
3. Click **Continue**

---

## 2.3 Add Payment Information

1. Enter your credit card details
2. AWS will charge $1 temporarily to verify the card — this is refunded
3. Click **Verify and Continue**

---

## 2.4 Verify Your Phone Number

1. Choose **Text message (SMS)**, enter your phone number, click **Send SMS**
2. Enter the 4-digit code you receive, click **Continue**

---

## 2.5 Choose a Support Plan

1. Select **Basic support — Free**
2. Click **Complete sign up**

---

## 2.6 Sign In to the AWS Console

1. Go to [console.aws.amazon.com](https://console.aws.amazon.com)
2. Click **Root user**, enter your email and password
3. You are now in the **AWS Management Console**

   > 📸 **Screenshot:** The AWS Console homepage shows a search bar at the top and a grid of service icons.

---

## 2.7 Create an IAM User for Deployments

We do not use the root account for deployments — it is too powerful.
Instead we create a limited user just for deploying your app.

1. In the search bar, type **IAM** and click the first result
2. In the left sidebar, click **Users** → **Create user**
3. For **User name**, type: `github-deployer`
4. Click **Next**

---

## 2.8 Attach Permissions to the IAM User

1. Select **Attach policies directly**
2. Search for and check each of these 6 policies:

   - `AWSCloudFormationFullAccess`
   - `AWSLambda_FullAccess`
   - `IAMFullAccess`
   - `AmazonAPIGatewayAdministrator`
   - `AmazonS3FullAccess`
   - `AmazonBedrockFullAccess`

   > 📸 **Screenshot:** Each policy appears as a row with a checkbox. Check all 6.

3. Click **Next** → **Create user**

---

## 2.9 Create an Access Key for the IAM User

1. Click on the **github-deployer** user → **Security credentials** tab
2. Scroll to **Access keys** → **Create access key**
3. Select **Application running outside AWS** → **Next** → **Create access key**
4. Copy and save both values:
   - **Access key ID** — looks like `AKIAIOSFODNN7EXAMPLE`
   - **Secret access key** — long random string

   > ⚠️ The secret access key is only shown once. Save it now before closing this page.

5. Click **Done**

---

## 2.10 Create an S3 Bucket for Deployment Artifacts

SAM (the deployment tool) needs a place to store files during deployment.

1. Search for **S3** → **Create bucket**
2. **Bucket name:** `yourname-sam-deploy-2024` (must be globally unique)
3. **Region:** US East (N. Virginia) us-east-1
4. Leave all other settings as default → **Create bucket**
5. Save the bucket name — you will need it later

---

## 2.11 Set API Gateway Throttling Limits

**What this does in plain English:** Throttling is like a bouncer at a door — it limits how many
requests can hit your app per second. Without it, a sudden spike in traffic (or someone
accidentally hammering your app in a loop) could run up your AWS bill.

We will set this up in Step 7.5 after deployment. For now, note these target values:

- **Rate limit:** 10 requests per second
- **Burst limit:** 20 requests (short spikes allowed up to this number)

> 💡 For a personal app with a handful of users, 10 requests/second is generous.
> A single person generates maybe 1–2 requests per second at most.

---

## 2.12 Plan for Lambda Concurrency Limits

**What this does in plain English:** Lambda runs your backend code. "Concurrency" means how many
copies can run at the same time. By default AWS allows up to 1,000 — which sounds great,
but if something goes wrong you could accidentally use all 1,000 and get a large bill.

Setting a **reserved concurrency** of 5 per function caps your costs safely.
For a personal app, 5 simultaneous users is more than enough.

We will set this up in Step 7.5 after deployment.

> 💡 Think of it like setting a maximum on a water tap. You can always turn it up later
> if your app grows, but starting low keeps things safe and affordable.

---

## 2.13 Add a Lifecycle Rule to Your SAM Artifacts Bucket

Every time you deploy, SAM uploads a copy of your code to the S3 bucket from step 2.10.
Over time these old copies pile up. A **lifecycle rule** automatically deletes files older than 30 days.

1. In the AWS Console, go to **S3** → click your SAM deploy bucket
2. Click the **Management** tab → **Create lifecycle rule**

   > 📸 **Screenshot:** The Management tab shows a "Lifecycle rules" section with a "Create lifecycle rule" button.

3. **Lifecycle rule name:** `expire-old-artifacts`
4. Under **Rule scope**, select **Apply to all objects in the bucket**
   (check the confirmation box that appears)
5. Under **Lifecycle rule actions**, check **Expire current versions of objects**
6. **Days after object creation:** `30`
7. Click **Create rule**

   > 📸 **Screenshot:** The new rule appears in the lifecycle rules list with status "Enabled".

> 💡 This is a "set it and forget it" step. AWS automatically cleans up old deployment files every 30 days.

---

## 2.14 Make Your AWS Region a GitHub Variable

Instead of hardcoding `us-east-1` in your deployment files, we store it as a **GitHub variable**.
This makes it easy to change later without hunting through code files.

**Secret vs Variable:**
- **Secret** — hidden value like a password (for credentials)
- **Variable** — visible configuration value (for settings like region names)

The region is not sensitive, so it goes in a variable. You will add it in Step 6.5.
For now, note the value:

| Variable Name | Value |
|---|---|
| `AWS_REGION` | `us-east-1` |

> 💡 If your lab or account uses a different region, use that value instead.
> Check the top-right corner of the AWS Console to see your current region.

---

## ✅ Checkpoint

Before moving on, you should have:
- [ ] AWS account created and verified
- [ ] Signed in to the AWS Console
- [ ] IAM user `github-deployer` created with all 6 policies attached
- [ ] Access Key ID saved somewhere safe
- [ ] Secret Access Key saved somewhere safe
- [ ] S3 bucket created, bucket name saved
- [ ] S3 lifecycle rule created on the SAM bucket (expire after 30 days)
- [ ] Noted throttling and concurrency values for Step 7
- [ ] Noted that `AWS_REGION` will be added as a GitHub variable in Step 6
