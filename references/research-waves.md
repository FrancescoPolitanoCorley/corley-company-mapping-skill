# Wave di ricerca (Fase 2, fan-out parallelo per azienda)

## Come si lanciano le wave

Usa il tool Task per il dispatch di sub-agent in parallelo, uno o più per azienda. Se il dispatch di sub-agent non è disponibile nell'ambiente, esegui le wave in sequenza nello stesso contesto. In entrambi i casi gli agent (o le wave eseguite in sequenza) scrivono solo in `raw/{slug}/wave-X.md`. Se l'azienda è già stata coperta in una run di tier inferiore, i grezzi del Deep vanno in `raw-deep/{slug}/wave-X.md`: non sovrascrivere i grezzi precedenti, sono l'audit trail della run Standard. Non scrivono i deliverable finali: la separazione tra grezzo e sintesi è un vincolo. Ogni wave applica `research-principles.md` e `honesty-protocol.md`.

**Liste lunghe: lavora a lotti.** Oltre le ~8 aziende non lanciare tutto insieme: dividi la lista in lotti di 5-8 aziende e completa il ciclo Fasi 2-3-4 (ricerca, sintesi, verifica con append delle righe al CSV) per ogni lotto prima di passare al successivo. A fine lotto aggiorna `PROGRESS.md` con le aziende completate. Tre ragioni: il numero di agent concorrenti resta gestibile (3 wave x 8 aziende = 24 agent è il tetto pratico), una run interrotta riprende dal primo lotto incompleto invece di rifare tutto (le aziende completate sono già nel datastore), e gli errori si vedono al primo lotto invece che a fine lista. Il rendering (Fase 5) avviene una volta sola, a lista completa, leggendo il CSV.

Nei path, `{slug}` è l'`id` del datastore (`data-store.md`: kebab-case, senza forma societaria, es. `kedrion-biopharma`). Un'azienda è "già coperta in un tier inferiore" se `company-mapping-db.csv` contiene una riga con lo stesso `id` in tier Standard, oppure se esiste già `raw/{slug}/` da una run precedente.

Un round di ricerca è un ciclo di query, fetch e valutazione dei risultati: si formulano le query, si recuperano le fonti, si valuta cosa è stato trovato e cosa manca, e si decide se serve un altro ciclo. I numeri indicati nello scaling (2-3 Standard, 4-6 Deep) si riferiscono a questi cicli per wave.

## Wave A — Azienda & finanza

Obiettivo: capire cosa fa davvero l'azienda, il modello di business, la dimensione, i dipendenti, ricavi e utile aggiornati e datati, il gruppo di appartenenza, i round di investimento e lo stato di salute finanziaria. Fonti: visure e bilanci (Tier 1 per le private), comunicati e relazioni FY (per le quotate).

Cattura anche i **trigger di timing** ("perché ora"): operazioni M&A, round recenti, nomine di nuovi C-level, espansioni, scadenze o cambi di contratto. E i **segnali di dimensione del deal**: numero dipendenti, fatturato, scala dell'IT, che servono alla sintesi per stimare la banda di deal.

## Wave B — Tecnologia & AWS

Obiettivo: maturità cloud, stack tecnologico, iniziative AI pubbliche (anche da piano industriale), legame con AWS (case study, partnership, job posting su competenze cloud), livello di utilizzo AWS. Identifica l'**incumbent** (il fornitore o il cloud concorrente già in casa, es. OCI, Azure, un system integrator): serve alla sintesi per costruire la contro-leva al "perché non l'incumbent". Le claim tecniche vanno verificate su fonte primaria (documentazione, repository, demo).

## Wave C — Persone

Obiettivo: organigramma (vedi `people-mapping.md`), classificazione decisore/influencer/utente, contatti prioritari. Parte dall'output lead della Fase 1: i contatti già noti si arricchiscono. In Deep aggiunge il layer personale e i ganci.

## Scaling Standard vs Deep

- Standard: Wave A + Wave B + organigramma essenziale (Wave C), contatto prioritario senza ganci. 2-3 round di ricerca per wave.
- Deep: aggiunge il layer personale completo e i ganci su tutti i contatti chiave, lo studio del prodotto alla fonte e una triangolazione più aggressiva. 4-6 round per wave.
- L'override dell'utente vince sempre.

## Template di prompt agente (per wave, per azienda)

"Sei un ricercatore. Azienda: {nome}. Angolo: {angolo}. Esegui {Wave X} secondo le regole in {skill-dir}/references/research-principles.md e {skill-dir}/references/honesty-protocol.md (per la Wave C leggi anche {skill-dir}/references/people-mapping.md). Scrivi i risultati grezzi in {path-grezzi}/wave-X.md con label `[Dato]`/`[Stima]`/`[Ipotesi]`, date e fonti. Dichiara i gap con come verificarli."

Chi lancia gli agent risolve i segnaposto PRIMA del dispatch: `{skill-dir}` è il path assoluto della directory della skill e `{path-grezzi}` è il path assoluto di `raw/{slug}` (o `raw-deep/{slug}`, regola sopra). I sub-agent girano nella directory di lavoro: un path relativo `references/...` per loro non esiste, e senza le regole procederebbero senza label né protocollo di onestà.
