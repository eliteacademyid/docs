# Step 11 — Delete Your App (Cleanup)

If you no longer need your app, or you want to start fresh, this step shows you
how to cleanly remove all the AWS resources your app created.

> ⚠️ Deleting your app is permanent. All your Lambda functions, API Gateway,
> and app-specific S3 buckets will be deleted. Your GitHub repository and code
> are NOT affected — you can always redeploy later.

---

## Before You Start This Step

- [ ] You are sure you want to delete the app
- [ ] You have saved any data or outputs you want to keep
- [ ] You are signed in to the AWS Console

---

## 11.1 Delete the CloudFormation Stack

Your entire app — Lambda functions, API Gateway, S3 frontend bucket — was created
as a single CloudFormation "stack." Deleting the stack removes everything at once.

1. In the AWS Console search bar, type **CloudFormation** and click it
2. Find your stack in the list (e.g. `my-partyrock-app` or `health-tracker`)
3. Check the box next to your stack name
4. Click the **Delete** button in the top right

   > 📸 **Screenshot:** The CloudFormation stacks list shows your stack selected
   > with a checkbox. The "Delete" button appears in the top right toolbar.

5. A confirmation dialog appears — click **Delete stack**

   > 📸 **Screenshot:** A dialog box asks "Are you sure you want to delete this stack?"
   > Click the red "Delete stack" button to confirm.

6. The stack status changes to `DELETE_IN_PROGRESS` — wait 3–5 minutes
7. When complete, the stack disappears from the list

   > 📸 **Screenshot:** After deletion, the stack no longer appears in the list.
   > All associated AWS resources have been removed.

> 💡 If the deletion fails with an error like "S3 bucket is not empty", you need to
> empty the S3 bucket first (see step 11.3), then try deleting the stack again.

---

## 11.2 Delete the SAM Artifacts Bucket

The S3 bucket you created in Step 2.10 for SAM deployment artifacts is NOT part of
the CloudFormation stack — it must be deleted manually.

1. In the AWS Console, search for **S3** and click it
2. Find your SAM artifacts bucket (the one named `yourname-sam-deploy-2024`)
3. Click on the bucket name

   > 📸 **Screenshot:** The S3 buckets list shows all your buckets. Click the name
   > of your SAM artifacts bucket.

4. Click **Empty** (you must empty the bucket before deleting it)
5. Type `permanently delete` in the confirmation box
6. Click **Empty**

   > 📸 **Screenshot:** The empty bucket confirmation dialog asks you to type
   > "permanently delete". Type it exactly and click Empty.

7. After emptying, click **Delete**
8. Type the bucket name in the confirmation box
9. Click **Delete bucket**

---

## 11.3 Empty and Delete the Frontend Bucket (if not deleted by CloudFormation)

If the CloudFormation deletion failed because the frontend S3 bucket was not empty:

1. In S3, find your frontend bucket (named something like `my-app-frontend-123456789`)
2. Click on the bucket name
3. Click **Empty** → type `permanently delete` → click **Empty**
4. After emptying, go back to CloudFormation and try deleting the stack again

---

## 11.4 Delete the CloudWatch Log Groups

Lambda functions automatically create log groups in CloudWatch. These are not deleted
with the stack and accumulate over time (though they cost very little).

1. In the AWS Console, search for **CloudWatch** and click it
2. In the left sidebar, click **Log groups**
3. Look for log groups starting with `/aws/lambda/` followed by your app name
4. Check the boxes next to each one
5. Click **Actions** → **Delete log group(s)**
6. Confirm the deletion

   > 📸 **Screenshot:** The log groups list shows entries starting with "/aws/lambda/".
   > Check the boxes next to your app's log groups and click Actions → Delete.

---

## 11.5 Delete the CloudFront Distribution (if you set one up)

If you added CloudFront in Step 9.1:

1. In the AWS Console, search for **CloudFront** and click it
2. Find your distribution in the list
3. Check the box next to it
4. Click **Disable** first (CloudFront distributions must be disabled before deletion)
5. Wait 5–10 minutes for the status to change to **Disabled**
6. Check the box again → click **Delete**

   > 📸 **Screenshot:** The CloudFront distributions list shows your distribution.
   > First click Disable, wait for it to show "Disabled", then click Delete.

---

## 11.6 Delete the DynamoDB Table (if you set one up)

If you added DynamoDB for chat sessions in Step 9.4:

1. In the AWS Console, search for **DynamoDB** and click it
2. In the left sidebar, click **Tables**
3. Click on your table name (e.g. `my-app-chat-sessions`)
4. Click **Delete** in the top right
5. Type `confirm` in the confirmation box
6. Click **Delete**

---

## 11.7 Delete the IAM User (optional)

If you no longer need the `github-deployer` IAM user:

1. In the AWS Console, search for **IAM** and click it
2. Click **Users** in the left sidebar
3. Click on `github-deployer`
4. Click **Delete** in the top right
5. Type the username to confirm → click **Delete**

---

## 11.8 What Is NOT Deleted

After completing the steps above, these things remain:
- Your **GitHub repository** — your code is safe, you can redeploy anytime
- Your **AWS account** — the account itself is not deleted
- Your **billing alarm** — this is fine to keep
- Your **CloudWatch dashboard** — this is fine to keep or delete manually

---

## 11.9 Redeploy Later

If you want to bring your app back:

1. Go to your GitHub repository
2. Click the **Actions** tab
3. Click your workflow name → **Run workflow**
4. Your app will be redeployed from scratch in 3–5 minutes

---

## ✅ Checkpoint

- [ ] CloudFormation stack deleted
- [ ] SAM artifacts S3 bucket emptied and deleted
- [ ] CloudWatch log groups deleted
- [ ] CloudFront distribution disabled and deleted (if applicable)
- [ ] DynamoDB table deleted (if applicable)
- [ ] IAM user deleted (optional)
