# Hugo Site Revamp Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace the old Pelican site with a Hugo + Congo site that auto-deploys to GitHub Pages, plus three Claude Code skills that make publishing a one-line workflow.

**Architecture:** Hugo (static site generator, single binary) renders markdown from `content/` using the Congo theme (git submodule). A GitHub Action builds on every push to `main` and deploys to GitHub Pages. Three project skills in `.claude/skills/` wrap post creation, local preview, and publishing.

**Tech Stack:** Hugo Extended, Congo theme, GitHub Actions, GitHub Pages, KaTeX, Mermaid.

## Global Constraints

- Repo: `mayankgrd.github.io` (GitHub **user pages** — served at the domain root).
- Working branch: `main` (orphan branch already created and checked out; old site preserved on `old-pelican`, already pushed).
- Site title: `Coded thoughts`. Tagline: `Computer Vision and AI for healthcare`.
- Social links: LinkedIn `https://www.linkedin.com/in/mayankgrd/`, GitHub `mayankgrd`.
- Theme: Congo, included as a **git submodule** at `themes/congo` (no Go toolchain).
- Hugo must be the **Extended** edition (Congo requires it).
- Never commit generated output (`public/`, `resources/_gen/`).
- Deployment is Pages "GitHub Actions" mode — no HTML committed by hand.

---

### Task 1: Install Hugo and scaffold the Hugo site skeleton

**Files:**
- Create: `hugo.toml` (temporary minimal config, replaced in Task 3)
- Create: `.gitignore`
- Create: `archetypes/default.md`

**Interfaces:**
- Produces: a buildable Hugo site rooted at repo top; `themes/congo` submodule path reserved for Task 2.

- [ ] **Step 1: Install Hugo Extended**

Run: `brew install hugo`
Then verify: `hugo version`
Expected: version string containing `extended` (e.g. `hugo v0.1xx.x+extended ...`).

- [ ] **Step 2: Create `.gitignore`**

```gitignore
# Hugo
/public/
/resources/_gen/
.hugo_build.lock

# OS / editor
.DS_Store
```

- [ ] **Step 3: Create a minimal `hugo.toml`**

```toml
baseURL = "https://mayankgrd.github.io/"
languageCode = "en-us"
title = "Coded thoughts"
theme = "congo"
```

- [ ] **Step 4: Create `archetypes/default.md`**

```markdown
+++
title = "{{ replace .File.ContentBaseName "-" " " | title }}"
date = {{ .Date }}
draft = true
tags = []
+++
```

- [ ] **Step 5: Verify Hugo recognizes the site**

Run: `hugo --printPathWarnings 2>&1 | head` (theme missing is expected here)
Expected: Hugo runs and reports the missing `congo` theme (confirms config is valid). Proceed to Task 2 to add the theme.

- [ ] **Step 6: Commit**

```bash
git add .gitignore hugo.toml archetypes/default.md
git commit -m "chore: scaffold Hugo site skeleton"
```

---

### Task 2: Add the Congo theme as a submodule and build successfully

**Files:**
- Create: `.gitmodules` (via submodule add)
- Add: `themes/congo` (submodule)

**Interfaces:**
- Consumes: `hugo.toml` `theme = "congo"` from Task 1.
- Produces: a site that builds with `hugo` and serves with `hugo server`.

- [ ] **Step 1: Add the Congo theme submodule**

```bash
git submodule add -b stable https://github.com/jpanther/congo.git themes/congo
```
Expected: `themes/congo` populated; `.gitmodules` created.

- [ ] **Step 2: Build the site to verify the theme loads**

Run: `hugo`
Expected: build succeeds, `public/` generated, no fatal theme errors.

- [ ] **Step 3: Smoke-test the dev server**

Run: `hugo server --port 1313 &` then `sleep 2 && curl -sSI http://localhost:1313/ | head -1 && kill %1`
Expected: `HTTP/1.1 200 OK`.

- [ ] **Step 4: Commit**

```bash
git add .gitmodules themes/congo
git commit -m "feat: add Congo theme as submodule"
```

---

### Task 3: Configure Congo (profile homepage, social links, math/diagrams)

**Files:**
- Delete: `hugo.toml` (replaced by structured config)
- Create: `config/_default/hugo.toml`
- Create: `config/_default/params.toml`
- Create: `config/_default/languages.en.toml`
- Create: `config/_default/menus.en.toml`
- Create: `config/_default/markup.toml`

**Interfaces:**
- Consumes: Congo theme from Task 2.
- Produces: profile-mode homepage, KaTeX + Mermaid enabled site-wide config, `posts` section, `about` menu entry.

- [ ] **Step 1: Remove the temporary root config**

```bash
git rm hugo.toml
```

- [ ] **Step 2: Create `config/_default/hugo.toml`**

```toml
baseURL = "https://mayankgrd.github.io/"
theme = "congo"
defaultContentLanguage = "en"
enableEmoji = true
enableRobotsTXT = true

[pagination]
  pagerSize = 10

[taxonomies]
  tag = "tags"
  category = "categories"
```

- [ ] **Step 3: Create `config/_default/languages.en.toml`**

```toml
languageCode = "en"
languageName = "English"
title = "Coded thoughts"

[params.author]
  name = "Mayank"
  headline = "Computer Vision and AI for healthcare"
```

- [ ] **Step 4: Create `config/_default/params.toml`**

```toml
colorScheme = "congo"
defaultAppearance = "light"
autoSwitchAppearance = true

enableSearch = true
enableCodeCopy = true

mainSections = ["posts"]
robots = "index, follow"

[header]
  layout = "basic"

[homepage]
  layout = "profile"
  showRecent = true
  showRecentItems = 6
  recentLimit = 6

[article]
  showDate = true
  showAuthor = true
  showReadingTime = true
  showTableOfContents = true
  showTaxonomies = true
  showWordCount = false
  math = true

[list]
  showTableOfContents = false

# Enable KaTeX (math) and Mermaid (diagrams) loading
[params.math]
  enable = true
```

- [ ] **Step 5: Create `config/_default/menus.en.toml`**

```toml
[[main]]
  name = "Posts"
  pageRef = "posts"
  weight = 10

[[main]]
  name = "About"
  pageRef = "about"
  weight = 20

[[footer]]
  name = "LinkedIn"
  url = "https://www.linkedin.com/in/mayankgrd/"
  weight = 10

[[footer]]
  name = "GitHub"
  url = "https://github.com/mayankgrd"
  weight = 20
```

- [ ] **Step 6: Create `config/_default/markup.toml`**

```toml
[goldmark.renderer]
  unsafe = true

[highlight]
  noClasses = false
  lineNos = false
  style = "onedark"

[tableOfContents]
  startLevel = 2
  endLevel = 3
```

- [ ] **Step 7: Build and verify config is valid**

Run: `hugo`
Expected: build succeeds with no config errors.

- [ ] **Step 8: Commit**

```bash
git add config/ && git rm --cached hugo.toml 2>/dev/null; git add -A
git commit -m "feat: configure Congo (profile homepage, social, math)"
```

---

### Task 4: Add homepage, about page, and seed posts (math + diagram + code)

**Files:**
- Create: `content/_index.md`
- Create: `content/about.md`
- Create: `content/posts/_index.md`
- Create: `content/posts/hello-world/index.md`
- Create: `content/posts/sublime-anaconda/index.md`
- Read for reference: old post at `git show old-pelican:content/python/sublime-anaconda.md` (source lives on `source` branch: `git show source:content/python/sublime-anaconda.md`)

**Interfaces:**
- Consumes: Congo config from Task 3 (`mainSections = ["posts"]`, profile homepage).
- Produces: renderable homepage + at least one post exercising KaTeX, Mermaid, and code highlighting.

- [ ] **Step 1: Create `content/_index.md` (homepage profile front-matter)**

```markdown
---
title: "Coded thoughts"
description: "Computer Vision and AI for healthcare"
---
```

- [ ] **Step 2: Create `content/about.md`**

```markdown
---
title: "About"
date: 2026-07-01
---

I work on computer vision and AI for healthcare. This is where I write about
deep learning, systems, and things I'm figuring out.

Find me on [LinkedIn](https://www.linkedin.com/in/mayankgrd/) and
[GitHub](https://github.com/mayankgrd).
```

- [ ] **Step 3: Create `content/posts/_index.md`**

```markdown
---
title: "Posts"
description: "Articles on computer vision, AI, and healthcare."
---
```

- [ ] **Step 4: Create `content/posts/hello-world/index.md` (feature demo)**

````markdown
---
title: "Hello, world"
date: 2026-07-01
draft: false
tags: ["meta"]
math: true
---

Welcome to the rebuilt site. This post exists to demonstrate that math,
diagrams, and code all render correctly.

## Math

The loss for a linear regression model:

$$
\mathcal{L}(\theta) = \frac{1}{n} \sum_{i=1}^{n} \left( y_i - \theta^\top x_i \right)^2
$$

Inline math like $e^{i\pi} + 1 = 0$ works too.

## Diagram

```mermaid
graph LR
    A[Write markdown] --> B[Preview locally]
    B --> C[Publish]
    C --> D[Live on GitHub Pages]
```

## Code

```python
def mse(y_true, y_pred):
    return ((y_true - y_pred) ** 2).mean()
```
````

- [ ] **Step 5: Port the old sublime-anaconda post**

Retrieve the old content for reference:
Run: `git show source:content/python/sublime-anaconda.md`
Then create `content/posts/sublime-anaconda/index.md` with Hugo front-matter
(title, original date, `tags`, `draft: false`) followed by the original markdown
body. Preserve the article text; only convert the front-matter to Hugo's format.

- [ ] **Step 6: Build and verify pages render**

Run: `hugo && test -f public/index.html && test -f public/posts/hello-world/index.html && echo OK`
Expected: `OK`.

- [ ] **Step 7: Verify math, mermaid, and code are present in output**

Run: `grep -l "katex\|mermaid" public/posts/hello-world/index.html && grep -c "highlight" public/posts/hello-world/index.html`
Expected: the hello-world HTML references katex/mermaid assets and contains highlighted code (`highlight` count > 0).

- [ ] **Step 8: Commit**

```bash
git add content/
git commit -m "feat: add homepage, about, and seed posts"
```

---

### Task 5: Add the GitHub Actions deploy workflow

**Files:**
- Create: `.github/workflows/hugo.yml`

**Interfaces:**
- Consumes: the buildable site from Tasks 1-4 (including the `themes/congo` submodule).
- Produces: automatic build + Pages deploy on push to `main`.

- [ ] **Step 1: Create `.github/workflows/hugo.yml`**

```yaml
name: Deploy Hugo site to Pages

on:
  push:
    branches: ["main"]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

defaults:
  run:
    shell: bash

jobs:
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.148.1
    steps:
      - name: Install Hugo CLI
        run: |
          wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb \
          && sudo dpkg -i ${{ runner.temp }}/hugo.deb
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive
          fetch-depth: 0
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v5
      - name: Build with Hugo
        env:
          HUGO_ENVIRONMENT: production
        run: hugo --minify --baseURL "${{ steps.pages.outputs.base_url }}/"
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

- [ ] **Step 2: Sanity-check the workflow YAML locally**

Run: `python3 -c "import yaml,sys; yaml.safe_load(open('.github/workflows/hugo.yml')); print('valid yaml')"`
Expected: `valid yaml`.

- [ ] **Step 3: Verify the pinned Hugo version exists**

Confirm the `HUGO_VERSION` in the workflow matches a real release and is `>=` the locally installed version. Run: `hugo version` and, if needed, bump `HUGO_VERSION` to the local minor version.
Expected: workflow version is a valid published Hugo Extended release.

- [ ] **Step 4: Commit**

```bash
git add .github/
git commit -m "ci: add Hugo build and Pages deploy workflow"
```

---

### Task 6: Build the `new-post` skill

**Files:**
- Create: `.claude/skills/new-post/SKILL.md`

**Interfaces:**
- Produces: a skill that scaffolds `content/posts/<slug>/index.md` via `hugo new`.

- [ ] **Step 1: Create `.claude/skills/new-post/SKILL.md`**

```markdown
---
name: new-post
description: Use when the user wants to start a new blog post/article on the Coded thoughts Hugo site. Scaffolds a markdown file with correct front-matter in content/posts/.
---

# New Post

Create a new article for the Hugo site.

## Steps

1. Ask the user for the post **title** if not given, and optional **tags**.
2. Derive a slug: lowercase, spaces/punctuation to hyphens.
3. Run from the repo root:
   `hugo new posts/<slug>/index.md`
4. Open the created file and set front-matter: real `title`, today's date,
   `draft: false`, the provided `tags`, and `math: true` if the post will use
   LaTeX.
5. Tell the user the file path and remind them they can now write markdown,
   including code fences, `$...$` / `$$...$$` math, and ```mermaid diagrams.
6. Suggest running the `preview` skill to see it locally.

## Notes
- Bundle format is `content/posts/<slug>/index.md` so images can sit alongside
  the post in the same folder.
- Do not set `draft: true` unless the user asks — drafts are not published.
```

- [ ] **Step 2: Verify the scaffolding command works**

Run: `hugo new posts/skill-smoke-test/index.md && test -f content/posts/skill-smoke-test/index.md && echo OK && rm -rf content/posts/skill-smoke-test`
Expected: `OK` (temporary test post created then removed).

- [ ] **Step 3: Commit**

```bash
git add .claude/skills/new-post/
git commit -m "feat: add new-post skill"
```

---

### Task 7: Build the `preview` skill

**Files:**
- Create: `.claude/skills/preview/SKILL.md`

**Interfaces:**
- Produces: a skill that starts `hugo server` and returns the local URL.

- [ ] **Step 1: Create `.claude/skills/preview/SKILL.md`**

```markdown
---
name: preview
description: Use when the user wants to preview the Coded thoughts Hugo site locally before publishing. Starts the Hugo dev server and returns the localhost URL.
---

# Preview

Run the site locally with live reload.

## Steps

1. From the repo root, start the dev server in the background (include drafts so
   work-in-progress is visible):
   `hugo server -D --port 1313`
2. Wait ~2 seconds, then confirm it is up:
   `curl -sSI http://localhost:1313/ | head -1` (expect `HTTP/1.1 200 OK`).
3. Give the user the URL: http://localhost:1313/
4. Tell them edits to markdown reload automatically, and to say "stop preview"
   to shut it down (kill the background `hugo server` process).

## Notes
- If port 1313 is busy, retry on 1314 and report the new URL.
- If `hugo` is missing, tell the user to run `brew install hugo`.
```

- [ ] **Step 2: Verify the server command works**

Run: `hugo server -D --port 1313 & sleep 2; curl -sSI http://localhost:1313/ | head -1; kill %1`
Expected: `HTTP/1.1 200 OK`.

- [ ] **Step 3: Commit**

```bash
git add .claude/skills/preview/
git commit -m "feat: add preview skill"
```

---

### Task 8: Build the `publish` skill

**Files:**
- Create: `.claude/skills/publish/SKILL.md`

**Interfaces:**
- Produces: a skill that commits, pushes to `main`, and reports the deploy.

- [ ] **Step 1: Create `.claude/skills/publish/SKILL.md`**

```markdown
---
name: publish
description: Use when the user wants to publish the Coded thoughts site — commit current changes, push to main, and trigger the GitHub Pages deploy. Use after writing/editing posts.
---

# Publish

Push changes so the GitHub Action rebuilds and deploys the live site.

## Steps

1. From the repo root, confirm the site builds cleanly first: `hugo`
   (abort and report if the build fails).
2. Show the user what changed: `git status --short`.
3. Stage and commit with a message describing the change (ask the user for a
   short message, or infer one from the changed post titles):
   `git add -A && git commit -m "<message>"`
4. Push: `git push origin main`.
5. Report that the deploy Action is running and link to it:
   `gh run watch` or point to
   https://github.com/mayankgrd/mayankgrd.github.io/actions
6. Tell the user the site will be live at https://mayankgrd.github.io/ once the
   Action finishes (usually a minute or two).

## Notes
- Never commit `public/` or `resources/_gen/` (already gitignored).
- If `git push` is rejected, run `git pull --rebase origin main` then push again.
```

- [ ] **Step 2: Verify the pre-publish build check works**

Run: `hugo >/dev/null && echo "build OK"`
Expected: `build OK`.

- [ ] **Step 3: Commit**

```bash
git add .claude/skills/publish/
git commit -m "feat: add publish skill"
```

---

### Task 9: Add README and push `main`; configure GitHub Pages

**Files:**
- Create: `README.md`

**Interfaces:**
- Consumes: everything from Tasks 1-8.
- Produces: `main` on remote as default branch; Pages set to "GitHub Actions"; live site.

- [ ] **Step 1: Create `README.md`**

```markdown
# Coded thoughts

Personal site of Mayank — Computer Vision and AI for healthcare.
Built with [Hugo](https://gohugo.io) + the [Congo](https://github.com/jpanther/congo) theme, auto-deployed to GitHub Pages.

## Publishing (via Claude Code skills)

- **new-post** — scaffold a new article
- **preview** — see the site locally (http://localhost:1313/)
- **publish** — commit, push, and deploy

## Manual commands

```bash
hugo server -D      # local preview
hugo new posts/<slug>/index.md   # new post
git add -A && git commit -m "..." && git push origin main   # publish
```

The old Pelican site is preserved on the `old-pelican` branch.
```

- [ ] **Step 2: Commit the README**

```bash
git add README.md
git commit -m "docs: add README with publishing workflow"
```

- [ ] **Step 3: Push `main` to remote**

Run: `git push -u origin main`
Expected: branch `main` created on remote.

- [ ] **Step 4: Set `main` as the default branch**

Run: `gh repo edit mayankgrd/mayankgrd.github.io --default-branch main`
Expected: success. (If it fails, tell the user to set it in repo Settings → Branches.)

- [ ] **Step 5: Set Pages source to GitHub Actions**

Run:
```bash
gh api -X POST repos/mayankgrd/mayankgrd.github.io/pages -f build_type=workflow 2>/dev/null \
  || gh api -X PUT repos/mayankgrd/mayankgrd.github.io/pages -f build_type=workflow
```
Expected: Pages configured for `workflow` build type. (If the API call fails, tell the user to set Settings → Pages → Source = "GitHub Actions".)

- [ ] **Step 6: Verify the deploy Action succeeds**

Run: `gh run watch` (or `gh run list --limit 1`)
Expected: the "Deploy Hugo site to Pages" run completes successfully.

- [ ] **Step 7: Verify the live site**

Run: `curl -sSI https://mayankgrd.github.io/ | head -1`
Expected: `HTTP/1.1 200 OK` (may take a minute after the Action finishes).

---

## Self-Review

**Spec coverage:**
- Hugo + Congo + submodule → Tasks 1-3. ✓
- Deploy via GitHub Actions → Task 5, Task 9. ✓
- Branch strategy (old-pelican backup, main default) → done pre-plan + Task 9. ✓
- Profile homepage, title, tagline, LinkedIn/GitHub links → Tasks 3-4. ✓
- KaTeX + Mermaid + code highlighting + dark/light + ToC + reading time → Tasks 3-4. ✓
- Seed posts incl. ported sublime-anaconda + feature demo → Task 4. ✓
- Three skills (new-post, preview, publish) → Tasks 6-8. ✓
- README → Task 9. ✓
- Pages source manual step → Task 9 with fallback instructions. ✓

**Placeholder scan:** No TBD/TODO; every file has full content. Task 4 Step 5 (porting old post) intentionally defers exact body text to the retrieved original — acceptable since content is copied verbatim from `git show`. ✓

**Type/name consistency:** Section name `posts` consistent across `mainSections`, menu `pageRef`, `hugo new posts/...`, and content paths. Skill names (`new-post`, `preview`, `publish`) consistent between files and README. ✓
