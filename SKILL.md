---
name: corley-company-mapping
description: Mappa potenziali aziende clienti per il team sales/marketing Corley. Data una lista di aziende (o una singola), produce per ciascuna una scheda con business, dati finanziari aggiornati, maturità tecnologica e iniziative AI, legame con AWS, organigramma e contatti, stato nel CRM Corley (CALDA/FREDDA). Due profondità: Standard e Deep (con ganci personali). Trigger: "mappa queste aziende", "company mapping", "qualifica questi lead", "scheda azienda", "chi contatto in [azienda]", "prepara la call su [azienda]", "analizza questa lista di aziende", "company intelligence", "dossier azienda".
---

# Corley Company Mapping

Trasforma una lista di aziende (o una singola) in schede di mappatura strutturate, coerenti e azionabili per il team sales e marketing di Corley. Sostituisce il lavoro manuale azienda per azienda con un processo ripetibile, parallelizzabile e con l'onestà delle fonti integrata nel flusso.

## Quando si attiva e cosa produce

Si attiva quando l'utente fornisce una o più aziende da qualificare per la pipeline Corley. Copre due bisogni con lo stesso processo:

- Sgrezzare in fretta liste lunghe di lead (es. i 40 nomi di un export), per capire quali valgono un contatto.
- Preparare a fondo un singolo account prima di una call (deep ABM), con organigramma e ganci personali sui contatti chiave.

L'output è un singolo file HTML self-contained, scritto nella directory di lavoro: CSS inline, nessun asset esterno, apribile con un doppio clic. Contiene una tabella riepilogativa in testa e una card per azienda raggruppate per settore. Le label `[Dato]`/`[Stima]`/`[Ipotesi]` e lo stato CRM (`CALDA`/`FREDDA`) sono evidenziati con badge colorati; le fonti sono link cliccabili. L'HTML è il formato di lavoro corrente.

La ricerca gira come fan-out di sub-agent in parallelo che scrivono in `raw/`; la sintesi legge `raw/` e produce le schede; un sub-agent di verifica rilegge tutto prima che l'utente veda l'output. Per il fan-out usa il tool Task (dispatch di sub-agent in parallelo, uno o più per azienda); se il dispatch di sub-agent non è disponibile nell'ambiente, esegui le wave in sequenza nello stesso contesto. In entrambi i casi i risultati grezzi finiscono in `raw/{azienda}/wave-X.md`. Gli agent non scrivono mai i deliverable finali: la separazione tra grezzo e sintesi è un vincolo.

## Reference loading (progressive disclosure)

`SKILL.md` resta sottile e descrive solo flusso e checkpoint. Il dettaglio operativo vive nei file `references/`, caricati una sola volta, alla fase che li usa.

| File | Quando leggerlo |
|---|---|
| `references/research-principles.md` | una volta, inizio Fase 2 |
| `references/honesty-protocol.md` | una volta, inizio sessione |
| `references/crm-crossref.md` | inizio Fase 1 |
| `references/research-waves.md` | quando si lanciano gli agent di Fase 2 |
| `references/people-mapping.md` | all'avvio della Wave C (Fase 2) |
| `references/synthesis.md` | inizio Fase 3 |
| `references/verification-agent.md` | inizio Fase 4 |
| `references/scheda-template.md` | Fase 3 (scrittura schede) |

Leggi `references/honesty-protocol.md` all'inizio della sessione: le sue regole valgono per ogni fase, non solo per una.

## Flusso a 6 fasi

### Fase 0 — Intake & angolo

Ricevi la lista o la singola azienda. L'angolo di default è quello Corley: "è un buon lead per consulenza AWS (migrazione, modernizzazione, GenAI) e chi contatto per primo?". L'utente può fornire un angolo diverso per la run, che vince sempre.

Determina il tier: `Standard` di default; `Deep` se l'utente lo richiede o se l'input è un singolo account da preparare per una call. Crea o riprende `PROGRESS.md` nella directory di lavoro. Se esiste già, riparti dalla prima fase incompleta. Scheletro di `PROGRESS.md`:

```
# Company Mapping — {data}
Aziende: {lista o singolo account}
Angolo: {default Corley o override}
Tier: {Standard | Deep}
Fasi: [ ] 0 intake  [ ] 1 CRM  [ ] 2 ricerca  [ ] 3 sintesi  [ ] 4 verifica  [ ] 5 output (HTML+PDF)
Note: {decisioni, gap aperti, aziende ancora da completare}
```

### Fase 1 — CRM cross-reference (sempre, prima del web)

Prima di qualunque ricerca web, leggi `references/crm-crossref.md` ed esegui la scansione del CRM Corley su Google Drive. Matching fuzzy sul nome azienda. Per ogni azienda classifica `CALDA` (almeno un match) o `FREDDA`; se calda, raccogli per ciascuna persona evento, anno, note, livello AWS, status, account manager. Questo output informa la Wave C: i contatti già in CRM vanno arricchiti, non ricostruiti da zero.

### Fase 2 — Ricerca in wave (fan-out parallelo per azienda)

Leggi `references/research-waves.md` e `references/research-principles.md`. Lancia gli agent con il tool Task (dispatch di sub-agent in parallelo, uno o più per azienda), una wave alla volta. Se il dispatch di sub-agent non è disponibile nell'ambiente, esegui le wave in sequenza nello stesso contesto. In ogni caso gli agent o le wave scrivono solo in `raw/{azienda}/wave-X.md`.

- Wave A: azienda e finanza (business, dimensione, ricavi e utile datati, gruppo, round, salute finanziaria).
- Wave B: tecnologia e AWS (maturità cloud, stack, iniziative AI pubbliche, legame con AWS).
- Wave C: persone (organigramma, classificazione decisore/influencer/utente, contatti prioritari; in Deep, layer personale e ganci). Leggi `references/people-mapping.md` all'avvio di questa wave.

Il tier regola la profondità (round di ricerca, fonti, ganci), come da `references/research-waves.md`.

### Fase 3 — Sintesi

Leggi `references/synthesis.md` e `references/scheda-template.md`. Leggi tutti i file in `raw/`, connetti i segnali tra le wave, riconcilia i conflitti, applica le label e le date, deriva pain point, leve per Corley e approccio consigliato. La sintesi è ragionamento, non formattazione. Scrivi le schede secondo il template.

### Fase 4 — Verifica

Leggi `references/verification-agent.md` e lancia il sub-agent di self-check (tool Task) sulle schede prodotte, prima che l'utente le veda. Se non puoi lanciare un sub-agent, esegui la checklist di verifica nello stesso contesto prima di produrre l'output. Se emergono problemi critici, fermati, presentali all'utente e chiedi se correggere o procedere. Se ci sono solo warning, riportali in una riga e procedi.

### Fase 5 — Output

Genera il file HTML self-contained nella directory di lavoro: tabella riepilogativa ordinata per priorità (coda di lavoro), schede raggruppate per settore, e in chiusura solo la legenda (le note di una singola azienda stanno nella sua card). Segui il contratto di stile e l'implementazione di riferimento in `references/scheda-template.md`. Poi genera il PDF dall'HTML (stessa resa) come descritto nello stesso file. Aggiorna `PROGRESS.md` a sessione conclusa.

## Standard vs Deep

| | Standard (default) | Deep |
|---|---|---|
| Wave A + B | sì | sì, con studio del prodotto alla fonte (doc/repo/demo) |
| Organigramma (Wave C) | essenziale | layer personale completo |
| Ganci sui contatti | nessuno (solo contatto prioritario) | 2-3 ganci autentici su ogni contatto chiave |
| Round di ricerca per wave | 2-3 | 4-6, triangolazione più aggressiva |

Il tier regola la profondità dell'agente, non il tempo dell'utente, che lancia e rivede senza restare nel loop. L'override dell'utente vince sempre.

## Regole non negoziabili

Valgono in ogni fase e sono dettagliate in `references/honesty-protocol.md`: niente invenzione (gap dichiarato con "come verificarlo" prima di una cella vuota, mai un valore inventato); label `[Dato]`/`[Stima]`/`[Ipotesi]` e data su ogni affermazione; flag sui dati probabilmente stale (un dato è stale quando è probabilmente superato rispetto a oggi, tipicamente bilanci oltre 12-18 mesi); conflitti riconciliati ad alta voce; creepy test sui ganci; solo fonti pubbliche, uso professionale e proporzionato, niente dati sensibili (GDPR).
