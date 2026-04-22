# TOOLS.md — CTO

## Adapter
- Runtime: `opencode_local`. Model: `opencode/minimax-m2.5-free`.
- No special permissions beyond ticket writes.

## CLI inside your container
- `curl`, `jq`, `git`, `gh` (authenticated as `elmisi`), `ssh`, `python3`, `ripgrep`, `node`, `pnpm`.
- `opencode` — your adapter CLI.

## Paperclip skills loaded
- `paperclip` · `paperclip-create-agent` · `paperclip-create-plugin` · `para-memory-files`.

## Endpoints you use most
- `GET /api/goals/ab53e7e2-002a-4702-b5aa-9d994825fad7` — charter (re-read for locked-in tech before any architecture decision).
- `GET /api/companies/2b70f2d3-5211-4623-af48-108c063f669c/issues?assigneeAgentId=<engineer|devops>&status=todo,in_progress,in_review,blocked` — your reports' state.
- `POST /api/companies/2b70f2d3-5211-4623-af48-108c063f669c/issues` — create Engineer/DevOps tickets.
- `POST /api/issues/:id/comments` — code review + decisions.
- `PATCH /api/issues/:id` — status changes, assignee changes.

## Visibility repo
- Location: `/paperclip/tools/meteogatto-ops/`.
- Review `decisions/` for prior architecture calls before opening a new technical ticket.

## Stack reference (charter §Locked-in tech — do not renegotiate)
- Next.js 14+ App Router (TS) · PostgreSQL · MinIO · Caddy · Open-Meteo → OpenWeatherMap fallback · Amigo/Pollinations for images · Plausible analytics · GitHub Actions CI/CD.
