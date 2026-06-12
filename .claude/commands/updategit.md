# /updategit

Publish the project to GitHub safely. Run every step in order; stop and report if any step fails.

## Step 1 — Security scan (MUST pass before any push)

Scan staged and unstaged files for secrets. Fail hard if any are found.

1. Check that `.gitignore` exists and blocks common secret files:
   - If missing entries for `.env`, `*.env`, `*.pem`, `*.key`, `*.p12`, `*.pfx`, `secrets.*`, add them.

2. Run a grep scan over all files git would include (`git ls-files` + untracked non-ignored files). Flag any line matching these patterns (case-insensitive):
   - `password\s*=\s*['"][^'"]{4,}`
   - `secret\s*=\s*['"][^'"]{4,}`
   - `api[_-]?key\s*=\s*['"][^'"]{4,}`
   - `token\s*=\s*['"][^'"]{4,}`
   - `private[_-]?key`
   - `BEGIN (RSA|EC|OPENSSH|PGP) PRIVATE KEY`
   - `AKIA[0-9A-Z]{16}` (AWS access key)
   - Hardcoded connection strings: `mongodb://`, `postgres://`, `mysql://` with credentials

3. Check that form action targets (e.g. formsubmit.co/ajax/...) only expose a **public** email address — not an internal API key or webhook secret embedded in the URL.

4. If any secrets are found: list each file and line, stop immediately, and tell the user to fix them before re-running `/updategit`.

5. If clean: print "Security scan passed — no secrets detected."

## Step 2 — README

Check whether `README.md` exists and is non-trivial (> 5 lines).

- **If missing or trivial**: generate a README that covers: project name & tagline, what the site does (features list), how to run locally, and how deploys work. Write it to `README.md`.
- **If it exists and looks complete**: leave it unchanged.

## Step 3 — GitHub Actions workflow for Pages

Check whether `.github/workflows/deploy.yml` (or any Pages deploy workflow) exists.

- **If missing**: create `.github/workflows/deploy.yml` with the standard GitHub Pages static-site workflow:
  - Trigger: `push` to `main` + `workflow_dispatch`
  - Permissions: `pages: write`, `id-token: write`, `contents: read`
  - Jobs: `actions/checkout@v4` → `actions/configure-pages@v5` → `actions/upload-pages-artifact@v3` (path: `.`) → `actions/deploy-pages@v4`
- **If it exists**: verify it has the correct permissions and leave it unchanged.

## Step 4 — Commit and push

1. Run `git status` to see what has changed.
2. Stage all modified/new project files **excluding** anything that should be secret (`.env`, keys, etc.). Prefer staging specific files by name over `git add -A`.
3. If there is nothing to commit, skip to Step 5.
4. Write a clear commit message describing what changed (use a heredoc to avoid shell quoting issues).
5. Push to `origin main`. If the remote doesn't exist yet, create the GitHub repo first:
   ```
   gh repo create <repo-name> --public --source=. --remote=origin --push
   ```
   Otherwise: `git push origin main`.

## Step 5 — Enable GitHub Pages

Check whether Pages is already enabled:
```
gh api repos/{owner}/{repo}/pages 2>&1
```

- **If not enabled (404)**: enable it with the workflow build type:
  ```
  gh api repos/{owner}/{repo}/pages --method POST -f build_type=workflow
  ```
- **If already enabled**: skip.

## Step 6 — Update repo About

Fetch the current repo description and homepage via:
```
gh repo view --json description,homepageUrl
```

Update if either is blank or stale:
```
gh repo edit --description "<concise one-line description of the project>" \
             --homepage "https://{owner}.github.io/{repo}/" \
             --add-topic "html" --add-topic "css" --add-topic "github-pages"
```

Derive the description from the README tagline or the project's purpose. Do not invent fictional detail.

## Step 7 — Report

Print a summary table:

| Step | Status | Detail |
|------|--------|--------|
| Security scan | ✓ / ✗ | files scanned, issues found |
| README | created / updated / unchanged | |
| Pages workflow | created / existed | |
| Commit & push | pushed / nothing to push | commit SHA |
| GitHub Pages | enabled / already active | URL |
| Repo About | updated / unchanged | description, URL |

End with the live Pages URL so the user can open it immediately.
