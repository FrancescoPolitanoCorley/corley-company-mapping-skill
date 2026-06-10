---
name: corley-company-mapping
description: Mappa potenziali aziende clienti per il team sales/marketing Corley. Data una lista di aziende (o una singola) produce un dossier HTML+PDF di qualificazione lead con cross-reference sulle liste lead Corley su Drive. Due profondità, Standard e Deep. Trigger: "mappa queste aziende", "company mapping", "qualifica questi lead", "scheda azienda", "chi contatto in [azienda]", "prepara la call su [azienda]", "analizza questa lista di aziende", "company intelligence", "dossier azienda".
---

# Corley Company Mapping

Trasforma una lista di aziende (o una singola) in un dossier di qualificazione lead per il team sales e marketing di Corley: un file HTML self-contained + PDF + DOCX editabile nella directory di lavoro, più una riga per azienda nel datastore CSV locale (`company-mapping-db.csv`), da cui HTML e PDF si rigenerano senza rifare la ricerca. Due bisogni, stesso processo: sgrezzare liste lunghe (Standard) e preparare a fondo un singolo account per una call (Deep). Onestà delle fonti integrata: label, gap dichiarati, mai inventare.

## Reference loading (progressive disclosure)

`SKILL.md` resta sottile e descrive solo flusso e checkpoint. Il dettaglio operativo vive nei file `references/`, caricati una sola volta, alla fase che li usa.

| File | Quando leggerlo |
|---|---|
| `references/research-principles.md` | una volta, inizio Fase 2 |
| `references/honesty-protocol.md` | una volta, inizio sessione |
| `references/lead-crossref.md` | inizio Fase 1 |
| `references/research-waves.md` | quando si lanciano gli agent di Fase 2 |
| `references/people-mapping.md` | all'avvio della Wave C (Fase 2) |
| `references/synthesis.md` | inizio Fase 3 |
| `references/verification-agent.md` | inizio Fase 4 |
| `references/scheda-template.md` | Fase 3 (scrittura schede) |
| `references/data-store.md` | Fase 3 (preparazione righe CSV) e per rigenerare da CSV |
| `references/report-template.html` | Fase 5 (rendering): template HTML canonico, richiamato da `scheda-template.md` |
| `references/docx-template.md` | Fase 5 (output editabile DOCX) |
| `references/reference.docx` | Fase 5: stili DOCX per pandoc (`--reference-doc`, non si legge) |

Leggi `references/honesty-protocol.md` all'inizio della sessione: le sue regole valgono per ogni fase, non solo per una.

## Flusso a 6 fasi

### Fase 0 — Intake & angolo

**Prima azione, prima di tutto il resto: chiedi all'utente la profondità con una domanda a risposta multipla** (tool AskUserQuestion se disponibile; altrimenti stampala in chat e attendi la risposta). Salta la domanda solo se l'utente ha già dichiarato il tier nella richiesta. Le due opzioni, con implicazioni esplicite:

1. **Standard (consigliata per liste)** — qualifica essenziale: azienda & finanza, tecnologia & AWS, organigramma con contatto prioritario (senza layer personale). 2-3 round di ricerca per wave. Costa circa un terzo del Deep in tempo e token. Serve a sgrezzare e ordinare la coda di lavoro.
2. **Deep (consigliata per un singolo account da preparare per una call)** — tutto lo Standard più: layer personale dei contatti chiave (percorso, temi pubblici, stile, canale), studio del prodotto alla fonte (docs, repository, demo), triangolazione aggressiva, schede persona complete. 4-6 round per wave. Costa ~3x lo Standard (riferimento misurato: ~9 minuti e ~150k token per azienda).

Se la risposta non arriva (ambiente non interattivo), procedi in Standard e dichiaralo.

Ricevi la lista o la singola azienda. L'angolo di default è quello Corley: "è un buon lead per consulenza AWS (migrazione, modernizzazione, GenAI) e chi contatto per primo?". L'utente può fornire un angolo diverso per la run, che vince sempre.

Crea o riprendi `PROGRESS.md` nella directory di lavoro. Se esiste già, riparti dalla prima fase incompleta. Spunta ogni fase in `PROGRESS.md` al suo completamento, non a fine sessione: è ciò che rende possibile riprendere una run interrotta. Scheletro di `PROGRESS.md`:

```
# Company Mapping — {data}
Aziende: {lista o singolo account}
Angolo: {default Corley o override}
Tier: {Standard | Deep}
Fasi: [ ] 0 intake  [ ] 1 lead Drive  [ ] 2 ricerca  [ ] 3 sintesi  [ ] 4 verifica + append CSV  [ ] 5 output (HTML+PDF)
Lotto corrente: {n/k, aziende del lotto} (solo liste lunghe)
Completate: {aziende già verificate e appese al CSV}
Note: {decisioni, gap aperti}
```

### Fase 1 — Cross-reference lead Drive (sempre, prima del web)

Prima di qualunque ricerca web, leggi `references/lead-crossref.md` ed esegui la scansione delle liste lead Corley su Drive (shell-first sul mount locale, tool Drive come alternativa; lì c'è anche il fallback se le liste non sono accessibili). Matching fuzzy sul nome azienda. Per ogni azienda classifica `CALDA` (almeno un match) o `FREDDA`; se calda, raccogli per ciascuna persona evento, anno, note, livello AWS, status, account manager, email e telefono. Questo output informa la Wave C: i contatti già nei lead Drive vanno arricchiti, non ricostruiti da zero.

### Fase 2 — Ricerca in wave (fan-out parallelo per azienda)

Leggi `references/research-waves.md` e `references/research-principles.md`: lì vivono il dispatch dei sub-agent (tool Task), la regola dei lotti per le liste lunghe (oltre ~8 aziende: lotti di 5-8, ciclo Fasi 2-4 per lotto, rendering unico alla fine), il fallback senza sub-agent, i path dei grezzi (`raw/{slug}/`, e `raw-deep/{slug}/` per il Deep su azienda già coperta) e il template di prompt per gli agent.

- Wave A: azienda e finanza (business, dimensione, ricavi e utile datati, gruppo, round, salute, trigger di timing, segnali di dimensione).
- Wave B: tecnologia e AWS (maturità cloud, stack, iniziative AI pubbliche, legame AWS, incumbent).
- Wave C: persone (organigramma, decisore/influencer/utente, contatti prioritari; in Deep layer personale: percorso, temi, stile, canale). Leggi `references/people-mapping.md` all'avvio di questa wave.

### Fase 3 — Sintesi

Leggi `references/synthesis.md` e `references/scheda-template.md`. Leggi tutti i grezzi della run corrente (`raw/` o, per il Deep su azienda già coperta, `raw-deep/`), connetti i segnali tra le wave, riconcilia i conflitti, applica le label e le date, deriva la lettura dalle nostre angolazioni (ICP fit, pain, leve). La sintesi è ragionamento, non formattazione. Il dossier è la fotografia del cliente: niente materiale di ingaggio (bozze email, mosse, ganci, contro-leve). Per ogni azienda **prepara la riga del datastore** come da `references/data-store.md`: l'append avviene solo a verifica superata (Fase 4).

### Fase 4 — Verifica

Leggi `references/verification-agent.md` e lancia il sub-agent di self-check (tool Task) sui contenuti sintetizzati, prima del rendering. Se non puoi lanciare un sub-agent, esegui la checklist nello stesso contesto; il report di verifica va scritto in ogni caso. Se emergono problemi critici, fermati, presentali all'utente e chiedi se correggere o procedere. Se ci sono solo warning, correggili (o riportali in una riga) e procedi. **A verifica superata, appendi le righe preparate a `company-mapping-db.csv`.**

### Fase 5 — Output

Genera il file HTML self-contained nella directory di lavoro: tabella riepilogativa ordinata per priorità (coda di lavoro), schede raggruppate per settore, e in chiusura solo la legenda (le note di una singola azienda stanno nella sua card). Con una sola azienda la tabella riepilogativa si omette: si va dritti alla scheda (vedi `scheda-template.md`). Segui il contratto di stile e l'implementazione di riferimento in `references/scheda-template.md`. Poi genera il PDF dall'HTML (stessa resa) come descritto nello stesso file, e il **DOCX editabile** come da `references/docx-template.md` (formato ripensato per il documento di testo, non una conversione dell'HTML; se pandoc manca, consegna HTML+PDF e segnala). Spunta la Fase 5 in `PROGRESS.md`.

**Nome dei file di output.** Singola azienda: `{id}.html` (lo slug del datastore, es. `kedrion-biopharma.html`). Lista: `company-mapping-{YYYY-MM-DD}.html`. PDF e DOCX prendono lo stesso nome con la loro estensione. Se l'utente indica un nome, quello vince.

**Rigenerazione senza ricerca.** Se l'utente chiede di ricreare, ri-stilizzare o ri-esportare un dossier di aziende già mappate (o di comporne uno con aziende già note), non rifare le Fasi 1-4 (la sintesi è già nel CSV): leggi `company-mapping-db.csv`, prendi la riga più recente per ciascun `id`, renderizza HTML/PDF/DOCX dai dati. Vedi `references/data-store.md`.

## Standard vs Deep

Il tier lo sceglie l'utente con la domanda di Fase 0 (Standard se non risponde). `Standard` sgrezza e qualifica; `Deep` costa ~3x tempo e token e aggiunge il layer personale dei contatti chiave e lo studio del prodotto alla fonte: vale per l'account che vale la call. Scaling e dettagli in `references/research-waves.md`. L'override dell'utente vince sempre, anche a run avviata.

## Regole non negoziabili

Valgono in ogni fase e sono dettagliate in `references/honesty-protocol.md`: niente invenzione (gap dichiarato con "come verificarlo" prima di una cella vuota, mai un valore inventato); label `[Dato]`/`[Stima]`/`[Ipotesi]` e data su ogni affermazione; flag sui dati probabilmente stale (tipicamente bilanci oltre 12-18 mesi); conflitti riconciliati ad alta voce; ogni informazione personale citata viene da fonti pubbliche e regge il test "se la persona la leggesse nel dossier, si sentirebbe spiata?"; uso professionale e proporzionato, niente dati sensibili (GDPR).
