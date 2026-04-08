# Git and GitHub Basics: Version Control Essentials for Vibe Coders

[English](../en/10-git-and-github-version-control-en.md) · [繁中](../zh/10-git-and-github-version-control-zh.md) · [Español](../es/10-git-and-github-version-control-es.md) · [日本語](../jp/10-git-and-github-version-control-jp.md) · [Português (BR)](../pt-br/10-git-and-github-version-control-pt-br.md)

**Author:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Updated:** March 2026  
**Reading time:** ~12 minutes

-----

## Why Do You Need Git?

You quickly built a feature with AI and deployed it.

Next day, you discover this feature broke something else. You want to go back to yesterday's version, but you've modified dozens of files, don't know what changed, and don't remember what it looked like before.

**Git solves this problem.**

Git is a **version control tool**—it records every "save" state, letting you return to any point in time. It runs on your own computer, works without internet.

**GitHub** is a cloud platform that lets you back up Git's version history online. Your code has cloud backup, no fear of losing it when switching computers, and others can see your project.

Simply put: **Git is the tool, GitHub is the warehouse.**

-----

## GitHub Core Concepts

### Repository (Repo)

A **repository** is where GitHub stores all code and history for a project. Each project corresponds to one repo.

Your repo can be:

- **Public**: Anyone can see, suitable for open source projects or educational content
- **Private**: Only you and invited people can see, suitable for commercial product source code

The vibefast-docs repo is public, anyone can see the tutorial content. vibefast.app template source code is in another private repo, only paying customers can access—this is how many commercial templates are delivered.

### Clone

**Clone** copies a GitHub repo to your local computer:

```bash
git clone https://github.com/vibefast-app/vibefast-docs.git
```

After cloning, you have the complete code and history locally.

### Fork

**Fork** copies someone else's repo to your own GitHub account, letting you freely modify your version without affecting the original repo. This is a common open source collaboration method.

-----

## Step 1: Install Git and Set Identity

### Check if Git is installed

macOS usually has Git built-in:

```bash
git --version
# If it outputs a version number, it's installed, e.g.: git version 2.39.3
```

If not, install with Homebrew:

```bash
brew install git
```

### Set your identity

Git records who made each commit. Tell Git who you are (only need to do once):

```bash
git config --global user.name "Danko Peng"
git config --global user.email "danko@example.com"
```

Email should match your GitHub account so GitHub can link commits to your account.

### Create GitHub account

Go to [github.com](https://github.com) and register, free account is sufficient.

-----

## Step 2: Create New Repo on GitHub

1. Log into GitHub, click **+** in top right → **New repository**
2. Fill **Repository name** (e.g., `my-app`, only English, numbers, hyphens)
3. Choose **Public** or **Private**
4. **Don't** check "Add a README file"—because you already have a local project, let local take precedence
5. Click **Create repository**

GitHub creates an empty repo and shows commands to run next—you'll use these shortly.

-----

## Step 3: git init and Connect to GitHub

You have a project folder locally, an empty repo on GitHub, now connect them.

### Scenario 1: Local project exists, push to GitHub

```bash
# Enter your project folder
cd my-app

# Initialize Git (make this folder tracked by Git)
git init

# Create .gitignore first, exclude files that shouldn't be tracked (explained below)
echo ".env" >> .gitignore
echo "node_modules/" >> .gitignore

# First time add all files and commit
git add .
git commit -m "initial commit"

# Connect to GitHub repo (replace with your username and repo name)
git remote add origin https://github.com/YOUR_USERNAME/my-app.git

# First push to GitHub
git push -u origin main
```

`git remote add origin` tells local Git: "My cloud backup is at this GitHub URL, name it origin."

`-u origin main` only needed for first push, afterwards just `git push`.

### Scenario 2: Project cloned from GitHub

If you cloned from GitHub (like cloning vibefast.app), Git already connected origin automatically, just use:

```bash
git add .
git commit -m "my first change"
git push
```

### Confirm connection succeeded

```bash
git remote -v
```

Output should look like:

```
origin  https://github.com/YOUR_USERNAME/my-app.git (fetch)
origin  https://github.com/YOUR_USERNAME/my-app.git (push)
```

-----

## Step 4: Daily Workflow

After setup, daily development is these three commands:

### git add .

```bash
git add .
```

Tells Git: "Prepare all files I've modified for saving."

`.` means "all changes in current directory." To add specific files only:

```bash
git add apps/web/app/routes/blog.tsx
```

### git commit -m "update order features"

```bash
git commit -m "update order features"
```

The actual save action. `-m` followed by description of what changed.

Good commit messages let you three months later immediately know what was done:

```bash
# ✅ Clear
git commit -m "add reading time display to blog posts"
git commit -m "fix order status not updating after Stripe webhook"
git commit -m "remove unused imports in api/users.ts"

# ❌ Meaningless
git commit -m "update"
git commit -m "fix bug"
git commit -m "asdfgh"
```

### git push

```bash
git push
```

Sync local commits to GitHub. Your code has cloud backup.

-----

## View History

```bash
git log --oneline
```

Output looks like:

```
a3f2c1d add email notification for new orders
9b8e4a2 fix order status not updating after webhook
3d7f1c5 add reading time to blog posts
1a2b3c4 initial commit
```

Each line is one commit, the string in front is the commit ID. To return to a version:

```bash
git checkout 9b8e4a2
```

-----

## Important: Things Pushed to GitHub Have History

This is what many don't know, and the easiest way to cause serious problems.

**Deleting a file and pushing doesn't mean the file disappeared.**

Git is designed to preserve all history. What you deleted can still be found in commit history. Anyone cloning your repo and looking at old commits can see that file's previous contents.

And GitHub has automated programs scanning new pushes 24/7, specifically looking for leaked API keys. From the moment you push, it may have already been scanned.

### Most Common Dangerous Scenario

You accidentally committed `.env`:

```bash
git add .   # Accidentally included .env
git commit -m "add stripe integration"
git push    # Pushed to GitHub
```

Then you noticed, immediately delete and push:

```bash
git rm .env
git commit -m "remove .env"
git push
```

**This doesn't work.**

Current commit doesn't have `.env`, but previous commit still does. Anyone looking at commit history can see your Stripe key, JWT secret, all secrets.

### Correct Remedy Steps

**Step 1 (most important): Immediately go to corresponding platform to revoke that key and regenerate a new one.** Regardless of how you clean git history later, do this first. The key is already leaked, must be considered invalid.

**Step 2: Remove from history completely.** Use `git filter-repo`:

```bash
# Install
pip install git-filter-repo

# Remove .env from all commit history
git filter-repo --path .env --invert-paths

# Force push to GitHub (overwrite history)
git push --force
```

But the best method is **never let it enter git in the first place**.

-----

## Prevention: .gitignore

`.gitignore` tells Git which files to never track:

```
# .gitignore
.env
.env.local
.env.production
node_modules/
.wrangler/
dist/
```

**Critical sequence: After `git init`, before first `git add .`, create `.gitignore` first.**

If you `git add .` first then create `.gitignore`, `.env` might already be tracked, adding to `.gitignore` later won't help. Order matters.

vibefast.app's `.gitignore` is already configured, you don't need to manually add. Remember this step when creating new projects yourself.

-----

## Glance Before Pushing

```bash
# See files prepared for commit
git status

# See specific content changes
git diff --staged
```

Develop the habit of glancing at `git status` before pushing, confirm no unwanted files snuck in.

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [Back to course index](../README.md)

Ready to skip the setup and start building?  
👉 **[vibefast.app](https://vibefast.app/pricing)** — Early bird $99, price increases to $199 on June 1, 2026.
