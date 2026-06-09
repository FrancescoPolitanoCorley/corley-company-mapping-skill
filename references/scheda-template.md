# Template schede

Il target di rendering è un singolo file HTML self-contained (CSS inline, nessun asset esterno): un `<h1>` con i conteggi, una `<table>` riepilogativa ordinata per priorità (omessa per dossier su singola azienda, vedi sotto), poi una `<section>` per settore contenente le card azienda, e in chiusura una sezione di sola legenda. Le schede persona sono blocchi annidati dentro la card della loro azienda. Tono da stile Corley (sobrio, da collega tecnico). **Niente gergo di pipeline nel dossier**: il lettore è un sales, non chi ha eseguito la skill. Mai "tier", "wave", "Deep conferma", "creepy test": scrivi "dossier approfondito", "verifica diretta sulle fonti", e sui ganci "(ok in apertura)" / "(per qualificare)" / "(solo in call)" / scartato con motivo. I layout sottostanti definiscono i campi che ogni card deve contenere; i segnaposto in `{}` sono campi del template.

## Scheda azienda

```
AZIENDA: {nome}      Settore: {settore}      Angolo: {angolo}      Aggiornato: {data}
PRIORITA: {Alta/Media/Bassa} [Stima] {se diverge dall'ICP fit: il perché, es. "alta per probabilità e timing, non per dimensione"}
AZIONE: {sintesi della MOSSA in una riga: "Azione proposta: scrivere a {contatto ★} ({canale})"} — proposta senza mittente interno e SENZA scadenze (il dossier non deve invecchiare), è la prima cosa che il sales deve vedere
Stato lead: {CALDA/FREDDA/NON VERIFICATO} {se calda: lista di provenienza col nome breve del file (es. "lead AWS Summit 2026") + persone + evento + anno + note + livello AWS + status + account manager}

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
MOSSA: chi contattare per primo, canale + obiettivo di chiusura della call (con cosa si esce). Niente scadenze né riferimenti temporali relativi
BOZZA APERTURA: {riga Oggetto + 3-4 righe pronte da incollare, personalizzate sul contatto ★; marcata "da rivedere"}
NOTE: {verifiche e smentite sui dati di QUESTA azienda: conflitti riconciliati (es. consolidato vs civilistico), precisazioni su un dato o un contatto; ogni caveat per esteso una sola volta, altrove solo rimando}
GAP & RISCHI: cosa manca + come verificarlo
FONTI: [link cliccabili]
```

PRIORITA e ICP FIT sono giudizi, sempre marcati `[Stima]`: nascono da fit con l'ICP, segnali di intent/timing, raggiungibilità del contatto e dimensione potenziale. Servono a ordinare il lavoro e vanno tarati dal sales, non sono dati certi. Nessun "deal stimato" in euro: il valore economico non è deducibile dalla ricerca e va dimensionato dal sales in offerta.

## Scheda persona (contatti prioritari)

```
NOME, ruolo reale, da quando, decide su: {...}
GANCI: {solo Deep} 2-3 appigli autentici, ciascuno con l'uso consigliato: (ok in apertura) / (per qualificare) / (solo in call); gli scartati con il motivo
CANALE MIGLIORE: {DM, commento, intro, 1:1}
STILE: dati vs storie / builder vs narratore / social vs riservato {eventuali interessi personali qui, solo se trovati: niente riga dedicata per un gap}
OPINIONI/TEMI: 2-3 cose che dice o scrive
PERCORSO: formazione + esperienze
FONTI: [link]
```

L'ordine è operativo-prima: chi prepara la call cerca cosa dire e su che canale; la biografia è approfondimento e sta in fondo.

## Aggregati (lista)

Header: N aziende · N settori · N contatti · aggiornato {data} · nota fonti.

"N contatti" = numero di contatti **azionabili**: persone con almeno un recapito reperito (email, telefono o LinkedIn verificato), deduplicate tra fogli lead e web. Le persone citate senza recapito non si contano: un masthead che promette 5 contatti quando il sales ne può lavorare 1 brucia la fiducia nel resto dei numeri. Con una sola azienda i conteggi diventano descrittivi (es. "1 contatto diretto (CTO) · percorso al CEO mappato").
Tabella riepilogativa = coda di lavoro, ordinata per priorità decrescente: Priorità | Azienda | Settore | Perché ora | Stato lead | Primo contatto. L'ordinamento per priorità fa sì che la prima riga sia il lead da lavorare per primo.
Poi le schede di dettaglio raggruppate per settore.
Chiusura: **solo la legenda** (come leggere label, badge, priorità/deal, dati stale). Le note specifiche di una singola azienda (conflitti riconciliati, precisazioni su un dato o un contatto) NON vanno in fondo: vanno nel campo NOTE della card di quell'azienda.

## Contratto di stile HTML

Il rendering finale è un dossier leggibile e scannabile, non un muro di testo. Il file `references/report-template.html` è l'implementazione di riferimento (template canonico con un'azienda di esempio fittizia): replicane struttura, CSS e classi. Regole fisse.

**Estetica.** Stile dossier da analista, sobrio (tono Corley). Sfondo carta caldo, card bianche; serif (Georgia) per i titoli e i nomi azienda, sans di sistema per il testo, monospace maiuscoletto per le label dei campi e numeri tabellari. Primario navy Corley `#1b4f72`, accento caldo `#b5512a` per la parte commerciale. Self-contained (CSS inline), print-friendly, responsive (le colonne collassano sotto i 760px).

**Struttura della pagina.**
- Masthead sticky con titolo, conteggi (aziende · settori · contatti azionabili) e nav ad ancore per azienda.
- Riga di lead con angolo, profondità in linguaggio piano ("dossier approfondito" per il Deep), data, fonti.
- Tabella riepilogativa (numeri tabellari, badge stato, link all'ancora dell'azienda).
- Una `<section>` per settore con intestazione, poi una card `<article>` per azienda.

**Dossier su singola azienda.** Con una sola azienda la tabella riepilogativa si omette (una coda di lavoro da una riga duplica l'header della card): si va dritti alla scheda. In più il `<body>` prende `class="single"`: in stampa la card può spezzarsi tra le pagine (i componenti interni restano interi grazie ai loro `break-inside:avoid`), altrimenti la prima pagina del PDF resta quasi vuota.

**Struttura della card azienda.**
- Header: nome (serif) + eventuale alias gruppo + descrizione di una riga; a destra (`.co__meta`) il badge priorità (`.prio`) e la pill stato lead (la lista di provenienza si cita per nome dentro la card).
- Striscia di fatti chiave (`.stats`, griglia a 6): Sede · Dimensione · Ricavi · Utile/Risultato · Salute · AWS. Leggibili a colpo d'occhio. Nella striscia vanno solo numeri verificati o stime solide: un numero con caveat forte (es. un utile single-source da verificare) NON va in evidenza, perché è quello che il sales ricorda e cita; al suo posto un dato verificato che vende (es. la crescita %), il dettaglio col caveat sta nella riga Finanza.
- Callout `.why` ("Perché ora") subito sotto la striscia: il trigger di timing in una riga, chiuso dalla riga AZIONE in grassetto ("→ Azione proposta: scrivere a {contatto ★} ({canale})"): la risposta a "chi contatto per primo?" deve stare sulla prima schermata, non in fondo alla rail. Attenzione: `.why` è un flex container, quindi tutto il contenuto dopo il `<b>` va racchiuso in un unico `<span>` (testo e tag sciolti diventano flex item separati e il testo si spezza in colonne).
- Corpo a due colonne: a sinistra (`.main`) i blocchi descrittivi come righe `label / valore` (`.row`), l'organigramma e le schede persona; a destra la rail evidenziata (`.rail`) "Per Corley" con ICP fit / Pain / Leve / Contro-leva / Mossa (contatto, canale, obiettivo di chiusura; mai scadenze) e il box `.draft` con la bozza di apertura (riga Oggetto inclusa). La rail è la parte che serve al sales: tenerla in evidenza.
- Il campo NOTE si rende come callout a tutta larghezza (riusa lo stile `.gap`) etichettato "Verifiche sui dati", subito sopra Gap & rischi: dentro la colonna, dopo le schede persona, sembrerebbe riferito all'ultima persona invece che all'azienda.
- Callout `.gap` (bordo tratteggiato) per Gap & rischi.
- Footer `.src` con le fonti come chip-link.

**Mappatura e colori.**
- Stato lead: pill `.pill--calda` (verde) / `.pill--fredda` (grigia). Se le liste lead non erano accessibili (vedi `lead-crossref.md`): pill con testo `NON VERIFICATO` (riusa lo stile della fredda) e spiegazione in legenda.
- Priorità: badge `.prio--alta` (rosso) / `.prio--media` (ambra) / `.prio--bassa` (grigio).
- Label affidabilità inline come tag `.t`: `.t--d` Dato (verde), `.t--s` Stima (ambra), `.t--i` Ipotesi/gap (rust). Priorità e ICP fit portano sempre `.t--s` (sono giudizi).
- Ogni riga `CHIAVE: valore` dei layout diventa una `.row` (label monospace a sinistra, valore a destra).
- Fonti come `<a>` a pillola.

### Organigramma (componente `.org`)

I contatti si rendono come organigramma a tre fasce, non come elenco piatto:

- Tre colonne: **Decide** (budget/firma, accento caldo) · **Influenza** (tecnici/champion) · **Usa**.
- Ogni persona è un nodo `.node` (nome in navy + ruolo). Il contatto consigliato per il primo approccio è `.node--star` (★). Chi è decisore ma ignoto o non raggiungibile (es. CTO non nei lead, direttore IT da identificare) è un nodo `.node--ghost` (tratteggiato).
- Dentro il nodo, una riga `.c` con i dati di contatto: email (✉, come `mailto:`) e telefono (☎) per le persone presenti nei fogli lead; telefono assente si rende "n.d.". Quando esiste un profilo LinkedIn verificato, aggiungi la pillola `.li` (testo "in") che linka al profilo. Mai inventare email, telefono o URL LinkedIn: si riportano solo se reperiti.
- Riga finale `.org__path`: il percorso consigliato, da chi contattare al target, con la freccia del ponte (es. "engineer Senior ★ → CTO"). Questo rende esplicito al sales chi scrivere per primo e dove vuole arrivare.
- Sotto l'organigramma, una riga sintetica sul contatto prioritario (note lead/web, email, link), con le label di affidabilità dove serve.
- Le figure di contorno (contractor, maintainer esterni, consulenti: vedi `people-mapping.md`) non occupano nodi: si riassumono in una seconda riga stile `.org__path` etichettata "Contorno", come segnale (es. capacità interna minima).

### Scheda persona (componente `.person`)

La scheda persona (Standard: contatto prioritario; Deep: tutti i contatti chiave) si rende dentro un blocco `.person` annidato nella card dell'azienda, usando righe `.row` (label monospace / valore) per i campi PERCORSO / OPINIONI-TEMI / PASSIONI / STILE / GANCI / CANALE del layout testuale; le FONTI della persona chiudono il blocco come link a pillola (stesso stile del footer `.src`). I ganci portano inline il tag di affidabilità e l'uso consigliato — "(ok in apertura)" / "(per qualificare)" / "(solo in call)" — con gli scartati e il loro motivo (mai nominare il test nel testo, vedi regola sul gergo). Non introdurre classi nuove: nel template canonico `.person` ha solo la regola di stampa `break-inside:avoid` e il blocco si compone con le `.row` esistenti.

### Legenda in chiusura (solo glossario, niente note per-azienda)

La sezione finale è SOLO la legenda: spiega come leggere il dossier, in linguaggio per un sales non tecnico. NON contiene osservazioni su singole aziende. Glossa in parole semplici:

- cosa significano le label: `[Dato]` = verificato su fonte affidabile; `[Stima]` = calcolato/valutato da noi con un'assunzione dichiarata (incluse priorità e ICP fit); `[Ipotesi]` = da confermare, non ancora verificato;
- cosa significano i badge di stato: `CALDA` = l'azienda è già nelle liste lead Corley (contatto o evento passato, con la lista citata per nome); `FREDDA` = nessun contatto pregresso nelle liste lead su Drive; `NON VERIFICATO` = liste non accessibili in questa run;
- cosa significano i badge di priorità: `Alta/Media/Bassa` ordinano la coda di lavoro in base a fit, timing e raggiungibilità (il valore economico del lead non viene stimato: lo dimensiona il sales in offerta);
- cosa vuol dire "dato datato/stale": un numero che fa riferimento a un periodo ormai passato (tipicamente un bilancio di oltre 12-18 mesi) e che potrebbe essere superato.

Niente gergo tecnico qui. **Le note specifiche di una singola azienda** (un conflitto consolidato/civilistico riconciliato, una precisazione su un contatto, un'avvertenza su un dato) vanno nel campo NOTE della card di quell'azienda, mai accumulate in fondo.

### Implementazione di riferimento

Lo scheletro HTML canonico, completo di CSS e di tutte le classi citate sopra (`.mast`, `.stats`, `.co__meta`, `.prio`, `.why`, `.co__body`, `.rail`, `.draft`, `.org`, `.pill`, `.t`, ecc.), è il file `references/report-template.html`. In fase di rendering, partire da quel file: riusarne il blocco `<style>` invariato e popolare i contenuti al posto dell'azienda di esempio. Non reintrodurre stili generici (Arial, card piatte a tutta larghezza): l'obiettivo della resa è la leggibilità a colpo d'occhio.

Il blocco `<style>` di riferimento include già le regole `@media print` necessarie (formato A4, `print-color-adjust:exact` per mantenere i colori dei badge, `break-inside:avoid` su card/organigramma/gap per non spezzarli, e il ripristino delle griglie a due colonne: senza, la media query responsive da 760px scatterebbe anche in stampa A4 e il PDF collasserebbe a una colonna). Mantienile invariate: sono ciò che fa sì che il PDF conservi la stessa leggibilità dell'HTML.

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
