# Vercel Deploy — Detailed Documentation

Deploy applications to Vercel with automatic Discord notifications for deployment status.

## 📑 Table of Contents

- [Overview](#overview)
- [Goal](#goal)
- [Intake](#intake)
- [Output Contract](#output-contract)
- [Quality Bar](#quality-bar)
- [How It Works](#how-it-works)
- [First-Time Setup](#first-time-setup)
- [Deploy Commands](#deploy-commands)
- [Deployment Workflow](#deployment-workflow)
- [Discord Notifications](#discord-notifications)
- [Environment Variables](#environment-variables)
- [Troubleshooting](#troubleshooting)
- [Rollback](#rollback)
- [Security Notes](#security-notes)

---

## 📋 Overview

This skill automates Vercel deployments with real-time Discord notifications. It includes:

- **Automatic notifications** on every deploy (success or failure)
- **Staging/Production routing** — different Discord channels for different environments
- **First-time setup wizard** — guided configuration
- **GitHub Actions workflow** — handles notification logic

---

## 🎯 Goal

Deploy applications to Vercel with automatic Discord notifications via GitHub Actions, including setup wizard, staging/production routing, and troubleshooting.

---

## 📥 Intake

No input required. Everything is configured by the setup wizard on first execution.

---

## 📤 Output Contract

Deploy completed + Discord notification (success or failure) + status log + rollback available. Notification file: `.github/workflows/discord-notification.yml`.

---

## ✅ Quality Bar

- Webhooks configured
- GitHub secrets correct
- Workflow running
- Notification appears in the correct Discord channel (staging vs production)
- Message formatted correctly with emojis and links
- Fallback for errors with descriptive message
- If any check fails, report the issue and do not deliver until fixed

---

## ⚙️ How It Works

```
Developer pushes to any branch
        |
Vercel detects the push
        |
Vercel deploys:
  * main branch -> Production URL (https://project.vercel.app)
  * Other branch -> Preview URL (https://project-branch.vercel.app)
        |
Vercel fires "deployment_status" event
        |
GitHub Actions receives the event
        |
discord-notification.yml runs:
  1. Checks out the code (fetch-depth: 0 for full git history)
  2. Reads the commit message via git log
  3. Determines environment (Production vs Staging)
  4. Builds the Discord message with python3 (safe JSON escaping)
  5. Sends to the correct webhook
        |
Discord receives the message
  * #production channel <- production deploys
  * #staging channel    <- preview deploys
```

---

## 🧙 First-Time Setup

When the AI loads this skill for the first time and detects that the setup is incomplete, it will guide you through the configuration step by step.

### Step A — Check Prerequisites

**Create the notification workflow file** at:
```
.github/workflows/discord-notification.yml
```

Use the template from **Appendix A** as the content.

Also verify the following file exists (optional):
```
vercel.json                      <- Vercel configuration (optional)
```

If `discord-notification.yml` does not exist, create it using the template in **Appendix A**.

### Step B — Configure Vercel

1. Link the project to Vercel:
   ```bash
   npx vercel link
   ```
2. In the Vercel Dashboard → **Settings → Git**:
   - Set **Production Branch** to `main`
   - Confirm **Preview Branches** = "All branches" (default)

### Step C — Create Discord Webhooks

1. **Open Discord** → Select the server
2. **Create channels** (or use existing):
   - `#staging` — for preview deploys (develop, feature branches)
   - `#production` — for production deploys (main branch)
3. For **each channel**:
   - Channel Settings → Integrations → Webhooks
   - Click "New Webhook"
   - Name: `Vercel Deploy`
   - Copy the webhook URL

### Step D — Configure GitHub Secrets

Go to: `https://github.com/{owner}/{repo}/settings/secrets/actions`

Add two secrets:

| Secret Name | Value |
|-------------|-------|
| `DISCORD_WEBHOOK_STAGING` | Staging webhook URL |
| `DISCORD_WEBHOOK_PRODUCTION` | Production webhook URL |

### Step E — Deploy the Workflow

```bash
git add .github/workflows/discord-notification.yml
git commit -m "ci: add deploy notification workflow"
git push origin main
```

### Step F — Verify Setup

Push to the `develop` branch and verify the notification appears in the `#staging` channel on Discord.

---

## 🚀 Deploy Commands

### Standard Deploy

```bash
# Deploy to production
vercel --prod

# Deploy to preview
vercel

# Deploy with specific environment
vercel --env NODE_ENV=production
```

### Deploy with Manual Notification

```bash
# Send Discord notification before deploy
curl -X POST "$DISCORD_WEBHOOK_URL" \
  -H "Content-Type: application/json" \
  -d '{"content":"Deployment started..."}'

# Run deploy
vercel --prod

# Send success notification
curl -X POST "$DISCORD_WEBHOOK_URL" \
  -H "Content-Type: application/json" \
  -d '{"content":"Deployment successful!"}'
```

---

## 🔄 Deployment Workflow

### 1. Pre-Deploy Checks

```bash
# Run linting
npm run lint

# Run tests
npm test

# Build locally to verify
npm run build
```

### 2. Deploy

```bash
# Push changes (Vercel auto-deploys on push)
git push origin main

# Or manually deploy:
vercel --prod
```

### 3. Post-Deploy Verification

```bash
# Check deployment status
vercel ls

# Check function logs
vercel logs

# Inspect deployment
vercel inspect
```

---

## 💬 Discord Notifications

### Production Channel (`#production`)

Triggered when: push to `main` branch → Vercel production deploy succeeds.

**Success message:**
```
✅ Deploy Succeeded

{full commit message — subject + body}

🔗 <https://{project-name}.vercel.app>
```

**Failure message:**
```
❌ Deploy Failed

{full commit message — subject + body}

🔗 <https://{project-name}.vercel.app>
```

### Staging Channel (`#staging`)

Triggered when: push to any branch other than `main` → Vercel preview deploy succeeds.

**Success message:**
```
✅ Deploy Succeeded

**Commit SHA**
[`{full-commit-sha}`](<{commit-url}>)

**Message**
{full commit message — subject + body}

**Environment**
🧪 Staging

**URL**
<{preview-deploy-url}>
```

**Failure message:**
```
❌ Deploy Failed

**Commit SHA**
[`{full-commit-sha}`](<{commit-url}>)

**Message**
{full commit message — subject + body}

**Environment**
🧪 Staging

**URL**
<{preview-deploy-url}>
```

---

## 🔐 Environment Variables

### GitHub Secrets (configured by user)

| Secret | Purpose |
|--------|---------|
| `DISCORD_WEBHOOK_STAGING` | Webhook URL for `#staging` channel |
| `DISCORD_WEBHOOK_PRODUCTION` | Webhook URL for `#production` channel |

### Workflow Environment Variables (auto-populated)

| Variable | Source | Purpose |
|----------|--------|---------|
| `COMMIT_SHA` | `github.event.deployment.sha` | SHA of the deployed commit |
| `COMMIT_MSG` | `git log --pretty=format:'%s'` | Subject line of commit |
| `COMMIT_BODY` | `git log --pretty=format:'%b'` | Body of commit |
| `ENVIRONMENT` | `github.event.deployment.environment` | "Production" or "Preview" |
| `STATUS` | `github.event.deployment_status.state` | "success" or "failure" |
| `URL` | `github.event.deployment_status.target_url` | Vercel deploy URL |
| `REPO_FULL` | `github.repository` | Full repository name (owner/repo) |
| `PROD_URL` | `secrets.PRODUCTION_URL` | Production URL |

---

## 🔧 Troubleshooting

### Discord notification not appearing

| Symptom | Cause | Fix |
|---------|-------|-----|
| No notification after push | Webhook URL wrong or secret missing | Check GitHub Secrets: `DISCORD_WEBHOOK_STAGING` and `DISCORD_WEBHOOK_PRODUCTION` must match the Discord webhook URLs |
| No notification after push | `discord-notification.yml` not on `main` branch | The workflow only runs if the file exists on the branch that Vercel deploys from. Push `discord-notification.yml` to `main` first |
| Notification appears but message is empty | Commit message not read correctly | The workflow uses `git log` with `fetch-depth: 0`. Verify the checkout step has `fetch-depth: 0` |
| 404 error from Discord | Webhook URL expired or deleted | Regenerate the webhook in Discord and update the GitHub Secret |

### Vercel deploy not triggering

| Symptom | Cause | Fix |
|---------|-------|-----|
| Push succeeds but no deploy | Vercel not linked to repo | Run `npx vercel link` and configure in Dashboard → Settings → Git |
| Push succeeds but no deploy | Production branch not set to `main` | Vercel Dashboard → Settings → Git → Production Branch = `main` |
| Deploy fails immediately | Build error in project | Check Vercel build logs. Fix the error, then push again |

### Staging shows wrong environment

| Symptom | Cause | Fix |
|---------|-------|-----|
| Staging notification says "Production" | Branch is named `main` | Only `main` branch triggers production. Use a different branch name |
| Production notification says "Staging" | Vercel environment not configured | Vercel Dashboard → Settings → Environments → set Production branch to `main` |

### Commit message not showing in notification

| Symptom | Cause | Fix |
|---------|-------|-----|
| Message shows "no message" | `git log` can't read the commit | Verify `fetch-depth: 0` in checkout step. The SHA must be valid |
| Only first line shown (no body) | Commit body is empty | This is normal — `%b` returns empty if no body |
| Special characters break the message | JSON escaping issue | The workflow uses `python3 json.dumps` for safe escaping |

### Build fails

```bash
# Check build logs
vercel logs <deployment-url>

# Debug locally
vercel dev
```

### Environment variables

```bash
# List environment variables
vercel env ls

# Add environment variable
vercel env add VARIABLE_NAME

# Pull environment variables
vercel env pull .env.local
```

### Domain issues

```bash
# Check domain status
vercel domains ls

# Verify domain
vercel domains verify <domain>

# Add custom domain
vercel domains add <domain>
```

---

## ⏪ Rollback

```bash
# List recent deployments
vercel ls

# Rollback to specific deployment
vercel rollback <deployment-url>

# Rollback to previous version
vercel rollback
```

---

## 🔒 Security Notes

- Never commit `.vercel-deploy-config.json` to git (add to .gitignore)
- Use GitHub Secrets for sensitive data in workflows
- Restrict Discord webhook permissions
- Use Vercel's built-in environment encryption
- Rotate tokens periodically

---

## 📊 Quick Reference

| Trigger | Branch | Channel | Title | Fields |
|---------|--------|---------|-------|--------|
| Push to `main` | main | `#production` | ✅ Deploy Succeeded | Full message + URL |
| Push to `develop` | develop | `#staging` | ✅ Deploy Succeeded | SHA + Message + Environment + URL |
| Deploy failure | any | `#production` or `#staging` | ❌ Deploy Failed | Full message + URL (prod) / Environment + URL (staging) |

| Command | Description |
|---------|-------------|
| `vercel --prod` | Deploy to production |
| `vercel` | Deploy to preview |
| `vercel ls` | List deployments |
| `vercel logs` | View logs |
| `vercel rollback` | Rollback deployment |
| `vercel env ls` | List environment variables |

---

## 📝 Appendix A — Workflow Template

If `discord-notification.yml` does not exist, create the file at:
```
.github/workflows/discord-notification.yml
```

**The file content must be exactly:**

```yaml
name: Deploy Notification

on:
  deployment_status:

jobs:
  notify:
    if: github.event.deployment_status.state == 'success' || github.event.deployment_status.state == 'failure'
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          ref: ${{ github.event.deployment.sha }}
          fetch-depth: 0

      - name: Send Discord notification
        env:
          WEBHOOK_STAGING: ${{ secrets.DISCORD_WEBHOOK_STAGING }}
          WEBHOOK_PRODUCTION: ${{ secrets.DISCORD_WEBHOOK_PRODUCTION }}
          STATUS: ${{ github.event.deployment_status.state }}
          URL: ${{ github.event.deployment_status.target_url }}
          COMMIT_SHA: ${{ github.event.deployment.sha }}
          ENVIRONMENT: ${{ github.event.deployment.environment }}
          REPO_FULL: ${{ github.repository }}
          PROD_URL: ${{ secrets.PRODUCTION_URL }}
        run: |
          export COMMIT_MSG=$(git log -1 --pretty=format:'%s' "$COMMIT_SHA" 2>/dev/null || echo "no message")
          export COMMIT_BODY=$(git log -1 --pretty=format:'%b' "$COMMIT_SHA" 2>/dev/null || echo "")

          if [ "$STATUS" = "success" ]; then
            export TITLE="✅ Deploy Succeeded"
          else
            export TITLE="❌ Deploy Failed"
          fi

          IS_PROD="false"
          if [ "$ENVIRONMENT" = "Production" ]; then
            IS_PROD="true"
          fi

          # ================================================================
          # PRODUCTION → Full commit message + production URL
          # ================================================================
          if [ "$IS_PROD" = "true" ]; then
            export ENV_LABEL="🚀 Production"
          else
            export ENV_LABEL="🧪 Staging"
          fi

          if [ "$IS_PROD" = "true" ]; then

            PAYLOAD=$(python3 -c "
          import json, os
          title = os.environ['TITLE']
          msg = os.environ['COMMIT_MSG'].strip()
          body = os.environ.get('COMMIT_BODY', '').strip()
          url = os.environ['PROD_URL']
          env_label = os.environ['ENV_LABEL']
          full = msg
          if body:
              full += '\n\n' + body
          content = f'{title}\n\n{full}\n\n🔗 <{url}>'
          print(json.dumps({'content': content}))
          ")

            curl -s -H "Content-Type: application/json" -d "$PAYLOAD" "$WEBHOOK_PRODUCTION"

          # ================================================================
          # STAGING → Commit SHA + Message + Environment + URL
          # ================================================================
          else

            PAYLOAD=$(python3 -c "
          import json, os
          title = os.environ['TITLE']
          sha = os.environ['COMMIT_SHA']
          repo_full = os.environ['REPO_FULL']
          msg = os.environ['COMMIT_MSG'].strip()
          body = os.environ.get('COMMIT_BODY', '').strip()
          env_label = os.environ['ENV_LABEL']
          url = os.environ['URL']
          full = msg
          if body:
              full += '\n\n' + body
          commit_url = f'https://github.com/{repo_full}/commit/{sha}'
          content = f'{title}\n\n**Commit SHA**\n[\`{sha}\`](<{commit_url}>)\n\n**Message**\n{full}\n\n**Environment**\n{env_label}\n\n**URL**\n<{url}>'
          print(json.dumps({'content': content}))
          ")

            curl -s -H "Content-Type: application/json" -d "$PAYLOAD" "$WEBHOOK_STAGING"
          fi
```
