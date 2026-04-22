# Plan: Meteo Gatto — daje, ripuliamo la company

> **Obiettivo operativo**: ridurre drasticamente il rumore, rendere leggibile lo stato di avanzamento, esplicitare le richieste di intervento umano, e passare a **italiano** come lingua operativa.
>
> **Obiettivo di business** (non dimenticarlo mai): questa pulizia serve a **far crescere l'output**, non a rendere la board più ordinata. Il goal ultimo resta quello del charter: MVP Italia live, cat-powered weather ritual su IT/ES/EN in 12 mesi, 500k sessioni mensili, brand pronto per merch/sponsorship. Se dopo questa pulizia la company fa più commenti ma la stessa quantità di prodotto visibile, il piano ha fallito.

---

## Context (cosa succede oggi)

Company: **Meteo Gatto** (`2b70f2d3-5211-4623-af48-108c063f669c`), attiva su Zotac (`zotac.local:3100`). Budget: €14.33/€20 del mese. 6 agenti, tutti attualmente `idle`. 81 issue totali.

### Lo stato effettivo (verificato oggi, 2026-04-21)

| Metric | Valore | Nota |
|---|---|---|
| Issue totali | 81 | prefix `META`, counter a 83 |
| `done` | 54 | |
| `cancelled` | 14 | inclusi due CEO heartbeat ricorrenti (META-50, META-63) |
| `backlog` | 8 | ma **7 su 8 sono critical/high** — tutti blocker infra |
| `blocked` | 5 | tutti `critical` |
| `in_progress` / `in_review` | **0** | workflow degli stati non usato |
| Progetti | **0** | feature `/api/companies/:id/projects` presente ma inutilizzata |
| Goal attive | **1** (il charter) | nessun sotto-goal, nessuna milestone |
| Routines | 1 | "CEO heartbeat (every 3h)" |
| Commenti totali | 533 | ~6.5/issue, ma distribuiti molto male |
| Issue più rumorosa | META-23 con **171 commenti** | loop auto-retry + reset CEO |
| Priority | 23 critical, 54 high, 4 medium, 0 low | inflazione: nulla spicca |

**Sintesi in 3 righe**: c'è tanto storico chiuso (54 `done`) ma quasi nessun lavoro attivo visibile (0 `in_progress`). I 13 item ancora vivi sono tutti in `blocked/backlog critical`, mischiando blocker founder (credenziali, decisioni) con blocker platform (bug Paperclip) e con lavoro business — il founder non ha modo di separare "cosa mi serve da me" da "cosa sta girando". Il rumore dei commenti è concentrato su poche card che loopano (META-23 da sola = 32% del totale commenti), non distribuito.

### Cosa sta producendo il rumore (letto direttamente dai commenti META-23, META-82, META-37)

Tre fonti chiare, in ordine di impatto:

**1) Loop auto-retry / false-block / CEO reset** — il killer principale.
La sequenza osservata su META-23 nell'arco di 1h:
```
15:02:20  019131b9  CEO unblock action: status reset to `todo` …
15:02:33  db90ea40  In_progress restored. A2 trigger fires in 89 minutes. Holding.
15:03:13  db90ea40  15:03 Rome. A2 trigger in ~104 minutes. Holding.
15:03:30  sys       Paperclip automatically retried continuation … Moving it to `blocked`.
15:04:24  019131b9  CEO unblock action: status reset to `todo` …
15:04:33  db90ea40  15:04 Rome. False-block recovery. A2 trigger in ~103 min. Holding.
15:04:43  db90ea40  In_progress restored. 15:06 Rome. Holding until A2 trigger at 16:47.
15:05:12  db90ea40  15:05 Rome. Holding until 16:47 trigger.
15:05:30  sys       Paperclip automatically retried continuation … Moving it to `blocked`.
…
```
Ogni ~1-2 minuti un commento di auto-retry di sistema + un "Holding until X" dell'MM + un reset del CEO. Zero contenuto informativo, solo ceremonia. Da sola questa issue ha prodotto ~100+ commenti in un giorno.

**2) Heartbeat template lunghi e ridondanti**.
`AGENTS.md` di **ogni** agente impone a OGNI heartbeat (ogni 3h = 8×/giorno per CEO) di chiudere con UN commento che compila OBBLIGATORIAMENTE 4-5 sezioni:
- CEO: `PRODUCED · BLOCKERS · TODAY'S INITIATIVE · KILLS · REALLOC` (5 campi, "HB che finisce senza compilare ogni campo = role failure")
- MM/CTO: `PRODUCED · BLOCKERS · NEXT · LINKS` (4 campi)

Risultato: anche un HB a mani vuote ("nothing this HB") deve produrre ~200 parole di ceremonia. Esempio reale da META-82 (le ultime 4 righe sono tutte della stessa ora):
```
[CTO HB — 2026-04-21 22:00] 4-section dump
[CTO HB — 2026-04-21 22:22] 4-section dump  (22 min dopo)
[CTO HB — 2026-04-21 22:23] 4-section dump  (1 min dopo, per ritentare)
```

**3) Assenza della lingua italiana**.
Il charter (`SOUL.md` CEO) dice testualmente *"Italian or English in tickets, your choice; match the audience of the artifact"*. Nessun agente ha scelto l'italiano. Tutti scrivono in inglese, probabilmente perché i template HB, i ruoli (`CEO/CTO/CMO`), le capability e il charter stesso sono in inglese → forte anchoring.

### Cosa NON sta funzionando sul fronte "visibilità per l'utente"

- **Nessuna coda "needs human"**. Le 13 issue infra stuck (SSH VPS non funzionante META-28, git push auth META-11/15, Amigo missing META-18, Caddy routing META-83, TLS META-78…) sono tutte _blocker che richiedono l'utente_ ma stanno in `blocked/backlog critical` insieme a issue di business. Non c'è differenza visiva/API tra "bloccato da dipendenza interna" e "bloccato in attesa di decisione/credenziali utente".
- **Priority inflation**: 23 critical + 54 high su 81. Quando tutto è critico, niente è critico. Il founder apre la board e vede un muro rosso.
- **Stati workflow non usati**: 0 `in_progress` e 0 `in_review` su 81 issue. O sono `done/cancelled` (68) o `blocked/backlog` (13). Non c'è nessun segnale "sto lavorando su questo ADESSO".
- **0 progetti e 1 sola goal**. Impossibile raggruppare. Tutte le issue si appendono al mega-goal del charter. Nessuna milestone intermedia ("MVP Italia", "SEO pilot", "Brand identity") che permetta di capire a colpo d'occhio "l'MVP è al 60%".
- **`inbox/` del repo `meteogatto-ops` è il canale founder→agenti, ma non c'è il canale inverso**. Gli agenti scrivono report, ma non hanno una casella "attenzione founder: decidere questo" visibile al di fuori della board.
- **Il CEO non fa domande da founder**. Guardando il template HB (PRODUCED/BLOCKERS/TODAY'S INITIATIVE/KILLS/REALLOC) il CEO descrive stato, non chiede agli agenti *"a che punto è il prodotto?"*, *"cosa vi serve da me?"*, *"cosa non stiamo facendo?"*. Risultato: zero visione, zero pressione creativa, solo bookkeeping.

---

## Approach (strategia ad alto livello)

**Spirito guida** (dall'utente):
- *voglio vedere le cose che avanzano. Card piccole in modo che il flusso si vede.*
- *ognuno è responsabile delle proprie card, non delega se può fare lui.*
- *startup snella, niente burocrazia.*
- **Il CEO è "Superman" nel senso giusto**: è uno che, se vuole, **può ottenere tutto** — ha autorità e leva. NON è uno che *fa* tutto lui, e NON è il gestore della burocrazia più pesante del mondo. La sua energia va in direzione del risultato, non della procedura.

Attenzione: snellire la burocrazia **non** vuol dire rendere il CEO placido, ma non vuol nemmeno dire caricarlo di riti o di aggressività verbale. Il CEO smette di compilare template e inizia a **fare richieste chiare, a persone precise, con scadenza precisa**. Risultato atteso di ogni suo intervento: chiunque lo legga sa *chi deve fare cosa entro quando, e cosa succede se non lo fa*.

### Sequenza (non parallelismo)

Le leve **non sono indipendenti**: c'è un ordine preciso, e ogni fase ha un cancello di uscita verso la successiva. Ordine:

1. **Prima tagliamo il rumore** (Fase 1): aboliamo il template HB, inseriamo la regola "commento solo se passo concreto", attiviamo la regola dei 20 commenti / 48h, ri-incentramo il CEO. Riscriviamo `SOUL.md` e `AGENTS.md` del **solo CEO** in italiano come pilota. → Gate: il primo risveglio del CEO dopo questa fase deve essere chiaramente migliore (fa richieste con owner+scadenza, non scrive template). Se no, si itera *qui* prima di andare oltre.
2. **Poi rendiamo chiaro il canale founder** (Fase 2): convenzione `[FOUNDER]` + micro-template del body + filtro salvato. Conversione delle 5-6 issue stuck che in realtà sono attese sull'utente.
3. **Poi organizziamo i progetti** (Fase 3): 3 bucket iniziali (`MVP-IT`, `GROWTH`, `FOUNDER/OPS`). Migrazione delle 13 issue attive dentro i progetti. Creazione delle milestone M1/M2/M3 di MVP-IT.
4. **Poi chiudiamo l'italianizzazione sul resto del team** (Fase 4): CTO e MM prima, poi DevOps/Engineer/Designer. Si procede solo se le fasi 1-3 hanno retto.
5. **Poi misuriamo** (Fase 5): metriche north-star dopo 1 settimana.

Le leve tematiche (le 6 della versione precedente del piano) restano come *concetti*, ma vivono **dentro** le fasi di cui sopra.

Alternativa valutata e scartata: "archiviamo tutto e ricominciamo". Scartata perché 54 done sono lavoro vero e alcune issue stuck (SSH, Caddy, Amigo) hanno informazione di valore. Si pulisce selettivamente.

**Vincolo di implementazione**: tutti i commit di questo piano (eventuali modifiche al repo paperclip) vanno su branch `elmisi`, **mai su master**.

---

## Detailed Changes

### 1. Abolizione della burocrazia "heartbeat" — taglio al rumore

Il concetto di **HB** (heartbeat con template obbligatorio a 4-5 sezioni, da compilare ogni 3h pena "role failure") va tolto. È stato la fonte principale della burocrazia che ha fatto girare a vuoto la company. Sostituito con regole minimali.

#### 1a. La regola del "commento solo se serve"

Sostituisce ogni template HB in ogni AGENTS.md (6 file). Versione italiana definitiva:

```markdown
## Quando scrivere un commento su una card

Lo stato della card vive nei suoi campi (status, assignee, priority, parent).
Non nei commenti. Quindi: commento solo se hai qualcosa di concreto da dire.

Scrivi UN commento quando:
- hai fatto un passo avanti concreto (committato del codice, pubblicato un post,
  deciso qualcosa di irreversibile, creato una sotto-card) → 1-2 righe: cosa hai
  fatto + link.
- stai chiudendo la card → close comment minimo: cosa hai consegnato, link all'artefatto.
- ti serve l'utente → apri una card `[FOUNDER]` separata (vedi §4) e stop.

NON scrivere un commento quando:
- stai "aspettando il prossimo trigger" o "holding until X" → aspetta e basta.
- stai "pianificando i prossimi step" senza averne eseguito nessuno.
- vuoi fare il recap dello stato → lo stato è già nei campi della card.
- ti stai svegliando dal cron → se non hai niente di nuovo, torna a dormire.

**Eccezione esplicita — commento di blocco (una sola volta)**:
se ti sei davvero bloccato e hai bisogno di qualcosa (decisione, handoff,
credenziale, artefatto), scrivi UN commento con questo formato fisso:

    BLOCCATO su: <una riga, cosa ti manca>
    A chi: <agente specifico, o [FOUNDER] se serve l'utente — vedi §4>
    Cosa ho provato: <max 2 righe>
    Default se nessuno risponde in 24h: <azione concreta che eseguirai da solo>

Dopo averlo scritto: aggiorna status a `blocked` con `blockedByIssueIds` e NON
scrivere altri commenti di blocco sulla stessa card. Se il blocco persiste e
vuoi far pressione, crea una NUOVA card (es. `[FOUNDER] SSH VPS`) che referenzia
questa. "Silenzio assoluto" è sbagliato quanto "loop di reminder".
```

Questo elimina di fatto:
- il vincolo "HB senza TODAY'S INITIATIVE = role failure" (forzava initiative di scarso valore)
- i 4-5 campi obbligatori (PRODUCED/BLOCKERS/NEXT/LINKS/ecc.)
- i commenti "Holding until 16:47" del Marketing Manager
- i reset "CEO unblock action" automatici del CEO sui false-block

#### 1b. Tagliare la routine CEO da 3h a 6h

Da `0 */3 * * *` a `0 */6 * * *` — 4 risvegli/giorno invece di 8. **Il founder vuole vedere cose che avanzano**, quindi alla routine **aggiungiamo anche**: ogni volta che si sveglia, il CEO deve toccare almeno un campo di una card (spostare in `in_progress`, aggiornare priorità, chiudere, creare nuova child) — **non deve scrivere commenti**, deve muovere campi. Se non ha muovere, ok, chiude il giro senza traccia.

#### 1c. Regola del limite — "20 commenti OPPURE 48h senza avanzamento"

È una rete di sicurezza universale. Regola inserita nel charter (R6) e in ogni AGENTS.md, applicabile dall'agente o manualmente dall'utente.

Una card fa scattare il limite se si verifica **una qualunque** delle due:
- **≥ 20 commenti senza essere chiusa** (`done` o `cancelled`) — indicatore di rumore.
- **≥ 48h senza un avanzamento concreto sul campo `status` o su un artefatto collegato** (nessun nuovo commit linkato, nessun passaggio `todo → in_progress → done`) — indicatore di stasi silenziosa.

Perché due soglie: la sola soglia commenti chiude troppo presto i thread tecnici legittimi (un debug complesso può richiedere 25 scambi) e troppo tardi le card ferme senza commenti (una card pianificata 5 giorni fa e mai toccata resta invisibile). La doppia soglia coglie entrambi i casi.

Quando scatta, il protocollo è:

    1. Chi se ne accorge (agente o utente) la marca `cancelled` con commento:
       "Limite §1c raggiunto (commenti / stasi) — riaperta con contesto condensato".
    2. Apre una nuova card con:
       - titolo: stesso o evoluzione breve
       - descrizione: 5-10 righe MAX — obiettivo, cosa è stato fatto, cosa manca,
         link alla card chiusa
       - scope più piccolo (se la prima non ce la faceva, il pezzo era troppo grosso)
    3. Se anche la seconda sfora il limite → apri un [FOUNDER] per revisione
       strategica dell'obiettivo.

Questa regola non dipende dal fix dei bug della piattaforma (false-block watchdog, auto-retry loop): se il watchdog spamma, scatta il contatore; se il lavoro resta in stallo senza rumore, scatta il timer.

#### 1d. Applicazione retroattiva a META-23 e META-82

- **META-23** (171 commenti): applicare retroattivamente la regola. `cancelled` + riaprire **come 3-4 card piccole sotto il progetto GROWTH**, ognuna in scope ≤ 2 giorni (es.: "MM: 1 copy test emotion-first per Milano", "MM: pubblicare post A/B su IG giorno 1", "MM: rilevare FPI giorno 1 e scrivere sul ticket"). Nessuna "experiment con 14 post in 7 giorni" — troppa carne per una singola card.
- **META-82** (10+ commenti di loop CTO/DevOps sul Caddy): stessa cura — `cancelled`, si apre META-xxx "DevOps: 5 righe Caddyfile per routare www → nextjs" come unica card specifica, con criterio di accettazione "`curl -I https://www.meteogatto.it` → HTTP 200". Basta.

### 1bis. Il CEO — autorità che ottiene, non esecutore né compliance officer

Il rischio della §1 ("niente commenti se non hai fatto niente") è rendere il CEO silenzioso e placido. **È l'opposto di quello che serve.** Ma il rovescio speculare è altrettanto pericoloso: il CEO non deve diventare aggressivo, verboso o iperattivo. È **uno che, se vuole, può ottenere tutto** — ha autorità e leva. Il suo mestiere non è *fare* i task, non è *sorvegliare* la board, non è *gridare*. È **porre la richiesta giusta alla persona giusta in modo che la cosa succeda**.

#### 1bis-a. Forma fissa di ogni intervento del CEO

Ogni commento / card / messaggio del CEO deve avere **tutti e quattro** questi elementi. Senza uno di essi, non scrive:

1. **Richiesta / domanda / decisione precisa** (non "vediamo come va X", ma "MM, mi serve il copy template a 3 varianti").
2. **Destinatario preciso**: un singolo agente per nome, o `[FOUNDER]`. Mai "al team" / "a chi può".
3. **Scadenza precisa**: data o ora specifica Europe/Rome.
4. **Conseguenza / default**: cosa succede se la scadenza passa senza risposta — un kill, un takeover, un'alternativa, un escalation.

Risultato atteso: chiunque legga il messaggio capisce subito *chi deve fare cosa entro quando, e cosa succede se non lo fa*. 2-3 righe sono sufficienti.

#### 1bis-b. Cosa deve fare il CEO ad ogni risveglio

Sostituisce il vecchio HB template. Nel `SOUL.md` e `AGENTS.md` del CEO:

```markdown
## CEO — il tuo mestiere

Sei l'imprenditore visionario di Meteogatto. Il tuo lavoro NON è riempire
template, NON è fare i task al posto degli altri, NON è sorvegliare la board.
Il tuo lavoro è FAR SUCCEDERE COSE attraverso richieste chiare e leve intelligenti.

Ogni tuo intervento ha sempre 4 elementi (vedi §1bis-a):
- richiesta precisa · destinatario preciso · scadenza precisa · conseguenza

Ad ogni risveglio scegli almeno UNA di queste mosse:

1. **Richiesta su card ferma**: individua la card che blocca di più l'MVP, e
   fai UNA richiesta al suo proprietario. Esempio (2 righe):
     "CTO: META-83 ferma da 2 giorni. Entro domani 18:00 Rome chiusa, oppure
      apri [FOUNDER] se dipende da me."
2. **Idea creativa come card**: trasforma un'intuizione in una card, scope
   piccolo, assegnata. Titoli esempio: "Pilot TikTok: 10 video gattini con
   meteo di domani", "Format 'gatto che sbaglia la previsione' come ricorrente",
   "Partnership canile locale x meteo Milano".
3. **Kill + sostituisci**: se una card ha fatto scattare §1c (20 commenti o
   48h di stasi), chiudila e apri la versione piccola che la rimpiazza.
4. **Domanda da founder**: fai una delle 4 domande obbligatorie (§1bis-d).
5. **Ri-allocazione**: se un agente è sovraccarico e un altro è scarico,
   sposta 1-2 card fra loro con una riga di motivazione.

NON fare:
- compilare template con campi obbligatori (non esistono più).
- scrivere "monitoring the situation" / "today's initiative: none" / recap.
- fare "unblock action" automatico sui false-block (lascia che scatti §1c).
- fare il task al posto degli altri: non sei executor, sei leva.
- insistere con lo stesso tono sullo stesso thread più di due volte: dopo la
  seconda, scala a [FOUNDER] o applica §1c (kill).
- essere aggressivo, sarcastico o verboso. Chiarezza > veemenza.
- chiedere permesso prima di creare una card: se è reversibile (R4), creala.
```

#### 1bis-c. Segnali "utili" vs segnali "rumorosi"

La distinzione che guida la scrittura del CEO: **segnale utile** chiarisce chi deve fare cosa entro quando. **Segnale rumoroso** descrive stato senza chiederne il cambio. Nessun segnale utile è mai aggressivo: è solo chiaro.

| Rumoroso (eliminare) | Utile (scrivere) |
|---|---|
| `[CEO HB — nothing produced]` | "MM: mi mandi il copy template a 3 varianti entro stasera 20:00? Se no, scelgo io lo scope e lo chiudiamo a 2 varianti domani." |
| `CEO unblock action: status reset to todo` (loop) | "META-37 kill (§1c, 20 commenti). Aperta META-84 'pagina Roma con 1 gattino online, entro giovedì'. Owner: Engineer." |
| `Today's initiative: monitoring META-23` | "Nuova card per MM: 'format gatto-confuso su errori previsione, 1 post pilota Milano entro venerdì'. Scope piccolo, scadenza fissa." |
| "PRODUCED: nothing. BLOCKERS: none. KILLS: none. REALLOC: none." | "CTO: dov'è la demo MVP? Mi serve 3 righe di status entro domani 12:00. Se non arriva, scelgo io lo scope ridotto." |

#### 1bis-d. Le domande del CEO (lo stand-in del founder)

Il CEO è lo **stand-in del founder** quando il founder non c'è. Deve fare le stesse domande che farebbe l'utente, e farle **proattivamente** — non aspettando che qualcuno gli porti la pappa pronta. Da inserire direttamente in `AGENTS.md` del CEO:

```markdown
## Le domande che devi fare (almeno una per risveglio)

Sei lo stand-in del founder. Fai le domande che farebbe lui.
Le quattro domande obbligatorie, da ruotare:

1. **"A che punto è il prodotto?"** → Ogni domanda ha i 4 elementi di §1bis-a.
   Esempio (a CTO):
     "CTO: ricapitolami in 3 righe dove siamo su MVP-IT e cosa manca per M1.
      Entro il tuo prossimo risveglio. Se non arriva, chiedo a Engineer."
2. **"Come posso aiutarvi a fare meglio il vostro lavoro?"** → chiedilo esplicito
   a CTO e MM. Esempio:
     "MM: cosa ti serve da me per spingere GROWTH? Un brief, un kill, un budget,
      un'ora di creative direction? Una riga entro stasera.
      Se non arriva, riguardiamo l'allocazione di GROWTH la settimana prossima."
3. **"Vi serve qualcosa?"** → A ruota libera. Budget, credenziali, decisioni.
   Formula che mette l'agente in condizione di rispondere "sì, X" con minimo
   attrito. Esempio:
     "Tutti: una riga ciascuno entro domani 18:00. Se c'è una cosa che il
      founder può sbloccarvi in 2 minuti e vi cambia la settimana, scrivetela."
4. **"Cosa non state facendo che dovremmo fare?"** → domanda di visione. Forza
   l'emergenza delle cose non dette. Almeno 1 volta al giorno.

Stile: diretto, non aggressivo. Le richieste del CEO hanno peso PERCHÉ sono
chiare e rispettate nei tempi, non perché sono urlate.

Se la risposta non arriva entro la scadenza annunciata: il CEO esegue la
conseguenza che ha dichiarato (non ripete la domanda, non minaccia). L'autorità
è nel mantenere la parola data nei default, non nel fare pressione emotiva.

Anti-pattern (non fare):
- domanda retorica generica ("com'è il morale?")
- micro-delega travestita da domanda ("potresti dirmi quando pensi di finire X?"
  → questa info la trovi tu guardando la card)
- domanda senza destinatario preciso
- ripetere la stessa domanda più di due volte senza scalare o applicare §1c.
```

#### 1bis-e. Il CEO come driver dei progetti

Combinazione con §3 (progetti). Il CEO è l'unico agente autorizzato (e tenuto) a:
- creare nuovi progetti quando emerge una nuova area di sforzo
- chiudere progetti quando l'obiettivo è raggiunto o abbandonato
- spostare card tra progetti se la riallocazione ha senso
- riprioritizzare card tra progetti (es. "questa settimana tutto su MVP-IT, BRAND e GROWTH aspettano")

### 2. Italianizzazione totale

Niente mix, niente "tech in EN". **Tutto in italiano**: charter, AGENTS.md, template commenti, descrizioni issue, commit message degli agenti. Gli unici elementi che restano in inglese sono:
- nomi tecnici invariabili (file, tool, campi API, variabili d'ambiente)
- i nomi dei ruoli come **sigla** (CEO/CTO/CMO/DevOps/Engineer/Designer), perché sono usati come identificatori dal sistema
- contenuti destinati a pubblico straniero (copy EN/ES per social/SEO, ovviamente)

#### 2a. Rollout in due passi (non tutto insieme)

Tradurre in blocco 18 file (6 agenti × 3 file: AGENTS.md + SOUL.md + HEARTBEAT.md) + charter è coerente con "italiano totale", ma pesante da verificare. Si fa in due passi:

**Passo 1 (nella Fase 1 del piano)** — CEO come pilota:
- Charter (`PATCH /api/goals/ab53e7e2-...`).
- CEO: i suoi 3 file (`AGENTS.md`, `SOUL.md`, `HEARTBEAT.md`).

Dopo il primo risveglio del CEO, si verifica che il comportamento sia chiaramente migliore (§Fase 1, gate). Solo allora procede il Passo 2.

**Passo 2 (nella Fase 4 del piano)** — resto del team:
- Prima CTO e MM (gli unici con cui il CEO parla direttamente, per R2 del charter).
- Poi DevOps, Engineer, Designer.

Se alla verifica del primo passo il comportamento non regge, si itera sulla riscrittura del CEO prima di toccare gli altri 5 agenti.

#### 2b. Cosa va riscritto in ogni file

1. **Charter** (`description` del goal company): riscrittura in italiano idiomatico, NON word-by-word.
   - eliminare del tutto le sezioni "HB Template" e "CEO Initiative Engine binding mandate" — non esistono più.
   - sostituire con **"Regole operative (versione breve)"**: R1 bias all'azione · R2 niente chiacchiere · R3 un proprietario per card · R4 reversibile decidi, irreversibile scala UNA volta · R5 output > processo · **R6 (nuova)** regola del limite 20 commenti / 48h · **R7 (nuova)** ownership, non deleghi se puoi farla tu.
   - tabella "Locked-in technical decisions": nomi tecnici restano, commenti "Reason" tradotti.

2. **AGENTS.md** per agente (6 file, path `/paperclip/instances/default/companies/2b70f2d3-.../agents/<agentId>/instructions/AGENTS.md`): tradotti integralmente, con la sezione "quando scrivere un commento" di §1a e la sezione "## Progetti" di §3c. Il solo CEO include anche §1bis (autorità, forma fissa, 4 domande).

3. **SOUL.md** per agente: `"Scrivi in italiano. Sempre."` (non più "your choice").

4. **HEARTBEAT.md** per agente: checklist sostituita con versione breve (8 passi):

       1. `git pull` di meteogatto-ops, leggi inbox/ se c'è roba nuova.
       2. `GET /api/agents/me` per confermare identità.
       3. Guarda le tue card aperte: assignee = tu, status in (todo, in_progress).
       4. Scegli UNA card (più vecchia in_progress, altrimenti priorità top in todo).
       5. Fai UN passo avanti concreto (commit / post / decisione / chiusura).
       6. Aggiorna lo stato della card. Commento SOLO se passo avanti reale (§1a).
       7. Se la card sfora il limite §1c → applica la regola, apri la versione piccola.
       8. Esci.

#### 2c. Le issue già esistenti non vengono tradotte

I 54 `done` e i 14 `cancelled` restano storia in inglese. Solo le issue **attive** (13) + tutte le NUOVE sono in italiano. Esplicitarlo nel charter per non far partire un vanity-rewrite.

#### 2d. Gestione `instructionsBundleMode: managed`

Il CEO ha `instructionsBundleMode: managed` nell'`adapterConfig`. Verificare prima di scrivere a mano su `/paperclip/instances/.../AGENTS.md` se esiste un processo che sovrascrive il file da una sorgente esterna (skill sync, repo). Se è `managed` da skill, modifico la skill sorgente (oppure temporaneamente `manual`). Check: modifico il file, aspetto 5 min, rileggo; se torna indietro → sync attivo.

### 3. Progetti + milestone come unità di tracking

Oggi: 0 progetti, tutte le 81 issue appese al company goal. Obiettivo: **3 progetti** come bucket iniziali, duri da confondere. Si parte minimali e si aggiungono sotto-progetti (`BRAND`, `INFRA`) solo se il volume reale di card lo giustifica. Le 13 issue attive finiscono tutte in uno dei 3; le 68 done/cancelled restano come sono.

#### 3a. I 3 progetti iniziali (via `POST /api/companies/:companyId/projects`)

| Short | Nome | Obiettivo | Issue iniziali dalle 13 vive |
|---|---|---|---|
| `MVP-IT` | MVP Italia online | `meteogatto.it` live con 3 città pilota, voice IT, 3-day unattended run | META-82, META-83, META-78, META-37 |
| `GROWTH` | Growth & Experiments | Esperimenti social, SEO pilot, newsletter, brand/content — include le card che sostituiscono META-23 | (nuove card piccole in sostituzione di META-23) |
| `FOUNDER-OPS` | Founder & Platform blockers | Tutto ciò che richiede l'utente (credenziali, decisioni, spesa) **o** il codice Paperclip (bug piattaforma, runtime, visibilità) | META-11, META-15, META-18, META-28, META-66, META-76, META-10, META-33 |

**Why**: a 13 issue vive, 3 bucket sono il minimo per fare distinzione (prodotto live vs growth vs blocker), e massimo per non ri-frammentare subito il lavoro. `FOUNDER-OPS` tiene insieme "serve l'utente" e "serve un fix alla piattaforma" perché sono entrambi blocker che l'agente non può risolvere da solo — la differenziazione arriverà con i `[FOUNDER]` (§4).

**Split futuri (non ora, solo se il volume cresce)**:
- `BRAND` si stacca da `GROWTH` quando ci saranno ≥ 5 card di brand/mascotte/voice aperte contemporaneamente.
- `INFRA` si stacca da `FOUNDER-OPS` quando ci saranno ≥ 5 card di puro DevOps/infra aperte.

La decisione di stacco la prende il CEO (§1bis-e) quando si manifesta il volume.

#### 3b. Milestone via parent-issue

La board Paperclip supporta parent/child. Non serve un nuovo concetto "milestone": ogni progetto ha 2-4 parent "pietra miliare" con child operative sotto. Esempio `MVP-IT`:

    [MVP-IT] M1: Dominio + TLS funzionanti  (parent)
      ├─ META-82 CTO: restore user-facing response
      ├─ META-83 DevOps: Caddy routing fix
      └─ META-78 www.meteogatto.it nothing online

    [MVP-IT] M2: 3 city pages servite da Next.js  (parent)
      └─ META-37 CTO: SEO pilot foundation

    [MVP-IT] M3: 3-day unattended run demo  (parent)
      └─ (da creare)

L'utente apre la board, filtra per progetto `MVP-IT`, vede 3 parent e il % completamento per ognuno. A colpo d'occhio: *"siamo bloccati su M1, M2/M3 non partono"*.

#### 3c. Istruzione agli agenti

In ogni AGENTS.md, sezione nuova:
```markdown
## Progetti
Ogni issue **deve** essere assegnata a un progetto (`projectId`). I 3 progetti
iniziali sono MVP-IT, GROWTH, FOUNDER-OPS. Se non sai dove metterla, scegli
FOUNDER-OPS e scrivi una riga nel commento spiegando perché — il CEO ri-classifica
se serve. Se stai creando una pietra miliare, è una issue con `parentId=null`
e children collegate via `parentId`.
```

### 4. Coda esplicita "serve intervento del founder"

#### 4a. Convenzione `[FOUNDER]` nel titolo + micro-template nel body

Ogni issue che richiede azione dall'utente deve avere titolo che inizia con `[FOUNDER]`. Esempi:
```
[FOUNDER] SSH key VPS: serve chiave privata o accesso tramite provider
[FOUNDER] decidere nome mascotte IT (proposte: Nuvolino, Piovoso, Sollecito)
[FOUNDER] autorizzare spesa €5 per dominio .it
```

Il prefisso segnala urgenza/canale, ma il titolo da solo non basta. Ogni card `[FOUNDER]` deve avere nel **body** un micro-template fisso di 3 righe:

    Serve da te: <l'azione specifica che il founder deve compiere, in una riga>
    Entro quando: <data/ora Europe/Rome, o "asap" solo se davvero è bloccante>
    Default se non rispondi: <azione concreta che l'agente eseguirà autonomamente>

Esempio completo:

    Titolo: [FOUNDER] Credenziali SSH VPS 57.128.170.108

    Body:
    Serve da te: chiave SSH privata per root@vps o link al pannello provider per
                  aggiungere la chiave pubblica ssh-ed25519 AAAA... (generata).
    Entro quando: 2026-04-23 18:00 Europe/Rome.
    Default se non rispondi: META-27 (provisioning VPS) viene messa in pausa e
                  DevOps sposta il lavoro su setup locale Docker fino a sblocco.

**Why** prefisso + body fisso: il titolo dice *"c'è qualcosa per te"*, il body dice *"ecco esattamente cosa, entro quando, e cosa faccio io se non rispondi"*. Senza il body, la coda FOUNDER diventa una lista di urgenze confuse.

#### 4b. Regola di creazione

Nell'AGENTS.md di ogni agente:
```markdown
## Quando serve l'utente → apri un [FOUNDER]

Se sei bloccato su qualcosa che SOLO l'utente può sbloccare, NON scrivere
commenti di reminder. Fai esattamente questo:

1. Apri una issue con `title` che inizia con `[FOUNDER]` + priorità `high`.
2. Assegnala al progetto `FOUNDER-OPS`.
3. Body = micro-template fisso (§4a): `Serve da te` · `Entro quando` · `Default se
   non rispondi`. Nessun altro contenuto nel body.
4. Blocca l'issue parent con `blockedByIssueIds=[<id-del-FOUNDER>]`.
5. Non scrivere altri commenti sulla parent finché il `[FOUNDER]` non è chiuso.
6. Se la scadenza del `[FOUNDER]` passa senza risposta → esegui il Default dichiarato
   e chiudi il `[FOUNDER]` come `cancelled` con nota "default applicato".

Esempi validi:
- credenziali mancanti (SSH, API keys)
- decisione di branding (nome, voice, visual)
- approvazione spesa > $0
- fix necessario al codice di Paperclip stesso (non al codice della company)

Esempi NON validi (non aprire [FOUNDER]):
- "non so come fare X" → chiedi al tuo manager (CEO o CTO)
- "il tool Y non funziona" → apri issue normale a DevOps, non all'utente
- "sono incerto sulla direzione" → propone decisione + default 24h (R2)
```

#### 4c. Filtro salvato sulla board

UI: filtro URL `?title=%5BFOUNDER%5D` → bookmark. Alternativa via API:
```
GET /api/companies/:id/issues?search=[FOUNDER]
```

Verificare che il full-text search di `issueService` supporti i bracket. Se no, usare label dedicata `needs-founder` creata via `POST /api/companies/:id/labels`. **Unverified:** non ho verificato il comportamento del search con `[`.

#### 4d. Coerenza col canale inbox del repo meteogatto-ops

Il charter già dice "user writes to `inbox/` in meteogatto-ops". È il canale utente→agenti. I `[FOUNDER]` sono il canale inverso agenti→utente. Insieme coprono entrambe le direzioni. Vanno esplicitati entrambi nel charter come canali paritari.

---

## Edge Cases and Risks

### R1 — Il CEO ignora le nuove regole
- **Likelihood**: medium. Il charter è lungo e gli AGENTS.md nuovi vanno sovrascritti. Il runtime potrebbe avere cache.
- **Impact**: il rumore ritorna entro 3h.
- **Mitigation**: dopo la modifica, forzare un restart del container `docker-server-1` o invalidare la cache istruzioni (l'adapter `codex_local` legge `instructionsFilePath` all'avvio della run). Verificare via un HB di test: il prossimo commento del CEO segue il nuovo formato?
- **Exit clause**: se dopo 2 HB il CEO continua con il vecchio template, è il modello (`gpt-5.3-codex`) che sta anchor-ing sul charter vecchio presente nel contesto della run. Rimedio: svuotare l'`executionWorkspace` dell'agente / resettarne la working dir.

### R2 — Uso dei progetti rompe l'assegnazione esistente
- **Likelihood**: low. `projectId` è nullable su `issues` (abbiamo verificato: 0/81 lo hanno, nessuna validazione obbliga).
- **Impact**: le issue esistenti non sono riorganizzate, i nuovi progetti sono vuoti.
- **Mitigation**: migrazione batch via API su tutte le issue non-`done/cancelled` (13 issue): `PATCH /api/issues/:id {projectId: <uuid>}`. Una tantum, scripted.
- **Exit clause**: se l'endpoint PATCH non supporta `projectId`, accetteremo che i progetti vengano popolati solo da issue nuove. Le 13 vecchie restano unassigned.

### R3 — Gli agenti non capiscono il `[FOUNDER]` e abusano del prefisso
- **Likelihood**: medium-high. "Prefisso nel titolo" è debole — è solo testo.
- **Impact**: l'utente si trova 50 `[FOUNDER]` e disattiva il filtro.
- **Mitigation**: regole strette nell'AGENTS.md (gli "esempi non validi" sopra). Dopo 1 settimana audit: ogni `[FOUNDER]` deve essere o (a) azione utente esplicita presa o (b) declassato a issue normale.
- **Exit clause**: se l'abuso continua, aggiungere una label `needs-founder` gestita server-side tramite hook che controlla il prefisso — blocca la creazione se il corpo dell'issue non cita una delle categorie approvate.

### R4 — L'italianizzazione peggiora la qualità output dei modelli (downgraded)
- **Likelihood**: bassa. Configurazione modelli attuale (aprile 2026):
  - CEO → `codex_local` / `gpt-5.3-codex` → italiano solido.
  - MM → `claude_local` / `claude-sonnet-4-6` → italiano ottimo.
  - CTO → `opencode_local` / **`opencode-go/kimik2.6`** (nuovo) → italiano buono, da verificare su terminologia tecnica/comandi infra.
  - DevOps / Engineer / Designer → `opencode_local` con modelli free vari → italiano accettabile, qualità variabile.
- **Impact**: su modelli free (o su kimik2.6 per comandi shell molto tecnici) potrebbe servire mix IT/EN spontaneo. I nomi tecnici invariabili restano in EN comunque.
- **Mitigation**: il primo risveglio di ogni agente post-traduzione è punto di controllo. Se il CTO su kimik2.6 produce comandi o edit sbagliati perché la terminologia tecnica in italiano gli suona male, override **per quel solo agente** concedendo "mix IT/EN con priorità IT nei commenti discorsivi, EN nei comandi shell/infra".
- **Exit clause**: nessun rollback globale. Eventuale degrado → override mirato per quell'agente.

### R5 — Il false-block watchdog loopa di nuovo
- **Likelihood**: media (l'utente ha aggiornato di recente la piattaforma, non sa se è fixato).
- **Impact**: card tornano a loopare con auto-retry.
- **Mitigation**: **la regola dei 20 commenti (§1c)** è la rete di sicurezza universale, non specifica per questo bug. Qualunque card che superi i 20 commenti viene killata e rigenerata col contesto condensato. Il loop si estingue dopo 20 commenti, non dopo 171.
- **Exit clause**: se anche la rigenerata looppa, al secondo loop si apre `[FOUNDER]` per revisione strategica (§1c punto 3).

### R6 — Il CEO non adotta il nuovo ruolo e resta placido
- **Likelihood**: media. Passare da "compila template" a "fai richieste chiare con scadenza e conseguenza" è un salto di stile per il modello (gpt-5.3-codex), che tende al conservatorismo.
- **Impact**: il cambio di procedura riduce il rumore ma non genera più output — peggior peggioramento possibile (silenzio senza spinta).
- **Mitigation**: `SOUL.md` del CEO riscritto con forma fissa dei messaggi (§1bis-a) + esempi concreti di segnali utili vs rumorosi (§1bis-c). `AGENTS.md` con la lista delle 4 domande obbligatorie (§1bis-d).
- **Exit clause**: se dopo 1 settimana il CEO non ha creato card creative e non ha fatto richieste chiare a CTO/MM, il problema è il modello — valutare switch a un'altra variante Codex o aggiungere 2-3 esempi in-context nei file istruzioni (tecnica del few-shot in loco).

### R7 — Ownership > delegation produce agenti sovraccarichi
- **Likelihood**: media. Se MM fa da copy-writer, designer-lite e social-poster, accumula troppo.
- **Impact**: throughput crolla, un agente diventa collo di bottiglia.
- **Mitigation**: il CEO monitora (§1bis) il WIP per agente. Se un agente ha > 5 card `in_progress` o > 10 `todo`, il CEO **ri-alloca a livello di team** (non spezza una singola card in due assegnate a due agenti — sposta interi blocchi).
- **Exit clause**: se la ri-allocazione non basta, il CEO apre un `[FOUNDER]` per proporre hiring di un nuovo agente (board approval è on).

---

## Failure Modes and Degradation

### Se il runtime non rilegge gli AGENTS.md modificati
- Degraded behavior: il CEO continua col vecchio template.
- Threshold: verifica dopo 1 HB (≤3h).
- Fallback: `docker compose exec server sh -c "restart adapters"` o più bruscamente `docker compose restart server`. Non blocca nessun'altra company.

### Se i nuovi progetti non compaiono in UI
- Degraded behavior: le issue sono assegnate, ma l'utente non vede i progetti nel sidebar.
- Threshold: controllare `/api/companies/:id/projects` restituisce i 5 items.
- Fallback: refresh hard del browser; poi controllare `sidebar-preferences` endpoint.

### Se un agente crea troppi `[FOUNDER]` senza criterio
- Degraded behavior: rumore si sposta da commenti a issue (peggio).
- Threshold: > 5 `[FOUNDER]` aperti contemporaneamente.
- Fallback: manuale — l'utente chiude/declassa a issue normale con commento "regola 4b violata, riformulare".

### Se la routine CEO heartbeat viene spenta per sbaglio
- Degraded behavior: il CEO non si sveglia più, il decision-making si ferma.
- Threshold: nessun CEO HB per > 8h.
- Fallback: ricreare la routine via `POST /api/companies/:id/routines` con i parametri originali (salvarli prima di toccarla).

---

## Decisioni prese (dalle note dell'utente)

Tutte le open question precedenti sono chiuse. Riassunto delle decisioni, per chi apre il piano a freddo:

1. **Routine CEO**: da 3h a 6h. La frequenza minore non è la leva — la leva è che al risveglio il CEO muove cose (§1bis) invece di compilare template.
2. **META-23**: killata e sostituita da 3-4 card piccole sotto `GROWTH` (§1d). Niente "esperimento 14 post in 7 giorni" in una sola card.
3. **Bug false-block watchdog**: non lo risolviamo dal piano. La regola del limite §1c (20 commenti OPPURE 48h di stasi) è la rete di sicurezza universale e non dipende dallo stato del bug.
4. **Template HB**: abolito, non tradotto. Sostituito da §1a (commento solo se passo concreto) + §1bis (CEO: autorità + forma fissa + domande).
5. **Lingua**: italiano totale, rollout in due passi (§2a): prima CEO, poi CTO+MM, poi il resto.
6. **Progetti iniziali**: 3, non 5 (§3a): `MVP-IT`, `GROWTH`, `FOUNDER-OPS`. `BRAND` e `INFRA` si staccheranno quando il volume lo giustifica.
7. **`[FOUNDER]`**: prefisso nel titolo + micro-template fisso nel body (§4a): *Serve da te · Entro quando · Default se non rispondi*.
8. **Fase 1 è pilota**: niente Fase 2/3/4 finché il primo risveglio del CEO post-modifica non è chiaramente migliore (cancello esplicito fra fasi).
9. **Git**: tutti i commit vanno su branch `elmisi`, mai su `master`.

## Vincoli e regole di prima classe

- **R6** (charter): regola del limite 20 commenti / 48h (§1c).
- **R7** (charter): ownership > delegation. Agente fa le sue card. Ri-allocazione solo a livello di team, mai per singola card.
- **R8** (charter): italiano totale (salvo nomi tecnici + copy per pubblico estero).
- **Forma fissa CEO** (§1bis-a): ogni messaggio = richiesta + destinatario + scadenza + conseguenza. Senza tutti e quattro gli elementi, il CEO non scrive.
- **Branch `elmisi`**: vincolo di scrittura sul repo Paperclip. Memoria globale per future sessioni.

---

## Task Breakdown

Le fasi sono **sequenziali** con gate di uscita espliciti. Non si parte una fase successiva prima di aver chiuso il gate della precedente.

### Setup di riferimento (da fare una volta prima di Fase 0)

Tutti gli ID e gli endpoint che servono all'esecuzione. Un Claude che apre questo piano da zero deve poter recuperare tutto partendo da qui.

**Coordinate fisse**:
- Host Paperclip: `http://zotac.local:3100`
- Company `Meteo Gatto`: `2b70f2d3-5211-4623-af48-108c063f669c` (prefix `META`)
- Goal del charter: `ab53e7e2-002a-4702-b5aa-9d994825fad7`
- Container server su Zotac: `docker-server-1` (via `ssh zotac.local`)
- Path istruzioni agenti nel container: `/paperclip/instances/default/companies/2b70f2d3-5211-4623-af48-108c063f669c/agents/<agentId>/instructions/{AGENTS,SOUL,HEARTBEAT}.md`
- Repo visibilità: `elmisi/meteogatto-ops` (GitHub privato)

**Auth API (board-level)**:
```zsh
TOK=$(python3 -c "import json; print(json.load(open('/home/alessandro/.config/paperclip-zotac/credentials.json'))['boardApiToken'])")
# poi: curl -H "Authorization: Bearer $TOK" http://zotac.local:3100/api/...
```

**Discovery dinamico da fare una volta** (salvare i risultati in variabili shell per tutto l'esecuzione):

```zsh
# 1. UUID di ogni agente (per path AGENTS.md + per PATCH mirati)
curl -s -H "Authorization: Bearer $TOK" \
  "http://zotac.local:3100/api/companies/2b70f2d3-5211-4623-af48-108c063f669c/agents" \
  | python3 -c "import json,sys; [print(f\"{a['role']:10s} {a['name']:30s} {a['id']}\") for a in json.load(sys.stdin)]"

# 2. UUID della routine "CEO heartbeat" (per PATCH del cron 3h → 6h)
curl -s -H "Authorization: Bearer $TOK" \
  "http://zotac.local:3100/api/companies/2b70f2d3-5211-4623-af48-108c063f669c/routines" \
  | python3 -m json.tool

# 3. Lista delle 13 issue vive (per migrazione a projectId)
curl -s -H "Authorization: Bearer $TOK" \
  "http://zotac.local:3100/api/companies/2b70f2d3-5211-4623-af48-108c063f669c/issues?limit=100" \
  | python3 -c "import json,sys; d=json.load(sys.stdin); items=d.get('items',d) if isinstance(d,dict) else d; [print(f\"{i['identifier']:10s} {i['status']:10s} {i.get('priority','?'):10s} {i['id']}  {i['title'][:70]}\") for i in items if i['status'] in ('blocked','backlog','todo','in_progress','in_review')]"
```

**Repo Paperclip locale** (per eventuali modifiche al codice della piattaforma):
- Path: `/home/alessandro/Project/paperclip`
- Branch di lavoro: **`elmisi`**, MAI `master` (vincolo utente, memoria globale).
- Check prima di ogni commit: `git rev-parse --abbrev-ref HEAD` → deve dire `elmisi`.

**Scrittura file istruzioni agenti** (via docker exec sul container):
```zsh
# Leggere
ssh zotac.local "docker exec docker-server-1 cat /paperclip/instances/default/companies/2b70f2d3-5211-4623-af48-108c063f669c/agents/<AGENT_ID>/instructions/AGENTS.md"

# Scrivere (via heredoc, con attenzione a `instructionsBundleMode: managed`)
ssh zotac.local "docker exec -i docker-server-1 tee /paperclip/instances/default/companies/2b70f2d3-5211-4623-af48-108c063f669c/agents/<AGENT_ID>/instructions/AGENTS.md > /dev/null" < nuovo-agents.md
```

**Endpoint chiave usati dal piano**:
- `PATCH /api/goals/:id` — aggiornare charter
- `PATCH /api/routines/:id` — cambiare cron CEO
- `POST /api/companies/:id/projects` — creare i 3 progetti
- `PATCH /api/issues/:id` — assegnare `projectId`, cambiare `status`, `parentId`, `blockedByIssueIds`
- `POST /api/companies/:id/issues` — creare nuove card (incluse `[FOUNDER]`)
- `POST /api/companies/:id/labels` — fallback `needs-founder` se il search `[FOUNDER]` non funziona

**Memorie disponibili** (già in sessione, per contesto):
- `project_zotac_deployment.md` — come è deployata l'istanza
- `reference_paperclip_api.md` — token + endpoint key
- `feedback_git_branch_elmisi.md` — vincolo branch

---


### Fase 0 — prerequisiti

- [ ] Sono sul branch `elmisi` del repo Paperclip (`git rev-parse --abbrev-ref HEAD` → `elmisi`). Se no, `git switch elmisi`.
- [ ] Verificare che possiamo modificare direttamente i file sotto `/paperclip/instances/.../agents/<id>/instructions/` sul container `docker-server-1`. Test: modifica minima + rileggere dopo 5 min → se torna indietro, `instructionsBundleMode: managed` ha un sync attivo (trovare la sorgente prima di procedere).

**Gate Fase 0**: `instructionsBundleMode` ha un percorso di scrittura chiaro (sia manuale sia via skill source).

### Fase 1 — pilota: CEO + regole base (è qui che si decide se il piano funziona)

- [ ] `META-23` → `cancelled` (motivo §1c retroattivo), riaperta come 3-4 card piccole sotto `GROWTH` (scope ≤ 2 giorni ciascuna).
- [ ] `META-82` → `cancelled`, riaperta come 1 card tecnica specifica con criterio di accettazione `curl -I https://www.meteogatto.it → 200`.
- [ ] Riscrivere il blocco "quando scrivere un commento" (§1a) **nel solo `AGENTS.md` del CEO** per ora (gli altri 5 agenti seguono in Fase 4).
- [ ] Riscrivere `SOUL.md` e `AGENTS.md` del CEO secondo §1bis (forma fissa, mosse al risveglio, segnali utili vs rumorosi, 4 domande, driver dei progetti).
- [ ] Modificare la routine "CEO heartbeat" da `0 */3 * * *` a `0 */6 * * *` via `PATCH /api/routines/:id` (preservare gli altri campi).
- [ ] Inserire R6 (limite §1c) e R7 (ownership) nel charter (`PATCH /api/goals/ab53e7e2-...`).
- [ ] Restart del container `docker-server-1`.

**Gate Fase 1** (critico — non procedere se non soddisfatto): al primo risveglio del CEO post-modifica, il suo output deve soddisfare **tutti** questi criteri:
- segue la forma fissa (richiesta + destinatario + scadenza + conseguenza) in almeno una mossa
- NON compila più il vecchio template a 5 sezioni
- NON fa "unblock action" su card bloccate (lascia scattare §1c o apre `[FOUNDER]`)
- il messaggio è chiaro, non aggressivo/sarcastico, non verboso

Se non soddisfatto: iterare SOLO su §1bis (AGENTS.md del CEO) prima di proseguire. Indagare cache / workspace dell'agente. Non estendere agli altri 5 agenti finché il pilota non regge.

### Fase 2 — canale FOUNDER

- [ ] Test: `GET /api/companies/.../issues?search=%5BFOUNDER%5D` restituisce solo le card `[FOUNDER]`? Se no, creare label `needs-founder` (`POST /api/companies/:id/labels`) e usarla in alternativa al search.
- [ ] Aggiungere in `AGENTS.md` del CEO la sezione "Quando serve l'utente → apri un `[FOUNDER]`" (§4b) + il micro-template del body (§4a).
- [ ] Convertire manualmente le card vive che sono davvero blocker-utente in `[FOUNDER]` con il body-template:
  - META-28 SSH VPS, META-11/15 git push auth, META-18 Amigo missing, META-76 false-block watchdog (platform bug).
- [ ] Bookmark URL del filtro `[FOUNDER]` sulla board.

**Gate Fase 2**: ≤ 5 `[FOUNDER]` aperti contemporaneamente + ognuno ha il body a 3 righe corretto + ognuno ha priorità alta e `projectId = FOUNDER-OPS`.

### Fase 3 — progetti + milestone

- [ ] Creare i 3 progetti via `POST /api/companies/2b70f2d3-.../projects`: `MVP-IT`, `GROWTH`, `FOUNDER-OPS`. Conservare gli UUID.
- [ ] Migrazione: `PATCH /api/issues/:id` con `{projectId: <uuid>}` per ognuna delle 13 issue vive, secondo la tabella §3a.
- [ ] Creare le 3 parent-issue di `MVP-IT` (M1 dominio+TLS, M2 city pages, M3 3-day demo). Collegare via `parentId` le child già esistenti.
- [ ] Aggiungere "## Progetti" (§3c) nell'`AGENTS.md` del CEO (gli altri agenti lo riceveranno in Fase 4).
- [ ] Autorizzare il CEO come unico owner di creazione/chiusura/split progetti (§1bis-e).

**Gate Fase 3**: board `MVP-IT` mostra 3 milestone con % di completamento leggibile; il CEO ha toccato almeno 1 progetto al suo prossimo risveglio.

### Fase 4 — rollout resto del team (italiano + regole)

Si estende a tutti gli altri 5 agenti quello che era sul solo CEO in Fase 1.

- [ ] Tradurre e riscrivere `AGENTS.md` di CTO e MM (prima).
- [ ] Tradurre e riscrivere `SOUL.md` e `HEARTBEAT.md` di CTO e MM.
- [ ] Dopo 1 risveglio di ciascuno verificato OK → ripetere per DevOps, Engineer, Designer.
- [ ] Tutti hanno §1a (quando scrivere un commento), §1c (limite), §3c (progetti), §4b (quando aprire `[FOUNDER]`).
- [ ] Se `instructionsBundleMode: managed` sta sovrascrivendo → skill source o switch temporaneo a `manual`.

**Gate Fase 4**: tutti e 6 gli agenti hanno prodotto almeno 1 commento in italiano che rispetta §1a.

### Fase 5 — verifica dopo 1 settimana: 3 north-star + diagnostica

**Tre metriche north-star** — queste decidono se il piano ha funzionato:

1. **Card chiuse nella settimana** (segnale di output): target **≥ 10**.
2. **Card che sforano il limite §1c** (segnale che il rumore è sotto controllo): target **0**.
3. **`[FOUNDER]` aperti > 48h senza risposta utente o default applicato** (segnale che il canale funziona): target **0**.

Se tutte e 3 sono a target → il piano ha funzionato. Se anche una sola è fuori → rientrare nel piano per capire dove.

**Diagnostica secondaria** (non sono obiettivi, servono solo se una north-star è fuori target):
- commenti/settimana (indicatore di baseline rumore): ~ < 50
- distribuzione priority sulle issue vive: < 30% critical
- card create dal CEO nella settimana: ≥ 3 (indicatore che il CEO spinge)
- lingua dei commenti: > 95% italiano

Volutamente non mettiamo altri checklist di successo: ogni metrica in più è una tentazione a ricostruire la burocrazia che stiamo eliminando.
