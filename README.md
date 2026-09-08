# UOB IT PMO — Kanban Board (Internal Demo)

A single-file, dependency-free Kanban board demo built for UOB's internal IT PMO. This is an internal demo/training tool only — it does not use UOB's real logo, trademarks, or imitate any official UOB system.

## Running it

No build step, no server, no install. Just open the file directly:

```
open index.html
```

or double-click `index.html` in Finder.

## What it does

- Kanban board with four columns: Backlog, In Progress, Blocked, Done
- Drag-and-drop cards between columns (plus a keyboard-accessible "Move ▸" fallback)
- Add Task form with client-side validation and optimistic UI
- New tasks trigger an email notification via [FormSubmit](https://formsubmit.co) (see below)
- Client-side filters by project, assignee, and priority
- Live summary counts (total, per-status, overdue)

## Notes on data

Board state lives only in memory (a JS array) — there is no backend, database, or browser storage. **Refreshing the page resets the board to the seeded demo data.** This is intentional for a demo tool.

## FormSubmit setup

New-task notifications are sent via FormSubmit's AJAX endpoint, configured with the `FORMSUBMIT_ENDPOINT` constant near the top of the `<script>` block in `index.html`. To receive notifications:

1. Replace the placeholder email in `FORMSUBMIT_ENDPOINT` with your own address.
2. Submit one task through the Add Task form — this triggers a one-time confirmation email from FormSubmit to that address.
3. Click the confirmation link in that email. Notifications will only start delivering after that.

The board itself works regardless of whether this step has been completed — a failed or unconfirmed FormSubmit call never blocks the UI, it just shows a warning toast.

## Tech

Vanilla HTML, CSS, and JavaScript only — no frameworks, no build tooling, no external resources (no CDN scripts, no Google Fonts, no image files).
