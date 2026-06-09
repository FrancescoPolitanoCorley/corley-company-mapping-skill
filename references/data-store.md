# Datastore CSV (memoria delle mappature)

La skill non deve perdere quello che ricava: ogni azienda sintetizzata viene salvata in un unico file CSV locale. Così l'HTML e il PDF si possono **rigenerare leggendo il CSV**, senza rifare la ricerca (che costa molti token e minuti). Il CSV è la fonte dati; HTML/PDF sono viste.

## Il file

- Nome: `company-mapping-db.csv` nella directory di lavoro.
- **Locale, non versionato** (è in `.gitignore`): contiene dati su persone e aziende, resta sulla macchina.
- **Se non esiste, crealo** con la riga di intestazione; **se esiste, vai in append** (una riga per azienda sintetizzata nella run). È un registro storico: non riscrivere né cancellare le righe vecchie (audit trail, come per il file domande nei deal).

## Una riga = una azienda (in una data run)

Colonne (in quest'ordine):

```
id,data_aggiornamento,angolo,tier,azienda,alias,settore,sede,dimensione,dipendenti,ricavi,ricavi_anno,ricavi_label,utile_perdita,utile_label,situazione_finanziaria,salute,gruppo_round,tech_ai,incumbent,legame_aws,crm_stato,crm_evento,crm_anno,crm_status,crm_account_manager,perche_ora,icp_fit,priorita,pain,leve,contro_leva,mossa,bozza_apertura,note,gap_rischi,contatti_json,fonti_json
```

Compatibilità: i CSV creati prima della v1.1.2 hanno una colonna in più, `deal_stimato` (rimossa dagli output). Se il file esistente la contiene, mantieni la SUA intestazione e lascia il campo vuoto nelle righe nuove; in rigenerazione la colonna si ignora.

- `id`: slug stabile dell'azienda (kebab-case, senza forma societaria: "Kedrion Biopharma" → `kedrion-biopharma`). Le run successive sulla stessa azienda riusano lo stesso `id`.
- `data_aggiornamento`: data della run (YYYY-MM-DD).
- I campi testuali (`ricavi`, `pain`, `leve`, `mossa`, `bozza_apertura`, `note`, ...) **mantengono le label inline** `[Dato]`/`[Stima]`/`[Ipotesi]` così la card si ricostruisce identica.
- `priorita` (Alta/Media/Bassa), `icp_fit`: i giudizi, già con `[Stima]`.
- `crm_stato`: `CALDA`/`FREDDA`. `perche_ora`: il trigger di timing.
- `contatti_json`: array JSON, un oggetto per persona dell'organigramma:
  `{"nome","fascia":"decide|influenza|usa","ruolo","star":bool,"ghost":bool,"email","telefono","linkedin","fonte_crm","note"}`
- `fonti_json`: array JSON `[{"label","url"}, ...]`.

Queste due colonne JSON gestiscono la cardinalità variabile (più contatti, più fonti) restando dentro un solo CSV.

## Come scrivere (in modo sicuro)

I campi contengono virgole, virgolette e a volte newline: **non concatenare le righe a mano**, romperesti il CSV. Usa uno strumento che faccia il quoting RFC4180:

- macOS/Linux: il modulo `csv` di Python (`csv.writer`, scrive header se il file non esiste, poi append in `'a'`).
- Windows (PowerShell): `Export-Csv -Append -NoTypeInformation -Encoding UTF8` (oppure `Import-Csv`/`Export-Csv` per leggere e riscrivere).

Esempio (Python, append-safe):

```python
import csv, os, json
COLS = ["id","data_aggiornamento","angolo","tier","azienda","alias","settore","sede","dimensione","dipendenti","ricavi","ricavi_anno","ricavi_label","utile_perdita","utile_label","situazione_finanziaria","salute","gruppo_round","tech_ai","incumbent","legame_aws","crm_stato","crm_evento","crm_anno","crm_status","crm_account_manager","perche_ora","icp_fit","priorita","pain","leve","contro_leva","mossa","bozza_apertura","note","gap_rischi","contatti_json","fonti_json"]
# row = dict con i campi della scheda; "contatti" e "fonti" sono liste Python (vengono serializzate qui)
row = {"id": "kedrion-biopharma", "data_aggiornamento": "2026-06-09", "contatti": [], "fonti": []}  # esempio
path = "company-mapping-db.csv"
new = not os.path.exists(path)
with open(path, "a", newline="", encoding="utf-8") as f:
    w = csv.DictWriter(f, fieldnames=COLS)
    if new: w.writeheader()
    row["contatti_json"] = json.dumps(row.pop("contatti", []), ensure_ascii=False)
    row["fonti_json"] = json.dumps(row.pop("fonti", []), ensure_ascii=False)
    w.writerow({k: row.get(k, "") for k in COLS})
```

## Rigenerare HTML/PDF senza ricerca (il punto del datastore)

Quando l'utente chiede di **ricreare, ri-stilizzare o ri-esportare** un dossier di aziende già mappate (o di metterne alcune già note in un nuovo report), NON rifare le Fasi 1-4 (CRM, ricerca, sintesi e verifica sono già consolidate nel CSV): leggi il CSV e renderizza.

1. Carica `company-mapping-db.csv`. Per ogni `id` tieni **solo la riga più recente** (`data_aggiornamento` massima; a parità di data vince l'ultima riga in ordine di file, cioè l'append più recente): le run vecchie restano nel file come storico ma non si renderizzano.
2. Filtra alle aziende richieste (se l'utente ne indica un sottoinsieme).
3. Per ogni riga: ricostruisci la card secondo `scheda-template.md` (i campi testuali hanno già le label; `contatti_json`/`fonti_json` si de-serializzano in organigramma e fonti).
4. Genera HTML + PDF come in Fase 5.

Questo legge pochi KB per azienda invece di rilanciare wave di ricerca da centinaia di migliaia di token: è la via economica per iterare sulla resa o rimescolare i dossier.

## Quando scrivere

La Fase 3 (sintesi), dopo aver prodotto i dati di un'azienda, **appende la sua riga** al CSV prima o insieme al rendering. Ogni azienda mappata finisce sempre nel datastore, anche quando l'output richiesto è un singolo HTML.
