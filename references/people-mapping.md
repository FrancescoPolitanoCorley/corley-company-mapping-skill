# Mappatura persone

Costruisce l'organigramma utile alla vendita e, in Deep, i ganci sui contatti prioritari. Si applica nella Wave C della Fase 2.

## Organigramma (cascata di fonti)

LinkedIn come fonte primaria (ruolo reale e stile della persona, "che tipo è") → siti aziendali (team, chi siamo) → web search mirata sui ruoli CxO quando LinkedIn non basta.

## Classificazione

- Chi decide (budget e firma) / chi influenza (tecnici, champion) / chi usa.
- Per ciascuno: ruolo reale (non solo il titolo), da quando è in azienda, su cosa decide.

## Output: organigramma a fasce

L'organigramma non è un elenco piatto di nomi, è una mappa che dice al sales chi contattare per primo e dove vuole arrivare. Si rende come tre fasce (vedi componente `.org` in `scheda-template.md`):

- **Decide / Influenza / Usa**: ogni persona va collocata nella fascia giusta. Nell'organigramma vanno solo le persone rilevanti per la decisione d'acquisto (decisori, influencer reali, utenti interni). Le figure di contorno (contractor, maintainer esterni, consulenti) non occupano nodi: si citano in una riga di contorno sotto l'organigramma, come segnale (es. "mobile esternalizzato: capacità interna minima").
- **Contatto consigliato (★)**: marca la persona da cui partire per il primo approccio. Di norma è chi è già nel CRM o il decisore raggiungibile.
- **Nodi fantasma (tratteggiati)**: chi è rilevante ma ignoto o non raggiungibile (es. decisore di budget da identificare, CTO non presente nel CRM). Vanno mostrati come obiettivo, non nascosti. In Deep, ogni nodo fantasma ereditato da una run di tier inferiore va ri-tentato alla fonte: se il ruolo si conferma, promuovilo a nodo pieno nella fascia corretta; il fantasma resta solo per chi è ancora ignoto o non raggiungibile dopo i round Deep.
- **Percorso**: una riga che esplicita il ponte dal contatto consigliato al target (es. "engineer Senior ★ → CTO"). Se il decisore è già raggiungibile, dichiarare "contatto diretto, nessun ponte".

Questa mappa si costruisce intrecciando i contatti dal CRM (Fase 1) con l'organigramma dal web: un contatto caldo in CRM diventa quasi sempre il nodo ★.

## Dati di contatto su ogni persona

- **Contatti presenti nei fogli CRM**: riporta sempre email e, se presente, telefono accanto alla persona (nel nodo dell'organigramma e nella scheda). Telefono assente nel foglio si segnala "n.d.", mai inventato.
- **Link LinkedIn**: ogni volta che si cita una persona (nodo dell'organigramma, contatto prioritario, qualsiasi citazione) si aggiunge il link al suo profilo LinkedIn se il profilo esiste ed è stato reperito. Vale la regola di onestà: l'URL va trovato e verificato, mai costruito a indovinare. Se il profilo non è stato trovato, si omette il link (eventualmente "LinkedIn da reperire"), non si mette un URL plausibile ma non verificato.
- **Quanto cercare il profilo, per tier**: in Standard reperisci il LinkedIn del solo contatto consigliato (★) e dei decisori, senza spendere round extra sugli altri (gli altri restano "LinkedIn da reperire" se non emergono dalla ricerca già fatta). In Deep reperisci il profilo di tutti i contatti chiave. In entrambi i casi non inventare l'URL.

## Non trovato e single-source

Tre casistiche distinte, mai fuse né inventate:

- **Profilo assente da LinkedIn e dal web** (figure riservate o di vecchio stile): si segnala come "non trovato sul web", mai inventato.
- **Profilo presente ma su singola fonte** (tipicamente solo LinkedIn, Tier 3): si riporta con label `[Ipotesi]` e nota "single-source, da confermare". Non si promuove a `[Dato]` né si degrada a "non trovato": la persona esiste, il dato non è ancora corroborato.
- **Non trovato nel CRM vs non trovato sul web**: sono due stati diversi e vanno etichettati come tali. "Non trovato nel CRM" significa assente dai file lead Corley (campo placeholder o nessun match). "Non trovato sul web" significa nessun profilo pubblico reperibile. Una persona può essere presente sul web ma assente dal CRM (o viceversa): in quel caso si dichiarano entrambi gli stati separatamente, mai riassunti in un unico "non trovato".

## Ganci (solo Deep, contatti prioritari)

Costruiti da fonti pubbliche, incrociando i temi della persona con l'autenticità di Corley:

- gancio professionale (un'opinione o una sfida condivisa);
- gancio di competenza (un terreno dove Corley ha credibilità reale utile a loro);
- gancio personale (un interesse genuino in comune).

Regola: un gancio vale solo se autentico e specifico. Applica il creepy test (vedi `honesty-protocol.md`). Niente dati sensibili.
