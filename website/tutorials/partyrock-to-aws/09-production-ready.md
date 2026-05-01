# Step 9 — HTTPS, Custom Domain, and Advanced Features

This section covers important upgrades for your app. Some are **recommended for everyone**
(like HTTPS), and some are **optional extras** depending on what your app does.

---

## 9.1 Add HTTPS with CloudFront (Recommended for Everyone)

**Why this matters:** Right now your app URL starts with `http://` — no padlock.
Most modern browsers show a "Not Secure" warning for http sites, and some features
(like the copy-to-clipboard button) only work on secure https connections.

**CloudFront** is AWS's global content delivery network. Adding it in front of your S3
website gives you HTTPS for free, faster load times, and an extra security layer.

> 💡 This used to be in the "bonus" section, but we recommend everyone do this.
> It takes about 15 minutes and costs nothing for a personal app.

### Step 1 — Open CloudFront

1. In the AWS Console search bar, type **CloudFront** and click it
2. Click **Create a CloudFront distribution**

   > 📸 **Screenshot:** The CloudFront console shows a "Create distribution" button. Click it.

### Step 2 — Configure the origin

3. For **Origin domain**, click the input box — a dropdown shows your S3 buckets
4. Select your frontend S3 bucket

   > ⚠️ Select the option that ends in `.s3-website-us-east-1.amazonaws.com`
   > (the website endpoint), not the one ending in `.s3.amazonaws.com`.
   > If you do not see it in the dropdown, type your bucket's website URL manually.

5. For **Origin access**, select **Public**

### Step 3 — Configure viewer settings

6. For **Viewer protocol policy**, select **Redirect HTTP to HTTPS**
7. For **Allowed HTTP methods**, select **GET, HEAD**

### Step 4 — Create the distribution

8. Leave all other settings as default
9. Scroll to the bottom and click **Create distribution**
10. Wait 5–10 minutes for the distribution to deploy

    > 📸 **Screenshot:** The distribution appears in the list with status "Deploying".
    > Wait until it changes to "Enabled" (green).

### Step 5 — Find your new HTTPS URL

11. Click on your distribution in the list
12. Copy the **Distribution domain name** — it looks like `d1234abcd.cloudfront.net`
13. Open this URL in your browser — your app now loads over HTTPS with a padlock

    > 📸 **Screenshot:** The browser address bar shows `https://d1234abcd.cloudfront.net`
    > with a padlock icon.

> 💡 Share this CloudFront URL instead of the S3 URL going forward.

### Step 6 — Lock down your S3 bucket to CloudFront only

Now that CloudFront is in front of your S3 bucket, you can restrict the bucket so
it only accepts requests from CloudFront — not directly from the internet.

14. In the AWS Console, go to **S3** → your frontend bucket → **Permissions** tab
15. Click **Edit** under **Bucket policy**
16. Replace the existing policy with this
    (replace `YOUR-BUCKET-NAME`, `YOUR-ACCOUNT-ID`, and `YOUR-DISTRIBUTION-ID`):

    ```json
    {
      "Version": "2012-10-17",
      "Statement": [
        {
          "Sid": "AllowCloudFrontOnly",
          "Effect": "Allow",
          "Principal": {
            "Service": "cloudfront.amazonaws.com"
          },
          "Action": "s3:GetObject",
          "Resource": "arn:aws:s3:::YOUR-BUCKET-NAME/*",
          "Condition": {
            "StringEquals": {
              "AWS:SourceArn": "arn:aws:cloudfront::YOUR-ACCOUNT-ID:distribution/YOUR-DISTRIBUTION-ID"
            }
          }
        }
      ]
    }
    ```

    > 💡 Find your account ID: click your account name in the top right of the AWS Console.
    > Find your distribution ID: it is the first column in the CloudFront distributions list.

17. Click **Save changes**

    > 📸 **Screenshot:** After saving, direct S3 URLs return "Access Denied".
    > Only CloudFront can serve the files — this is correct.

---

## 9.2 Add a Custom Domain (Optional)

Want your app at `www.myapp.com` instead of a long CloudFront URL?

> 💡 You need to own a domain name first. You can buy one from Route 53 (~$12/year),
> Namecheap (~$10/year), or any domain registrar.

### Step 1 — Request an SSL Certificate

1. In the AWS Console, search for **Certificate Manager** and click it
2. Make sure you are in the **us-east-1** region (required for CloudFront certificates)
3. Click **Request a certificate** → **Request a public certificate** → **Next**
4. Enter your domain name (e.g., `www.myapp.com`)
   - To cover both `www.myapp.com` and `myapp.com`, click **Add another name** and add `myapp.com`
5. Select **DNS validation** → **Request**

   > 📸 **Screenshot:** The certificate request form shows a domain name field and
   > "DNS validation" selected.

6. Click on the certificate you just created
7. Click **Create records in Route 53** — this automatically proves you own the domain
8. Wait a few minutes for the status to change to **Issued**

### Step 2 — Attach the domain to CloudFront

9. Go to **CloudFront** → click your distribution → click **Edit**
10. Under **Alternate domain name (CNAME)**, click **Add item** and type your domain
11. Under **Custom SSL certificate**, select the certificate you just created
12. Click **Save changes** and wait 5–10 minutes

### Step 3 — Point your domain to CloudFront

13. In the AWS Console, search for **Route 53** and click it
14. Click **Hosted zones** → click your domain name
15. Click **Create record**
16. **Record name:** `www`
17. **Record type:** A
18. Enable the **Alias** toggle
19. **Route traffic to:** Alias to CloudFront distribution → select your distribution
20. Click **Create records**

    > 📸 **Screenshot:** The new record appears in the hosted zone with type "A"
    > and an alias pointing to your CloudFront distribution.

Your app will be live at `https://www.myapp.com` within 10–30 minutes.

---

## 9.3 Add AWS WAF for Basic Rate Limiting (Optional)

**What is WAF?** Web Application Firewall. It sits in front of your API and blocks
suspicious traffic before it reaches your Lambda functions.

The most useful basic rule is **rate limiting** — if someone sends more than 100 requests
per minute from the same IP address, WAF blocks them automatically.

> 💡 WAF has a small cost (~$5/month for basic rules). For a personal app, this is optional.
> But if you share your app publicly, it is worth considering.

### Step 1 — Open AWS WAF

1. In the AWS Console search bar, type **WAF** and click **AWS WAF & Shield**
2. Click **Create web ACL**

### Step 2 — Configure the web ACL

3. **Resource type:** Regional resources (API Gateway, etc.)
4. **Region:** US East (N. Virginia)
5. **Name:** `my-app-waf`
6. Click **Next**

### Step 3 — Add a rate limiting rule

7. Click **Add rules** → **Add managed rule groups**
8. Expand **Free rule groups** → enable **AWSManagedRulesCommonRuleSet**
9. Click **Add rules** → **Next**
10. Click **Add rules** → **Add my own rules and rule groups** → **Rule builder**
11. **Name:** `rate-limit-rule`
12. **Type:** Rate-based rule
13. **Rate limit:** `100` (requests per 5 minutes per IP)
14. **Action:** Block
15. Click **Add rule** → **Next** through remaining screens → **Create web ACL**

### Step 4 — Attach WAF to your API Gateway

16. In the AWS Console, go to **API Gateway** → your API → **Stages** → `prod`
17. Click the **Web Application Firewall (WAF)** tab
18. Select your `my-app-waf` web ACL from the dropdown
19. Click **Save Changes**

    > 📸 **Screenshot:** The WAF tab on the API stage shows a dropdown. Select your
    > web ACL and click Save Changes.

---

## 9.4 Add Chat Session Persistence with DynamoDB (For Chatbot Apps)

**Who needs this:** If your app has a chatbot widget — where users have a back-and-forth
conversation with the AI — this section is for you. Without session persistence, the AI
forgets the entire conversation every time the user sends a new message.

> 💡 If your app does not have a chatbot widget, skip this section.

### Step 1 — Create a DynamoDB table

1. In the AWS Console search bar, type **DynamoDB** and click it
2. Click **Create table**
3. Fill in:
   - **Table name:** `my-app-chat-sessions`
   - **Partition key:** `session_id` (type: String)
4. Leave all other settings as default → **Create table**
5. Wait about 30 seconds for the table status to show **Active**

   > 📸 **Screenshot:** The DynamoDB create table form shows fields for table name
   > and partition key. Fill them in and click Create table.

### Step 2 — Ask Kiro to update your Lambda function

6. Open Kiro and share your chatbot Lambda function
7. Ask: *"Update this Lambda function to store and retrieve conversation history
   from a DynamoDB table called my-app-chat-sessions. Each session should be identified
   by a session_id that the frontend generates and sends with each request.
   Add a TTL of 24 hours so old sessions are automatically deleted."*

8. Kiro updates the Lambda to:
   - Accept a `session_id` in the request
   - Look up previous messages from DynamoDB
   - Include them in the prompt to Claude
   - Save the updated conversation back to DynamoDB

### Step 3 — Give Lambda permission to use DynamoDB

9. In the AWS Console, go to **IAM** → **Roles**
10. Find your Lambda execution role (search for your app name)
11. Click **Add permissions** → **Attach policies**
12. Search for `AmazonDynamoDBFullAccess` and check it → **Add permissions**

    > 💡 For tighter security, ask Kiro to generate a custom policy that only allows
    > access to your specific table instead of all DynamoDB tables.

### Step 4 — Update the frontend to send a session ID

13. Open Kiro and share your `index.html`
14. Ask: *"Update my frontend to generate a unique session_id when the page loads
    and include it in every API request to the chat endpoint. Also add a Clear Chat
    button that resets the session."*

15. Redeploy by pushing your changes to GitHub

---

## 9.5 Large File Uploads via Presigned S3 URL (For Apps with File Upload)

**Who needs this:** If your app lets users upload files larger than ~15MB.

**How file uploads work by default:** The App Planner generates code that reads the file in the browser using the FileReader API, converts it to base64, and sends it directly in the POST request body to your Lambda Function URL. This works for the vast majority of files:

| File type | Typical size | Works directly? |
|---|---|---|
| Photos (JPG/PNG) | 1–5MB | ✅ yes |
| PDFs | 1–10MB | ✅ yes |
| DOCX / PPTX | 1–8MB | ✅ yes |
| CSV / JSON | usually small | ✅ yes |
| Large video / raw files | 15MB+ | ❌ use presigned URL |

Lambda Function URLs support up to 20MB payloads. Base64 encoding adds ~33% overhead, so files up to ~15MB original size work without any changes.

**When you need presigned URLs:** Only if users might upload files larger than ~15MB. In that case, instead of sending the file through Lambda, the frontend gets a temporary upload link and uploads directly to S3.

> 💡 If your app only handles documents, images, and typical office files, skip this section.

### How presigned URLs work

1. User selects a file in the browser
2. Frontend asks your Lambda for a "presigned URL" — a temporary upload link
3. Lambda generates the URL and returns it (tiny, fast request)
4. Frontend uploads the file directly to S3 using that URL (bypasses Lambda entirely)
5. Lambda then processes the file from S3

### Ask Kiro to add this

1. Open Kiro and share your `infra/template.yaml` and your file-handling Lambda function
2. Ask: *"Add a presigned URL upload endpoint to my app. I need a Lambda function that
   generates a presigned S3 PUT URL, and update the frontend to use it for file uploads
   larger than 15MB. Store uploaded files in a new S3 bucket called my-app-uploads."*

3. Kiro will:
   - Add a new Lambda function that generates presigned URLs
   - Add a new API Gateway route (e.g. `/get-upload-url`)
   - Create a new S3 bucket for uploaded files
   - Update the frontend to use the presigned URL for large file uploads

4. Give Lambda permission to generate presigned URLs:
   - In IAM → your Lambda role → Add permissions
   - Add a policy that allows `s3:PutObject` on your uploads bucket

---

## 9.6 Staging Environment Setup

**What is a staging environment?** A second copy of your app for testing changes before
they go live. You test on staging first, then deploy to production when confident.

> 💡 If you already set this up in Step 7.7, you are done. This section is for anyone
> who skipped it and wants to add it now.

### How to create a staging environment

1. In your GitHub repository, navigate to `.github/workflows/`
2. Click **Add file** → **Create new file**
3. Name it: `deploy-staging.yml`
4. Copy the contents of `deploy.yml` into it
5. Change the stack name from `my-partyrock-app` to `my-partyrock-app-staging`
6. Change the frontend S3 bucket name to `my-app-frontend-staging`
7. Commit the file

### How to use staging

- To deploy to staging: Actions → select `deploy-staging.yml` → Run workflow
- To deploy to production: Actions → select `deploy.yml` → Run workflow
- The staging URL will be different from the production URL — check the Print URLs step

> 💡 A common workflow: make a change → deploy to staging → test it → deploy to production.
> This way your users never see broken changes.

---

## ✅ Checkpoint

Pick the sections that apply to your app:

**Recommended for everyone:**
- [ ] CloudFront distribution created and HTTPS URL working
- [ ] S3 bucket policy updated to allow CloudFront only

**Optional extras:**
- [ ] Custom domain connected via Route 53 and ACM
- [ ] AWS WAF web ACL created and attached to API Gateway
- [ ] DynamoDB chat sessions table created (chatbot apps only)
- [ ] Presigned URL upload endpoint added (large file upload apps only)
- [ ] Staging environment workflow created
