# Cross-reference lead Drive (Fase 1, sempre prima del web)

Questo componente gira prima di qualunque ricerca web. Dice se l'azienda è già nelle liste lead Corley su Drive e con quale storia. Nel dossier la fonte interna si cita sempre col nome breve del file (es. "lead AWS Summit 2026", "lead formazione febbraio 25"), mai come "CRM": è una cartella di fogli lead, non un sistema.

## Sorgente

Cartella Drive `1biph3a8C6w7o1YvtIoIcJQqUW-Nhs9yP` e relative sottocartelle (liste lead per evento, importazioni, dossier). Lo stesso contenuto si raggiunge in due modi: **usa sempre prima lo scripting shell**, e solo se non è praticabile ripiega sul tool Google Drive.

## Come cercare nei file dei lead

### 1. Primario: scripting shell (sempre, quando i file sono sul filesystem)

Cerca direttamente nei file con la shell: niente limiti di token, gestisce i fogli grandi che mandano in errore il tool Drive. Vale quando le liste sono raggiungibili dal filesystem (Google Drive Desktop montato, o una copia/export locale). I file di testo (CSV/TSV/TXT) si cercano direttamente; per gli `.xlsx` e i Google Sheet nativi (non testo grezzo) usa il fallback tool o esportali prima in CSV.

Prima cosa: verifica se esiste un mount/copia locale (vedi `find`/`Get-ChildItem` sotto). **Se non c'è nessun mount né copia locale (es. ambiente cloud), salta subito al punto 2**: non perdere tempo a cercare file inesistenti sul filesystem.

Su macOS/Linux (bash): individua la cartella lead e cerca l'azienda per nome e per dominio email.

```bash
ROOT="$(find "$HOME/Library/CloudStorage" "$HOME/Google Drive" "$HOME" \
        -maxdepth 5 -type d \( -iname '*lead*' -o -iname '*importazioni*' \) 2>/dev/null | head -1)"
grep -rinaE 'vimar|@vimar\.com' "$ROOT" --include='*.csv' --include='*.tsv' --include='*.txt' 2>/dev/null
```

Su Windows usa **PowerShell** (`Select-String`), individuando l'unità di Google Drive:

```powershell
$root = (Get-ChildItem 'G:\','H:\',"$env:USERPROFILE" -Directory -Recurse -Depth 4 -ErrorAction SilentlyContinue |
         Where-Object { $_.Name -match 'lead|importazioni' } | Select-Object -First 1).FullName
Get-ChildItem -Path $root -Recurse -Include *.csv,*.tsv,*.txt -ErrorAction SilentlyContinue |
  Select-String -Pattern 'vimar','@vimar\.com'
```

Cerca sempre due segnali (nome azienda esatto + dominio email) per evitare i falsi positivi da token comune.

### 2. Alternativa: tool Google Drive MCP (solo se la shell non è praticabile)

Quando i file non sono sul filesystem (es. ambiente cloud senza mount) o manca la shell:

- **Regola dura sui token (non opzionale)**: **non leggere mai un foglio grande integralmente con `read_file_content`** — su file da decine di migliaia di righe restituisce "result exceeds maximum allowed tokens" e blocca il flusso. Per trovare un'azienda in un foglio usa **sempre** `search_files` con `fullText contains '{nome azienda}'`, che restituisce solo i file rilevanti senza scaricarne il contenuto integrale. Apri un file per intero solo se è piccolo o se l'hai già ristretto.
- **Se devi proprio ispezionare un file grande**: se il tool lo materializza/scarica in locale (spill), **non rileggerlo col tool, grep-alo con la shell** (nessun limite di token). Questa è la via di recupero canonica, non un'eccezione.
- **Priorità agli eventi più recenti**: ordina i file per data nel nome (es. "2026/05/28 AWS Summit") o per `modifiedTime` e parti da quelli; i lead freschi stanno lì. Non serve leggere tutto l'archivio storico per primo.
- **search_files snello**: `excludeContentSnippets: true` e `pageSize` ridotto, per non gonfiare la risposta.
- **Ricorsione e paginazione**: elenca le sottocartelle con `mimeType = 'application/vnd.google-apps.folder' and parentId = '{id}'` e ricorri; pagina passando `next_page_token` finché è vuoto.

### 3. Senza accesso alle liste lead (né mount né connettore Drive)

Avvisa subito l'utente e non bloccarti: prosegui con le sole fonti web. In ogni scheda marca lo stato lead come `NON VERIFICATO`, mai `FREDDA`: FREDDA è un'assenza verificata nelle liste, qui non hai verificato niente. Dichiara il limite nelle note metodologiche e suggerisci di rilanciare il cross-reference quando l'accesso torna disponibile.

## Cosa estrarre per fonte

| Tipo di fonte (nome osservato) | Campi |
|---|---|
| Liste lead per evento (in sottocartelle importazioni, es. "2026/05/28 AWS Summit") | Campagna/evento, Note, Status (Engaged/Greenfield), Addetto Vendite (= account manager), Utilizzo AWS, ruolo, email, telefono — spesso la fonte più ricca per i contatti caldi |
| Raccolta lead generico manuale (Risposte) | contatti raccolti a mano: azienda, persona, ruolo, note |
| Export cloud lead (es. "corley-cloud-lead-export-{data}", CSV/Sheet) | Notes, Level of AWS Usage, Date created (anno), TAG:* (walter, francesco, ai, migrazione, iot, ...) |
| Aziende Greenfield e Engaged ({anno}) | Status (ENGAGED/GREENFIELD/Contattato), Outcome e Note, referenti (ruolo/mail/LinkedIn), Account Manager, Data contatto |
| Contatti Sales Navigator | Campagna (evento), Note, anno |
| Lista Newsletter clienti | AccountSource (es. AWS Event), AnnualRevenue, NumberOfEmployees |
| Sottocartelle importazioni (ODOO / AWS APN / SalesForce) e "Moduli di Accredito Training & Liste eventi" | liste presenze e importazioni per evento |
| Dossier aziende già sintetizzati | sintesi pre-esistenti su azienda e contatti |

**I nomi sono indicativi, mappa dai campi non dal nome.** I file reali cambiano nome nel tempo: identifica la fonte dai campi che contiene, non dal nome atteso. In particolare un dossier può citare come propria fonte un nome (es. "Lead_Walter-AWS_Summit") che non corrisponde a nessun file con quel nome letterale nella cartella: è la lista lead di quell'evento, cercala per evento/data, non per quel nome.

**Attenzione al conflitto di colonna.** Due informazioni diverse viaggiano spesso vicine e tra file diversi possono finire scambiate o in colonne inattese: lo **status commerciale** (Engaged / Greenfield / Contattato) e il **livello di utilizzo AWS** (Produzione multipla/singola, Dev/Test, In valutazione, Non usa AWS). Non sono la stessa cosa. Se trovi "Engaged" sotto un'intestazione di utilizzo AWS, o "Produzione (multipla)" sotto lo status, riconoscilo dal significato del valore e mettilo nel campo giusto; se per la stessa persona i due file divergono, riportalo nel campo NOTE della card, non scegliere in silenzio.

I dossier sintetizzati vanno trattati come fonte interna, non come seconda fonte web indipendente: contarli come corroborazione esterna crea falsa conferma (lo stesso dato contato due volte). Le cifre chiave riprese da un dossier vanno comunque ri-verificate su fonte pubblica in Fase 2.

## Matching

Fuzzy sul nome azienda: ignora le forme societarie (S.p.A., S.r.l.) e le abbreviazioni, e gestisci rebrand e gruppi noti (es. "ILLVA Saronno" vicino a "Disaronno Group", "Haier" vicino a "Haier Europe", "Candy Hoover"). Ogni match cita il file di origine. I match incerti vanno segnalati, non risolti in silenzio.

Nota operativa sul matching: `fullText contains '{token}'` è rumoroso sui token comuni o brevi (es. "Saronno" intercetta file che condividono la parola senza riguardare l'azienda) ed è paginato (continua a richiamarlo sul `next_page_token`). Non fidarsi del solo fullText: conferma sempre il match incrociando il nome azienda esatto con un secondo segnale, in primis il dominio email (es. `@disaronno.it`), e in subordine sede o settore. Questo evita i falsi positivi da token comune.

## Output del componente (per azienda)

- Classificazione: `CALDA` (almeno un match) o `FREDDA`.
- Se calda: elenco delle persone già nelle liste, ciascuna con la lista di provenienza (nome breve del file), evento, anno, note, livello AWS, status, account manager. L'account manager è un'informazione di contesto, non il mittente della mossa: il dossier non prescrive chi scrive.
- **Email e telefono**: per ogni persona presente nei fogli, riporta sempre l'email e, se presente nel foglio, il telefono. Sono i dati di contatto che il sales userà per scrivere o chiamare. Leggi sempre la colonna telefono del foglio per esteso: "n.d." va scritto solo se la cella è davvero vuota, non se la colonna non è stata letta (errore già osservato in un test: numero presente nel foglio, scheda uscita con "n.d."). In una run Deep su un'azienda già coperta, ri-verifica i campi di contatto sul foglio lead invece di ereditarli dal raw della run precedente.
- Questo output informa la Wave C: se il contatto esiste già, la wave lo arricchisce invece di ripartire da zero. Email e telefono dai fogli lead vanno mantenuti nella scheda finale accanto alla persona.
