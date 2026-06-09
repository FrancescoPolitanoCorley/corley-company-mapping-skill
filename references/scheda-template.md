# Template schede

Il target di rendering è un singolo file HTML self-contained (CSS inline, nessun asset esterno): un `<h1>` con i conteggi, una `<table>` riepilogativa ordinata per priorità, poi una `<section>` per settore contenente le card azienda, e in chiusura una sezione di sola legenda. Le schede persona sono blocchi annidati dentro la card della loro azienda. Tier e tono seguono lo stile Corley (sobrio, da collega tecnico). I layout sottostanti definiscono i campi che ogni card deve contenere; i segnaposto in `{}` sono campi del template.

## Scheda azienda

```
AZIENDA: {nome}      Settore: {settore}      Angolo: {angolo}      Aggiornato: {data}
PRIORITA: {Alta/Media/Bassa} [Stima]   DEAL STIMATO: {banda €/anno} [Stima]
Stato CRM: {CALDA/FREDDA} {se calda: persone + evento + anno + note + livello AWS + status + account manager}

DESCRIZIONE: {una riga: cosa fa, quotata o privata, gruppo, marchi}
PERCHE ORA: {trigger di timing: M&A, round, nuovo CxO, hiring cloud, rinnovo contratto incumbent; se assente, dirlo}
ANAGRAFICA: sede / dimensione / dipendenti
FINANZA: ricavi {anno} [label] · utile o perdita [label] · situazione finanziaria · stato di salute
GRUPPO/ROUND: {appartenenza a gruppo, round, controllo}
TECNOLOGIA & AI: maturità cloud · iniziative AI {fonte} · incumbent/stack attuale
LEGAME AWS: {case study / partnership / utilizzo} [label]
CONTATTI: organigramma a fasce decide/influenza/usa + contatto consigliato (vedi sotto)
ICP FIT: {alto/medio/basso + perché: settore, dimensione, maturità cloud}
PAIN POINT: espliciti + [Ipotesi]
LEVE PER CORLEY: dove tocchiamo un loro problema reale
CONTRO-LEVA: {risposta al "perché non in casa / perché non l'incumbent"}
MOSSA: chi contattare per primo, canale, ed entro quando (next action con scadenza)
BOZZA APERTURA: {3-4 righe pronte da incollare, personalizzate sul contatto ★; marcata "da rivedere"}
NOTE: {note specifiche di QUESTA azienda: conflitti riconciliati (es. consolidato vs civilistico), precisazioni su un dato o un contatto}
GAP & RISCHI: cosa manca + come verificarlo
FONTI: [link cliccabili]
```

PRIORITA, DEAL STIMATO e ICP FIT sono giudizi, sempre marcati `[Stima]`: nascono da fit con l'ICP, segnali di intent/timing, raggiungibilità del contatto e dimensione potenziale. Servono a ordinare il lavoro e vanno tarati dal sales, non sono dati certi.

## Scheda persona (contatti prioritari)

```
NOME, ruolo reale, da quando, decide su: {...}
PERCORSO: formazione + esperienze
OPINIONI/TEMI: 2-3 cose che dice o scrive
PASSIONI: {solo Deep}
STILE: dati vs storie / builder vs narratore / social vs riservato
GANCI: {solo Deep} 2-3 appigli autentici (creepy test superato)
CANALE MIGLIORE: {DM, commento, intro, 1:1}
FONTI: [link]
```

## Aggregati (lista)

Header: N aziende · N settori · N contatti · aggiornato {data} · nota fonti.

"N contatti" = numero di contatti distinti, conteggiati come unione di contatti CRM e contatti web deduplicati per persona (la stessa persona presente sia in CRM sia sul web conta una volta sola). Usare questo criterio rende il conteggio coerente tra run diverse.
Tabella riepilogativa = coda di lavoro, ordinata per priorità decrescente: Priorità | Azienda | Settore | Deal stimato | Perché ora | Stato CRM | Primo contatto. L'ordinamento per priorità fa sì che la prima riga sia il lead da lavorare per primo.
Poi le schede di dettaglio raggruppate per settore.
Chiusura: **solo la legenda** (come leggere label, badge, priorità/deal, dati stale). Le note specifiche di una singola azienda (conflitti riconciliati, precisazioni su un dato o un contatto) NON vanno in fondo: vanno nel campo NOTE della card di quell'azienda.

## Contratto di stile HTML

Il rendering finale è un dossier leggibile e scannabile, non un muro di testo. Il file `references/report-template.html` è l'implementazione di riferimento (template canonico con un'azienda di esempio fittizia): replicane struttura, CSS e classi. Regole fisse.

**Estetica.** Stile dossier da analista, sobrio (tono Corley). Sfondo carta caldo, card bianche; serif (Georgia) per i titoli e i nomi azienda, sans di sistema per il testo, monospace maiuscoletto per le label dei campi e numeri tabellari. Primario navy Corley `#1b4f72`, accento caldo `#b5512a` per la parte commerciale. Self-contained (CSS inline), print-friendly, responsive (le colonne collassano sotto i 760px).

**Struttura della pagina.**
- Masthead sticky con titolo, conteggi e nav ad ancore per azienda.
- Riga di lead con angolo, tier, data, fonti.
- Tabella riepilogativa (numeri tabellari, badge stato, link all'ancora dell'azienda).
- Una `<section>` per settore con intestazione, poi una card `<article>` per azienda.

**Struttura della card azienda.**
- Header: nome (serif) + eventuale alias gruppo + descrizione di una riga; a destra (`.co__meta`) il badge priorità (`.prio`), la pill stato CRM e il deal stimato (`.deal`).
- Striscia di fatti chiave (`.stats`, griglia a 6): Sede · Dimensione · Ricavi · Utile/Risultato · Salute · AWS. Leggibili a colpo d'occhio.
- Callout `.why` ("Perché ora") subito sotto la striscia: il trigger di timing in una riga.
- Corpo a due colonne: a sinistra (`.main`) i blocchi descrittivi come righe `label / valore` (`.row`), l'organigramma, e il campo NOTE specifico dell'azienda; a destra la rail evidenziata (`.rail`) "Per Corley" con ICP fit / Pain / Leve / Contro-leva / Mossa (con scadenza) e il box `.draft` con la bozza di apertura. La rail è la parte che serve al sales: tenerla in evidenza.
- Callout `.gap` (bordo tratteggiato) per Gap & rischi.
- Footer `.src` con le fonti come chip-link.

**Mappatura e colori.**
- Stato CRM: pill `.pill--calda` (verde) / `.pill--fredda` (grigia).
- Priorità: badge `.prio--alta` (rosso) / `.prio--media` (ambra) / `.prio--bassa` (grigio).
- Label affidabilità inline come tag `.t`: `.t--d` Dato (verde), `.t--s` Stima (ambra), `.t--i` Ipotesi/gap (rust). Priorità, deal stimato e ICP fit portano sempre `.t--s` (sono giudizi).
- Ogni riga `CHIAVE: valore` dei layout diventa una `.row` (label monospace a sinistra, valore a destra).
- Fonti come `<a>` a pillola.

### Organigramma (componente `.org`)

I contatti si rendono come organigramma a tre fasce, non come elenco piatto:

- Tre colonne: **Decide** (budget/firma, accento caldo) · **Influenza** (tecnici/champion) · **Usa**.
- Ogni persona è un nodo `.node` (nome in navy + ruolo). Il contatto consigliato per il primo approccio è `.node--star` (★). Chi è decisore ma ignoto o non raggiungibile (es. CTO non in CRM, direttore IT da identificare) è un nodo `.node--ghost` (tratteggiato).
- Dentro il nodo, una riga `.c` con i dati di contatto: email (✉, come `mailto:`) e telefono (☎) per le persone presenti nei fogli CRM; telefono assente si rende "n.d.". Quando esiste un profilo LinkedIn verificato, aggiungi la pillola `.li` (testo "in") che linka al profilo. Mai inventare email, telefono o URL LinkedIn: si riportano solo se reperiti.
- Riga finale `.org__path`: il percorso consigliato, da chi contattare al target, con la freccia del ponte (es. "engineer Senior ★ → CTO"). Questo rende esplicito a Oriana chi scrivere per primo e dove vuole arrivare.
- Sotto l'organigramma, una riga sintetica sul contatto prioritario (note CRM/web, email, link), con le label di affidabilità dove serve.

### Legenda in chiusura (solo glossario, niente note per-azienda)

La sezione finale è SOLO la legenda: spiega come leggere il dossier, in linguaggio per un sales non tecnico. NON contiene osservazioni su singole aziende. Glossa in parole semplici:

- cosa significano le label: `[Dato]` = verificato su fonte affidabile; `[Stima]` = calcolato/valutato da noi con un'assunzione dichiarata (incluse priorità, deal stimato e ICP fit); `[Ipotesi]` = da confermare, non ancora verificato;
- cosa significano i badge di stato: `CALDA` = l'azienda è già nota a Corley (contatto o evento passato); `FREDDA` = nessun contatto pregresso nel CRM;
- cosa significano i badge di priorità: `Alta/Media/Bassa` ordinano la coda di lavoro in base a fit, timing e raggiungibilità;
- cosa vuol dire "dato datato/stale": un numero che fa riferimento a un periodo ormai passato (tipicamente un bilancio di oltre 12-18 mesi) e che potrebbe essere superato.

Niente gergo tecnico qui. **Le note specifiche di una singola azienda** (un conflitto consolidato/civilistico riconciliato, una precisazione su un contatto, un'avvertenza su un dato) vanno nel campo NOTE della card di quell'azienda, mai accumulate in fondo.

### Implementazione di riferimento

Lo scheletro HTML canonico, completo di CSS e di tutte le classi citate sopra (`.mast`, `.stats`, `.co__meta`, `.prio`, `.why`, `.co__body`, `.rail`, `.draft`, `.org`, `.pill`, `.t`, ecc.), è il file `references/report-template.html`. In fase di rendering, partire da quel file: riusarne il blocco `<style>` invariato e popolare i contenuti al posto dell'azienda di esempio. Non reintrodurre stili generici (Arial, card piatte a tutta larghezza): l'obiettivo della resa è la leggibilità a colpo d'occhio.

Il blocco `<style>` di riferimento include già le regole `@media print` necessarie (formato A4, `print-color-adjust:exact` per mantenere i colori dei badge, `break-inside:avoid` su card/organigramma/gap per non spezzarli). Mantienile invariate: sono ciò che fa sì che il PDF conservi la stessa leggibilità dell'HTML.

### Generazione del PDF (portabile, nessun path hardcoded)

Il PDF si ottiene stampando l'HTML con un browser Chromium-based in headless (fedeltà piena al layout, nessuna libreria di conversione che reinterpreta il CSS). La skill gira su macchine diverse (macOS, Linux, Windows), quindi **non assumere mai un percorso fisso**: rileva il browser disponibile e, se non c'è, consegna comunque l'HTML segnalando che il PDF va generato a parte. Non fallire l'intera run per il solo PDF.

Ordine di scelta: **prima il browser predefinito di sistema, se è Chromium-based** (Chrome, Edge, Brave, Chromium, Vivaldi, Opera); se il predefinito è Safari o Firefox (che non sanno stampare via questa pipeline) o non è rilevabile, **ripiega su un Chromium installato**; se non c'è nulla, consegna solo l'HTML. Il rilevamento del predefinito è per-sistema operativo, nessun percorso assoluto fisso.

Routine (POSIX sh / bash, vale anche per Git Bash su Windows):

```bash
detect_default_family() {
  case "$(uname -s)" in
    Darwin)
      PL="$HOME/Library/Preferences/com.apple.LaunchServices/com.apple.launchservices.secure.plist"
      bid="$(plutil -convert json -o - "$PL" 2>/dev/null | python3 -c "import json,sys;d=json.load(sys.stdin);h=[x.get('LSHandlerRoleAll','') for x in d.get('LSHandlers',[]) if x.get('LSHandlerURLScheme')=='https'];print((h[0] if h else '').lower())" 2>/dev/null)"
      case "$bid" in com.google.chrome*) echo chrome;; com.microsoft.edgemac) echo edge;; com.brave.browser) echo brave;; org.chromium.chromium) echo chromium;; com.vivaldi.vivaldi) echo vivaldi;; com.operasoftware.opera) echo opera;; com.apple.safari) echo safari;; org.mozilla.firefox) echo firefox;; *) echo "";; esac ;;
    Linux)
      d="$(xdg-settings get default-web-browser 2>/dev/null)"
      case "$d" in *chrome*) echo chrome;; *edge*) echo edge;; *brave*) echo brave;; *chromium*) echo chromium;; *vivaldi*) echo vivaldi;; *opera*) echo opera;; *firefox*) echo firefox;; *) echo "";; esac ;;
    *) # Windows via Git Bash
      pid="$(reg query 'HKCU\Software\Microsoft\Windows\Shell\Associations\UrlAssociations\https\UserChoice' //v ProgId 2>/dev/null | grep -oiE 'ChromeHTML|MSEdgeHTM|BraveHTML|FirefoxURL|ChromiumHTM')"
      case "$pid" in ChromeHTML) echo chrome;; MSEdgeHTM) echo edge;; BraveHTML) echo brave;; ChromiumHTM) echo chromium;; FirefoxURL) echo firefox;; *) echo "";; esac ;;
  esac
}
cands_for() {
  case "$1" in
    chrome) printf '%s\n' google-chrome google-chrome-stable chrome "/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" "/c/Program Files/Google/Chrome/Application/chrome.exe" "/c/Program Files (x86)/Google/Chrome/Application/chrome.exe" ;;
    edge) printf '%s\n' microsoft-edge msedge "/Applications/Microsoft Edge.app/Contents/MacOS/Microsoft Edge" "/c/Program Files (x86)/Microsoft/Edge/Application/msedge.exe" ;;
    brave) printf '%s\n' brave-browser "/Applications/Brave Browser.app/Contents/MacOS/Brave Browser" ;;
    chromium) printf '%s\n' chromium chromium-browser /snap/bin/chromium /usr/bin/chromium "/Applications/Chromium.app/Contents/MacOS/Chromium" ;;
    vivaldi) printf '%s\n' vivaldi "/Applications/Vivaldi.app/Contents/MacOS/Vivaldi" ;;
    opera)  printf '%s\n' opera "/Applications/Opera.app/Contents/MacOS/Opera" ;;
  esac
}
# Predefinito per primo (se Chromium), poi gli altri Chromium come fallback.
pick_browser() {
  for fam in "$1" chrome edge brave chromium vivaldi opera; do
    [ -z "$fam" ] && continue
    while IFS= read -r c; do
      [ -z "$c" ] && continue
      command -v "$c" >/dev/null 2>&1 && { command -v "$c"; return 0; }
      [ -x "$c" ] && { printf '%s\n' "$c"; return 0; }
    done <<EOF
$(cands_for "$fam")
EOF
  done
  return 1
}

DEF="$(detect_default_family)"
BROWSER="$(pick_browser "$DEF")" || { echo "Nessun browser Chromium (predefinito: ${DEF:-ignoto}). Consegno solo l'HTML; il PDF va generato a parte."; exit 0; }
"$BROWSER" --headless --disable-gpu --no-pdf-header-footer \
  --print-to-pdf="{nome-output}.pdf" "file://$(pwd)/{nome-output}.html"
```

Non sostituire con convertitori HTML→PDF che non supportano grid/flex e `print-color-adjust` (es. wkhtmltopdf, weasyprint): perderebbero il layout a colonne, l'organigramma e i colori.
