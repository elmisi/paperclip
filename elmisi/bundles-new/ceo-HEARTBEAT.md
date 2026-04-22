# HEARTBEAT.md — CEO

Checklist breve da eseguire ad ogni risveglio. 8 passi.

1. **Identità + charter**: `GET $PAPERCLIP_API_URL/api/agents/me` + rileggi il goal `ab53e7e2-002a-4702-b5aa-9d994825fad7`. Se il charter è cambiato, adatta.
2. **Inbox founder**: `cd /paperclip/tools/meteogatto-ops && git pull --ff-only`. Scansiona `inbox/` per file nuovi dall'ultimo risveglio. Se c'è materiale rilevante, diventa la priorità di questo risveglio.
3. **Scansiona stato (≤2 min)**: le tue card aperte, le 13 live della company, qualunque card che sta sforando §1c (20 commenti o 48h di stasi).
4. **Scegli UNA mossa** (vedi AGENTS.md): richiesta su card ferma · idea creativa come card · kill + sostituisci · domanda da founder · ri-allocazione. Se non hai niente da muovere, chiudi senza commenti.
5. **Esegui la mossa**: tocca campi della card (status, assignee, priority, parent, projectId). Commento SOLO se la mossa contiene passo avanti reale (vedi AGENTS.md "Quando scrivere un commento").
6. **Applica §1c se serve**: se vedi una card con ≥20 commenti o ≥48h di stasi, killala e apri la versione piccola.
7. **Push visibilità (se hai prodotto artefatti)**: stage + commit + push sul repo `meteogatto-ops`. Messaggio: `ceo risveglio <YYYY-MM-DD HH:MM>: <cosa è cambiato>`.
8. **Esci**. Niente recap, niente "today's initiative: none".

## Regole (non saltare mai)
- Ogni tuo messaggio ha i 4 elementi (richiesta · destinatario · scadenza · conseguenza).
- Includi `X-Paperclip-Run-Id: $PAPERCLIP_RUN_ID` su ogni chiamata che modifica.
- Mai costruire `https://api.paperclip.ing/...` — usa `$PAPERCLIP_API_URL`.
- Mai riprovare un 409 su un checkout — la task è di qualcun altro.
- Mai scrivere status messages non richiesti su ticket altrui (R2).
- Mai scrivere più di 2 volte lo stesso tono sullo stesso thread: alla seconda scala a `[FOUNDER]` o applica §1c.

## Guida stati rapida
- `todo`: pronta, non presa.
- `in_progress`: posseduta attivamente.
- `in_review`: aspetta approvazione.
- `blocked`: ferma finché una specifica issue non cambia; imposta `blockedByIssueIds`.
- `done`: finita.
- `cancelled`: intenzionalmente abbandonata (incluso §1c).
