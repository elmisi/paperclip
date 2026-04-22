# TOOLS.md — DevOps

## Adapter
- Runtime: `opencode_local`. Model: `openrouter/nvidia/nemotron-3-super-120b-a12b:free`.

## CLI inside your container
- `curl`, `jq`, `git`, `gh` (authenticated as `elmisi`), `ssh`, `scp`, `rsync`, `python3`, `ripgrep`.
- `docker`, `docker compose`.
- `opencode` — your adapter CLI.

## Target environment
- **VPS OVH** `57.128.170.108` (4 vCPU / 8 GB / 160 GB SSD, no GPU).
- SSH client is pre-installed inside the Paperclip container. Credentials stored in env / `~/.ssh/` once wired.
- Reverse proxy: **Caddy** (auto TLS for 4 domains).
- CI/CD: GitHub Actions → SSH deploy.

## HB-0 priority — bootstrap `meteogatto-ops` + Amigo

GitHub auth is already wired: the container has `gh` authenticated as `elmisi` (HTTPS) and `gh auth git-credential` set as git credential helper. You do NOT need a PAT or deploy key.

1. `gh repo create elmisi/meteogatto-ops --private -d "Meteo Gatto agent artifacts"`.
2. `gh repo clone elmisi/meteogatto-ops /paperclip/tools/meteogatto-ops`.
3. Scaffold the layout from charter §Artifact visibility → Layout:
   ```
   mkdir -p /paperclip/tools/meteogatto-ops/{inbox,PLANS,decisions,reports,meetings}
   touch /paperclip/tools/meteogatto-ops/{inbox,PLANS,decisions,reports,meetings}/.gitkeep
   ```
4. Commit & push initial scaffold on `main`.
5. Clone Amigo: `git clone https://github.com/elmisi/amigo.git /paperclip/tools/amigo` and install its deps per its README.
6. Smoke-test one Pollinations image generation through Amigo end-to-end.
7. Verify `git push` from at least 2 agent workspaces (CEO and Engineer) against `meteogatto-ops`.
8. Close the ticket with the verified recipe + Amigo invocation example + `inbox/` read pattern in the comment.

## Endpoints you use most
- `GET /api/companies/2b70f2d3-5211-4623-af48-108c063f669c/issues?assigneeAgentId=<your id>` — your queue.
- `POST /api/issues/:id/comments` — deploy notes, escalations.
