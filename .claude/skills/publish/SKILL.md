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
