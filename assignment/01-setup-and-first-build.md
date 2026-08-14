# Assignment 1 — Make it build

**Layer:** Push to `main` → Slidev build → GitHub Pages deployment

**Time:** ~30 minutes

---

## Context

You are a team of three. Together, you will build a public GitHub repository that hosts a Slidev presentation about what you know about SDLC and CI/CD. The presentation grows with every assignment — each chapter adds a new slide section.

By the end of the masterclass, the live URL of your deck is your deliverable.

---

## Discovery first (5 minutes)

Before writing YAML, discuss in your team:

1. What can go wrong between `git push` and a live Pages update?
2. Which parts should be automated immediately, and which should stay manual for now?
3. What signal tells you the pipeline is healthy?

Write your hypotheses down. You will validate them during this assignment.

---

## Learning goals

- Set up a GitHub repository with Slidev
- Write a workflow that builds and deploys to GitHub Pages on every push to `main`
- Understand the Pages deployment artifact model

---

## Steps

### 1. Create the repository

1. One team member creates a **public** GitHub repository named `cicd-masterclass` (or similar).
2. Invite the other two team members as collaborators (Settings → Collaborators).
3. Clone the repository locally.

### 2. Initialise Slidev

Initialize a Slidev deck in the repository root and add a short section about SDLC and CI/CD.

Constraints:

- `slides.md` exists in the repository root.
- The deck contains at least one SDLC slide and one CI/CD slide.
- The deck builds locally before you automate it.

### 3. Draft a minimal workflow first

Create a workflow file that does the following, without copy-pasting from the slides or other teams:

- triggers on push to `main`
- checks out the repository
- installs dependencies
- builds Slidev

Checkpoint:

- Explain why the build command needs a stable base path for GitHub Pages assets.

### 4. Add deployment to GitHub Pages

Extend the workflow so it also deploys to GitHub Pages.

Requirements:

- deployment runs only after a successful build
- workflow uploads the Pages artifact from the build output directory
- deployment targets the Pages environment
- workflow declares explicit token permissions required for Pages deploy

Checkpoint:

- Be ready to justify each permission line in your workflow.

### 5. Enable GitHub Pages

In your repository: **Settings → Pages → Source → GitHub Actions**.

### 6. Push and verify

Commit and push to `main`, then validate:

- the workflow ran automatically
- build completed before deploy
- your live Pages URL renders correctly

Watch the **Actions** tab. When the workflow passes, open the Pages URL.

---

## Deliverables

- ✅ GitHub repository with `slides.md` containing a "What is SDLC/CI/CD?" section
- ✅ `.github/workflows/build.yml` that builds and deploys on push to `main`
- ✅ A live URL on GitHub Pages that loads the presentation

---

## Stretch goals

- Add a `workflow_dispatch:` trigger so you can manually redeploy from the Actions tab
- Add a README badge that shows the workflow status
- Try changing a slide and observe how quickly the update appears on the live URL
