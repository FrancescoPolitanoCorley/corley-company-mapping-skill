# Sintesi (Fase 3)

La sintesi è ragionamento, non formattazione. Se il modello supporta l'extended thinking, spendilo qui. Scrive le schede secondo `scheda-template.md`.

## Procedura

1. Leggi tutti i grezzi della run corrente prima di scrivere: `raw/{azienda}/` oppure, per il Deep su azienda già coperta in un tier inferiore, `raw-deep/{azienda}/` (vedi `research-waves.md`).
2. Connetti i segnali tra le wave: hiring più finanza più PR danno la traiettoria; legame AWS più maturità cloud danno la priorità per Corley.
3. Riconcilia i conflitti (vedi `honesty-protocol.md`). Il conflitto riconciliato e ogni precisazione vanno nel campo NOTE della card di QUELLA azienda, non in fondo al documento.
4. Applica le label `[Dato]`/`[Stima]`/`[Ipotesi]` e le date.
5. Deriva la lettura dalle nostre angolazioni (AWS, innovazione tecnologica). Il dossier è la fotografia del cliente: niente materiale di ingaggio (bozze email, mosse con scadenza, ganci, contro-leve), le mosse le decide il sales.
   - **Perché ora**: il trigger di timing (M&A, round, nuovo CxO, hiring cloud, rinnovo contratto incumbent). Se non c'è, dichiararlo.
   - **ICP fit** (alto/medio/basso, con il perché), `[Stima]`. Niente bande economiche o "deal stimato": tariffe e valore del deal non sono deducibili dalla ricerca, il dimensionamento economico spetta al sales in fase di offerta.
   - **Priorità** (Alta/Media/Bassa, `[Stima]`): sintesi di ICP fit, intent/timing, raggiungibilità del contatto e dimensione potenziale dell'azienda. NON far sembrare debole un lead solo perché ha gap sui ricavi: un lead con need verbalizzato e decisore raggiungibile può essere Alta anche senza fatturato pubblico. Se priorità e ICP fit divergono (es. Alta con fit Medio), esplicita il perché accanto alla priorità: "alta per probabilità e timing, non per dimensione", così il sales tara tempo e proposta.
   - **Pain** (espliciti e ipotizzati) e **leve** per Corley: dove la nostra offerta tocca un loro problema reale.
6. Aggrega i gap nel campo GAP & RISCHI della card, con "come verificarlo".
7. Scrivi la scheda secondo `scheda-template.md`.
8. **Prepara la riga del datastore** `company-mapping-db.csv` come da `references/data-store.md`, ma NON appenderla ancora: l'append avviene a verifica superata (Fase 4), così il registro append-only non conserva righe sbagliate. Ogni azienda mappata finisce sempre nel CSV: è la memoria che consente di rigenerare HTML/PDF senza rifare la ricerca.

Un caveat si spiega per esteso una sola volta, nella sezione pertinente (es. un utile da verificare: in FINANZA); altrove solo un rimando o l'azione di verifica. Quattro mezzi allarmi pesano meno di un avvertimento detto bene.

## Aggregati su lista

Header con i conteggi (N aziende, N settori, N contatti). La tabella riepilogativa è la coda di lavoro, **ordinata per priorità decrescente**. Schede di dettaglio raggruppate per settore. In chiusura solo la legenda, niente note per-azienda.

## Confine della fase

L'output della Fase 3 sono le righe preparate per il datastore e i contenuti delle schede: è ciò che la Fase 4 verifica. Il rendering HTML/PDF avviene solo in Fase 5, dopo la verifica, secondo `scheda-template.md`: non renderizzare prima, o l'utente vede un output non verificato.
