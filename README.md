# Corley Company Mapping Skill

Skill per [Claude Code](https://claude.com/claude-code) (e agenti compatibili con il formato skill) che trasforma un elenco di aziende, o una singola azienda, in un **dossier di mappatura azionabile** per il team sales/marketing di Corley: business, dati finanziari aggiornati, maturità tecnologica e iniziative AI, legame con AWS, organigramma con contatti, stato nel CRM Corley (calda/fredda), priorità del lead e mossa consigliata. Output: un file **HTML** dossier più il **PDF** corrispondente.

È pensata per qualificare lead, preparare call e personalizzare l'outreach (ABM), con onestà delle fonti integrata: ogni numero è etichettato e datato, i dati mancanti sono dichiarati, niente viene inventato.

---

## Cosa produce

Per ogni azienda, una scheda con:

- **Priorità** (Alta/Media/Bassa), per ordinare la coda di lavoro. Il valore economico del lead non viene stimato: lo dimensiona il sales in offerta.
- **Perché ora**: il trigger di timing (M&A, round, nuovo CxO, hiring cloud, rinnovo contratto incumbent).
- Anagrafica, **finanza datata**, salute, gruppo/round.
- **Tecnologia & AI**, incumbent attuale, **legame con AWS**.
- **Organigramma a fasce** Decide / Influenza / Usa, con contatto consigliato (★), nodi "fantasma" per decisori non raggiungibili, e la riga "percorso" (chi contattare per primo, dove arrivare). Email e telefono per i contatti presenti nel CRM; link LinkedIn per ogni persona citata se il profilo esiste.
- Rail **"Per Corley"**: ICP fit, pain, leve, contro-leva (perché non in casa / non l'incumbent), mossa con scadenza e **bozza di apertura** pronta da incollare.
- Gap & rischi (con "come verificarlo") e fonti cliccabili.

A livello di lista: tabella riepilogativa ordinata per priorità (coda di lavoro), schede raggruppate per settore, e in chiusura solo la legenda.

---

## Installazione

La skill è questo repository: il file `SKILL.md` ha il frontmatter (`name`, `description` con i trigger) e il flusso; la cartella `references/` contiene i dettagli caricati on-demand.

### Per Claude Code (umano)

Clona il repository e copia la skill in una delle directory che Claude Code legge (la cartella installata deve chiamarsi `corley-company-mapping`, come il `name` nel frontmatter):

```bash
git clone git@github.com:FrancescoPolitanoCorley/corley-company-mapping-skill.git

# A livello utente (disponibile in tutte le sessioni)
mkdir -p ~/.claude/skills
cp -R corley-company-mapping-skill ~/.claude/skills/corley-company-mapping
rm -rf ~/.claude/skills/corley-company-mapping/.git

# oppure a livello progetto
mkdir -p .claude/skills
cp -R corley-company-mapping-skill .claude/skills/corley-company-mapping
rm -rf .claude/skills/corley-company-mapping/.git
```

Riavvia/riapri la sessione. La skill si attiva da sola quando il messaggio contiene i trigger (vedi sotto), oppure invocala esplicitamente.

### Per un agente AI

Leggi `SKILL.md` e segui il flusso a 6 fasi, aprendo i file in `references/` quando il flusso lo indica (progressive disclosure). Il template di rendering canonico è `references/report-template.html`.

### Requisiti dell'ambiente

> **Prerequisito obbligatorio: accesso al CRM su Google Drive.** La Fase 1 (cross-reference) legge i file lead Corley che vivono su Google Drive. L'accesso può avvenire in due modi, in quest'ordine: **mount locale** (Google Drive Desktop o un export dei file) cercato via shell, oppure **connettore Google Drive (MCP)** quando non c'è nessun mount (es. ambiente cloud). Serve almeno uno dei due: senza, la Fase 1 non parte (niente stato CALDA/FREDDA né contatti dal CRM, dossier basato solo sul web). Se lavori in un ambiente senza filesystem condiviso con Drive, installa e autorizza il connettore **prima** di usare la skill.

- **Accesso al CRM**: mount locale di Google Drive **oppure** connettore Google Drive (MCP). Almeno uno dei due, vedi sopra.
- **WebSearch / WebFetch** per la ricerca.
- Per il PDF: un **browser Chromium-based** (Chrome, Chromium, Edge, Brave, Vivaldi, Opera) e accesso alla shell. La skill usa il **browser predefinito di sistema** se è Chromium, altrimenti ripiega su un Chromium installato; rileva tutto da sola su macOS/Linux/Windows, senza percorsi da configurare. Se il predefinito è Safari o Firefox (che non stampano via questa pipeline) e non c'è alcun Chromium, consegna comunque l'HTML e segnala che il PDF va generato a parte.

---

## Come si usa

Frase tipo: *"mappa queste aziende: …"*, *"qualifica questi lead"*, *"prepara la call su {azienda}"*, *"company mapping di {azienda}"*.

**Input:** un elenco di aziende o una singola azienda. Opzionali: l'angolo (default: lead per consulenza AWS) e il tier.

**Output:** nella directory di lavoro, un HTML + il PDF corrispondente. Naming: singola azienda `{id}.html` (slug dell'azienda, es. `kedrion-biopharma.html`), lista `company-mapping-{data}.html`; un nome indicato dall'utente vince sulla convenzione.

---

## Standard vs Deep

| | Standard (default) | Deep |
|---|---|---|
| Copertura | Azienda & finanza, tecnologia & AWS, organigramma essenziale | + layer personale completo, **ganci** sui contatti chiave, studio del prodotto alla fonte, triangolazione aggressiva |
| Round di ricerca per wave | 2-3 | 4-6 |
| Quando | sgrezzare liste lunghe, qualificare | preparare a fondo un singolo account per una call |

> **Costo del Deep.** Il tier Deep consuma circa **3 volte il tempo e i token** del tier Standard, a parità di azienda. Riferimento misurato: una run Deep su una azienda ha richiesto ~9 minuti e ~150k token di output; lo Standard si attesta intorno a un terzo. Usa Deep sull'account che vale la call, Standard per sgrezzare il resto.

L'utente può sempre forzare il tier.

---

## Come funziona (flusso a 6 fasi)

0. **Intake & angolo** — lista o singola azienda; angolo default Corley con override; tier; crea/riprende `PROGRESS.md`.
1. **CRM cross-reference** (sempre, prima del web) — cerca nei file lead Corley (shell sul mount locale di Drive se c'è, altrimenti tool Drive con query mirate), fuzzy match sul nome azienda, classifica calda/fredda, estrae evento, anno, note, livello AWS, status, account manager, email e telefono.
2. **Ricerca web in wave** (fan-out) — A azienda & finanza (+ trigger di timing, segnali di deal), B tecnologia & AWS (+ incumbent), C persone (organigramma; in Deep layer personale + ganci). I grezzi vanno in `raw/`.
3. **Sintesi** — connette i segnali, calcola priorità e ICP fit, deriva pain/leve/contro-leva/mossa/bozza, applica le label, riconcilia i conflitti, dichiara i gap.
4. **Verifica** — un controllo finale rilegge l'output (claim senza fonte, contraddizioni, dati stale, gap non dichiarati) prima della consegna.
5. **Output** — HTML dossier + PDF (Chrome headless).

### Onestà delle fonti (regola non negoziabile)

Ogni affermazione porta una label: `[Dato]` (verificato), `[Stima]` (calcolato con assunzione dichiarata; include priorità, deal e ICP fit), `[Ipotesi]` (da confermare). Mai inventare: un gap dichiarato con "come verificarlo" è sempre preferibile a un valore tirato a indovinare. Vale per dati finanziari, email, telefono e URL LinkedIn. Solo fonti pubbliche; sui ganci personali si applica il "creepy test".

---

## Persistenza: il datastore CSV

La skill non butta via quello che ricava. Ogni azienda sintetizzata viene salvata in un unico file CSV locale, `company-mapping-db.csv`, nella directory di lavoro. È la **memoria** della skill: l'HTML e il PDF sono viste su quei dati.

- **Una riga per azienda** (per run): campi piatti (anagrafica, finanza, priorità, perché ora, pain/leve/mossa, bozza apertura, note, gap) più due colonne JSON, `contatti_json` (l'organigramma: persone, fascia decide/influenza/usa, email, telefono, LinkedIn) e `fonti_json`. I testi conservano le label `[Dato]`/`[Stima]`/`[Ipotesi]`, così le card si ricostruiscono identiche.
- **Crea o append**: se il file non c'è viene creato con l'intestazione, altrimenti si appende. È un registro storico: le righe vecchie non si toccano (audit trail). Su una stessa azienda mappata più volte, in rigenerazione vince la riga più recente.
- **Locale, non versionato**: è in `.gitignore` perché contiene dati su persone e aziende. Resta sulla macchina, non finisce su git.
- **Rigenerare senza ricerca**: per ricreare, ri-stilizzare o ri-esportare dossier di aziende già mappate, la skill legge il CSV e renderizza HTML/PDF **senza rifare la ricerca**. Legge pochi KB per azienda invece di rilanciare wave da centinaia di migliaia di token: è la via economica per iterare sulla resa o ricomporre i report. Dettaglio in `references/data-store.md`.

## Struttura

```
corley-company-mapping/
├── SKILL.md                      # driver: flusso 6 fasi, tier, progressive disclosure
├── ACCEPTANCE.md                 # criteri di qualità
├── README.md
└── references/
    ├── research-principles.md    # source tier, recency, triangolazione
    ├── honesty-protocol.md       # label, no-invenzione, GDPR, creepy test
    ├── crm-crossref.md           # scansione Drive, fuzzy match, email/telefono
    ├── research-waves.md         # Wave A/B/C, scaling Standard/Deep, trigger, incumbent
    ├── people-mapping.md         # organigramma a fasce, ganci, LinkedIn
    ├── synthesis.md              # priorità, deal, perché ora, bozza, contro-leva, append al CSV
    ├── verification-agent.md     # self-check pre-output
    ├── scheda-template.md        # contratto di stile + generazione PDF
    ├── data-store.md             # datastore CSV: schema, append, rigenerazione senza ricerca
    └── report-template.html      # template canonico di rendering (azienda di esempio)
```

Gli output delle run (HTML/PDF dei dossier reali, grezzi, `PROGRESS.md`) e il datastore `company-mapping-db.csv` restano nella directory di lavoro e non fanno parte di questo repository.

---

## Configurazione specifica Corley

Il cross-reference CRM punta a una cartella Google Drive interna di Corley (lead da eventi, export, dossier). L'ID della cartella è in `references/crm-crossref.md`: cambiandolo si adatta la skill a un altro archivio. Tono e posizionamento seguono le convenzioni Corley (partner tecnico, non vendor; i numeri parlano da soli).
