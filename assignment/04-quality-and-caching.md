# Assignment 4 — Speed and quality

**Layer:** lockfile-based cache, Markdown lint, artifact upload with retention, cross-job output

**Time:** ~25 minutes

---

## Context

Your workflows run dependency installation on every run. With caching, that step can drop from 30-60 seconds to under 5. You also want automated quality checks so a typo or broken Markdown does not make it into the live deck — and you want to keep the built `dist/` available for inspection after every PR run.

---

## Learning goals

- Cache the package manager store across workflow runs
- Add a lint step that fails the workflow on errors
- Upload a build artifact with an explicit retention period
- Pass data between jobs using `outputs` and `needs`

---

## Discovery first (5 minutes)

Run the current validation workflow twice.

1. Record rough install time for run 1 and run 2.
2. Discuss where the time is actually spent.
3. Predict what should be cached and what should never be cached.

Then implement a lockfile-based cache.

---

## Steps

### 1. Add caching first (keep your current package manager)

Add lockfile-based dependency caching in your validation workflow.

Requirements:

- cache key includes OS + lockfile hash
- restore strategy supports branch-to-branch reuse
- cache path matches your package manager
- install step remains deterministic (no hidden global state)

Checkpoint:

- Explain why lockfile hash is safer than branch name as primary cache key.

### 2. Add Markdown lint

Add markdown linting and quality gates.

Requirements:

- install and run markdown lint in CI
- add a lint job that executes before (or gates) the main build job
- ensure a lint failure prevents downstream build/deploy
- keep rule configuration explicit in repo config

Then extend the pipeline with:

- an artifact upload of build output with explicit retention period
- one cross-job output from build consumed by a summary/report step

### 3. Optional: migrate to pnpm later

If you want faster local installs and a shared content-addressable store, migrate to pnpm as a follow-up step after caching is working.

### 4. Add a new slide section

Add a section titled `Quality in CI` to `slides.md` with your own wording.

Include:

- why cache correctness matters more than raw speed
- one concrete linting failure mode your team experienced
- what artifacts are used for after a failed run

### 5. Intentionally break lint

In a commit, introduce a Markdown lint error (e.g. a trailing space or an extra blank line inside a list). Watch the `lint` job fail and the `build` job skip. Fix it and watch the full pipeline go green.

---

## Deliverables

- ✅ Lockfile-based dependency cache in both workflows (cache hit on second run)
- ✅ `lint` job in `validate.yml` using `markdownlint`
- ✅ `upload-artifact` of `dist/` with 7-day retention in the `build` job
- ✅ `summary` job reading the slide count from `needs.build.outputs`
- ✅ New slide section "Quality in CI" in `slides.md`

---

## Stretch goals

- Add `restore-keys:` to the cache action to allow partial cache hits across branches
- Try `npm run export` (or `pnpm run export`) to generate a PDF artifact alongside the HTML build
- Add a step that fails if `slide-count` is less than 8 (enforcing minimum content)
