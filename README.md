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
