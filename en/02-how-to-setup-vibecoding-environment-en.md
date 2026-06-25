# Getting Started with Vibe Coding: Setting Up Your Development Environment from Scratch

[English](../en/02-how-to-setup-vibecoding-environment-en.md) · [繁中](../zh/02-how-to-setup-vibecoding-environment-zh.md) · [Español](../es/02-how-to-setup-vibecoding-environment-es.md) · [日本語](../jp/02-how-to-setup-vibecoding-environment-jp.md) · [Português (BR)](../pt-br/02-how-to-setup-vibecoding-environment-pt-br.md)

**Author:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Updated:** March 2026  
**Reading time:** ~10 minutes

-----

## Follow This Guide and Your Environment Will Be Ready

A lot of people get stuck at this stage — not because it’s too hard, but because they don’t know what to do or in what order.

This guide walks through every prerequisite, one step at a time. By the end, you’ll have a complete Vibe Coding development environment set up, with vibefast.app already deployed and running on Cloudflare.

What you need to install: Node.js, Git, Cursor. What you need to do: clone the repo, run `npm install`, run `npm run setup`. That’s it.

-----

## Step 1: Install Node.js

Node.js is the runtime that lets your computer run JavaScript. Without it, `npm` and `npx` won’t work.

Go to [nodejs.org](https://nodejs.org) and download the **LTS version** (Long Term Support).

After installing, open your terminal and verify:

```bash
node --version
# You should see something like v22.0.0

npm --version
# You should see something like 10.0.0
```

If you see version numbers, the installation was successful.

-----

## A Quick Note on the Terminal

If you’re not familiar with the terminal (the black window), don’t be intimidated.

**Mac:** Press `Cmd + Space`, search for “Terminal”, and open it.  
**Windows:** Search for “PowerShell” or install [Windows Terminal](https://aka.ms/terminal).

The terminal is just a place where you communicate with your computer using text commands. You type a command, the computer runs it. About half of the Vibe Coding workflow happens here.

-----

## What Are npm and npx? (30-Second Version)

**npm (Node Package Manager)** is JavaScript’s package management tool. Developers around the world publish their tools and libraries to the npm registry, and you can download and use them for free.

```bash
npm install stripe   # download the Stripe package into your project
npm run dev          # run the "dev" script defined in package.json
```

**npx** runs a tool directly without installing it first:

```bash
npx wrangler deploy  # run wrangler directly, no npm install needed
```

That’s all you need to know for now. If you encounter other npm commands, ask AI to explain them.

-----

## Step 2: Install Git

Git is a version control tool that lets you clone repositories and track changes to your code.

**Mac:** Type this in your terminal:

```bash
git --version
```

If Git isn’t installed, macOS will automatically prompt you to install Xcode Command Line Tools. Follow the prompts.

**Windows:** Go to [git-scm.com](https://git-scm.com) and download the installer.

Verify after installing:

```bash
git --version
# You should see something like git version 2.40.0
```

-----

## Step 3: Create a Cloudflare Account

Go to [cloudflare.com](https://cloudflare.com) and sign up for a free account.

vibefast.app deploys to Cloudflare, so you'll need an account to complete the setup. The free plan's limits are more than enough for a new app.

### Enable R2 (Requires a Credit Card)

vibefast.app uses Cloudflare R2 to store files and images. **R2 is disabled by default** and must be enabled manually — Cloudflare will ask for a credit card when you do.

Steps:

1. Log in to the Cloudflare Dashboard
2. Click **R2** in the left sidebar
3. Click "Purchase R2 Plan" or "Enable R2" and follow the prompts to enter your credit card

**You won't be charged immediately.** R2 includes a free tier each month (10 GB storage, 1 million read operations). You're only billed if you exceed those limits. The credit card is used for identity verification and for any usage beyond the free tier. For a new app, you won't come close to those limits.

-----

## Step 4: Clone vibefast.app

After purchasing vibefast.app, you’ll receive a collaborator invitation to the private repo. Accept the invitation, then run:

```bash
git clone https://github.com/vibefast-app/vibefast.git my-app
cd my-app
```

`git clone` copies the entire repository to your computer. `cd my-app` moves you into that folder.

-----

## Step 5: npm install

```bash
npm install
```

This reads the project’s `package.json` and downloads all the required packages into a `node_modules` folder.

The first run might take a few minutes. Wait for the completion message.

**Why is this necessary?**  
The `node_modules` folder is large (hundreds of MB) and isn’t stored on GitHub. Every time you clone a new project, you need to run `npm install` to download the packages locally.

-----

## Step 6: npm run setup

```bash
npm run setup
```

This is the most magical step in the whole process.

Combined with the clone and install, you’ve now run just three commands:

```bash
git clone https://github.com/vibefast-app/vibefast.git my-app
cd my-app && npm install
npm run setup
```

`npm run setup` handles everything automatically:

1. Guides you through logging into your Cloudflare account
1. Creates the D1 database and runs the bootstrap SQL
1. Auto-generates a JWT secret and writes it to Cloudflare environment variables
1. Deploys both the frontend and backend Workers to production simultaneously

When it finishes, your app is running on 300+ Cloudflare edge nodes around the world. From clone to global deployment — three commands, a few minutes. That’s Vibe Coding on Cloudflare.

The terminal will display your app URL. Open it in a browser to confirm everything is live.

-----

## Step 7: Install Cursor

Cursor is currently the most popular AI code editor and the core tool for Vibe Coding.

**Cursor is paid software at $20/month.** After signing up, you get a 7-day free trial with full functionality — no credit card required. Decide whether to subscribe after the trial ends.

Go to [cursor.com](https://cursor.com), download and install, then sign up with your email to start the 7-day trial.

After installing, open your project in Cursor:

```bash
cursor .
```

Or go to File → Open Folder inside Cursor and select your `my-app` folder.

Once open, press `Cmd+L` (Mac) or `Ctrl+L` (Windows) to open the AI chat panel. Try typing:

```
What's the structure of this project? Can you explain the main folders and files?
```

Cursor will analyze your entire project and explain what each part does.

-----

## Local Development

Once your environment is set up, your everyday development flow is:

```bash
npm run dev
```

This starts a local development server. Open `http://localhost:5173` to see your app running locally.

**Make changes locally → confirm everything works → `npm run deploy` to push to production.**

This is the basic development loop.

-----

## What You Now Have

After completing these seven steps, you have:

- ✅ Node.js and npm installed
- ✅ Git installed
- ✅ A Cloudflare account
- ✅ vibefast.app deployed to production and accessible worldwide
- ✅ Cursor installed and ready for Vibe Coding

Continue to the next tutorial and start building the features you want.

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [Back to course index](../README.md)

Ready to skip the setup and start building?  
👉 **[vibefast.app](https://vibefast.app/pricing)** — Early bird $99, price increases to $199 on August 1, 2026.
