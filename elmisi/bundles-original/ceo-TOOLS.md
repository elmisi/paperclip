# TOOLS.md — CEO

## Adapter
- Runtime: `codex_local`. Model: `gpt-5.3-codex`.
- You have `canCreateAgents: true` (charter §Hiring governance applies — board approval gate ON).
- You have `dangerouslyBypassApprovalsAndSandbox: true` for Codex — use responsibly.

## CLI inside your container
- `curl`, `jq`, `git`, `gh` (authenticated as `elmisi`, HTTPS), `ssh`, `python3`, `ripgrep`.
- `codex` — your own adapter CLI.

## Paperclip skills loaded
- `paperclip` — coordination API wrapper.
- `paperclip-create-agent` — for hiring new agents (charter-gated).
- `para-memory-files` — PARA memory structure, IF you want to keep personal notes. Not required.

## Endpoints you use most
- `GET /api/goals/ab53e7e2-002a-4702-b5aa-9d994825fad7` — your charter.
- `GET /api/companies/2b70f2d3-5211-4623-af48-108c063f669c/issues?...` — backlog scan.
- `POST /api/companies/2b70f2d3-5211-4623-af48-108c063f669c/issues` — create strategic tickets (set `goalId`, `parentId`, `assigneeAgentId`).
- `POST /api/companies/2b70f2d3-5211-4623-af48-108c063f669c/agents` — hire (board approval gated).
- `GET /api/companies/2b70f2d3-5211-4623-af48-108c063f669c/approvals?status=pending` — pending approvals.

## Visibility repo
- Location: `/paperclip/tools/meteogatto-ops/` (cloned during HB-0 by DevOps).
- Read `inbox/` at every HB start for user directives.
- Write your strategy docs under `PLANS/` and decisions under `decisions/`.

