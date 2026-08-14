# Assignment 6 — Prove it

**Layer:** Reusable deploy workflow, rich `GITHUB_STEP_SUMMARY`, "Lessons Learned" slide, group showcase

**Time:** ~30 minutes + showcase

---

## Context

Every good CI/CD pattern you have learned today should now be visible in your pipeline. The final assignment extracts the deployment logic into a reusable workflow (so any future repo in your org could call it), generates a comprehensive run summary, and completes the slide deck with a reflection section.

At the end, each team presents their live URL to the group.

---

## Learning goals

- Extract a job into a reusable workflow using `workflow_call`
- Expose inputs, secrets, and outputs from a reusable workflow
- Generate a rich `GITHUB_STEP_SUMMARY` that serves as a run report

---

## Discovery first (5 minutes)

Before extracting anything, discuss this scenario:

Your org has 10 repositories with near-identical deploy jobs.

1. How many PRs do you open when a deploy bug is found?
2. How do you guarantee each repo receives the same fix?
3. What interface would a shared deploy workflow need?

Use your answers to design the reusable workflow contract.

---

## Steps

### 1. Create a reusable deploy workflow

Create `.github/workflows/deploy-pages.yml` as a reusable workflow.

Requirements:

- trigger type is `workflow_call`
- workflow exposes at least one input and one output
- workflow handles Pages deploy with correct least-privilege permissions
- output exposes the deployed URL (or equivalent deploy result)

### 2. Update `build.yml` to call the reusable workflow

Replace inline deploy logic in `build.yml` by calling the reusable workflow.

Requirements:

- main build job remains unchanged in purpose
- deploy waits for build completion
- input/secrets mapping is explicit enough for your team to reason about

Checkpoint: discuss when to replace `secrets: inherit` with explicit secret mapping.

### 3. Add a rich build summary to the build job

At the end of the build job, write a rich run report to `GITHUB_STEP_SUMMARY`.

Must include at least:

- repository and branch
- commit and triggering actor
- slide count (from your earlier output/action)
- expected live URL or deployed URL

### 4. Complete the slide deck — Lessons Learned

Add the final section to `slides.md`. This slide is your team's reflection:

Requirements:

- section title is `Lessons Learned`
- includes what you would change next time
- includes one surprise and one transfer-to-day-job insight

Fill in the bullets with your team's genuine reflections from the day.

### 5. Final push and verify

1. Merge everything to `main`
2. Confirm the reusable deploy workflow runs and completes
3. Open the live Pages URL and verify all six slide sections are present
4. Open the workflow run summary and confirm the build report appears

---

## Showcase (5 minutes per team)

Prepare to present:

1. **Your live URL** — open the deck in the browser
2. **One workflow run** — walk through the job graph and point to the summary
3. **One design decision** — explain why you made it (e.g. why you pinned to SHA, why you chose a concurrency group)
4. **One thing you would do differently** — honest reflection

---

## Deliverables

- ✅ `.github/workflows/deploy-pages.yml` as a reusable `workflow_call` workflow
- ✅ `build.yml` calling the reusable deploy workflow
- ✅ A rich `GITHUB_STEP_SUMMARY` with repo, branch, commit, actor, slide count, and live URL
- ✅ "Lessons Learned" section in `slides.md` with genuine team reflections
- ✅ Live Pages URL with all six content sections visible

---

## Final pipeline checklist

Before the showcase, verify your pipeline has all six layers:

| Layer | Evidence |
|---|---|
| Build on push | `build.yml` triggers on `main` push |
| PR validation | `validate.yml` triggers on pull_request |
| Custom action | `.github/actions/count-slides/action.yml` |
| Cache + lint + artifact | Cache key on lockfile; markdownlint step; upload-artifact |
| Security | SHA pins; permissions scoping; required reviewer on production |
| Reusable workflow | `deploy-pages.yml` called via `workflow_call` |
