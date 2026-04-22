# TOOLS.md — Designer

## Adapter
- Runtime: `opencode_local`. Model: `opencode-go/mimo-v2-omni`.

## CLI inside your container
- `curl`, `jq`, `git`, `gh` (authenticated as `elmisi`), `python3`, `ripgrep`.
- `opencode` — your adapter CLI.

## Primary generation tool — Amigo (charter-locked)
- **Public API endpoint:** `https://amigo.elmisi.com` (as of 2026-04-20).
- **Auth:** Bearer token `amg_FZihZHOdf-PTvawkhQPNduOh4zk2DXicrKq92DHXFic` (CEO-provided).
- **Docs:** `https://amigo.elmisi.com/api/docs`.
- **Allowed backends:** `local-flux2-klein` (fast, good quality) and `local-playground` (quality, slow). Use only these two.
- **Other backends:** `pollinations` (remote, free) and `openrouter` (remote, paid) are available but not preferred per CEO directive.
- **Invocation:** HTTP POST to `/api/generate` with JSON `{"prompt": "...", "backend": "local-flux2-klein"}` and Authorization header.
- **Legacy path:** `/paperclip/tools/amigo/` (cloned from `https://github.com/elmisi/amigo` by DevOps during HB-0) — still可用但 prefer public API.
- **Rate limit:** One request at a time — server not suited for parallel requests. **Sequential only.** Do not send multiple concurrent requests.

## Output conventions
- Three ratios per asset: `1x1`, `9x16`, `2x3`.
- Commit to visibility repo under `reports/design/<YYYY-MM-DD>/<mood>-<locale>/`.
- Metadata file per batch: `prompt.txt` + `weather_mood.txt` + `locale.txt`.

## Visibility repo
- Location: `/paperclip/tools/meteogatto-ops/`.
- Read `inbox/` at HB start — the user may drop reference images or style directives there.
- Write assets under `reports/design/...` as specified above.

## Endpoints you use most
- `GET /api/companies/2b70f2d3-5211-4623-af48-108c063f669c/issues?assigneeAgentId=<your id>` — your queue (from MM).
- `POST /api/issues/:id/comments` — ship the asset commit link + 2-line caption.
