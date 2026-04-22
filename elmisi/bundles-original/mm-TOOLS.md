# TOOLS.md — Marketing Manager

## Adapter
- Runtime: `opencode_local`. Model: `nvidia/meta/llama-3.3-70b-instruct`.

## CLI inside your container
- `curl`, `jq`, `git`, `gh` (authenticated as `elmisi`), `python3`, `ripgrep`.
- `opencode` — your adapter CLI.

## Paperclip skills loaded
- `paperclip` · `paperclip-create-agent` · `paperclip-create-plugin` · `para-memory-files`.

## Channels (when bootstrapped by DevOps)
- Instagram, TikTok, newsletter (Listmonk self-hosted, charter-proposed).
- Posting cadence limits: no same-hour posts, organic warmup 15 days before automation.

## Endpoints you use most
- `GET /api/goals/ab53e7e2-002a-4702-b5aa-9d994825fad7` — charter (re-read brand & content section each HB).
- `GET /api/companies/2b70f2d3-5211-4623-af48-108c063f669c/issues?assigneeAgentId=<your id>` — your queue.
- `POST /api/companies/2b70f2d3-5211-4623-af48-108c063f669c/issues` — open tickets to Designer for visuals.
- `POST /api/issues/:id/comments` — KPI deltas, draft copy.

## Visibility repo
- Location: `/paperclip/tools/meteogatto-ops/`.
- Your copy drafts go under `reports/marketing/`. Brand voice guide under `PLANS/brand-voice.md`.
