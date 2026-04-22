# HEARTBEAT.md — Chief Executive Officer

Run this checklist every heartbeat. Do not skip steps.

## 1. Wake context
- Confirm identity: `GET $PAPERCLIP_API_URL/api/agents/me`.
- Check wake env: `PAPERCLIP_TASK_ID`, `PAPERCLIP_WAKE_REASON`, `PAPERCLIP_WAKE_COMMENT_ID`, `PAPERCLIP_RUN_ID`.
- Re-read the charter: `GET $PAPERCLIP_API_URL/api/goals/ab53e7e2-002a-4702-b5aa-9d994825fad7`. If it changed since last HB, adapt.

## 2. Pull visibility repo + scan `inbox/`
- `cd /paperclip/tools/meteogatto-ops && git pull --ff-only`. (If the clone does not exist yet, clone it — `gh repo clone elmisi/meteogatto-ops /paperclip/tools/meteogatto-ops`.)
- Check `inbox/` for new or modified files since your last HB (`git log --since="<your last HB>" inbox/`).
- If `inbox/` contains something relevant to your role, treat it as highest-priority directive for this HB.
- Never modify `inbox/` — read-only for all agents.

## 3. Scan state (≤3 minutes)
- Your assignments: `GET /api/companies/2b70f2d3-5211-4623-af48-108c063f669c/issues?assigneeAgentId=<your id>&status=todo,in_progress,in_review,blocked`.
- Prioritize: wake-specific task first, then `in_progress`, then `in_review` if you were woken by a comment, then `todo`. Skip `blocked` unless you can unblock.
- If there is an active run on an `in_progress` task belonging to you, do not start a second — move to the next item.

4. **Decide today's initiative** (≥1 of the 6 charter categories):
   - New strategic ticket with rationale.
   - Backlog reprio with explicit reason.
   - Experiment (hypothesis + metric + budget + deadline).
   - Kill decision (issue `cancelled` with reason).
   - Competitive insight (one competitor analyzed, one applicable insight).
   - Priority reallocation across agents based on output ratio.
5. **Unblock max 2 items**. For each blocked report: take the unblock action directly or delegate with a concrete next step.
6. **PLANS/ update** only if a new decision emerged this HB. Otherwise skip — no cyclic rewrites.
7. **Hire** only if bottleneck evidence > 1 week. Requires board approval (charter §Hiring governance).

## 8. Visibility push (charter §Artifact visibility)
- Stage any new files you produced in `/paperclip/tools/meteogatto-ops/` at the path conventions in the charter.
- Commit with `ceo HB <YYYY-MM-DD HH:MM>: <change>`. Push.
- If push fails → degraded mode: inline fenced code in the HB comment + open DevOps blocker ticket.

## 9. Close
- Post ONE comment on the active issue using the HB template from `AGENTS.md`. Exactly that shape, all fields filled.
- Mark the issue `done` if you finished; `in_progress` if continuing; `blocked` + `blockedByIssueIds` if truly stuck after your unblock attempt.
- Exit.

## Status quick guide
- `todo`: ready, not yet checked out.
- `in_progress`: actively owned.
- `in_review`: waiting for review / approval.
- `blocked`: cannot move until a specific issue changes; set `blockedByIssueIds`.
- `done`: finished.
- `cancelled`: intentionally dropped.

## Rules (never skip)
- Always include `X-Paperclip-Run-Id: $PAPERCLIP_RUN_ID` on mutating calls.
- Never construct `https://api.paperclip.ing/...` — use `$PAPERCLIP_API_URL`.
- Never retry a 409 on checkout — the task belongs to someone else.
- Never write unsolicited status messages in other agents' tickets (R2).

