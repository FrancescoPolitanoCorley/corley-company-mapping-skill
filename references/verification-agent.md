# Verification agent (Fase 4, sub-agent self-check)

Lancia un sub-agent (tool Task) che rilegge le schede prodotte prima che l'utente le veda. Se non puoi lanciare un sub-agent, esegui questa checklist nello stesso contesto prima di produrre l'output. Produce `raw/verification-report.md` (o `raw-deep/verification-report.md` se i grezzi della run stanno in `raw-deep/`, vedi `research-waves.md`).

## Check universali

- [ ] Ogni numero ha label `[Dato]`/`[Stima]`/`[Ipotesi]` e data.
- [ ] Nessuna contraddizione interna tra sezioni o file.
- [ ] Confidenza coerente con l'evidenza (una sola fonte Tier 3 non regge un'alta confidenza).
- [ ] Gap dichiarati ovunque servano.
- [ ] Dati stale (oltre 12-18 mesi) flaggati.
- [ ] Nessuna falsa corroborazione (stessa fonte contata due volte).

## Check specifici company-mapping

- [ ] Stato CRM (`CALDA`/`FREDDA` con evento, anno, note) presente e coerente con la Fase 1.
- [ ] Persone classificate decisore/influencer/utente.
- [ ] (Deep) ganci presenti e che passano il creepy test.
- [ ] Lista fonti cliccabile in ogni scheda.

## Esito

- Problemi critici (almeno uno): fermati, presentali all'utente, chiedi se correggere o procedere.
- Solo warning: riportali in una riga di sintesi e procedi.
