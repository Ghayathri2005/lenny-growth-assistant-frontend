# Lenny Growth Assistant — Frontend

A ChatGPT-like workspace for querying Lenny's Podcast transcripts, generating Ship30for30-style
essays, and viewing generated artifacts (Markdown/HTML) side-by-side with the conversation.

This repo is the **frontend only**. It's built to run standalone against a mock so you can develop
and preview the UI before the FastAPI backend exists, and to switch seamlessly to the real backend
once it's up.

## Stack

- React 19 + TypeScript + Vite
- Tailwind CSS v4
- `react-markdown` + `remark-gfm` for artifact/message rendering
- `lucide-react` for icons

## Run it

```bash
npm install
cp .env.example .env      # point at your FastAPI backend (or leave default)
npm run dev
```

Opens on `http://localhost:5173` (or the port Vite picks).

If no backend is reachable at `VITE_API_BASE_URL`, the app transparently falls back to a local mock
(`src/lib/api.ts`) so every screen is fully demoable — new chats, streaming replies, citations, and
artifact generation all work without a server. Sessions persist to `localStorage` in mock mode.

## What's built

- **Sidebar** — "New chat" + session list, styled like a transcript log (`src/components/Sidebar.tsx`)
- **Chat window** — streaming assistant replies, a skill switch between **Q&A** and **Ship30for30
  essay**, and citation chips under grounded answers (`src/components/ChatWindow.tsx`)
- **Artifact Viewer** — side panel that renders generated Markdown or sandboxed HTML, with a
  Preview/Source toggle, copy, and download (`src/components/ArtifactViewer.tsx`)
- **LLM toggle** — switches the active session between Cloud and Local (Ollama) providers and model
  (`src/components/LLMToggle.tsx`)

## Backend contract

The frontend expects these endpoints from the FastAPI backend (see `src/lib/api.ts` for the exact
shapes):

```
GET    /api/health
GET    /api/sessions                 -> ChatSession[]
POST   /api/sessions                 -> ChatSession
DELETE /api/sessions/:id             -> 204
POST   /api/sessions/:id/messages    -> streamed response body, newline-delimited SSE-style
                                         `data: {...}\n\n` chunks:
                                           { "delta": "partial text" }               // while streaming
                                           { "done": true,
                                             "citations": [{ "episode": "...", "timestamp": "..." }],
                                             "artifact": { "kind": "markdown" | "html",
                                                            "title": "...", "content": "..." } }
```

Request body for `POST /api/sessions/:id/messages`:

```json
{
  "content": "user message",
  "skill": "qa" | "ship30",
  "llm": { "provider": "cloud" | "local", "cloudModel": "...", "localModel": "..." }
}
```

Point the backend team at this file — it's the whole contract the UI needs.

## Design notes

See `design.md` for the visual/UX rationale (palette, type, and the reasoning behind the transcript-
style message layout).
