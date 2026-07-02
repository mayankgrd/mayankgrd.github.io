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
