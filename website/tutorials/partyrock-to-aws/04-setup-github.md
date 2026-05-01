# Step 4 — Set Up GitHub

GitHub is a website where developers store code. We will use it as the
"launch button" for your app — every time you push code to GitHub,
it automatically deploys to AWS for you.

You do not need to understand code to use GitHub for this tutorial.

---

## Before You Start This Step

- [ ] You have completed Step 3 (Bedrock model access granted)
- [ ] You have your AWS Access Key ID, Secret Access Key, and S3 bucket name from Step 2 saved somewhere

> 💡 **Coming up in Step 6:** After uploading your files, you will add your AWS credentials
> to GitHub as "secrets." You will also add an `API_KEY` (a password for your app's backend)
> and an `AWS_REGION` variable. Keep your saved values handy.

---

## 4.1 Create a GitHub Account

1. Go to [github.com](https://github.com)
2. Click **Sign up**
3. Enter your email address
4. Create a password
5. Choose a username (this will be public)
6. Verify your email address
7. On the plan selection screen, choose **Free**

---

## 4.2 Create a New Repository

A "repository" (or "repo") is like a folder on GitHub that holds your app's files.

1. After signing in, click the **+** icon in the top right corner
2. Click **New repository**

   > 📸 **Screenshot:** Top right of GitHub shows a "+" icon. Clicking it
   > shows a dropdown with "New repository" as the first option.

3. Fill in the form:
   - **Repository name:** `my-partyrock-app` (or any name you like)
   - **Description:** `My AI app sliced from PartyRock` (optional)
   - **Visibility:** Select **Public** (required for free GitHub Actions minutes)
   - Check **Add a README file**

4. Click **Create repository**

   > 📸 **Screenshot:** The new repository page shows a green "Create repository"
   > button at the bottom of the form.

---

## 4.3 Understand the Repository Structure

After creating the repo, you will see a page with:
- A file called `README.md`
- Tabs at the top: Code, Issues, Pull requests, Actions, Settings

   > 📸 **Screenshot:** Repository page shows the file list with README.md,
   > and tabs across the top including "Settings" on the far right.

We will use:
- **Code** tab — to upload files
- **Actions** tab — to watch deployments run
- **Settings** tab — to add your AWS credentials

---

## ✅ Checkpoint

Before moving on, you should have:
- [ ] GitHub account created
- [ ] New repository created
- [ ] You can see the repository page with the tabs at the top
