# Sintesi (Fase 3)

La sintesi è ragionamento, non formattazione. Se il modello supporta l'extended thinking, spendilo qui. Scrive le schede secondo `scheda-template.md`.

## Procedura

1. Leggi tutti i grezzi della run corrente prima di scrivere: `raw/{azienda}/` oppure, per il Deep su azienda già coperta in un tier inferiore, `raw-deep/{azienda}/` (vedi `research-waves.md`).
2. Connetti i segnali tra le wave: hiring più finanza più PR danno la traiettoria; legame AWS più maturità cloud danno la priorità per Corley.
3. Riconcilia i conflitti (vedi `honesty-protocol.md`). Il conflitto riconciliato e ogni precisazione vanno nel campo NOTE della card di QUELLA azienda, non in fondo al documento.
4. Applica le label `[Dato]`/`[Stima]`/`[Ipotesi]` e le date.
5. Deriva la parte azionabile (è il valore del dossier, non un riempitivo):
   - **Perché ora**: il trigger di timing (M&A, round, nuovo CxO, hiring cloud, rinnovo contratto incumbent). Se non c'è, dichiararlo.
   - **ICP fit** (alto/medio/basso, con il perché), `[Stima]`. Niente bande economiche o "deal stimato": tariffe e valore del deal non sono deducibili dalla ricerca, il dimensionamento economico spetta al sales in fase di offerta.
   - **Priorità** (Alta/Media/Bassa, `[Stima]`): sintesi di ICP fit, intent/timing, raggiungibilità del contatto e dimensione potenziale dell'azienda. NON far sembrare debole un lead solo perché ha gap sui ricavi: un lead con need verbalizzato e decisore raggiungibile può essere Alta anche senza fatturato pubblico.
   - **Pain** (espliciti e ipotizzati), **leve** per Corley, **contro-leva** (risposta al "perché non in casa / perché non l'incumbent").
   - **Mossa**: chi contattare per primo, su quale canale, ed entro quando.
   - **Bozza apertura**: 3-4 righe pronte da incollare, personalizzate sul contatto ★ (aggancio a gancio o trigger, una frase sul valore, una call to action leggera), marcate "da rivedere".
6. Aggrega i gap nel campo GAP & RISCHI della card, con "come verificarlo".
7. Scrivi la scheda secondo `scheda-template.md`.
8. **Appendi la riga al datastore** `company-mapping-db.csv` come da `references/data-store.md` (crea il file con intestazione se non esiste). Ogni azienda mappata finisce sempre nel CSV: è la memoria che consente di rigenerare HTML/PDF senza rifare la ricerca.

## Aggregati su lista

Header con i conteggi (N aziende, N settori, N contatti). La tabella riepilogativa è la coda di lavoro, **ordinata per priorità decrescente**. Schede di dettaglio raggruppate per settore. In chiusura solo la legenda, niente note per-azienda.

## Rendering finale

Un unico file HTML self-contained (CSS inline) scritto nella directory di lavoro, più il PDF generato da esso. Segui il contratto di stile HTML in `scheda-template.md` e parti dal template `references/report-template.html`. Le label `[Dato]`/`[Stima]`/`[Ipotesi]`, lo stato CRM (`CALDA`/`FREDDA`) e la priorità sono evidenziati con badge colorati; le fonti sono link cliccabili. La sezione di chiusura contiene SOLO la legenda in linguaggio piano: le note di una singola azienda stanno nella sua card.
