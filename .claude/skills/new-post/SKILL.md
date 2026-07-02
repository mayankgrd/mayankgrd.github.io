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
   `draft: false`, and the provided `tags`.
5. If the post will use LaTeX math, add the `{{< katex >}}` shortcode on its own
   line near the top of the body (just after the intro). This is **required** —
   Congo only loads KaTeX on pages that use this shortcode; `$...$` / `$$...$$`
   alone will not render without it.
6. Tell the user the file path and remind them they can now write markdown,
   including code fences and math (needs the katex shortcode from step 5):
   inline `$e^{i\pi}=-1$` and display `$$ ... $$`. To show a literal dollar
   sign in text on a math page, escape it as `\$`.
7. Suggest running the `preview` skill to see it locally.

## Notes
- Bundle format is `content/posts/<slug>/index.md` so images can sit alongside
  the post in the same folder.
- Do not set `draft: true` unless the user asks — drafts are not published.
- **Diagrams:** use Congo's Mermaid *shortcode*, not a ```` ```mermaid ```` code
  fence. A code fence renders as raw text — Congo only loads the Mermaid library
  when the page uses the shortcode:

  ```
  {{< mermaid >}}
  graph LR
      A[Start] --> B[End]
  {{< /mermaid >}}
  ```
