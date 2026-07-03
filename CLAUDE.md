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

## Funzionalità

### Home
- Prima schermata all'apertura dell'app: due tasti grandi, centrati verticalmente nello schermo
  - **Rettifiche** (verde scuro `#276749`) → apre il menu Spedizione singola/multipla
  - **Orario** (blu `#2b6cb0`) → apre la tabella oraria mensile
- Tasto "‹ Home" in alto in entrambe le sezioni per tornare alla home
- Sfondo app: `#f0dccb` (beige/terracotta chiaro)

### Menu (dentro Rettifiche)
- Due sezioni: **Spedizione singola** e **Spedizione multipla**
- Tasto attivo: verde scuro `#276749`

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

## Stile tasti

| Tasto | Colore sfondo | Testo | Hover |
|---|---|---|---|
| Calcola PV | `#48bb78` verde | bianco | `#38a169` |
| Azzera | `#e53e3e` rosso | bianco | `#c53030` |
| Storico | `#c8960c` giallo | bianco | `#a87a08` |
| + Aggiungi collo / moltiplica | `#ebf8ff` + bordo `#4299e1` | `#2b6cb0` | `#bee3f8` |
| Cancella tutto (storico) | `#e53e3e` rosso | bianco | `#c53030` |
| Rimuovi (lista colli) | `#e53e3e` rosso | bianco | `#c53030` |
| Tastiera ⌫ | `#e53e3e` rosso | bianco | — |
| Tastiera → | `#ebf8ff` + bordo `#4299e1` | `#2b6cb0` | — |
| Tastiera Fatto | `#48bb78` verde | bianco | `#38a169` |
| Menu attivo | `#276749` verde scuro | bianco | — |

## Fix iOS applicati

- `font-size: 16px` sugli input per evitare zoom automatico
- Spinner input rimossi (`-webkit-appearance: none` + `appearance: textfield`)
- `touch-action: manipulation` sui tasti del tastierino per evitare zoom da doppio tap
- `user-scalable=no, maximum-scale=1.0` nel viewport meta tag per disabilitare completamente il pinch-to-zoom
- Overlay CSS fullscreen (z-index 9999, sfondo scuro) con icona e messaggio "Ruota il telefono in verticale" attivato da `@media screen and (orientation: landscape)` — nessun JS necessario
