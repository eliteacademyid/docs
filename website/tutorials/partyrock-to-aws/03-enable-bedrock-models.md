# Step 3 — Enable Bedrock AI Models

Amazon Bedrock is the AWS service that runs the AI in your app.
By default, AI models are not enabled — you have to turn them on manually.
This takes about 5 minutes and is free to request.

---

## Before You Start This Step

- [ ] You have completed Step 2 (AWS account created, IAM user created)
- [ ] You are signed in to the AWS Console
- [ ] You know which region you are using (should be `us-east-1` unless your lab specifies otherwise)

---

## 3.1 Open Amazon Bedrock

1. In the AWS Console search bar, type **Bedrock** and click **Amazon Bedrock**

   > 📸 **Screenshot:** Search results show "Amazon Bedrock" with a small robot icon. Click it.

2. If you see a welcome screen, click **Get started**

---

## 3.2 Make Sure You Are in the Right Region

Your sandbox is restricted to specific regions. Choose one of the allowed regions below:
Available regions for your sandbox:

| Region | Location | Recommended |
|--------|----------|-------------|
| **ap-southeast-1** | Singapore | ✅ Recommended (closest) |
| ap-southeast-3 | Jakarta | Good alternative |
| us-east-1 | N. Virginia | Available |
| ap-southeast-5 | Malaysia | Available |

> ⚠️ Other regions are blocked by sandbox policy. If you select a different region, your API calls will fail.

> 📸 **Screenshot:** Top right of the console shows a region name like "N. Virginia" or "us-east-1".
> Click it and select **US East (N. Virginia)**.

---

## 3.3 Request Model Access

1. In the left sidebar, click **Model access**
2. Click **Manage model access** (top right of the table)
3. Find the **Anthropic** section, check the box next to **Claude Haiku 4.5** (fast, cost-efficient, and allowed in sandbox)
4. Optionally also enable **Claude 3 Haiku (older alternative)** (latest Haiku — better quality, still cost-efficient)
5. Scroll down and click **Save changes**

   > 📸 **Screenshot:** A list of model providers. Under "Anthropic" you see checkboxes
   > for different Claude models. Check "Claude Haiku 4.5" or "Claude 3 Haiku (older alternative)".

   > ⚠️ **Sandbox restriction:** Opus and Sonnet models are blocked in sandbox accounts
   > to control costs. Only **Haiku** models are available. If you try to enable Sonnet/Opus,
   > your app will receive an Access Denied error at runtime.

6. Refresh the page every few minutes until the status shows **Access granted** ✅

> 💡 The model your app uses is `anthropic.claude-haiku-4-5-20251001-v1:0` or
> `anthropic.claude-3-5-haiku-20241022-v1:0`. Kiro may generate a different model ID —
> make sure it matches a Haiku model.

---

## 3.4 Verify Access is Granted

1. Go back to **Model access** in the left sidebar
2. Find Claude Haiku 4.5 — the **Access status** column should show a green checkmark

   > 📸 **Screenshot:** The model row shows a green checkmark in the "Access status" column.

---

## 3.4b Try the Model in Bedrock Playground

Before building your app, test the model to make sure it works for your use case.

1. In the left sidebar, click **Playgrounds** → **Chat**
2. In the model selector (top), choose **Claude Haiku 4.5**
3. Type a prompt related to your app idea and click **Run**
4. Verify the response is useful for your project

   > 📸 **Screenshot:** The chat playground shows your prompt and the model's response.

> 💡 If the model asks you to **submit a use case** before access is granted, fill in
> the form with a brief description of your project (e.g., "Student hackathon project —
> building an AI chatbot for environmental awareness"). Approval is usually instant.

---

## 3.5 Tighten the IAM Role to Only Allow Your Specific Model

**Why this matters in plain English:** When Kiro generated your app, it gave your Lambda
functions permission to use *any* Bedrock model in your account. That is like giving someone
a key that opens every door in a building when they only need one room. It is safer to
restrict access to just the specific model your app actually uses.

### Find your Lambda execution role

1. In the AWS Console, search for **IAM** and click it
2. In the left sidebar, click **Roles**
3. In the search box, type the name of your app (e.g. `health-tracker` or `my-partyrock-app`)
4. Click on the role that appears — it will have a name like `my-app-LambdaBedrockRole-XXXX`

   > 📸 **Screenshot:** The roles list shows your role with a name containing your app name. Click it.

### Replace the broad Bedrock permission

5. Click the **Permissions** tab
6. Find the policy that mentions `AmazonBedrockFullAccess` or `BedrockInvokePolicy`
7. Click the small arrow to expand it, then click **Edit**

   > 📸 **Screenshot:** The permissions tab shows attached policies. Click the policy name
   > to expand it, then click the "Edit" button.

8. Find the line that says `"bedrock:*"` and the `Resource` line that says `"*"`.
   Replace the entire statement with this:

   ```json
   {
     "Effect": "Allow",
     "Action": ["bedrock:InvokeModel", "bedrock:InvokeModelWithResponseStream"],
     "Resource": [
       "arn:aws:bedrock:*::foundation-model/anthropic.claude-haiku-4-5-20251001-v1:0",
       "arn:aws:bedrock:*::foundation-model/anthropic.claude-3-5-haiku-20241022-v1:0"
     ]
   }
   ```

   > 💡 Two actions are needed: `InvokeModel` for image generation and
   > `InvokeModelWithResponseStream` for text/chatbot streaming. Both Haiku model
   > ARNs are included so your app works with either version.

   > ⚠️ If your app uses image generation, also add:
   > `"arn:aws:bedrock:*::foundation-model/amazon.nova-canvas-v1:0"`

9. Click **Next** → **Save changes**

### Verify the change

10. Go back to the role's **Permissions** tab, expand the policy again
11. It should now show `bedrock:InvokeModel` with a specific Resource ARN, not `*`

    > 📸 **Screenshot:** The expanded policy shows "bedrock:InvokeModel" and a specific
    > ARN ending in the model name, not a wildcard asterisk.

---

## 3.6 Set Up a CloudWatch Dashboard

**What is CloudWatch?** It is AWS's built-in monitoring tool — like a health dashboard
for your app. It shows graphs of how often your app is being used, how fast it responds,
and whether any errors are happening.

Setting up a dashboard now means you have a place to look when something seems off,
instead of hunting through logs.

### Step 1 — Open CloudWatch

1. In the AWS Console search bar, type **CloudWatch** and click it

   > 📸 **Screenshot:** Search results show "CloudWatch" with a graph icon. Click it.

### Step 2 — Create a new dashboard

2. In the left sidebar, click **Dashboards** → **Create dashboard**
3. For the dashboard name, type: `my-app-overview` (replace `my-app` with your app's name)
4. Click **Create dashboard**

### Step 3 — Add widgets to the dashboard

5. A dialog appears — select **Line** and click **Next**
6. Under **Metrics**, click **Lambda** → **By Function Name**
7. Find your Lambda function(s) in the list — check the box next to **Errors** for each one
8. Click **Create widget**

   > 📸 **Screenshot:** The metrics browser shows Lambda functions listed by name.
   > Check the "Errors" metric checkbox next to your function name.

9. Click **Add widget** again, repeat steps 5–8 but this time select **Invocations**
   (this shows how many times your app is being called)
10. Click **Save dashboard** (top right)

    > 📸 **Screenshot:** The dashboard now shows two graphs — one for errors and one for
    > invocations. Both should be flat (near zero) for a new app.

> 💡 Bookmark this dashboard URL. When your app is live and you want to check on it,
> this is the first place to look. A flat line on the Errors graph means everything is fine.

---

## ✅ Checkpoint

Before moving on, you should have:
- [ ] Opened Amazon Bedrock in the AWS Console
- [ ] Confirmed you are in an allowed region (ap-southeast-1, ap-southeast-3, us-east-1, or ap-southeast-5)
- [ ] Claude Haiku 4.5 or Claude 3 Haiku (older alternative) shows **Access granted** status
- [ ] Lambda IAM role updated to allow `bedrock:InvokeModel` and `bedrock:InvokeModelWithResponseStream` on the Haiku model ARNs
- [ ] CloudWatch dashboard created with Errors and Invocations widgets
