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

**Nota — il deploy di GitHub Pages a volte fallisce silenziosamente:** il workflow "pages build and deployment" può concludersi con `conclusion: failure` e il messaggio generico "Deployment failed, try again later", senza che ci sia nulla di sbagliato nel codice pushato (è capitato anche con un commit di solo testo). Se dopo qualche minuto il sito non mostra le modifiche, non è detto sia colpa della cache del telefono: verificare lo stato dell'ultima build su `https://api.github.com/repos/Sandro2135/calcolo-bancali/actions/runs?per_page=1` (campo `conclusion`) prima di dare per scontato un problema di cache. Se `failure`, rilanciare con un commit vuoto: `git commit --allow-empty -m "retry deploy" && git push`.

## Funzionalità

### Home
- Prima schermata all'apertura dell'app: due tasti grandi, centrati verticalmente nello schermo
  - **Rettifiche** (marrone scuro `#5c3a1e`) → apre il menu Spedizione singola/multipla
  - **Orario** (verde-petrolio `#3d6b5c`) → apre la tabella oraria mensile
- Tasto "‹ Home" in alto in entrambe le sezioni per tornare alla home

### Tema visivo "bacheca in sughero"
- Sfondo app: foto reale di sughero (`sfondo2.jpg`, in repo accanto a `index.html`), `background-size: cover`, `background-attachment: fixed` (su iOS Safari l'attachment fixed non è supportato e scorre normalmente con la pagina — comportamento accettato, non un bug)
- I pannelli bianchi (`.container` di Rettifiche/Orario, e i `.modal` di Storico/Aggiungi colli multipli) sono trattati come **fogli di carta pinnati sulla bacheca**:
  - Colore pergamena calda (`#f7f0e0`) con una texture a grana sottile (SVG `feTurbulence` inline in data-URI, non un file esterno)
  - Leggermente ruotati (`transform: rotate(-0.4/-0.5deg)`), bordi poco arrotondati (`border-radius: 4px`)
  - Ombra morbida e calda (marrone scuro invece di nero puro) per sembrare appoggiati con luce ambiente
  - **Puntina rossa** in cima (`::before`, cerchio ~22px con riflesso lucido bianco e bordi in ombra/luce per un effetto 3D) + **ombra proiettata** (`::after`, ellisse sfumata leggermente spostata, per dare l'idea che la puntina sia sollevata dal foglio)
- **Palette colori unificata** (sostituisce i colori vivaci "web" originali con toni caldi legno/terracotta):
  - Rettifiche: marrone scuro `#5c3a1e` (attivo/primario), marrone medio `#8b5e34` (pulsanti/hover `#74491f`), terracotta `#a94442` (azzera/rimuovi/errori, hover `#8c3735`), senape `#a8790a` (storico, hover `#8a6408`), crema-tan `#f3e6d0`/`#8a5a2e`/`#c9a876` (accenti "aggiungi", risultati, badge storico)
  - Orario: verde-petrolio `#3d6b5c` (identità propria: intestazione tabella, rotella, Plus/minus, mese-nav, codice P), oliva `#6b7f4f` (positivo/ferie F), terracotta `#a94442` (negativo/malattia M, condiviso con Rettifiche)
- **Se si vuole cambiare la foto di sfondo**: sostituire `sfondo2.jpg` con un altro file (stesso nome o aggiornare il riferimento in CSS `background-image: url("sfondo2.jpg")` nel selettore `body`); ottimizzare prima le foto (le tre foto originali fornite dall'utente erano PNG da 2.5-3.3MB, ridotte con PowerShell/System.Drawing a JPEG ~300KB, 700px di larghezza)

### Menu (dentro Rettifiche)
- Due sezioni: **Spedizione singola** e **Spedizione multipla**
- Il menu si trova **sotto** il foglio (container), non sopra: prima vedi il foglio con la puntina, poi i tasti di scelta sezione
- Tasto attivo: marrone scuro `#5c3a1e`

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
- Tastierino scorrevole dal basso
- Layout: 1-9 + ⌫ + 0 + →
- **→** (azzurro): X → Y → Z → chiude il tastierino (no loop)
- **⌫** (rosso): cancella un carattere
- **Fatto** (verde): chiude il tastierino
- Il tasto → è nascosto nel popup "quanti colli" (un solo campo)
- Quando la tastiera è aperta con il modal moltiplica visibile: il modal si sposta in alto (classe `tastiera-aperta`)

### Orario
- Tabella mensile "tipo Excel" con 6 colonne: **Data** (gg/mm/aa) | Entrata | Uscita | Entrata | Uscita | Totale
- Una riga per **ogni giorno del mese** (anche weekend), non solo i giorni compilati
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
  - P/F/M **completano sempre la giornata a 6h** (se lavori solo mezza giornata, l'altra metà con una di queste lettere porta il totale a 6h)
  - **Assenza "/" non completa**: le ore mancanti restano scoperte (scostamento negativo nei feriali; nei weekend non ha comunque effetto perché il monte ore lì è già zero)
  - Per segnare un giorno intero, si imposta lo stesso codice su entrambi i turni
  - Reset: si toglie un codice riaprendo la rotella e mettendo "--" su ore e minuti
- **Colonna Totale**: mostra `Xh Y'` (o solo `Y'` se sotto l'ora), oppure **"/"** per i giorni con assenza intera o weekend senza orari — ma **solo se il giorno è già passato o è oggi** (i weekend futuri restano "—"). Weekend con orari realmente lavorati mostrano le ore effettive, non "/"
- Righe tabella tutte bianche (nessuna alternanza di colore)
- Key localStorage: `orario_giorni` — oggetto (non array) indicizzato per data

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

## Stile tasti (palette legno/terracotta, aggiornata)

| Tasto | Colore sfondo | Testo | Hover |
|---|---|---|---|
| Calcola PV | `#8b5e34` marrone | bianco | `#74491f` |
| Azzera | `#a94442` terracotta | bianco | `#8c3735` |
| Storico | `#a8790a` senape | bianco | `#8a6408` |
| + Aggiungi collo / moltiplica | `#f3e6d0` + bordo `#c9a876` | `#8a5a2e` | `#ead9bd` |
| Cancella tutto (storico) | `#a94442` terracotta | bianco | `#8c3735` |
| Rimuovi (lista colli) | `#a94442` terracotta | bianco | `#8c3735` |
| Tastiera ⌫ | `#a94442` terracotta | bianco | — |
| Tastiera → | `#f3e6d0` + bordo `#c9a876` | `#8a5a2e` | — |
| Tastiera Fatto | `#8b5e34` marrone | bianco | `#74491f` |
| Menu attivo | `#5c3a1e` marrone scuro | bianco | — |
| Home Rettifiche | `#5c3a1e` marrone scuro | bianco | — |
| Home Orario / identità Orario | `#3d6b5c` verde-petrolio | bianco | `#c9ded7` |

## Fix iOS applicati

- `font-size: 16px` sugli input per evitare zoom automatico
- Spinner input rimossi (`-webkit-appearance: none` + `appearance: textfield`)
- `touch-action: manipulation` sui tasti del tastierino per evitare zoom da doppio tap
- `user-scalable=no, maximum-scale=1.0` nel viewport meta tag per disabilitare completamente il pinch-to-zoom
- Overlay CSS fullscreen (z-index 9999, sfondo scuro) con icona e messaggio "Ruota il telefono in verticale" attivato da `@media screen and (orientation: landscape)` — nessun JS necessario
