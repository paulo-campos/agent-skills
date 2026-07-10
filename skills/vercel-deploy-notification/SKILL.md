---
name: vercel-deploy-notification
description: Automatically deploys to Vercel with Discord notifications via GitHub Actions. Includes setup wizard, staging/production routing, and troubleshooting.
---

# Vercel Deploy Notification

Deploy applications to Vercel with automatic Discord notifications for deployment status.

## Goal

Deploy applications to Vercel with automatic Discord notifications via GitHub Actions, including setup wizard, staging/production routing, and troubleshooting.

## Intake

No input required. Everything is configured by the setup wizard on first execution.

## Output Contract

Deploy completed + Discord notification (success or failure) + status log + rollback available. Notification file: `.github/workflows/vercel-deploy-notification.yml`.

## Quality Bar

- Webhooks configured
- GitHub secrets correct
- Workflow running
- Notification appears in the correct Discord channel (staging vs production)
- Message formatted correctly with emojis and links
- Fallback for errors with descriptive message
- If any check fails, report the issue and do not deliver until fixed

## First-Time Setup Wizard

> **When the AI loads this skill for the first time** and detects that the
> setup is incomplete, it MUST guide the user through the configuration below
> **before** running any deploy. Ask one step at a time, confirm each step
> before moving to the next.

### Step A — Check Prerequisites

**Create the notification workflow file** at:
```
.github/workflows/vercel-deploy-notification.yml
```

Use the template from **Appendix A** as the content.

Also verify the following file exists (optional):
```
vercel.json                      <- Vercel configuration (optional)
```

If `vercel-deploy-notification.yml` does not exist, create it using the template in **Appendix A**.

### Step B — Configure Vercel

1. Tell the user to link the project to Vercel:
   ```
   npx vercel link
   ```
2. In the Vercel Dashboard -> **Settings -> Git**:
   - Set **Production Branch** to `main`
   - Confirm **Preview Branches** = "All branches" (default)
3. Confirm with the user: "Is Vercel configured for automatic deploys?"

### Step C — Create Discord Webhooks

Guide the user through creating two webhooks:

1. **Open Discord** -> Select the server
2. **Create channel** (or use existing):
   - `#staging` — for preview deploys (develop, feature branches)
   - `#production` — for production deploys (main branch)
3. For **each channel**:
   - Channel Settings -> Integrations -> Webhooks
   - Click "New Webhook"
   - Name: `Vercel Deploy`
   - Copy the webhook URL
4. Ask the user to paste both URLs when done:
   - Staging webhook URL: `_______________`
   - Production webhook URL: `_______________`

### Step D — Configure GitHub Secrets

Tell the user to go to:
```
https://github.com/{owner}/{repo}/settings/secrets/actions
```

Add two secrets:

| Secret Name | Value |
|-------------|-------|
| `DISCORD_WEBHOOK_VERCEL_DEVELOP` | Staging webhook URL |
| `DISCORD_WEBHOOK_VERCEL_PRODUCTION` | Production webhook URL |

### Step E — Deploy the Workflow

Commit and push the `.github/workflows/vercel-deploy-notification.yml` file:
```bash
git add .github/workflows/vercel-deploy-notification.yml
git commit -m "ci: add deploy notification workflow"
git push origin main
```

### Step F — Verify Setup

Tell the user:
> "Setup complete! To test, push to the `develop` branch and verify the
> notification appears in the `#staging` channel on Discord."

---

## How It Works — End-to-End Flow

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
vercel-deploy-notification.yml runs:
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

## Deploy Commands

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

## Deployment Workflow

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

## How Workflow Determines Production vs Staging

The workflow checks the `ENVIRONMENT` variable from the Vercel deploy event:

```bash
if [ "$ENVIRONMENT" = "Production" ]; then
  IS_PROD="true"
else
  IS_PROD="false"
fi
```

- **Production** (`IS_PROD=true`): Webhook goes to `DISCORD_WEBHOOK_VERCEL_PRODUCTION`
- **Staging** (`IS_PROD=false`): Webhook goes to `DISCORD_WEBHOOK_VERCEL_DEVELOP`

---

## How the Commit Message Is Read

The workflow reads the commit via `git log` (NOT from the GitHub event):

```bash
COMMIT_MSG=$(git log -1 --pretty=format:'%s' "$COMMIT_SHA")
COMMIT_BODY=$(git log -1 --pretty=format:'%b' "$COMMIT_SHA")
```

- `%s` = subject line (first line)
- `%b` = body (everything after the first line)

Both are combined for the full message in the Discord notification.

---

## Discord Notification Formats

### Production Channel (`#production`)

Triggered when: push to `main` branch -> Vercel production deploy succeeds.

**Message format:**
```
Deploy Succeeded

{full commit message — subject + body}

https://{project-name}.vercel.app
```

**On failure:**
```
Deploy Failed

{full commit message — subject + body}

https://{project-name}.vercel.app
```

### Staging Channel (`#staging`)

Triggered when: push to any branch other than `main` -> Vercel preview deploy succeeds.

**Message format:**
```
Deploy Succeeded

**Commit SHA**
`{full-commit-sha}`

**Message**
{full commit message — subject + body}

**Environment**
Staging

**URL**
{preview-deploy-url}
```

**On failure:**
```
Deploy Failed

**Commit SHA**
`{full-commit-sha}`

**Message**
{full commit message — subject + body}

**Environment**
Staging

**URL**
{preview-deploy-url}
```

---

## Rollback

```bash
# List recent deployments
vercel ls

# Rollback to specific deployment
vercel rollback <deployment-url>

# Rollback to previous version
vercel rollback
```

---

## Error Handling

| Error | Action |
|-------|--------|
| Dirty working tree | Warn: "There are uncommitted changes. Stash or commit them first." |
| Push fails | Suggest `git pull --rebase origin main` and retry |
| Discord webhook fails | Log the curl response and suggest checking the webhook URL |
| Deploy fails (Vercel) | Send `Deploy Failed` notification with full context |

---

## Troubleshooting

### Discord notification not appearing

| Symptom | Cause | Fix |
|---------|-------|-----|
| No notification after push | Webhook URL wrong or secret missing | Check GitHub Secrets: `DISCORD_WEBHOOK_VERCEL_DEVELOP` and `DISCORD_WEBHOOK_VERCEL_PRODUCTION` must match the Discord webhook URLs |
| No notification after push | `vercel-deploy-notification.yml` not on `main` branch | The workflow only runs if the file exists on the branch that Vercel deploys from. Push `vercel-deploy-notification.yml` to `main` first |
| Notification appears but message is empty | Commit message not read correctly | The workflow uses `git log` with `fetch-depth: 0`. Verify the checkout step has `fetch-depth: 0` |
| 404 error from Discord | Webhook URL expired or deleted | Regenerate the webhook in Discord and update the GitHub Secret |

### Vercel deploy not triggering

| Symptom | Cause | Fix |
|---------|-------|-----|
| Push succeeds but no deploy | Vercel not linked to repo | Run `npx vercel link` and configure in Dashboard -> Settings -> Git |
| Push succeeds but no deploy | Production branch not set to `main` | Vercel Dashboard -> Settings -> Git -> Production Branch = `main` |
| Deploy fails immediately | Build error in project | Check Vercel build logs. Fix the error, then push again |

### Staging shows wrong environment

| Symptom | Cause | Fix |
|---------|-------|-----|
| Staging notification says "Production" | Branch is named `main` | Only `main` branch triggers production. Use a different branch name |
| Production notification says "Staging" | Vercel environment not configured | Vercel Dashboard -> Settings -> Environments -> set Production branch to `main` |

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

## Environment Variables

### GitHub Secrets (configured by user)

| Secret | Purpose |
|--------|---------|
| `DISCORD_WEBHOOK_VERCEL_DEVELOP` | Webhook URL for `#staging` channel |
| `DISCORD_WEBHOOK_VERCEL_PRODUCTION` | Webhook URL for `#production` channel |

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
| `TITLE` | Computed | "Deploy Succeeded" or "Deploy Failed" |
| `ENV_LABEL` | Computed | "Production" or "Staging" |

---

## Discord Webhook Setup

1. Go to Discord Server Settings > Integrations > Webhooks
2. Create new webhook
3. Copy webhook URL
4. Set as environment variable or add to config file

---

## Security Notes

- Never commit `.vercel-deploy-config.json` to git (add to .gitignore)
- Use GitHub Secrets for sensitive data in workflows
- Restrict Discord webhook permissions
- Use Vercel's built-in environment encryption
- Rotate tokens periodically

---

## File Structure

```
project/
|-- .github/
|   |-- workflows/
|       |-- vercel-deploy-notification.yml    # GitHub Actions workflow
|-- vercel.json                         # Vercel configuration (optional)
|-- .gitignore                          # Add sensitive config files
|-- ...
```

---

## Appendix A — Workflow Template

If `vercel-deploy-notification.yml` does not exist, create the file at:
```
.github/workflows/vercel-deploy-notification.yml
```

**The file content must be exactly:**

```yaml
name: Vercel Deploy Notification

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
          WEBHOOK_STAGING: ${{ secrets.DISCORD_WEBHOOK_VERCEL_DEVELOP }}
          WEBHOOK_PRODUCTION: ${{ secrets.DISCORD_WEBHOOK_VERCEL_PRODUCTION }}
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

---

## Appendix B — Quick Reference

| Trigger | Branch | Channel | Title | Fields |
|---------|--------|---------|-------|--------|
| Push to `main` | main | `#production` | Deploy Succeeded | Full message + URL |
| Push to `develop` | develop | `#staging` | Deploy Succeeded | SHA + Message + Environment + URL |
| Deploy failure | any | `#production` or `#staging` | Deploy Failed | Full message + URL (prod) / Environment + URL (staging) |

| Command | Description |
|---------|-------------|
| `vercel --prod` | Deploy to production |
| `vercel` | Deploy to preview |
| `vercel ls` | List deployments |
| `vercel logs` | View logs |
| `vercel rollback` | Rollback deployment |
| `vercel env ls` | List environment variables |
