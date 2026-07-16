# Calcolo Volume Spedizioni + Orario

App browser con due funzioni: **Rettifiche** (calcolo volume di colli/spedizioni moltiplicato per un fattore P/V) e **Orario** (tabella mensile ore di lavoro). Usata da iPhone sul lavoro.

## Stack

- Vanilla HTML/CSS/JS (tutto in un singolo `index.html`, no framework, no build step)
- localStorage per storico Rettifiche e dati Orario
- Hosting: GitHub Pages

## Repository e sito

- **Repo:** https://github.com/Sandro2135/calcolo-bancali
- **Sito live:** https://sandro2135.github.io/calcolo-bancali/

## Come aggiornare il sito

1. Modifica `index.html`
2. `git add index.html`
3. `git commit -m "messaggio"`
4. `git push`
5. Aspetta 1-2 minuti → GitHub Pages si aggiorna
6. Su iPhone Safari: tieni premuto 🔄 → "Ricarica senza contenuto della cache" se non vedi le modifiche

**Nota — il deploy di GitHub Pages a volte fallisce silenziosamente o resta a lungo `queued`:** il workflow "pages build and deployment" può concludersi con `conclusion: failure` e il messaggio generico "Deployment failed, try again later", senza che ci sia nulla di sbagliato nel codice pushato (è capitato anche con un commit di solo testo), oppure restare in coda per diversi minuti prima di partire. Se dopo qualche minuto il sito non mostra le modifiche, non è detto sia colpa della cache del telefono: verificare lo stato dell'ultima build su `https://api.github.com/repos/Sandro2135/calcolo-bancali/actions/runs?per_page=1` (campi `status`/`conclusion`, e `head_sha` per verificare che sia la build del commit giusto) prima di dare per scontato un problema di cache. Se `failure`, rilanciare con un commit vuoto: `git commit --allow-empty -m "retry deploy" && git push`.

**Nota — bug CSS visibili solo su iPhone Safari, non riproducibili su browser desktop:** alcuni bug (vedi "Fix iOS applicati" sotto, caso `.menu-btn`) si manifestano solo sul dispositivo reale. In questi casi non è possibile verificare in locale: si pubblica, si fa testare all'utente sul telefono, e si itera. Se un componente sembra "vinto" da uno stile generico solo su iOS pur avendo CSS più specifico che dovrebbe prevalere per specificità, la soluzione più affidabile è forzare le proprietà critiche con `!important` invece di affidarsi alla sola specificità dei selettori.

## Funzionalità

### Home
- Prima schermata all'apertura dell'app: due tasti grandi, centrati verticalmente nello schermo
  - **Rettifiche** → apre il menu Spedizione singola/multipla
  - **Orario** → apre la tabella oraria mensile
- Tasto "‹ Home" in alto in entrambe le sezioni per tornare alla home

### Tema visivo: "Neumorphism verde" (design moderno, attivo)
Il vecchio tema "bacheca in sughero" (foto sfondo, puntine, fogli di carta ruotati) è stato **completamente rimosso** su richiesta esplicita dell'utente ("redesign completo, tema moderno, essenziale"). Prima di arrivare al tema attuale sono stati provati e scartati (mai pubblicati, solo test locali con `git checkout` per tornare indietro):
1. Tema flat minimal stile iOS/Apple (sfondo grigio `#F2F2F7`, card bianche con bordo sottile, accento blu `#007AFF`) — pubblicato per primo, poi sostituito
2. Glassmorphism verde (pannelli semi-trasparenti con `backdrop-filter: blur()`, sfondo a macchie sfumate colorate) — solo locale
3. Neumorphism ambra (base crema, ombre doppie soft-UI, accento arancione/ambra) — solo locale, poi ricolorato in verde su richiesta

**Tema attuale (pubblicato):** Neumorphism con base crema e accento verde. Caratteristiche:
- Sfondo app e superficie di card/pulsanti tutti dello **stesso colore base** (`--base: #f1eee7`, crema caldo) — niente foto, niente gradiente
- Effetto "soft UI": ogni elemento sembra **estruso** (in rilievo, doppia ombra: chiara in alto-sinistra `rgba(255,255,255,0.85-0.9)` + scura calda in basso-destra `rgba(158,150,130,0.4-0.45)`) oppure **incassato/premuto** (stesse ombre ma `inset`), mai bordi netti
- Campi di input e caselle "lette" (es. riepilogo Plus/minus, fascia di selezione della rotella) usano l'effetto incassato; card, pulsanti e pillole di navigazione usano l'effetto in rilievo
- Bottoni primari e di navigazione hanno un leggero effetto "pressione" al tap (`:active` passa a ombra incassata)
- **Non ci sono più bordi colorati/pushpin**: tutta la profondità viene dalle ombre, non da bordi o texture

**Variabili colore principali (in `:root`):**
| Variabile | Valore | Uso |
|---|---|---|
| `--base` / `--base-hover` | `#f1eee7` / `#e8e4da` | Sfondo pagina, sfondo di ogni superficie (card, pulsanti neutri) |
| `--text` / `--text-secondary` / `--text-tertiary` | `#3d3a34` / `#8c8677` / `#b9b3a2` | Testo principale / secondario / placeholder-disabilitato |
| `--amber` / `--amber-dark` / `--amber-tint` | `#22a559` / `#167a3f` / `#e2f3e6` | **Verde primario** — Rettifiche, pulsanti principali (Calcola PV, Storico, Aggiungi, ecc.). Nome variabile storico (era ambra prima del cambio colore), il valore ora è verde |
| `--copper` / `--copper-dark` / `--copper-tint` | `#0d5c34` / `#0a4527` / `#dcece1` | **Verde scuro/forestale, identità di Orario** — intestazione tabella, rotella, Plus/minus, mese-nav, codice P |
| `--red` / `--red-dark` / `--red-tint` | `#ef4444` / `#c62828` / `#fbe0e0` | Azioni distruttive (Azzera, Rimuovi, Cancella tutto), codice M (malattia) |
| `--green-dark` | `#2f7d32` | Scostamento positivo in Orario, codice F (ferie) |

**Ombre neumorfiche (in `:root`):** `--shadow-out`/`--shadow-out-sm` (in rilievo), `--shadow-in`/`--shadow-in-sm` (incassato), `--shadow-amber`/`--shadow-amber-in` (rilievo/incassato colorati verdi, per i pulsanti primari), `--shadow-red`/`--shadow-red-in` (idem in rosso, per i pulsanti distruttivi). Tutte a doppio tono (chiaro + scuro caldo).

**Home:** i due tasti grandi (Rettifiche/Orario) sono card neutre in rilievo (`--shadow-out`) con testo colorato in grassetto (Rettifiche = `--amber-dark` verde, Orario = `--copper-dark` verde scuro) — niente riempimento colorato pieno, coerente con lo stile "soft UI" dove il colore vive nel testo/accenti, non nelle superfici.

**Se si vuole cambiare ancora il colore d'accento:** basta modificare i valori esadecimali di `--amber`/`--amber-dark`/`--amber-tint` (primario) e `--copper`/`--copper-dark`/`--copper-tint` (identità Orario) in `:root`, più le rgba dentro `--shadow-amber`/`--shadow-amber-in` (che devono restare coerenti col nuovo colore per l'effetto ombra colorata dei pulsanti). Il file `sfondo2.jpg` del vecchio tema bacheca è ancora nel repo ma non più referenziato da nessun CSS — può essere eliminato in un futuro cleanup, non è urgente.

### Icona home screen iPhone
- `<link rel="apple-touch-icon" href="icon.png" />` + `<link rel="icon" href="icon.png" />` nell'`<head>` — prima non c'era nessuna icona, iOS mostrava una "C" bianca su sfondo grigio generata automaticamente dal titolo della pagina
- `icon.png` (512×512, nel repo accanto a `index.html`): un pacco/scatola isometrico a 3 tonalità di verde (`#6fcf97` coperchio, `#22a559` parete sinistra, `#167a3f` parete destra) su sfondo crema (`#f1eee7`, stesso `--base` del tema), con un nastro bianco semi-trasparente diagonale
- Generata con **PowerShell + `System.Drawing`** (stesso approccio usato in passato per ridimensionare le foto di sfondo del vecchio tema bacheca), nessuno strumento esterno necessario. Script salvato solo nella cronologia della sessione, non nel repo — se va rigenerata bisogna ricostruire lo script (vedi logica sotto)
- **Geometria del nastro:** costruita nel sistema di coordinate proprie delle facce della scatola (vettori `u`/`v` del coperchio, `w` di profondità) invece che con un semplice offset perpendicolare in pixel — necessario perché un offset 2D "ingenuo" non rispetta la prospettiva isometrica e il nastro risultava storto/non simmetrico. Il nastro è un **unico poligono** (coperchio + discesa sulla parete sinistra uniti, nessuna giunzione visibile), con taglio finale parallelo agli spigoli obliqui della scatola e angoli netti (no arrotondamenti)
- **Dimensioni attuali della scatola nel canvas 512×512:** `half=195` (semi-larghezza), `halfH = half*75/130`, `depth = half*150/130` (stessi rapporti isometrici del disegno originale), centrata nel canvas col bounding-box verticale reale (non con un offset fisso) — ingrandita due volte su richiesta esplicita perché appariva troppo piccola nella schermata home dell'iPhone
- **Etichetta di spedizione** (stile corriere, es. GLS) sulla parete destra (più scura): rettangolo con lato lungo **verticale**, dello stesso colore/opacità del nastro (`rgba(255,255,255,110/255)`), posizionato in basso a destra vicino allo spigolo destro della scatola. Costruita con la stessa tecnica del nastro (parametrizzazione `s,t` della faccia destra con vettori `u2 = right-bottom` e `w2 = bottomLow-bottom`), non con un rettangolo 2D piatto, altrimenti i lati non risultano paralleli agli spigoli della scatola. Parametri finali: `s0=0.64, s1=0.88` (larghezza), `t0=0.50, t1=0.84` (altezza)
- **Nota importante:** su iPhone Safari, dopo aver pubblicato una nuova versione dell'icona, bisogna **rimuovere e ricreare il segnalibro** sulla home — Safari mantiene in cache l'icona vecchia per i segnalibri già esistenti, ricaricare la pagina non basta

### Menu (dentro Rettifiche)
- Due sezioni: **Spedizione singola** e **Spedizione multipla**
- Il menu si trova **sotto** il foglio (container), non sopra
- Tasto attivo: sfondo crema con ombra incassata + testo verde scuro in grassetto (non riempimento verde pieno — vedi bug iOS in "Fix iOS applicati")

### Spedizione singola
- Selezione fattore P/V a tendina: 300, 250, 200, 167, 150, 100
- Campi X, Y, Z sulla stessa riga (CSS grid 1fr 1fr 1fr)
- Risultato PV = (volume in m³) × fattore, mostrato con 1 decimale
- Dopo il calcolo azzera automaticamente i campi
- Salva nel localStorage

### Spedizione multipla
- Stessa selezione fattore P/V
- **+ Aggiungi collo**: aggiunge un collo singolo alla lista
- **+ Aggiungi collo e moltiplica**: apre un modal per inserire quanti colli uguali aggiungere; vengono mostrati come riga raggruppata (es. `10× X:12 Y:12 Z:15`)
- Pulsante Rimuovi per ciascun collo (rimuove tutti i colli del gruppo)
- Calcola PV totale sommando tutti i colli
- Salva nel localStorage

### Storico
- Apre come modal sopra la pagina
- Mostra i calcoli più recenti in cima
- Spedizioni multiple: menu a tendina "Vedi dimensioni" con `<details>/<summary>`; mostra `qty×` per colli raggruppati
- Pulsante "Cancella tutto" con conferma
- Key localStorage: `bancali_storia`

### Tastiera numerica personalizzata (iOS)
- Blocca la tastiera nativa iOS (input `readonly`)
- Tastierino scorrevole dal basso, stesso stile neumorfico (superficie crema, ombre in rilievo/incassate)
- Layout: 1-9 + ⌫ + 0 + →
- **→** (verde tenue): X → Y → Z → chiude il tastierino (no loop)
- **⌫** (rosso): cancella un carattere
- **Fatto** (verde): chiude il tastierino
- Il tasto → è nascosto nel popup "quanti colli" (un solo campo)
- Quando la tastiera è aperta con il modal moltiplica visibile: il modal si sposta in alto (classe `tastiera-aperta`)

### Orario
- Tabella mensile "tipo Excel" con 6 colonne: **Data** (gg/mm/aa) | Entrata | Uscita | Entrata | Uscita | Totale
- Una riga per **ogni giorno del mese** (anche weekend), non solo i giorni compilati
- Intestazione tabella (Data/Entrata/Uscita/Totale) a `font-size: 0.56rem` — ridotta da `0.64rem` perché su schermo iPhone risultava troppo grande
- Navigazione mese con frecce ‹ › sopra la tabella
- **Riepilogo "Plus/minus"** in cima: scostamento tra ore lavorate nel mese e monte ore teorico (6h × giorni feriali lun-ven già trascorsi nel mese, tutto il mese se è passato, zero se è futuro)
  - Formato `+Xh Y'` / `-Xh Y'`; se sotto l'ora mostra solo i minuti (es. `+45'`)
- **Inserimento orario**: tocchi una cella Entrata/Uscita e si apre una rotella stile sveglia iPhone (due colonne: ore 00-23, minuti solo 00/15/30/45), fascia centrale evidenziata
  - Cella vuota → rotella parte da "--" su ore e minuti
  - Cella già compilata → rotella parte dal valore salvato
  - **"--"** in cima a entrambe le rotelle: serve **su entrambe** per svuotare la cella (una sola non basta)
  - **"→ Avanti"**: conferma e passa al campo successivo della riga (Entrata→Uscita→Entrata2→Uscita2→chiude)
  - **"Fatto"**: chiude la rotella e aggiorna Totale/Plus-minus
- **Codici speciali per turno** (sotto la rotella, 4 pulsanti): **P** (permesso), **F** (ferie), **M** (malattia), **/** (assenza)
  - Impostano l'intero turno (sia entrata che uscita di quella metà giornata) sulla lettera; si vede solo l'iniziale nella cella
  - **Regola di calcolo (aggiornata):** se P/F/M copre **un solo turno** (mattina o pomeriggio) e nell'altro turno ci sono ore reali lavorate, il codice speciale **aggiunge al massimo 3h** al totale della giornata, **senza mai superare le 6h totali**. Es: 7:30-10:30 lavorate + pomeriggio P → 6h (3h+3h); 7:30-10:00 lavorate + P → 5h30 (2h30+3h, sotto il tetto); 7:30-11:00 lavorate + P → 6h (3h30+3h=6h30, tagliato a 6h). Questa regola vale ugualmente per P, F e M (non solo per il permesso)
  - Se P/F/M copre **entrambi i turni** dello stesso giorno (giornata intera, anche mista es. mattina M + pomeriggio P), il totale resta **6h fisse** come prima
  - **Assenza "/" non completa**: le ore mancanti restano scoperte (scostamento negativo nei feriali; nei weekend non ha comunque effetto perché il monte ore lì è già zero)
  - Per segnare un giorno intero, si imposta lo stesso codice su entrambi i turni
  - Reset: si toglie un codice riaprendo la rotella e mettendo "--" su ore e minuti
  - Logica in `minutiGiorno()`: nessun totale viene salvato in localStorage, viene **ricalcolato ad ogni apertura della tabella** dai dati grezzi (`e1/u1/e2/u2`) — quindi un cambio della formula si applica automaticamente anche ai giorni già inseriti in passato, senza dover reinserire nulla
- **Colonna Totale**: mostra `Xh Y'` (o solo `Y'` se sotto l'ora), oppure **"/"** per i giorni con assenza intera o weekend senza orari — ma **solo se il giorno è già passato o è oggi** (i weekend futuri restano "—"). Weekend con orari realmente lavorati mostrano le ore effettive, non "/"
- Righe tabella tutte bianche/crema (nessuna alternanza di colore forte, solo un lieve tint verde sui weekend)
- Key localStorage: `orario_giorni` — oggetto (non array) indicizzato per data
- **Limite gennaio 2026:** non si può navigare col calendario (frecce ‹ ›) a mesi precedenti gennaio 2026 (`cambiaMese` ha un controllo che blocca `nuovoAnno < 2026`); il tasto "‹" si disabilita visivamente (opacity ridotta) quando si è a gennaio 2026 (`id="mese-prev"`, proprietà `.disabled` impostata in `renderMeseOrario()`). Inoltre, al caricamento dell'app, ogni giorno salvato in `orario_giorni` con data precedente al 2026-01-01 viene **eliminato automaticamente** da localStorage (poche righe subito dopo `getOrarioMap()`)

### Backup Orario
- Due pulsanti in cima alla sezione Orario, sopra la navigazione mese: **⬇ Esporta backup** e **⬆ Importa backup**
- **Esporta:** scarica un file `.json` (`backup-orario-YYYYMMDD.json`) con l'intero contenuto di `orario_giorni` (nessun dato di Rettifiche incluso, su richiesta esplicita dell'utente — non gli interessa salvare lo storico Rettifiche)
- **Importa:** apre un file picker, legge il JSON, chiede conferma (`confirm`) e **sovrascrive interamente** `orarioMap`/`orario_giorni` con il contenuto del file
- Funzioni: `esportaBackupOrario()` e `importaBackupOrario(event)`, vicino a `salvaOrarioMap()` nello script
- Scopo: sicurezza contro perdita dati (cancellazione accidentale, reset telefono, pulizia dati Safari) — non c'entra con bug di codice, il localStorage non viene toccato dagli aggiornamenti del sito finché la key `orario_giorni` e la struttura dati restano le stesse

## Struttura dati localStorage

```js
// Spedizione singola
{ tipo: 'singolo', l, p, a, fattore, pv, data }

// Spedizione multipla
{ tipo: 'multiplo', nbancali, spedizioni: [{x, y, z, qty}, ...], fattore, pv, data }
// qty >= 1; qty > 1 = colli raggruppati

// Orario (key: orario_giorni), oggetto indicizzato per data 'YYYY-MM-DD'
{
  "2026-07-04": { e1: '08:00', u1: '12:00', e2: '13:00', u2: '17:00' },
  "2026-07-05": { e1: 'P', u1: 'P', e2: 'P', u2: 'P' } // giorno intero permesso
}
// e1/u1 = turno mattina (entrata/uscita), e2/u2 = turno pomeriggio
// valori possibili: 'HH:MM', '' (vuoto), oppure uno dei codici speciali 'P' 'F' 'M' '/'
```

## Fix iOS applicati

- `font-size: 16px` sugli input per evitare zoom automatico
- Spinner input rimossi (`-webkit-appearance: none` + `appearance: textfield`)
- `touch-action: manipulation` sui tasti del tastierino per evitare zoom da doppio tap
- `user-scalable=no, maximum-scale=1.0` nel viewport meta tag per disabilitare completamente il pinch-to-zoom
- Overlay CSS fullscreen (z-index 9999, sfondo scuro) con icona e messaggio "Ruota il telefono in verticale" attivato da `@media screen and (orientation: landscape)` — nessun JS necessario
- `button { -webkit-appearance: none; appearance: none; -webkit-tap-highlight-color: transparent; }` globale, per evitare che iOS applichi stili/tap-highlight nativi ai bottoni custom
- **Bug risolto — tasto menu (`.menu-btn`) "coperto" di verde su iOS:** su iPhone Safari (non riproducibile su browser desktop), il tasto selezionato di "Spedizione singola/multipla" veniva a volte visualizzato con sfondo verde pieno e testo invisibile, invece del previsto sfondo crema con testo verde scuro in ombra incassata. La causa era un conflitto con la regola generica `button { background: var(--amber); ... }` (usata per i pulsanti primari come "Calcola PV") che in alcuni casi vinceva su iOS nonostante `.menu-btn` avesse una specificità CSS teoricamente maggiore. **Soluzione:** tutte le proprietà di `.menu-btn`, `.menu-btn.active`, `.menu-btn:active` e `.menu-btn.active:active` sono ora dichiarate con `!important`, che ha risolto il problema in modo definitivo. Se in futuro si aggiungono altri elementi tipo "pillola di navigazione" (stile diverso dal pulsante generico ma stesso tag `<button>`), applicare da subito lo stesso pattern `!important` per evitare lo stesso bug.

- **Bug NON risolto — rotella Orario mostra l'ultimo orario invece di "--" su una cella nuova:** quando ci si sposta dalla rotella dell'ultima cella di un giorno alla prima cella del giorno successivo (sia chiudendo con "Fatto" sia con "Avanti"), a volte la rotella si apre già posizionata sull'ultimo orario inserito invece che su "--", su entrambe le colonne (ore e minuti). Riproducibile solo su iPhone reale, mai su browser desktop. **Due tentativi di fix falliti:** (1) cancellare un eventuale timer di aggiornamento rimasto in sospeso sul container della rotella (`container._ruotaTimeout`) via `clearTimeout`; (2) interrompere un eventuale scroll "a inerzia" residuo forzando un toggle `overflow: hidden` → `''` prima di reimpostare `scrollTop`. Nessuno dei due ha risolto il problema quando ritestato sul telefono — la causa reale non è stata identificata. L'utente ha deciso di lasciar perdere ("è un problema veramente irrisorio"), quindi **non è prioritario**, ma se si riprende in futuro conviene chiedere subito uno screenshot/video della sequenza esatta invece di ipotizzare cause a priori (le due ipotesi tentate qui si sono rivelate sbagliate).
