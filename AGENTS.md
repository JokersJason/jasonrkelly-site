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