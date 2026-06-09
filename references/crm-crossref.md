# CRM cross-reference (Fase 1, sempre prima del web)

Questo componente gira prima di qualunque ricerca web. Dice se l'azienda è già nota a Corley e con quale storia.

## Sorgente

Cartella Drive `1biph3a8C6w7o1YvtIoIcJQqUW-Nhs9yP` e relative sottocartelle (liste lead per evento, importazioni, dossier). Lo stesso contenuto si raggiunge in due modi: **usa sempre prima lo scripting shell**, e solo se non è praticabile ripiega sul tool Google Drive.

## Come cercare nei file dei lead

### 1. Primario: scripting shell (sempre, quando i file sono sul filesystem)

Cerca direttamente nei file con la shell: niente limiti di token, gestisce i fogli grandi che mandano in errore il tool Drive. Vale quando il CRM è raggiungibile dal filesystem (Google Drive Desktop montato, o una copia/export locale). I file di testo (CSV/TSV/TXT) si cercano direttamente; per gli `.xlsx` e i Google Sheet nativi (non testo grezzo) usa il fallback tool o esportali prima in CSV.

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

- **Priorità agli eventi più recenti**: ordina i file per data nel nome (es. "2026/05/28 AWS Summit") o per `modifiedTime` e parti da quelli; i lead freschi stanno lì. Non serve leggere tutto l'archivio storico per primo.
- **Evita i limiti di token**: usa `search_files` con `excludeContentSnippets: true` e un `pageSize` ridotto; per i fogli grandi usa `fullText contains '{nome azienda}'` invece di `read_file_content` integrale (che su file da decine di migliaia di righe va in errore "result exceeds maximum allowed tokens").
- **Ricorsione e paginazione**: elenca le sottocartelle con `mimeType = 'application/vnd.google-apps.folder' and parentId = '{id}'` e ricorri; pagina passando `next_page_token` finché è vuoto.

## Cosa estrarre per fonte

| File | Campi |
|---|---|
| Contatti Sales Navigator | Campagna (evento), Note, anno |
| corley-cloud-lead-export | Notes, Level of AWS Usage, Date created (anno), TAG:* |
| Aziende Greenfield e Engaged | Status (ENGAGED/GREENFIELD/Contattato), Outcome e Note, referenti (ruolo/mail/LinkedIn), Account Manager, Data contatto |
| Lista Newsletter clienti | AccountSource (es. AWS Event), AnnualRevenue, NumberOfEmployees |
| Sottocartelle eventi/importazioni | liste presenze per evento |
| Liste lead per evento (es. "{data} AWS Summit", in sottocartelle importazioni) | Campagna, Note, Status (Engaged/Greenfield), Addetto Vendite (= account manager), Utilizzo AWS, ruoli per persona |
| Dossier aziende già sintetizzati (es. "Dossier_Aziende") | sintesi pre-esistenti su azienda e contatti |

I nomi file in questa tabella sono indicativi: mappa i campi dal contenuto effettivo del file, non dal nome atteso. Se un file ha nome diverso ma contiene gli stessi campi, trattalo come la fonte corrispondente. Le liste lead per evento sono spesso la fonte più ricca per i contatti caldi (note per-persona, account manager, status).

I dossier sintetizzati vanno trattati come fonte CRM interna, non come seconda fonte web indipendente: contarli come corroborazione esterna crea falsa conferma (lo stesso dato contato due volte). Le cifre chiave riprese da un dossier vanno comunque ri-verificate su fonte pubblica in Fase 2.

## Matching

Fuzzy sul nome azienda: ignora le forme societarie (S.p.A., S.r.l.) e le abbreviazioni, e gestisci rebrand e gruppi noti (es. "ILLVA Saronno" vicino a "Disaronno Group", "Haier" vicino a "Haier Europe", "Candy Hoover"). Ogni match cita il file di origine. I match incerti vanno segnalati, non risolti in silenzio.

Nota operativa sul matching: `fullText contains '{token}'` è rumoroso sui token comuni o brevi (es. "Saronno" intercetta file che condividono la parola senza riguardare l'azienda) ed è paginato (continua a richiamarlo sul `next_page_token`). Non fidarsi del solo fullText: conferma sempre il match incrociando il nome azienda esatto con un secondo segnale, in primis il dominio email (es. `@disaronno.it`), e in subordine sede o settore. Questo evita i falsi positivi da token comune.

## Output del componente (per azienda)

- Classificazione: `CALDA` (almeno un match) o `FREDDA`.
- Se calda: elenco delle persone già in CRM, ciascuna con evento, anno, note, livello AWS, status, account manager.
- **Email e telefono**: per ogni persona presente nei fogli, riporta sempre l'email e, se presente nel foglio, il telefono. Sono i dati di contatto che il sales userà per scrivere o chiamare. Se il telefono non c'è nel foglio, segnala "n.d." (non inventarlo).
- Questo output informa la Wave C: se il contatto esiste già, la wave lo arricchisce invece di ripartire da zero. Email e telefono dal CRM vanno mantenuti nella scheda finale accanto alla persona.
