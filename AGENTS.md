# JasonRKelly.com Repository Instructions

## Project

This repository contains the static website for JasonRKelly.com.

## Development Workflow

Production branch: `main`

Normal development must use feature branches.

Feature branch format: `feature/<short-name>`

Do not make normal development changes directly on `main`.

Expected workflow:

main
→ feature branch
→ local changes
→ review diff
→ commit
→ push feature branch
→ pull request
→ review
→ merge to main
→ automatic production deployment

## Git Naming Convention

Project work uses this hierarchy:

Milestone
└── Commit Group
    └── Sub-commit

Examples:

M1-C4
M1-C4.1
M1-C4.4
M1-C4.4.2

Commit messages should begin with the relevant reference when one exists.

Example:

M1-C4.4.2 Add Codex repository instructions

## Safety Rules

- Do not push directly to `main`.
- Do not merge pull requests automatically.
- Do not delete branches unless explicitly instructed.
- Do not modify GitHub Actions deployment secrets.
- Do not modify SSH keys or credentials.
- Do not edit production files directly on the hosting server.
- GitHub is the source of truth.
- Changes to `main` trigger production deployment.

## Website Architecture

This is a no-build static website.

Primary files:

- index.html
- assets/styles.css
- assets/site.js
- .github/workflows/deploy.yml
- README.md
- README_DEPLOY.md

No framework, package manager, PHP, database, or build system is currently required.

## Codex Role

Codex may:

- Inspect repository files.
- Propose code changes.
- Edit files on a feature branch.
- Explain implementation choices.
- Review diffs.
- Help troubleshoot issues.

Codex should not:

- Bypass the feature branch workflow.
- Merge changes into `main`.
- Deploy directly to production.
- Alter credentials or secrets.
- Make destructive Git changes without explicit approval.

Jason remains the final reviewer and approves merges into `main`.
## Development Responsibility Model

This repository uses a defined responsibility model across Jason, jAIson, Codex, GitHub, GitHub Actions, and the hosting environment.

### Jason

Jason is responsible for:

- Defining requirements and desired outcomes.
- Deciding what should be changed.
- Approving implementation direction.
- Performing or approving local validation.
- Reviewing pull requests.
- Approving merges into `main`.
- Acting as the final authority for production changes.

### jAIson

jAIson is responsible for:

- Architecture and workflow planning.
- Explaining Git, GitHub, deployment, and development concepts.
- Troubleshooting development and deployment issues.
- Helping define implementation approaches.
- Maintaining milestone and commit structure.
- Helping review technical decisions before implementation.

jAIson does not directly authorize production deployment.

### Codex

Codex is responsible for:

- Inspecting repository files.
- Implementing approved changes on feature branches.
- Refactoring code when requested.
- Explaining implementation choices.
- Reviewing diffs.
- Assisting with troubleshooting.
- Performing scoped repository-aware development tasks.

Codex must not:

- Work directly on `main` for normal development.
- Merge pull requests.
- Push directly to production.
- Modify secrets, credentials, or SSH keys.
- Delete branches without explicit instruction.
- Perform destructive Git operations without explicit approval.

### GitHub

GitHub is responsible for:

- Acting as the authoritative source repository.
- Maintaining branch history.
- Hosting feature branches.
- Managing pull requests.
- Providing code review and merge controls.
- Recording approved changes to `main`.

### GitHub Actions

GitHub Actions is responsible for:

- Detecting changes merged into `main`.
- Running the configured deployment workflow.
- Connecting to the hosting environment using the dedicated deployment identity.
- Updating the hosted repository.

GitHub Actions should deploy only from the approved production branch.

### Code Support Hosting

The Code Support hosting environment is responsible for:

- Hosting the deployed website.
- Serving the production website files.
- Receiving approved updates from the GitHub Actions deployment workflow.

Production files should not normally be edited directly on the hosting server.

## Standard Development Flow

The expected workflow is:

Jason defines the requirement
→ jAIson helps design the approach
→ feature branch is created
→ Codex or Jason implements locally
→ Jason reviews the diff
→ local validation is performed
→ changes are committed
→ feature branch is pushed
→ pull request is created
→ Jason reviews and approves
→ pull request is merged into `main`
→ GitHub Actions deploys
→ production is verified

The core rule is:

Jason decides and approves.
jAIson helps design and troubleshoot.
Codex implements within repository guardrails.
GitHub controls source and review.
GitHub Actions deploys approved changes.