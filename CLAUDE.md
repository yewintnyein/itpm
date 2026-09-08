# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-file Kanban board demo for UOB's internal IT PMO. All markup, styles, and logic live in `index.html` — there is no build step, package manager, server, or test suite. Open the file directly in a browser (double-click, or `open index.html`) to run it.

## Hard constraints (do not violate)

- **No frameworks, no build tooling.** Vanilla HTML/CSS/JS only — no React/Vue/jQuery/Tailwind, no bundler, no npm install. Everything stays in the single `index.html` (`<style>` and `<script>` blocks).
- **No external resources.** No CDN scripts, no Google Fonts, no external images. Icons are inline SVG or Unicode glyphs; fonts are the system font stack.
- **No persistence.** Board state lives only in the in-memory `state` object. Do not add `localStorage`, `sessionStorage`, `IndexedDB`, or cookies — a page refresh resetting to seed data is intentional behavior.
- **No fake branding.** This is a demo/training tool, not a real UOB system — use only the "UOB IT PMO" text wordmark and the corporate blue palette (CSS custom properties in `:root`). No real UOB logo or trademarks.
- **FormSubmit is the only backend.** New-task notifications go through the FormSubmit AJAX JSON endpoint (`FORMSUBMIT_ENDPOINT` constant at the top of `<script>`). Calls are wrapped in try/catch and must never block or break the board on failure — a failed send just surfaces a warning toast while the card stays on the board (optimistic UI).

## Architecture

Single source of truth: `state = { tasks: [], filters: {...} }`. All UI is a pure re-render from this object — never mutate card DOM directly outside `renderBoard()`.

Key functions in `index.html`'s `<script>`, in the order the app calls them:
- `seedTasks()` — populates `state.tasks` with 8 demo tasks on load.
- `renderBoard()` / `renderCard(task)` — rebuild the four columns and each card from current state + filters; `renderCard` runs all user-supplied strings through `escapeHtml()` before insertion.
- `addTask(data)` — validates, generates the next `UOB-ITPM-####` ID, pushes to `state.tasks`, re-renders immediately, then calls `notifyNewTask()` in parallel (does not block the UI on the network call).
- `moveTask(taskId, newStatus)` — shared by both the native HTML5 drag-and-drop handlers and the "Move ▸" `<select>` fallback on each card.
- `deleteTask(taskId)` — paired with `askDelete()`/`cancelDelete()`, which drive the inline Yes/No confirm toggle (no native `confirm()`).
- `applyFilters()` — reads the filter bar inputs into `state.filters` and re-renders; filtering is pure client-side array filtering.
- `notifyNewTask(task)` — the only network call in the app; posts to `FORMSUBMIT_ENDPOINT`.

Four fixed status columns (`Backlog`, `In Progress`, `Blocked`, `Done`) are hardcoded in both the HTML skeleton and the `STATUSES` array — keep them in sync if either changes.

## FormSubmit setup

`FORMSUBMIT_ENDPOINT` must be pointed at a real email address to receive notifications. FormSubmit requires a one-time activation: the first submission to a new address triggers a confirmation email, and notifications only start delivering after that link is clicked. This has no effect on the board itself, which works regardless of activation state.
