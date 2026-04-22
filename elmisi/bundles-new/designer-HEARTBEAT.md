# HEARTBEAT.md — Designer

Checklist breve da eseguire ad ogni risveglio. 8 passi.

1. **Identità + charter**: `GET $PAPERCLIP_API_URL/api/agents/me` + rileggi il goal `ab53e7e2-002a-4702-b5aa-9d994825fad7`. Se il charter è cambiato, adatta.
2. **Inbox founder**: `cd /paperclip/tools/meteogatto-ops && git pull --ff-only` (se fallisce → degraded mode). Scansiona `inbox/` per input nuovi.
3. **Guarda le tue card aperte**: `GET /api/companies/2b70f2d3-.../issues?assigneeAgentId=<tu>&status=todo,in_progress,in_review,blocked`.
4. **Scegli UNA card** (prima `in_progress` più vecchia, altrimenti priorità top in `todo`). Se tutte `blocked` con `blockedByIssueIds` vuoto, applica R6.
5. **Fai UN passo avanti concreto** (commit / post / decisione / chiusura).
6. **Aggiorna lo stato** della card. Commento SOLO se passo avanti reale (vedi AGENTS.md §Commenti).
7. **Se la card sfora R6** (≥20 commenti o ≥48h stasi) → applica la regola, apri la versione piccola.
8. **Esci**. Niente recap, niente template a sezioni obbligatorie.

## Regole (non saltare mai)
- Ogni messaggio che scrivi è conforme ai 4 elementi (richiesta · destinatario · scadenza · conseguenza) solo se stai facendo una richiesta; altrimenti è un close comment di 1-2 righe.
- Includi `X-Paperclip-Run-Id: $PAPERCLIP_RUN_ID` su ogni chiamata che modifica.
- Mai costruire `https://api.paperclip.ing/...` — usa `$PAPERCLIP_API_URL`.
- Mai riprovare un 409 su un checkout — la task è di qualcun altro.
- Mai scrivere status messages non richiesti su ticket altrui (R2).

## Guida stati rapida
- `todo`: pronta, non presa.
- `in_progress`: posseduta attivamente.
- `in_review`: aspetta approvazione.
- `blocked`: ferma finché una specifica issue non cambia; imposta `blockedByIssueIds`.
- `done`: finita.
- `cancelled`: intenzionalmente abbandonata (incluso R6).
