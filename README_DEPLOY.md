# JasonRKelly.com Deployment Runbook

JasonRKelly.com is a no-build static website managed through Git and automatically deployed from GitHub to the Code Support hosting environment.

## Deployment Architecture

The production deployment path is:

```text
VS Code / Local Repository
        ↓
    git push
        ↓
GitHub Repository
        ↓
 GitHub Actions
        ↓
      SSH
        ↓
Code Support Hosting
        ↓
JasonRKelly.com
```

The `main` branch is the production branch.

A successful push to `main` automatically triggers the GitHub Actions deployment workflow and updates the hosted copy of the website.

---

## Repository Files

- `index.html` — page content and structure
- `assets/styles.css` — visual styling
- `assets/site.js` — mobile navigation and current-year footer
- `.github/workflows/deploy.yml` — GitHub Actions production deployment workflow
- `README.md` — project documentation
- `README_DEPLOY.md` — deployment and recovery runbook

No framework, package manager, PHP, database, or build process is required.

---

## Local Repository

Primary Windows development location:

```text
C:\Projects\jasonrkelly-site
```

Development and Git operations are normally performed through VS Code.

Before beginning work:

```powershell
git status
git pull --ff-only
```

The working tree should be clean and the local `main` branch should be synchronized with `origin/main`.

---

## Local Preview

From PowerShell inside the repository:

```powershell
python -m http.server 8080
```

Then open:

```text
http://127.0.0.1:8080/
```

Leave the Python process running while using the local preview.

If the browser reports:

```text
127.0.0.1 refused to connect
```

the local HTTP server has probably stopped. Restart it with:

```powershell
python -m http.server 8080
```

This does not indicate that the production website is unavailable.

---

## Standard Production Deployment

### 1. Make changes locally

Edit the website in VS Code.

### 2. Test locally

Use the local HTTP server and verify the change before committing it.

### 3. Review Git status

```powershell
git status
```

Verify that only the intended files were modified.

### 4. Commit

Use the project milestone/commit convention.

Example:

```powershell
git add .
git commit -m "M1-C3.2.2 Verify automatic production deployment"
```

### 5. Push

```powershell
git push origin main
```

A push to `main` is the production deployment trigger.

### 6. GitHub Actions

GitHub automatically runs:

```text
Deploy JasonRKelly.com
```

The workflow connects to the Code Support hosting server using a dedicated SSH deployment key.

The workflow then updates the production repository using Git.

### 7. Verify production

After the GitHub Action completes successfully, verify the change on:

```text
https://jasonrkelly.com
```

No manual `git pull` on the hosting server should be required during normal deployment.

---

# Hosting Environment

Hosting provider:

```text
Code Support
```

Hosting server:

```text
server270.com
```

Hosting account:

```text
jasonrke
```

Production Git working directory:

```text
/home/jasonrke/www/builder
```

Production branch:

```text
main
```

GitHub remote:

```text
https://github.com/JokersJason/jasonrkelly-site.git
```

The hosting repository's local `main` branch tracks:

```text
origin/main
```

---

# GitHub Actions Deployment

Workflow:

```text
.github/workflows/deploy.yml
```

The workflow runs when changes are pushed to:

```text
main
```

The deployment performs the equivalent of:

```bash
cd /home/jasonrke/www/builder
git fetch origin
git checkout main
git pull --ff-only origin main
```

`--ff-only` is intentional.

It prevents the production server from automatically creating merge commits if the server and GitHub histories unexpectedly diverge.

---

# GitHub Repository Secrets

The GitHub Actions workflow uses repository secrets for deployment.

Required secrets:

```text
HOST_NAME
HOST_USER
HOST_PATH
SSH_PRIVATE_KEY
```

Purpose:

| Secret | Purpose |
|---|---|
| `HOST_NAME` | Code Support SSH hostname |
| `HOST_USER` | Hosting SSH account |
| `HOST_PATH` | Production repository path |
| `SSH_PRIVATE_KEY` | Dedicated GitHub Actions deployment private key |

Never commit the private deployment key, passwords, tokens, or GitHub secrets into the repository.

---

# Deployment SSH Key

A dedicated ED25519 SSH keypair was created specifically for GitHub Actions deployment.

The deployment key is separate from Jason's normal personal GitHub SSH identity.

Architecture:

```text
GitHub Actions
    │
    │ Dedicated private deployment key
    ↓
Code Support SSH
    │
    │ Matching public key
    ↓
~/.ssh/authorized_keys
```

The server-side SSH permissions should remain:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

The private key must remain protected.

If the deployment key is ever suspected of being exposed:

1. Remove its public key from `~/.ssh/authorized_keys`.
2. Generate a new deployment keypair.
3. Install the new public key on the hosting server.
4. Replace `SSH_PRIVATE_KEY` in GitHub Actions secrets.
5. Test the deployment pipeline.

---

# Manual Deployment Fallback

If GitHub Actions is unavailable but GitHub and the hosting server are functioning, deployment can be performed manually.

SSH into the hosting server.

Then:

```bash
cd /home/jasonrke/www/builder
git status
git fetch origin
git pull --ff-only origin main
git status
```

Expected final state:

```text
On branch main
Your branch is up to date with 'origin/main'.

nothing to commit, working tree clean
```

Then verify the public website.

---

# Troubleshooting

## GitHub Action is green, but site did not change

On the hosting server:

```bash
cd /home/jasonrke/www/builder
git status
git log --oneline --decorate -5
```

Compare the latest production commit with GitHub `main`.

---

## Hosting repository is behind GitHub

Run:

```bash
git fetch origin
git status
```

If the repository is clean and simply behind:

```bash
git pull --ff-only origin main
```

---

## `git fetch` references a deleted branch

The hosting repository was previously configured to fetch only a feature branch.

The correct fetch configuration is:

```bash
git config remote.origin.fetch "+refs/heads/*:refs/remotes/origin/*"
```

Then:

```bash
git fetch --prune
```

This removes stale remote references and allows the server to retrieve current GitHub branches.

---

## Local `main` does not track `origin/main`

On the hosting server:

```bash
git branch --set-upstream-to=origin/main main
```

Verify:

```bash
git status
```

---

## Production working tree contains unexpected changes

Do not immediately run `git pull`.

First:

```bash
git status
git diff
```

Determine why production files were modified.

The production server should normally receive changes from GitHub rather than being edited directly.

---

# Security and Change-Control Notes

`main` currently represents production.

Therefore:

```text
push to main = production deployment
```

Treat pushes to `main` as production changes.

For larger future changes, the preferred workflow is:

```text
Feature Branch
      ↓
Local Testing
      ↓
Push Feature Branch
      ↓
Pull Request
      ↓
Review / Validation
      ↓
Merge to main
      ↓
Automatic Production Deployment
```

Branch protection for `main` should be considered as the project matures.

Direct modification of production files on the Code Support server should be avoided.

GitHub should remain the authoritative source for the website.

---

# Deployment Milestone History

## M1-C3.1 — Manual Deployment

Validated the complete manual deployment path:

```text
VS Code
  ↓
GitHub
  ↓
Manual git pull
  ↓
Code Support
  ↓
JasonRKelly.com
```

During testing:

- Hosting repository branch tracking was corrected.
- A stale `feature/initial-static-site` fetch reference was removed.
- `main` was configured to track `origin/main`.
- Manual `git pull --ff-only` deployment was successfully validated.

Status: **Complete**

---

## M1-C3.2 — Automatic Deployment

Implemented GitHub Actions deployment using a dedicated SSH deployment key.

### M1-C3.2.1

Created and successfully executed:

```text
.github/workflows/deploy.yml
```

GitHub Actions successfully authenticated to Code Support and executed the deployment.

Status: **Complete**

### M1-C3.2.2

Performed a visible production change and pushed it to GitHub.

The change appeared on JasonRKelly.com without manually accessing the Code Support SSH terminal or running `git pull`.

This validated the complete automatic production pipeline:

```text
VS Code
  ↓
GitHub main
  ↓
GitHub Actions
  ↓
SSH
  ↓
Code Support
  ↓
JasonRKelly.com
```

Status: **Complete**

---

# Current Deployment State

As of M1-C3.2:

- Local Git development is operational.
- GitHub is the authoritative repository.
- Code Support hosts the production website.
- Production tracks GitHub `main`.
- GitHub Actions automatically deploys pushes to `main`.
- Dedicated SSH authentication is operational.
- Manual deployment remains available as a recovery path.

**M1-C3 automatic production deployment pipeline: OPERATIONAL**