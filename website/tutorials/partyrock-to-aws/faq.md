# FAQ — Frequently Asked Questions

---

## Getting Started

**Q: Do I need to know how to code?**
No. This tutorial is designed for non-technical users. You use Kiro (an AI assistant)
to generate all the code, and GitHub's web interface to upload it. No coding required.

**Q: How long does this take?**
About 2–3 hours for a complete deployment. The quick path (Steps 0–8) takes about 2 hours.
Adding HTTPS, custom domain, and security hardening (Step 9) adds another 30–45 minutes.

**Q: How much does it cost?**
For a personal app with low traffic (under 100 requests/day), it costs ~$0 — covered by
the AWS free tier. At 1,000 requests/day, expect ~$1–3/month. The main cost is Bedrock
(Claude) at roughly $0.003 per AI request.

**Q: What if I already have an AWS account?**
Great — skip Step 2.1–2.6 and go straight to Step 2.7 (create the IAM user).

**Q: Can I use this with any PartyRock app?**
Yes. The tutorial is generic — it works for any PartyRock app with any combination
of widget types.

**Q: Can I use a different frontend framework (React, Vue) instead of plain HTML?**
Yes, but it requires more setup. The tutorial uses plain HTML/CSS/JS because it works
without any build tools and deploys directly to S3. If you want React or Vue, ask Kiro:
*"Convert my index.html to a React app and update the deploy workflow to run npm build
before uploading to S3."* This adds a build step to the GitHub Actions workflow.

---

## AWS Account

**Q: Why do I need a credit card if it's free?**
AWS requires a credit card to verify your identity and prevent abuse. You will not be
charged unless you exceed the free tier limits. Set a billing alarm in Step 8.7 to
get an email if any charges appear.

**Q: What is the AWS free tier?**
AWS gives new accounts 12 months of free usage on many services. After 12 months,
you pay for what you use — but at personal-app scale, costs remain very low (~$1–3/month).

**Q: I accidentally closed the page with my secret access key. What do I do?**
You cannot retrieve it again. Go to IAM → Users → github-deployer → Security credentials →
delete the old access key → create a new one. Update your GitHub secrets with the new values.

---

## Deployment

**Q: My deployment failed. What do I do?**
1. Click on the failed step in GitHub Actions to see the error message
2. Check the troubleshooting table in Step 7.4
3. Copy the error message and paste it into Kiro — it will help diagnose the problem

**Q: The deployment says "No changes to deploy." Is that an error?**
No — it means your app is already up to date. This is fine.

**Q: How long does deployment take?**
About 3–5 minutes for the first deployment. Updates are usually faster (2–3 minutes).

**Q: Can I deploy from my phone?**
Yes — GitHub's mobile website works for triggering deployments. You cannot upload files
from a phone easily, but you can trigger a re-run of an existing workflow.

**Q: My app deployed but the URL shows an error. What do I do?**
1. Check that the API key in GitHub secrets matches the one you set in API Gateway (Step 7.5)
2. Check CloudWatch logs (Step 8.6) for error messages
3. Make sure Bedrock model access is granted (Step 3.3)

---

## The App

**Q: The first request is very slow (10–15 seconds). Is something wrong?**
No — this is called a "cold start." Lambda sleeps when not in use and takes a moment
to wake up. Subsequent requests are much faster (1–3 seconds).

**Q: My AI output shows symbols like `**bold**` instead of formatted text.**
You need to add markdown rendering. Follow Step 5.8 to add marked.js to your frontend.

**Q: The copy button doesn't work.**
Copy buttons require HTTPS. Add CloudFront (Step 9.1) to get HTTPS, then test again.

**Q: My app works on desktop but not on mobile.**
The keyboard may be covering your input fields. Ask Kiro to add a CSS fix:
*"Add a CSS fix to my index.html so the keyboard does not cover input fields on mobile"*

**Q: Bedrock returned an empty response (no error, just blank).**
Claude sometimes refuses to answer certain types of content due to its safety filters.
This is not an error — it is the model declining to respond. Try rephrasing your prompt
to be more specific and less ambiguous.

**Q: How do I change what the AI says?**
Edit the prompt in your Lambda function file. See Step 10.1 for how to update and redeploy.

---

## Security

**Q: Is my app secure?**
After completing Steps 7.5 (API key) and 9.1 (CloudFront), your app has:
- API key authentication (blocks unauthorized callers)
- HTTPS (encrypted connection)
- Input length limits (prevents abuse)
- Scoped IAM permissions (Lambda can only call your specific model)

For a personal app, this is solid. For a public app with many users, also add WAF (Step 9.3).

**Q: Can someone find my API key and abuse my app?**
The API key is embedded in your frontend JavaScript, which means a determined person
could find it by inspecting the page source. For a personal app shared with trusted people,
this is acceptable. For a fully public app, add WAF rate limiting (Step 9.3) to limit
how many requests any single IP can make.

**Q: Someone is spamming my app and running up my bill. What do I do?**
1. Immediately go to API Gateway → Usage Plans → your plan → disable the API key
2. This stops all requests immediately
3. Add WAF rate limiting (Step 9.3) before re-enabling
4. Check your billing alarm — if charges are high, contact AWS Support

---

## Sandbox / Lab Environments

**Q: My lab session expired and the app stopped working. What do I do?**
Start a new lab session, copy the new credentials (Key ID + Secret + Session Token),
update all 3 credential secrets in GitHub, and redeploy.

**Q: Can I make the app permanent from a sandbox?**
No — sandbox credentials expire and the AWS account is temporary. To make the app
permanent, you need a personal AWS account (Path A).

**Q: My lab doesn't have Claude. What model can I use?**
Check Bedrock → Model access for models showing "Access granted." Amazon Titan Text Express
(`amazon.titan-text-express-v1`) is usually pre-enabled. Update the model ID in your
Lambda functions to match.

**Q: What if my PartyRock app uses a model that's not available in my region?**
Some Bedrock models are only available in certain AWS regions. If you get a "model not found"
error, check the [Bedrock model availability page](https://docs.aws.amazon.com/bedrock/latest/userguide/models-regions.html)
to see which regions support your model. You may need to change your `AWS_REGION` variable
to a region where the model is available (e.g. `us-west-2` instead of `us-east-1`).

---

## Costs and Billing

**Q: I got an unexpected AWS bill. What happened?**
The most common causes:
1. You left a resource running that is not in the free tier (e.g. an EC2 instance, NAT Gateway)
2. Your app received much more traffic than expected
3. You have multiple stacks deployed (staging + production)

Go to AWS Console → Billing → Cost Explorer to see which service is charging you.

**Q: How do I stop all charges immediately?**
Delete your CloudFormation stack (Step 11.1). This removes Lambda, API Gateway, and S3.
Also disable your API key in API Gateway to stop any in-flight requests.

**Q: What is the free tier limit for Bedrock?**
Bedrock does not have a free tier — every API call costs money. However, at personal-app
scale (a few hundred requests/month), the cost is typically under $1.

---

## Still Stuck?

1. Copy the exact error message and paste it into Kiro — describe what step you were on
2. Check the CloudWatch logs for your Lambda function (Step 8.6)
3. Re-read the troubleshooting table in Step 7.4
4. Check the AWS Service Health Dashboard at [status.aws.amazon.com](https://status.aws.amazon.com)
   to see if there is an AWS outage
