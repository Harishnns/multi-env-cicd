Project 14 — Multi-Environment CI/CD (GitHub Actions)

This project demonstrates a multi-environment CI/CD pipeline using GitHub Actions, with AI-assisted (ChatGPT) workflow authoring, matrix builds, and a manual approval gate for staging. The pipeline runs tests on Ubuntu + Windows, packages a dummy artifact, auto-deploys to Dev, and deploys to Staging after approval. A CodeQL job (JavaScript only) is included as an optional AI-suggested security scan.

🔧 What’s in this repo
.
├─ hello.sh                      # simple script used by jobs
└─ .github/
   └─ workflows/
      ├─ ci-cd.yml              # CI/CD: test → package → deploy_dev → deploy_staging
      └─ codeql.yml             # (optional) security scan: CodeQL for JavaScript

🎯 Objectives

Configure CI to deploy to dev and staging

Use AI to suggest jobs (ChatGPT wrote/optimized the YAML; CodeQL added as an AI-suggested job)

Add safety gates (manual approval before staging)

Demonstrate matrix builds (Ubuntu + Windows)

🏗️ Pipeline Overview

Triggers

On push and pull_request to main

Jobs (ci-cd.yml)

test (matrix: ubuntu-latest, windows-latest)

Checks out code, runs hello.sh

package (needs: test)

Creates build.txt, uploads as artifact app

deploy_dev (needs: package, environment: dev)

Checks out code + downloads artifact, runs hello.sh

deploy_staging (needs: deploy_dev, environment: staging)

Manual approval required in GitHub → Environments → staging

After approval, downloads artifact, runs hello.sh

Security (codeql.yml)

Runs CodeQL (JavaScript) on push/PR to main

Reports findings under Security → Code scanning alerts

🔐 Safety Gate (Approvals)

Set up once in your repo:

Settings → Environments → New environment → dev (no approvals)

Settings → Environments → New environment → staging

Add Required reviewers (your user or team)

Now deploy_staging will pause until approved

🧪 Matrix Builds

The test job runs in parallel on Ubuntu and Windows:

strategy:
  fail-fast: false
  matrix:
    os: [ubuntu-latest, windows-latest]
runs-on: ${{ matrix.os }}


This simulates cross-platform testing (common in real CI).

🤖 AI Involvement

ChatGPT: Authored and refined ci-cd.yml (jobs, matrix, approvals)

(Optional) CodeQL: Added as an AI-suggested security scan job (codeql.yml)

If you don’t want CodeQL, delete .github/workflows/codeql.yml.

▶️ How to Run / Re-run

Automatic: Push or open a PR to main

Manual re-run: Actions → open a run → Re-run all jobs (or failed jobs)

(Optional) Add a manual trigger by including in ci-cd.yml:

on:
  workflow_dispatch:

🧭 What to Expect (Run Flow)
push/PR → test (Ubuntu & Windows) → package → deploy_dev → (approve) → deploy_staging


If test fails, later jobs are skipped

deploy_staging waits for approval (safety gate)

All jobs run on GitHub-hosted runners (cloud VMs) — no external servers needed

🗂️ Key Files (snippets)

hello.sh

#!/usr/bin/env bash
set -e
echo "Hello Everyone from CI/CD pipeline"


.github/workflows/ci-cd.yml
(Already in your message; includes matrix tests, packaging, dev & staging deploys with approval.)

.github/workflows/codeql.yml (optional)

JavaScript-only CodeQL scan; remove this file if not needed.

🧰 Troubleshooting

“hello.sh: No such file or directory”
Add actions/checkout@v4 in deploy jobs (already included).

Staging never runs
Approve deployment in Environments → staging prompt in the run.

CodeQL fails
Ensure the matrix contains only 'javascript' and at least one .js file exists (even a tiny app.js).

✅ Status Checklist

 Matrix tests (Ubuntu + Windows)

 Artifact packaging (build.txt → app)

 Auto deploy to dev

 Approval gate for staging

 AI involvement (ChatGPT; optional CodeQL)

License

This project is for learning/demo purposes.
