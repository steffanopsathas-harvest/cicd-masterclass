# Assignment 2 — Control the gate

**Layer:** PR validation workflow, concurrency groups, manual trigger with input, README badge

**Time:** ~25 minutes

---

## Context

Your deck now deploys automatically on every push to `main`. The next problem: what if a pushed slide breaks the build? You want a safety net that catches broken builds *before* they reach `main` — and you want to avoid wasting runner minutes when multiple pushes arrive at once.

---

## Learning goals

- Trigger a workflow on pull requests without deploying
- Use path filters to run only when relevant files change
- Use concurrency groups to cancel stale workflow runs
- Surface build status in the repository README

---

## Discovery first (5 minutes)

Run this experiment before implementing the solution:

1. Push 2-3 small commits quickly to the same branch.
2. Observe the Actions queue behavior.
3. Discuss: should all runs finish, or should stale runs be cancelled?

Capture your team decision and reason in one sentence.

---

## Steps

### 1. Add a PR validation workflow

Create `.github/workflows/validate.yml` with these behaviors:

- runs on `pull_request`
- only runs when slide-related files change
- has a concurrency group per branch/ref
- cancels stale in-progress runs
- performs build validation only (no deploy)

Checkpoint:

- Describe how your `group` expression prevents unrelated branches from cancelling each other.

### 2. Add a manual redeploy trigger

Update `.github/workflows/build.yml` to support manual invocation with at least one typed input.

Requirements:

- keep push-to-main deployment behavior intact
- include one optional manual input that captures operator intent
- log that input only when manually triggered

### 3. Add a README status badge

Add a workflow badge to `README.md` for the main deploy workflow.

### 4. Test it

1. Create a branch: `git checkout -b feat/workflow-structure`
2. Add a new slide section titled `Workflow Triggers & Structure` with your own wording.

3. Commit and open a pull request.
4. Watch the `Validate PR` workflow run on the PR — it should build but not deploy.
5. Merge the PR and confirm the deploy workflow runs on `main`.

### 5. Reflect

As a team, answer:

1. When would you set `cancel-in-progress: false` instead?
2. Which files should be included in `paths:` for your real repo?

---

## Deliverables

- ✅ `.github/workflows/validate.yml` running on PRs (build only, no deploy)
- ✅ Concurrency group in the validate workflow
- ✅ `workflow_dispatch` with `reason` input on the build workflow
- ✅ README with a build status badge
- ✅ New slide section "Workflow Triggers & Structure" in `slides.md`

---

## Stretch goals

- Add a `paths-ignore:` filter to exclude changes to `README.md` from triggering the validate workflow
- Add `if: failure()` to a step that posts a comment on the PR when the build fails (requires `pull-requests: write` permission)
- Experiment with `cancel-in-progress: false` — observe how multiple pushes queue up
