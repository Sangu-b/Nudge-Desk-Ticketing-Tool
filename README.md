# Nudge Desk

An internal support ticketing tool prototype — built for The/Nudge Institute. Employees raise tickets for IT, HR, Finance, or Admin and track them to resolution; agents work them from a department queue; a lightweight AI layer handles auto-categorisation, duplicate detection, and draft replies.

![status](<img width="1516" height="667" alt="Screenshot 2026-06-22 002128" src="https://github.com/user-attachments/assets/aa5ced8e-4370-49f6-8007-0146ff4be20c" />

)

## Features

- **Raise a ticket** — title, description, category, urgency. As the employee types, the app suggests a department (with a confidence score) and surfaces similar already-resolved tickets to cut down on duplicates.
- **Full lifecycle** — `Open → In Progress → Resolved → Closed`, visualised on every ticket as a "nudge meter" that fills as it progresses.
- **Department queue** — agent view, grouped by IT / HR / Finance / Admin, sorted by urgency then age.
- **AI-drafted first response** — auto-generated, category- and urgency-aware, always editable before an agent saves it.
- **Notifications** — every status change is logged to a per-ticket timeline and surfaces as a toast + notification badge for the employee.
- **My tickets** — employees track their own tickets by name.
- **Analytics** — live totals, open vs. resolved, average resolution time, and breakdowns by department / urgency / status.

## Tech stack

Single self-contained `index.html` — vanilla HTML/CSS/JavaScript, no build step, no framework, no backend server.

- **Data** — persisted as one JSON blob via a key-value storage API (shared across users), so it behaves like a real shared system rather than per-browser local state.
- **AI layer** — runs entirely client-side: keyword scoring for categorisation, Jaccard similarity for duplicate/similar-ticket detection, and templated drafting for agent replies. No external API calls, no API key required. (See [Notes on the AI layer](#notes-on-the-ai-layer) below for why, and how to swap in a real LLM.)
- **Fonts** — [Fraunces](https://fonts.google.com/specimen/Fraunces) (display) + [Inter](https://fonts.google.com/specimen/Inter) (body), loaded from Google Fonts.

## Getting started

This is a static, single-file app — no install, no dependencies.

```bash
git clone <this-repo-url>
cd nudge-desk
open index.html   # or just double-click the file / drag it into a browser
```

> **Note:** the app expects a `window.storage` key-value API (`get` / `set` / `delete` / `list`) for persistence. This is provided automatically when run inside a Claude.ai artifact. To run it as a standalone static site outside that environment, swap the `loadTickets()` / `saveTickets()` calls in `index.html` for `localStorage`, or wire up your own backend (see [Adapting for production](#adapting-for-production)).

## Project structure

```
.
├── index.html                     # the entire app — markup, styles, and logic
├── nudge-desk-build-note.docx     # two-page write-up: architecture, tooling, key decisions
└── README.md
```

There's intentionally just one file for the app. All CSS is in a `<style>` block, all JS in a `<script>` block at the bottom — search for the section headers in `index.html` to navigate:

- `AI LAYER` — categorisation, similarity matching, draft replies
- `DATA / STORAGE` — load/save and the seed dataset
- `TABS`, `SUBMIT FORM`, `RENDER HELPERS`, `MY TICKETS`, `QUEUE`, `MODAL`, `ANALYTICS`, `TOAST`

## Notes on the AI layer

Categorisation, similarity, and drafting are deterministic heuristics (keyword scoring + Jaccard text similarity), not live model calls — chosen for a zero-latency, zero-dependency demo. Each piece is an isolated function, so swapping in a real LLM call later is a contained change:

| Function | Current approach | Swap-in point |
|---|---|---|
| `aiCategorize(text)` | Keyword dictionary scoring per department | Replace with an LLM classification call |
| similar-ticket matching | Jaccard similarity over resolved tickets | Replace with embeddings + vector search |
| `aiDraftReply(ticket)` | Category/urgency template | Replace with an LLM completion call |

## Adapting for production

This is a prototype, not a production system. Before real use, you'd want to add:

- A real backend + database in place of the shared key-value store
- Authentication (the current "My tickets" lookup is by name only, with no login)
- Server-side validation and access control (e.g. agents shouldn't be able to edit tickets outside their department)
- Real email/Slack notifications instead of in-app toasts
- Swapping the heuristic AI layer for live LLM calls per the table above

## Design notes

Warm brown/gold palette echoing The/Nudge Institute's brand; the recurring "nudge meter" (a four-segment progress bar shown on every ticket) is the one signature visual element, used consistently across the queue, employee view, and ticket detail modal. Full design and architecture rationale is in `nudge-desk-build-note.docx`.

## License

Prototype — license to be determined by The/Nudge Institute.
