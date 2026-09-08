# JasonRKelly.com Deployment & Development Runbook

JasonRKelly.com is a no-build static website managed through Git, GitHub, VS Code, Codex, GitHub Pull Requests, and GitHub Actions.

The repository uses a controlled development workflow in which normal development occurs on feature branches and approved changes are merged into `main`.

`main` is the deployment branch. The current deployment destinations are UAT: `builder.jasonrkelly.com` for the full site and `brk-uat.jasonrkelly.com` for the BRK archive. `brk.jasonrkelly.com` is reserved for future production and QR use; this workflow does not publish there.

---

# Current Development Architecture

The normal development and deployment path is:

```text
Jason defines requirement
        ↓
jAIson helps plan / troubleshoot
        ↓
Feature branch
        ↓
Jason and/or Codex implements locally
        ↓
Local review and testing
        ↓
Commit
        ↓
Push feature branch
        ↓
GitHub Pull Request
        ↓
Jason reviews / approves
        ↓
Merge into main
        ↓
GitHub Actions
        ↓
SSH
        ↓
Code Support Hosting
        ↓
builder.jasonrkelly.com
```

The core responsibility model is:

```text
Jason
  decides and approves

jAIson
  plans, explains, and troubleshoots

Codex
  implements within repository guardrails

GitHub
  controls source history and Pull Requests

GitHub Actions
  deploys approved changes

Code Support
  hosts the deployed website
```

---

# Repository Files

Primary repository files include:

- `index.html` — page content and structure
- `assets/styles.css` — visual styling
- `assets/site.js` — mobile navigation and current-year footer
- `.github/workflows/deploy.yml` — GitHub Actions deployment workflow
- `AGENTS.md` — repository instructions and AI development guardrails
- `README.md` — project documentation
- `README_DEPLOY.md` — development, deployment, and recovery runbook

No framework, package manager, PHP, database, or build process is currently required.

---

# Local Repository

Primary Windows development location:

```text
C:\Projects\jasonrkelly-site
```

Development and Git operations are normally performed through VS Code and PowerShell.

Before beginning new work, start from `main`.

```powershell
git switch main
git pull --ff-only
git status
```

Expected result:

```text
On branch main
Your branch is up to date with 'origin/main'.

nothing to commit, working tree clean
```

---

# Understanding `--ff-only`

The command:

```powershell
git pull --ff-only
```

should be read as:

```text
Git pull — fast-forward only
```

`ff` means `fast-forward`.

This allows Git to move the current branch forward when the local history is directly behind GitHub.

Example:

```text
Local:

A -- B -- C

GitHub:

A -- B -- C -- D -- E
```

Git can safely fast-forward local `main` to:

```text
A -- B -- C -- D -- E
```

If the local and remote histories have diverged, `--ff-only` stops instead of automatically creating a merge.

This makes unexpected repository divergence visible before Git changes history.

---

# Local Preview

From PowerShell inside the repository:

```powershell
python -m http.server 8080
```

Then open:

```text
http://127.0.0.1:8080/
```

Leave the Python process running while using the local preview.

Stop it with:

```text
Ctrl+C
```

If the browser reports:

```text
127.0.0.1 refused to connect
```

the local HTTP server has probably stopped.

Restart it with:

```powershell
python -m http.server 8080
```

This does not indicate that the hosted website is unavailable.

---

# Standard Development Workflow

Normal development should not be performed directly on `main`.

The standard workflow is:

```text
main
 ↓
feature branch
 ↓
local development
 ↓
local review
 ↓
local testing
 ↓
commit
 ↓
push feature branch
 ↓
Pull Request
 ↓
review
 ↓
merge into main
 ↓
automatic deployment
```

## 1. Start from `main`

```powershell
git switch main
git pull --ff-only
git status
```

Make sure the working tree is clean.

---

## 2. Create a feature branch

Branch format:

```text
feature/<short-name>
```

Example:

```powershell
git switch -c feature/m1-c4-closeout
```

Important distinction:

```powershell
git switch -c feature/name
```

means:

```text
CREATE a new branch and switch to it
```

while:

```powershell
git switch feature/name
```

means:

```text
SWITCH to an existing branch
```

The `-c` means `create`.

---

## 3. Make the change

Changes may be made manually by Jason or implemented with assistance from Codex.

Codex should operate according to the rules in:

```text
AGENTS.md
```

Codex should not bypass the feature-branch or Pull Request workflow.

---

## 4. Save files

Before using Git, make sure files modified in VS Code are saved.

Use:

```text
Ctrl+S
```

Git operates on the files saved to disk.

An editor may visually contain a change that Git cannot see until the file is saved.

This behavior was specifically encountered and validated during M1-C4 Codex testing.

---

## 5. Review the change

First:

```powershell
git status
```

Then inspect the actual content changes:

```powershell
git diff
```

For a summary:

```powershell
git diff --stat
```

The human review should confirm:

- only intended files changed
- no unexpected deletions occurred
- no secrets or credentials were added
- the change matches the requirement

---

## 6. Test locally

Start the local web server:

```powershell
python -m http.server 8080
```

Open:

```text
http://127.0.0.1:8080/
```

Verify the website behaves as expected.

Stop the server when finished:

```text
Ctrl+C
```

---

## 7. Stage the intended files

For one specific file:

```powershell
git add index.html
```

or:

```powershell
git add AGENTS.md
```

For multiple intentional changes:

```powershell
git add .
```

Then verify:

```powershell
git status
```

Files under:

```text
Changes to be committed
```

will be included in the next commit.

---

## 8. Commit

Use the project milestone / commit convention.

Example:

```powershell
git commit -m "M1-C4.5 Define development responsibility model"
```

Project reference hierarchy:

```text
Milestone
└── Commit Group
    └── Sub-commit
```

Examples:

```text
M1
M1-C4
M1-C4.4
M1-C4.4.3
```

---

## 9. Push the feature branch

The first time a new feature branch is pushed:

```powershell
git push -u origin feature/<branch-name>
```

Example:

```powershell
git push -u origin feature/m1-c4-closeout
```

The `-u` establishes the upstream branch relationship.

After that, additional commits on the same branch can normally be pushed with:

```powershell
git push
```

A feature-branch push should not trigger the production deployment workflow.

---

# Pull Request Workflow

Pull Requests are a GitHub feature rather than a basic Git feature.

The current learning workflow uses the GitHub web interface for Pull Request review and merge operations.

Create a Pull Request with:

```text
base: main
compare: feature/<branch-name>
```

Read this as:

```text
Bring the feature branch INTO main
```

Creating a Pull Request does not modify `main`.

Creating a Pull Request does not deploy the website.

---

## Pull Request Review

Review:

```text
Commits
Files changed
Diff
```

GitHub diff notation:

```text
- red line
```

means the old content.

```text
+ green line
```

means the proposed new content.

Before merging, verify:

- expected commits are present
- expected files are present
- no unexpected files are included
- no conflicts exist
- the diff matches the intended change
- local validation was completed

---

## Merge

After review:

```text
Merge pull request
```

then:

```text
Confirm merge
```

The merge places the approved feature-branch changes into:

```text
main
```

Because GitHub Actions watches `main`, the merge triggers the automatic deployment pipeline.

The important rule is:

```text
Push feature branch
≠ production deployment

Merge approved PR into main
= UAT deployment trigger
```

---

# Post-Merge Cleanup

After a Pull Request is merged, synchronize the local repository.

```powershell
git switch main
git pull --ff-only
git status
```

Then delete the completed local feature branch:

```powershell
git branch -d feature/<branch-name>
```

Delete the GitHub remote feature branch:

```powershell
git push origin --delete feature/<branch-name>
```

Verify:

```powershell
git status
git branch
```

Expected final state:

```text
On branch main
Your branch is up to date with 'origin/main'.

nothing to commit, working tree clean
```

and:

```text
* main
```

---

# Git Cheat Sheet

| Command | Meaning |
|---|---|
| `git status` | Show current branch and working-tree state |
| `git branch` | Show local branches |
| `git switch main` | Switch to `main` |
| `git switch feature/name` | Switch to an existing feature branch |
| `git switch -c feature/name` | Create and switch to a new feature branch |
| `git pull --ff-only` | Update branch only when Git can safely fast-forward |
| `git diff` | Show unstaged content differences |
| `git diff --stat` | Show summary of changed files |
| `git add file` | Stage a specific file |
| `git add .` | Stage all current intended changes |
| `git commit -m "message"` | Create a commit |
| `git commit --amend --no-edit` | Replace the most recent local commit while keeping its message |
| `git push -u origin branch` | First push and establish upstream tracking |
| `git push` | Push later commits on a tracked branch |
| `git branch -d branch` | Delete a merged local branch |
| `git push origin --delete branch` | Delete a remote GitHub branch |
| `git fetch --prune` | Fetch remote state and remove stale remote references |
| `git log --oneline --decorate -5` | Show recent commit history |

---

# Codex Development Workflow

Codex is integrated into VS Code as a repository-aware development assistant.

Codex should read and follow:

```text
AGENTS.md
```

before performing repository work.

The validated workflow is:

```text
Jason defines requirement
        ↓
jAIson helps design approach
        ↓
Feature branch created
        ↓
Codex reads AGENTS.md
        ↓
Codex implements scoped change
        ↓
Jason reviews git diff
        ↓
Jason tests locally
        ↓
Jason stages / commits
        ↓
Feature branch pushed
        ↓
Pull Request
        ↓
Jason reviews and approves
        ↓
Merge to main
        ↓
GitHub Actions deploys
        ↓
Hosted site verified
```

---

# Codex Guardrails

Codex may:

- inspect repository files
- propose code changes
- edit files on a feature branch
- explain implementation choices
- review diffs
- troubleshoot development issues

Codex should not:

- bypass the feature-branch workflow
- perform normal development directly on `main`
- merge Pull Requests
- deploy directly to production
- modify GitHub deployment secrets
- modify SSH keys or credentials
- delete branches without explicit instruction
- perform destructive Git actions without explicit approval

Jason remains the final reviewer and merge authority.

---

# Development Responsibility Model

## Jason

Jason is responsible for:

- defining requirements
- deciding what should change
- approving implementation direction
- reviewing changes
- validating results
- reviewing Pull Requests
- approving merges into `main`
- acting as final authority for production changes

---

## jAIson

jAIson is responsible for:

- architecture and workflow planning
- explaining Git and GitHub concepts
- explaining deployment concepts
- troubleshooting development and deployment problems
- helping define implementation approaches
- maintaining milestone and commit structure
- helping review technical decisions

jAIson does not directly authorize production deployment.

---

## Codex

Codex is responsible for:

- repository-aware implementation
- inspecting relevant files
- implementing approved changes
- refactoring when requested
- reviewing diffs
- explaining implementation choices
- troubleshooting scoped repository issues

Codex operates within the controls defined in `AGENTS.md`.

---

## GitHub

GitHub is responsible for:

- acting as the authoritative source repository
- branch history
- feature branches
- Pull Requests
- diff review
- merge history
- recording changes to `main`

---

## GitHub Actions

GitHub Actions is responsible for:

- detecting changes to `main`
- executing the deployment workflow
- authenticating to Code Support with the dedicated deployment identity
- updating the hosted Git repository

---

## Code Support

Code Support is responsible for:

- hosting the website
- serving deployed website content
- receiving approved deployment updates

Production files should not normally be modified directly on the hosting server.

---

# GitHub Actions Deployment

Workflow:

```text
.github/workflows/deploy.yml
```

The workflow runs when changes are pushed or merged into:

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

`--ff-only` prevents the UAT hosting repository from automatically creating merge commits if histories unexpectedly diverge.

After that existing builder update succeeds, the same SSH session publishes the **contents** of `archives/brk/` from the updated checkout to the existing `/home/jasonrke/www/brk-uat/` document root using `rsync -r --checksum`. For example, `archives/brk/index.html` becomes `www/brk-uat/index.html`, and `archives/brk/assets/` becomes `www/brk-uat/assets/`. No extra `archives/brk/` directory is created in the target.

The remote script stops on errors. Before copying, it verifies the canonical source and target paths, the three BRK foundation files, and an existing writable target directory. It rejects symlinks in either tree to avoid copying or writing outside the intended roots. The server must provide `realpath`, `find`, and `rsync`; a missing utility or failed check fails the workflow. The workflow does not create the target directory or change document-root configuration.

The BRK copy reuses the existing SSH deployment identity and secrets. It does not delete target-only files, so removing a published file from Git does not remove its BRK UAT copy. A failed BRK publish fails the workflow but does not roll back the completed builder update; a copy failure may leave BRK UAT partially updated.

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

Hosting Git working directory:

```text
/home/jasonrke/www/builder
```

UAT deployment branch:

```text
main
```

GitHub remote:

```text
https://github.com/JokersJason/jasonrkelly-site.git
```

The hosting repository's local `main` tracks:

```text
origin/main
```

Main-site UAT (existing deployment target, unchanged):

```text
https://builder.jasonrkelly.com
```

BRK UAT:

```text
https://brk-uat.jasonrkelly.com
/home/jasonrke/www/brk-uat/
```

Only `archives/brk/` contents are published to this isolated document root. `https://brk.jasonrkelly.com/` remains reserved for future production and printed QR codes.

---

# GitHub Repository Secrets

The GitHub Actions workflow uses repository secrets.

Required deployment secrets include:

```text
HOST_NAME
HOST_USER
HOST_PATH
SSH_PRIVATE_KEY
```

| Secret | Purpose |
|---|---|
| `HOST_NAME` | Code Support SSH hostname |
| `HOST_USER` | Hosting SSH account |
| `HOST_PATH` | Hosted repository path |
| `SSH_PRIVATE_KEY` | Dedicated GitHub Actions deployment private key |

Never commit:

- SSH private keys
- passwords
- access tokens
- GitHub secrets
- credentials

into the repository.

---

# Deployment SSH Key

A dedicated ED25519 SSH keypair was created specifically for GitHub Actions deployment.

The deployment identity is separate from Jason's normal GitHub SSH identity.

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

Server permissions should remain:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

If the deployment key is suspected of exposure:

1. Remove its public key from `~/.ssh/authorized_keys`.
2. Generate a new deployment keypair.
3. Install the new public key on the hosting server.
4. Replace `SSH_PRIVATE_KEY` in GitHub repository secrets.
5. Test the deployment pipeline.

---

# Manual Deployment Fallback

If GitHub Actions is unavailable but GitHub and Code Support remain functional, deployment can be performed manually.

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

Then verify the hosted website. This manual Git-only fallback updates builder UAT only; it does not perform the separate BRK UAT copy described above.

---

# Troubleshooting

## GitHub Action is green, but site did not change

On the hosting server:

```bash
cd /home/jasonrke/www/builder
git status
git log --oneline --decorate -5
```

Compare the latest hosted commit with GitHub `main`.

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

The hosting repository was previously configured to retrieve only an old feature branch.

Correct fetch configuration:

```bash
git config remote.origin.fetch "+refs/heads/*:refs/remotes/origin/*"
```

Then:

```bash
git fetch --prune
```

This removes stale remote references and retrieves current branches.

---

## Local `main` does not track `origin/main`

Use:

```bash
git branch --set-upstream-to=origin/main main
```

Then:

```bash
git status
```

---

## Production working tree contains unexpected changes

Do not immediately run:

```bash
git pull
```

First inspect:

```bash
git status
git diff
```

Determine why production files changed.

The hosting server should normally receive changes from GitHub rather than direct manual editing.

---

## Git says a feature branch already exists

Example:

```text
fatal: a branch named 'feature/example' already exists
```

This usually means:

```powershell
git switch -c feature/example
```

was used on a branch that had already been created.

Remember:

```powershell
git switch -c feature/example
```

means:

```text
CREATE the branch
```

while:

```powershell
git switch feature/example
```

means:

```text
SWITCH to the existing branch
```

---

## VS Code shows a change but Git does not

Save the file:

```text
Ctrl+S
```

Then:

```powershell
git status
git diff
```

Git reads the saved file on disk.

---

## LF / CRLF warning

Windows Git may report:

```text
LF will be replaced by CRLF the next time Git touches it
```

This is normally a line-ending warning rather than a Git failure.

Inspect:

```powershell
git diff
```

to verify whether meaningful content changed.

---

# Security and Change-Control Rules

`main` is the deployment branch.

Therefore:

```text
change enters main
        ↓
GitHub Actions deployment
        ↓
hosted website changes
```

Normal development should use:

```text
Feature Branch
      ↓
Local Validation
      ↓
Push Feature Branch
      ↓
Pull Request
      ↓
Human Review
      ↓
Merge to main
      ↓
Automatic Deployment
```

Direct modification of hosted production files should be avoided.

GitHub remains the authoritative source for the website.

Branch protection for `main` may be added in a future milestone to technically enforce the workflow already being followed operationally.

---

# Milestone History

## M1-C3.1 — Manual Deployment

Validated:

```text
VS Code
  ↓
GitHub
  ↓
Manual git pull
  ↓
Code Support
  ↓
Hosted website
```

During testing:

- hosting repository branch tracking was corrected
- stale `feature/initial-static-site` fetch configuration was removed
- `main` was configured to track `origin/main`
- manual `git pull --ff-only` deployment was validated

Status: **COMPLETE**

---

## M1-C3.2 — Automatic Deployment

Implemented GitHub Actions deployment using a dedicated SSH deployment identity.

### M1-C3.2.1

Created and successfully executed:

```text
.github/workflows/deploy.yml
```

GitHub Actions successfully authenticated to Code Support and executed deployment.

Status: **COMPLETE**

### M1-C3.2.2

Performed a visible website change and pushed it through the automated deployment pipeline.

Validated:

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
Hosted website
```

Status: **COMPLETE**

---

# M1-C4 — Controlled Development Workflow

M1-C4 introduced a structured development lifecycle around the automatic deployment pipeline.

---

## M1-C4.1 — Feature Branch Isolation

Created and validated feature-branch development.

Test change:

```text
[FEATURE TEST]
```

The test was:

- visible locally
- pushed to the GitHub feature branch
- not deployed by GitHub Actions
- absent from the hosted website until merged

This proved:

```text
feature branch
≠ production
```

Status: **COMPLETE**

---

## M1-C4.2 — Pull Request Workflow

Validated:

```text
Feature Branch
      ↓
Pull Request
      ↓
Review
      ↓
Merge
      ↓
main
      ↓
GitHub Actions
      ↓
Hosted website
```

The Pull Request review process successfully isolated proposed changes until human approval.

Status: **COMPLETE**

---

## M1-C4.3 — Workflow Documentation Foundation

Introduced formal documentation and Git learning references supporting the controlled development workflow.

Status: **COMPLETE**

---

## M1-C4.4 — Codex Integration

Installed the official OpenAI Codex VS Code extension.

Created:

```text
AGENTS.md
```

Codex successfully read and summarized the repository guardrails.

A controlled Codex change was then performed:

```text
[FEATURE TEST]
```

was changed to:

```text
[CODEX TEST]
```

Codex:

- worked on a feature branch
- modified only the requested file
- did not commit
- did not push
- did not merge
- did not deploy
- correctly requested clarification when the existing content differed from the original instruction

Jason:

- reviewed `git diff`
- tested locally
- committed the approved change
- pushed the feature branch
- reviewed the Pull Request
- approved the merge
- verified automatic deployment

Status: **COMPLETE**

---

## M1-C4.5 — Development Responsibility Model

Expanded `AGENTS.md` to formally define responsibilities for:

```text
Jason
jAIson
Codex
GitHub
GitHub Actions
Code Support
```

Jason remains final approval and production-change authority.

Status: **COMPLETE**

---

## M1-C4.6 — End-to-End Controlled Workflow Validation

The complete workflow was successfully demonstrated during the Codex integration test:

```text
Requirement
   ↓
Planning
   ↓
Feature Branch
   ↓
Codex Implementation
   ↓
Human Diff Review
   ↓
Local Testing
   ↓
Commit
   ↓
Push Feature Branch
   ↓
Pull Request
   ↓
Human Approval
   ↓
Merge
   ↓
GitHub Actions
   ↓
Code Support
   ↓
Hosted Website
   ↓
Production Verification
   ↓
Branch Cleanup
```

Status: **COMPLETE**

---

## M1-C4.7 — Workflow Documentation and Closeout

Updated this runbook to reflect the development process actually tested during M1-C4.

Documentation now includes:

- feature-branch workflow
- Pull Request workflow
- Git command cheat sheet
- `--ff-only` explanation
- Codex integration
- Codex guardrails
- responsibility model
- local testing workflow
- post-merge cleanup
- deployment recovery
- M1-C4 milestone history

Status: **COMPLETE upon merge of this documentation update**

---

# Current Project State

As of M1-C4.7:

- Local Git development is operational.
- VS Code is the primary local development environment.
- Codex is integrated into VS Code.
- `AGENTS.md` provides persistent repository-level AI instructions.
- GitHub is the authoritative source repository.
- Normal development uses feature branches.
- Pull Requests provide the human review gate.
- Jason remains final merge authority.
- `main` is the deployment branch.
- GitHub Actions automatically deploys changes entering `main`.
- Dedicated SSH deployment authentication is operational.
- Code Support hosts the deployed site.
- Manual deployment remains available as a recovery mechanism.
- End-to-end Codex-assisted development has been successfully validated.

**M1-C3 AUTOMATIC DEPLOYMENT PIPELINE: OPERATIONAL**

**M1-C4 CONTROLLED DEVELOPMENT WORKFLOW: OPERATIONAL**
