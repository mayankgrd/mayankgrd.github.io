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
