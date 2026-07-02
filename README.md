# Coded thoughts

Personal site of Mayank — Computer Vision and AI for healthcare.
Built with [Hugo](https://gohugo.io) + the [Congo](https://github.com/jpanther/congo) theme, auto-deployed to GitHub Pages via GitHub Actions.

## Publishing (via Claude Code skills)

The everyday workflow needs no knowledge of Hugo, npm, or git internals:

- **new-post** — scaffold a new article with correct front-matter
- **preview** — see the site locally at http://localhost:1313/
- **publish** — commit, push, and deploy the live site

Typical flow: *"new post about X"* → write markdown → *"preview"* → *"publish"*.

## Manual commands

```bash
hugo server -D                     # local preview with drafts
hugo new posts/<slug>/index.md     # new post (a page bundle)
git add -A && git commit -m "..." && git push origin main   # publish
```

Push to `main` triggers the `.github/workflows/hugo.yml` Action, which builds
with Hugo Extended and deploys to https://mayankgrd.github.io/.

## Writing posts

Posts live in `content/posts/<slug>/index.md` (page bundles, so images sit next
to the post). Set `draft: false` in front-matter to publish. Supported in the
body:

- **Math:** `$inline$` and `$$display$$` (KaTeX) — add a `{{< katex >}}`
  shortcode near the top of any post that uses math, or it won't render.
  (Single-`$` inline is enabled via `layouts/_partials/extend-footer.html`;
  escape a literal dollar sign in text as `\$`.)
- **Diagrams:** wrap Mermaid in the `{{< mermaid >}}` … `{{< /mermaid >}}`
  shortcode (a plain ```` ```mermaid ```` fence renders as raw text)
- **Code:** fenced blocks with syntax highlighting

## Repo layout

- `content/` — markdown content (homepage, about, posts)
- `config/_default/` — Hugo + Congo configuration
- `themes/congo` — theme (git submodule)
- `.github/workflows/hugo.yml` — build & deploy
- `.claude/skills/` — the publishing skills

The previous Pelican site is preserved on the **`old-pelican`** branch; its
markdown source remains on the **`source`** branch.
