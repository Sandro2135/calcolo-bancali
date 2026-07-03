# Calcolo Volume Spedizioni

App browser per calcolare il volume di colli/spedizioni moltiplicato per un fattore P/V. Usata da iPhone sul lavoro.

## Stack

- Vanilla HTML/CSS/JS (tutto in un singolo `index.html`, no framework, no build step)
- localStorage per lo storico dei calcoli
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

### Menu
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

## Struttura dati localStorage

```js
// Spedizione singola
{ tipo: 'singolo', l, p, a, fattore, pv, data }

// Spedizione multipla
{ tipo: 'multiplo', nbancali, spedizioni: [{x, y, z, qty}, ...], fattore, pv, data }
// qty >= 1; qty > 1 = colli raggruppati
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
