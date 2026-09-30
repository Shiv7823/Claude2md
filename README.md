# AI Chat Context Exporter (Vercel edition)

Paste a public Claude chat share link, pick a token budget and export format,
get back a compact context file (.md, .txt, .doc or .pdf) to continue the
work in a new AI chat.

No n8n, no server to keep alive, no Docker. Frontend and backend deploy
together as one Vercel project.

## Files

- `index.html` — the frontend. Calls `/api/generate` (same origin, no CORS setup needed).
- `api/generate.js` — the entire pipeline as one Vercel serverless function: validate URL → call the extractor → parse → compress → assemble markdown → package into the chosen format.
- `vercel.json` — sets `maxDuration: 120` so the function has enough time for a slow extractor response.
- `package.json` — zero runtime dependencies. Nothing to install, nothing to go out of date.

## How it works

Every submitted URL runs the whole pipeline top to bottom in one function
invocation, then the instance disappears. There's no persistent process —
which is exactly what avoids the memory crashes and spin-down delays you'd
get self-hosting n8n on a free tier.

## Deploying

1. Push this folder to a GitHub repo.
2. At vercel.com, **New Project** → import the repo → Deploy. No configuration needed; Vercel auto-detects the static `index.html` and the `api/` function.
3. That's it — your site is live at `https://<project>.vercel.app`, and the form works immediately since frontend and backend share the same origin.

To use a custom domain, add it under the project's **Domains** tab in Vercel.

## Current limitations

- **Providers**: only `claude.ai/share/...` links actually extract content, via the external `claude2pdf` service. ChatGPT, Gemini, Poe and Perplexity links are recognized and rejected with a clear message rather than silently failing — add a real extractor call in `prepareInput`/a new fetch call per provider to support one.
- **PDF/DOC export**: built with zero dependencies (a small hand-written PDF writer, and an HTML-in-.doc file for Word) — readable and correct, not a designed document.
- **The extractor service itself** (`claude2pdf.up.railway.app`) is outside this project's control — its uptime, speed, and rate limits are the same no matter where this backend is hosted.
