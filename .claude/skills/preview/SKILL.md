---
name: preview
description: Use when the user wants to preview the Coded thoughts Hugo site locally before publishing. Starts the Hugo dev server and returns the localhost URL.
---

# Preview

Run the site locally with live reload.

## Steps

1. Pick a port (default `1313`; if it's busy, use `1314`, etc.). Call it `PORT`.
2. From the repo root, start the dev server in the background (include drafts so
   work-in-progress is visible):
   `hugo server -D --port PORT`
3. Wait ~2 seconds, then confirm it is up on the **same** port you chose:
   `curl -sSI http://localhost:PORT/ | head -1` (expect `HTTP/1.1 200 OK`).
4. Give the user the URL: `http://localhost:PORT/`
5. Tell them edits to markdown reload automatically, and to say "stop preview"
   to shut it down (kill the background `hugo server` process).

## Notes
- Always use the same `PORT` in the start command, the health check, and the URL
  you report — don't hardcode 1313 in the check if you started on another port.
- If `hugo` is missing, tell the user to run `brew install hugo`.
