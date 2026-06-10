# Output editabile DOCX (Fase 5)

Accanto a HTML e PDF, la skill produce un **DOCX editabile**: il documento che il team apre in Word o carica su Drive (dove diventa un Google Doc) per annotare, tagliare e incollare. Non è una conversione dell'HTML: è un formato ripensato per un documento di testo, con gli stessi principi di leggibilità (gerarchia chiara, scannabilità, link a portata di click, label di affidabilità) ma senza il layout a colonne, i badge colorati e il CSS.

## Pipeline

1. Costruisci un **markdown intermedio** dai dati sintetizzati (le stesse righe del datastore usate per l'HTML), secondo la struttura sotto.
2. Converti con pandoc usando SEMPRE gli stili Corley: `pandoc "{basename}.docx.md" --reference-doc="{skill-dir}/references/reference.docx" -o "{basename}.docx"`. Senza `--reference-doc` il DOCX esce con i default pandoc (tabelle senza bordi, stili anonimi): non consegnarlo così. `reference.docx` è versionato nella skill: tabelle bordate con header evidenziato, heading navy Corley.
3. Cancella il markdown intermedio: l'editabile è il DOCX.

Naming: stesso basename di HTML e PDF (`{id}.docx` per singola azienda, `company-mapping-{data}.docx` per lista). Requisito: **pandoc** (già nel toolchain Corley). Se manca: prova `python-docx` se installato, altrimenti consegna HTML+PDF e segnala che il DOCX va generato a parte. Non far fallire la run per il solo DOCX.

## Struttura del documento (per azienda)

```
# {Azienda}

{Settore} · Stato lead: {CALDA (lead AWS Summit 2026)/FREDDA/NON VERIFICATO} · Priorità: {Alta/Media/Bassa} [Stima] · Aggiornato {data}
Angolo: {angolo}
Link rapidi: [sito](url) · [LinkedIn azienda](url) · [GitHub/docs](url) · [bilanci/visura](url)

## In breve
{descrizione in una riga + perché ora, 2-4 frasi piane. Le label [Dato]/[Stima]/[Ipotesi] restano nel testo, tra parentesi quadre}

## Dati chiave
| Campo | Valore |
{Sede / Dimensione / Dipendenti / Ricavi / Utile / Salute / Utilizzo AWS — un dato per riga, con label e, dove conta, il link alla fonte inline}

## Finanza
{paragrafo: numeri datati, situazione, caveat spiegato una volta}

## Tecnologia & AI
{paragrafo: maturità cloud, stack, iniziative AI, incumbent}

## Legame con AWS
{paragrafo}

## Persone e contatti
| Nome | Ruolo | Fascia | Recapiti | LinkedIn |
{una riga per persona; fascia = Decide/Influenza/Usa; ★ accanto al contatto consigliato; recapiti solo se reperiti, "n.d." mai inventato}

Percorso: {★ → decisore di budget, in una riga}
Contorno: {contractor/figure esterne, se presenti, in una riga}

### {Nome contatto ★} ({ruolo})        ← solo Deep, una sottosezione per contatto chiave
Canale migliore: ... / Stile: ... / Temi: ... / Percorso: ...

## Per Corley
**ICP fit** — {alto/medio/basso + perché} [Stima]
**Pain** — {espliciti e ipotizzati, con label}
**Leve** — {dove l'offerta Corley tocca un loro problema reale}

## Verifiche sui dati
{conflitti riconciliati e smentite, ogni caveat una volta sola}

## Gap e come verificarli
{bullet: cosa manca → azione di verifica}

## Fonti
{bullet di link, etichetta + URL}
```

Per una **lista**: un H1 di documento ("Company Mapping — {data}"), la riga di intestazione (angolo, profondità in linguaggio piano, conteggi), una tabella riepilogativa (Priorità | Azienda | Settore | Perché ora | Stato lead | Primo contatto), poi le aziende raggruppate per settore con la struttura sopra (H1 azienda → declassato a H2, e le sezioni a H3). In chiusura la legenda, identica nei contenuti a quella dell'HTML.

## Principi di leggibilità (valgono quanto nel contratto HTML)

- Paragrafi corti, tabelle solo per fatti enumerabili, niente muri di testo.
- Le label `[Dato]`/`[Stima]`/`[Ipotesi]` restano testuali nel flusso: in un DOCX si cercano con ctrl+F, non servono badge.
- Link inline vicino al dato che sostengono + link rapidi in testa + elenco fonti in fondo.
- Niente gergo di pipeline, niente materiale di ingaggio: vale tutto il contratto di `scheda-template.md`.
- Il DOCX deve reggere l'editing: niente struttura che si rompe cancellando una riga (no tabelle annidate, no colonne).
