# Design: Revamp of mayankgrd.github.io

**Date:** 2026-07-01
**Status:** Approved

## Goal

Replace the aging Pelican/Bootstrap 3 site with a clean, modern, text-first
personal site that makes publishing articles (with LaTeX math, diagrams, and
code blocks) as simple as writing a markdown file and running a skill.

## Current state (before)

- `mayankgrd.github.io` is a GitHub **user pages** repo.
- Old stack: Pelican (Python SSG) + Bootstrap 3 theme, generated HTML committed
  to `master`; markdown source on the `source` branch. Last updated Sept 2017.
- Effectively one real article (`content/python/sublime-anaconda.md`) plus a
  deep-nets regression post.

## Stack & hosting

- **Hugo** static site generator (single binary; no npm, no Ruby).
- **Congo** theme (Tailwind-based, clean/modern), included as a **git submodule**
  so no Go toolchain is required locally.
- **Deployment via GitHub Actions:** push markdown to `main` → an Action builds
  the site with Hugo and publishes to GitHub Pages. No hand-committed HTML.
- Domain stays `mayankgrd.github.io`.

## Repo & branch strategy

- `old-pelican` branch: snapshot of the old live `master`. **Already created and
  pushed to remote.** Existing `source` branch (Pelican markdown) left untouched.
- `main` branch: new Hugo source (markdown + config). Becomes the default branch.
- GitHub Pages source switched to **"GitHub Actions"** (build-from-Action mode).
- `master` / `gh-pages` legacy branches left in place; nothing is deleted.

## Site content & configuration

- **Title:** "Coded thoughts"
- **Tagline:** "Computer Vision and AI for healthcare"
- **Homepage:** Congo "profile" layout — name/title, tagline, social links, then
  a list of recent posts.
- **Social links:** LinkedIn (`https://www.linkedin.com/in/mayankgrd/`),
  GitHub (`mayankgrd`).
- **Features enabled:** KaTeX (LaTeX math), Mermaid (diagrams), syntax-highlighted
  code blocks, dark/light toggle, reading time, table of contents.
- **Structure:**
  - `content/_index.md` — homepage profile front-matter.
  - `content/posts/` — articles.
  - `content/about.md` — about page.
  - Seed posts: port the old `sublime-anaconda` post, plus one "hello world"
    post demonstrating math + a Mermaid diagram + a code block.

## Publishing skills (project skills in `.claude/skills/`)

Committed to the repo so the workflow needs no knowledge of Hugo/git internals.

| Skill | Purpose |
|-------|---------|
| `new-post` | Prompt for a title (+ optional tags), scaffold a markdown file in `content/posts/` with correct front-matter, ready for text/code/math/diagrams. |
| `preview`  | Start the local Hugo dev server; return the localhost URL for live preview. |
| `publish`  | Commit changes, push to `main`, and report the deploy (the Action then builds & publishes). |

Everyday workflow: *"new post about X"* → edit markdown → *"preview"* → *"publish"*.

## Components / files to create

- `hugo.toml` (or `config/_default/*`) — site config + Congo params (profile
  mode, author, social, KaTeX, Mermaid, highlighting, ToC).
- `.github/workflows/hugo.yml` — Hugo build + Pages deploy action (with submodule
  checkout).
- `themes/congo` — git submodule.
- `archetypes/default.md` — front-matter template for new posts.
- `content/_index.md`, `content/about.md`, `content/posts/*.md` — content.
- `.gitignore` — ignore `public/`, `resources/_gen/`, Hugo lock files.
- `.claude/skills/{new-post,preview,publish}/SKILL.md` — the three skills.
- `README.md` — short "how to publish" note pointing at the skills.

## Out of scope (YAGNI)

- Custom domain (staying on github.io).
- Comments system, analytics, newsletter.
- Full visual redesign beyond Congo defaults + configured profile/features.
- Migrating every historical post (only the one real post is ported).

## Manual steps (require GitHub UI or confirmation)

- Set default branch to `main` (attempt via `gh`; confirm with user).
- Set Pages source to "GitHub Actions" (attempt via `gh api`; confirm with user).

## Success criteria

- Pushing a markdown file to `main` results in a live, correctly-rendered page on
  `mayankgrd.github.io` within the Action's runtime.
- A post with LaTeX, a Mermaid diagram, and a code block renders all three
  correctly.
- The three skills let the user create, preview, and publish without touching
  Hugo or git directly.
