# TOOLS.md — Full-stack Engineer

## Adapter
- Runtime: `opencode_local`. Model: `opencode-go/kimi-k2.5`.

## CLI inside your container
- `curl`, `jq`, `git`, `gh` (authenticated as `elmisi`), `ssh`, `python3`, `ripgrep`, `node`, `pnpm`, `npm`.
- Test runners: `vitest` or `jest` per project, `playwright` for E2E.
- `opencode` — your adapter CLI.

## Paperclip skills loaded
- `paperclip` · `paperclip-create-agent` · `paperclip-create-plugin` · `para-memory-files`.

## Content pipeline dependencies (charter-locked)
- **Image gen**: Amigo at `/paperclip/tools/amigo/` (cloned from `https://github.com/elmisi/amigo` by DevOps during HB-0) with `pollinations` backend. Subprocess or Node fork.
- **Weather**: Open-Meteo (primary, free commercial). OpenWeatherMap fallback.
- **Storage**: MinIO self-hosted.
- **DB**: PostgreSQL (schema owned by CTO).

## Visibility repo
- Location: `/paperclip/tools/meteogatto-ops/`.
- Your implementation notes + migration runbooks go under `reports/eng/`.

## Endpoints you use most
- `GET /api/companies/2b70f2d3-5211-4623-af48-108c063f669c/issues?assigneeAgentId=<your id>&status=todo,in_progress,in_review,blocked`.
- `POST /api/issues/:id/checkout` — claim before starting.
- `PATCH /api/issues/:id` — status transitions.
- `POST /api/issues/:id/comments` — updates, spec requests to Designer/MM.
