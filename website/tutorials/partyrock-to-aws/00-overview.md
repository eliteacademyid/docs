# From PartyRock to Real App — Complete Tutorial

## What You Will Learn

You built a cool AI app on AWS PartyRock. Now you want to share it as a real website
that anyone can visit — not just people with a PartyRock account.

This tutorial walks you through every single step, with no coding experience required.
By the end, your app will be live on the internet, powered by real AWS services,
secured with an API key, monitored with alarms, and served over HTTPS.

---

## What Is Actually Happening

PartyRock is like a sandbox. It lets you build AI apps quickly, but:
- Users need a PartyRock account to use your app
- You cannot customize the look and feel
- You cannot connect it to your own domain
- AWS owns the infrastructure, not you

When we "slice" your PartyRock app, we are rebuilding the same AI logic using
real AWS services that you own and control.

```
BEFORE (PartyRock)                    AFTER (Your Real App)
─────────────────────                 ──────────────────────
PartyRock platform          →         Your own website (S3 + CloudFront)
PartyRock widgets           →         Web page with forms
PartyRock AI calls          →         AWS Lambda functions (Flask, streaming)
Amazon Bedrock (hidden)     →         Amazon Bedrock (yours, scoped)
No custom domain            →         Your own URL + HTTPS
No cost controls            →         API key + throttling + alarms
```

---

## Architecture Overview

```
User's Browser
      │  HTTPS
      ▼
CloudFront (CDN + HTTPS)
      │
      ▼
S3 Bucket (static website — index.html)
      │  streaming fetch() calls
      ▼
Lambda Function URLs (streaming, one per AI widget)
      │
      ├── text-generation widgets  ──►  Flask Lambda  ──►  Bedrock (Claude 3 Haiku (older alternative))
      ├── chatbot widgets          ──►  Flask Lambda  ──►  Bedrock (Claude 3 Haiku (older alternative))
      └── image-generation widgets ──►  Lambda        ──►  Bedrock (Nova Canvas)

Supporting services:
  DynamoDB  ──►  Chat session history (chatbot apps)
  CloudWatch ──►  Alarms + logs + dashboard
  IAM Role  ──►  Scoped to specific model ARNs only
```

> 💡 Text and chatbot Lambdas use **Lambda Function URLs** with streaming — responses
> appear token by token as Claude generates them. Image generation uses a standard
> Lambda handler. API Gateway is only used for image widgets.

---

## Choose Your Path

### Path A — Personal AWS Account
Create your own AWS account. Full control, permanent deployment, near-zero cost on free tier.
Requires a credit card for sign-up.

### Path B — AWS Lab / Sandbox Environment
Using AWS Skill Builder, Vocareum, AWS Academy, or an event/workshop sandbox?
No account creation needed — credentials are provided for you.
App works during the lab session only.
See `02b-sandbox-environment.md` for the sandbox-specific steps.

---

## The 6 AWS Services You Will Use

| Service | What it does | Plain English |
|---|---|---|
| Amazon Bedrock | Runs the AI (Claude 3 Haiku (older alternative)) | The brain |
| AWS Lambda | Runs your code (Flask, streaming) | The worker |
| Lambda Function URLs | Connects website to Lambda | The door |
| Amazon S3 | Hosts your website files | The shelf |
| CloudFront | Delivers your site over HTTPS | The secure courier |
| GitHub Actions | Deploys everything automatically | The robot helper |

---

## Realistic Time Estimates

| # | Section | Realistic Time | Notes |
|---|---|---|---|
| 0 | App Planner — generate your Kiro prompt | 15 min | Do this first |
| 1 | Read your PartyRock app widgets | 10 min | |
| 2 | Set up your AWS account | 20 min | Path A only |
| 2b | Sandbox environment guide | 20 min | Path B only |
| 3 | Enable Bedrock, tighten IAM, CloudWatch dashboard | 25 min | |
| 4 | Create a GitHub account and repository | 10 min | |
| 5 | Upload code + security + UX improvements | 25 min | |
| 6 | Add credentials + API key to GitHub | 15 min | Path B: 5 secrets |
| 7 | Deploy + API key + alarms + staging | 25 min | |
| 8 | Visit, test, and monitor your live app | 20 min | |
| 9 | HTTPS, custom domain, WAF, advanced features | 20–45 min | CloudFront recommended |
| 10 | Update your app after it's live | 5 min | |
| 11 | Delete your app (cleanup) | 5 min | Optional |

Total estimated time: **~3 hours** for a complete, production-ready deployment
Quick path (Steps 0–8 only, skip security hardening): **~2 hours**

---

## Realistic Cost Estimate

| Usage level | Monthly cost |
|---|---|
| Personal app, <100 requests/day | ~$0 (free tier covers it) |
| Small audience, ~1,000 requests/day | ~$1–3/month |
| Medium audience, ~10,000 requests/day | ~$10–20/month |
| High traffic, ~100,000 requests/day | ~$80–150/month |

The main cost driver is Bedrock (Claude) — roughly $0.003 per request.
Lambda, API Gateway, and S3 are negligible at personal-app scale.

> ⚠️ Always set a billing alarm (Step 8.7) so you are never surprised.

---

## Before You Start

You need:
- [ ] A PartyRock app you want to convert
- [ ] An email address (for GitHub account)
- [ ] A web browser (Chrome or Firefox recommended)
- [ ] **Path A only:** A credit card (AWS free tier — charges are near zero)
- [ ] **Path B only:** Access to your lab/sandbox environment credentials panel

---

## Quick Reference — What Each Step Covers

**Step 0 — App Planner:** Fill in your PartyRock app details → get a ready-to-paste Kiro prompt and spec plan. Do this before writing any code.

**Step 1 — Read Widgets:** Open each widget's edit panel in PartyRock and write down the exact settings. These become your Lambda prompts.

**Step 2 — AWS Account:** Create account, IAM user, S3 bucket, lifecycle rule. Plan throttling and concurrency limits.

**Step 3 — Bedrock + IAM + Monitoring:** Enable Claude, scope IAM to specific model ARN, create CloudWatch dashboard.

**Step 4 — GitHub:** Create account and repository.

**Step 5 — Upload + Harden:** Upload files, add input protection, requirements.txt, favicon, markdown rendering, character counters.

**Step 6 — Credentials:** Add AWS secrets, region variable, and API key to GitHub.

**Step 7 — Deploy + Protect:** Run deployment, set up API key + usage plan, Lambda concurrency, CloudWatch alarms, optional staging.

**Step 8 — Test + Monitor:** Test all features, check logs, set billing alarm, understand slow responses.

**Step 9 — Production-Ready:** Add CloudFront (HTTPS), optional custom domain, WAF, DynamoDB chat persistence, large file uploads.

**Step 10 — Update Your App:** How to make changes and redeploy after your app is live.

**Step 11 — Delete Your App:** How to cleanly remove all AWS resources when you no longer need the app.
