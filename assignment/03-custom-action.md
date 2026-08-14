# Assignment 3 — Package the logic

**Layer:** Composite action that counts slides, exposes an output, and writes to `GITHUB_STEP_SUMMARY`

**Time:** ~25 minutes

---

## Context

Your validation workflow builds the deck on every PR. That is useful, but it tells you nothing about the content. You want to know: how many slides does this PR add or have in total? You will extract that logic into a reusable composite action so it can be used in any workflow without copy-pasting steps.

---

## Learning goals

- Create a composite action with `inputs`, `outputs`, and workflow commands
- Use `GITHUB_OUTPUT` to expose a value to the calling workflow
- Write a rich Markdown summary with `GITHUB_STEP_SUMMARY`

---

## Discovery first (5 minutes)

Open a recent `Validate PR` run and inspect the Summary tab.

Discuss:

1. What is missing for a reviewer who did not run the pipeline?
2. Which metrics would you want at a glance?
3. Which repeated logic should become reusable across workflows?

Then implement the composite action as the answer.

---

## Steps

### 1. Create the composite action

Create `.github/actions/count-slides/action.yml` as a composite action.

Requirements:

- expose one configurable input for the slide file path
- expose one output representing the calculated slide count
- compute the value inside a shell step
- append a markdown table to `GITHUB_STEP_SUMMARY`

Constraints:

- action must work when the input is omitted (sensible default)
- action must not fail hard when the target file is missing; return a safe fallback

### 2. Use the action in the validate workflow

Update `.github/workflows/validate.yml` so it uses the composite action.

Requirements:

- call the local action from the workflow
- assign an `id` to the step so outputs can be referenced
- add one quality gate that fails when slide count is clearly invalid
- keep validation flow as build-only (no deploy)

Checkpoint: explain in your own words why `GITHUB_OUTPUT` and `GITHUB_STEP_SUMMARY` solve different problems.

### 3. Add a new slide section

Add a new section to `slides.md` titled `Custom Actions` using your own wording.

Must include:

- what composite actions are good for
- at least one contrast with JavaScript or Docker actions
- one practical team use case from your own context

### 4. Open a PR and inspect the summary

After pushing the branch and opening a PR:

1. Go to **Actions** → the `Validate PR` run
2. Click the run → **Summary** tab
3. Confirm the "Slide count" table appears

---

## Deliverables

- ✅ `.github/actions/count-slides/action.yml` as a composite action
- ✅ `validate.yml` uses the action and reads its `count` output
- ✅ The workflow summary shows the slide count table
- ✅ New slide section "Custom Actions" in `slides.md`

---

## Stretch goals

- Add an `min-slides` input to the action and fail the workflow if the count is below it
- Add a `::warning::` annotation when the slide count exceeds 40 (slides are getting too long!)
- Add the action to the build workflow as well so every deploy logs the slide count
