# Verification agent (Fase 4, sub-agent self-check)

Lancia un sub-agent (tool Task) che rilegge i contenuti sintetizzati (le righe CSV preparate e i testi delle schede) prima del rendering. Se non puoi lanciare un sub-agent, esegui questa checklist nello stesso contesto. Il report `raw/verification-report.md` (o `raw-deep/verification-report.md` se i grezzi della run stanno in `raw-deep/`, vedi `research-waves.md`) va scritto in ogni caso, anche nel fallback senza sub-agent: è l'evidenza che la Fase 4 è avvenuta.

## Check universali

- [ ] Ogni numero ha label `[Dato]`/`[Stima]`/`[Ipotesi]` e data.
- [ ] Nessuna contraddizione interna tra sezioni o file.
- [ ] Confidenza coerente con l'evidenza (una sola fonte Tier 3 non regge un'alta confidenza).
- [ ] Gap dichiarati ovunque servano.
- [ ] Dati stale (oltre 12-18 mesi) flaggati.
- [ ] Nessuna falsa corroborazione (stessa fonte contata due volte).

## Check specifici company-mapping

- [ ] Stato lead (`CALDA`/`FREDDA`/`NON VERIFICATO`, con lista di provenienza citata per nome, evento, anno, note) presente e coerente con la Fase 1.
- [ ] La mossa è formulata come proposta: nessun mittente interno prescritto, nessuna scadenza né riferimento temporale relativo (il dossier non deve invecchiare; le date assolute sui fatti sono ok). Il dossier non chiama mai "CRM" le liste lead.
- [ ] Persone classificate decisore/influencer/utente.
- [ ] (Deep) ganci presenti e che passano il creepy test.
- [ ] Lista fonti cliccabile in ogni scheda.

## Esito

- Problemi critici (almeno uno): fermati, presentali all'utente, chiedi se correggere o procedere.
- Solo warning: correggili (o riportali in una riga di sintesi) e procedi.
- **A verifica superata** (con le eventuali correzioni applicate): appendi le righe preparate al datastore `company-mapping-db.csv` (vedi `data-store.md`) e passa alla Fase 5.
