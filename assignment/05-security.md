# Assignment 5 — Lock it down

**Layer:** `permissions:` scoping, SHA pinning, Dependabot, `CODEOWNERS`, environment protection with required reviewer

**Time:** ~45 minutes (Part A: 20 min, Part B: 25 min)

---

## Context

Your pipeline works and is reasonably fast. But right now, any push to `main` deploys automatically to a live URL — with no human check. Third-party actions run at their latest tag version, not a pinned commit. And the workflow has more permissions than it needs.

This assignment hardens the pipeline against the most common security mistakes in GitHub Actions.

---

## Discovery first (5 minutes)

Inspect one existing workflow before editing:

1. Which third-party actions are pinned to mutable tags?
2. Which token permissions are broader than needed?
3. Where can an unreviewed deploy still reach production?

Write one concrete risk per category.

---

## Learning goals

- Restrict `GITHUB_TOKEN` to the minimum permissions per workflow
- Pin all `uses:` references to a full commit SHA
- Configure Dependabot to keep action pins up to date
- Protect the `production` environment with a required reviewer
- Use `CODEOWNERS` to require review on workflow changes

---

## Part A — Runtime hardening (20 min)

### 1. Scope permissions on all workflows

Add explicit `permissions:` blocks. Remove anything not needed.

Requirements:

- each workflow declares permissions explicitly
- `validate` stays read-only unless you can justify extra scopes
- `build/deploy` includes only scopes required for Pages deployment

Checkpoint: if this workflow only reads and validates, which extra permission would be risky to keep?

### 2. Pin all third-party actions to a SHA

Find the current SHA for each action you use:

Requirements:

- replace mutable tags with full commit SHAs for all third-party actions
- keep a human-readable version comment next to each SHA pin
- verify every workflow file (including reusable workflows)
- verify every third-party `uses:` reference is pinned, not just the obvious setup/build steps

Checkpoint: explain why `@v4` is easier to maintain but weaker for supply-chain control.

---

## Part B — Governance and deployment gates (25 min)

### 3. Add Dependabot for GitHub Actions

Create `.github/dependabot.yml` for the `github-actions` ecosystem.

Requirements:

- scheduled updates (at least weekly)
- predictable commit message prefix
- one label for dependency update PRs

Dependabot will open PRs automatically when new versions of pinned actions are released.

### 4. Protect `.github/` with CODEOWNERS

Create `.github/CODEOWNERS` to require review on workflow and action changes.

Requirements:

- include coverage for `.github/`
- assign at least one valid owner in your org/user namespace

In **Settings → Branches → Branch protection rules** for `main`:
- Enable **Require a pull request before merging**
- Enable **Require review from Code Owners**

### 5. Add an environment with required review

In **Settings → Environments → New environment**: name it `production`.

- Add yourself as a **Required reviewer**
- Set **Deployment branches** to `main` only

Update your deploy job so it targets this protected environment.

Requirements:

- deployment references `production`
- run pauses for approval when required reviewer policy applies
- action pinning policy is still respected in deploy steps

### 6. Trigger a deployment and approve it

Push a change to `main`. Watch the deploy job **pause** at the environment gate. Go to **Actions → the run → Review deployments** and approve it.

### 7. Add a new slide section

Add a new `Security in CI/CD` section to `slides.md` in your own words.

Include:

- least privilege rationale
- supply-chain integrity rationale
- why human gates may still be needed even with automation

---

## Deliverables

- ✅ All workflows have explicit `permissions:` blocks
- ✅ All `uses:` references pinned to a full SHA with a version comment
- ✅ `.github/dependabot.yml` configured for `github-actions` ecosystem
- ✅ `.github/CODEOWNERS` protecting `.github/`
- ✅ `production` environment with at least one required reviewer
- ✅ A deployment that required and received approval before running
- ✅ New slide section "Security in CI/CD" in `slides.md`

---

## Stretch goals

- Add a `wait-timer` to the production environment (e.g. 5 minutes) so there is always a window to cancel
- Review the default `permissions:` in your org settings and tighten the org-level default to `contents: read`
- Add a step that uses `gh api` to verify the deploying actor matches an allowed list
