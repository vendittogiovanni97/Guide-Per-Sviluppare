# Guida Essenziale CSS per Sviluppatori Frontend

## Indice
1. [Fondamenti CSS](#fondamenti-css)
2. [Box Model](#box-model)
3. [Layout e Posizionamento](#layout-e-posizionamento)
4. [Flexbox](#flexbox)
5. [Grid](#grid)
6. [Responsive Design](#responsive-design)
7. [Selettori Avanzati](#selettori-avanzati)
8. [Proprietà Visuali](#proprietà-visuali)
9. [Transizioni e Animazioni](#transizioni-e-animazioni)
10. [Best Practices](#best-practices)

## Fondamenti CSS

### Sintassi di Base
```css
selettore {
  proprietà: valore;
}
```

### Selettori Principali
- **Elemento**: `div { }`
- **Classe**: `.classe { }`
- **ID**: `#id { }`
- **Attributo**: `[tipo="text"] { }`
- **Pseudo-classi**: `:hover`, `:focus`, `:first-child`
- **Pseudo-elementi**: `::before`, `::after`

### Specificità
La specificità determina quale regola CSS viene applicata. In ordine crescente:
1. Selettori di elemento (`div`, `p`) - 1 punto
2. Classi (`.classe`), attributi, pseudo-classi - 10 punti
3. ID (`#id`) - 100 punti
4. Stili inline - 1000 punti
5. `!important` - sovrascrive tutto

**Perché è importante**: Comprendere la specificità ti aiuta a evitare codice ridondante e a scrivere CSS più pulito e manutenibile.

## Box Model

Il Box Model è fondamentale per comprendere come gli elementi occupano spazio nella pagina.

### Componenti (dall'interno all'esterno)
- **Content**: L'area dove appare il contenuto
- **Padding**: Lo spazio tra il contenuto e il bordo
- **Border**: La linea che circonda il padding
- **Margin**: Lo spazio esterno al bordo

```css
.box {
  width: 200px;
  height: 100px;
  padding: 20px;
  border: 1px solid black;
  margin: 30px;
}
```

### box-sizing
- `content-box`: Default, le dimensioni (width/height) si riferiscono solo al contenuto
- `border-box`: Le dimensioni includono padding e bordo

```css
* {
  box-sizing: border-box; /* Usato spesso per rendere il dimensionamento più intuitivo */
}
```

**Perché è importante**: Comprendere il box model è cruciale per controllare con precisione le dimensioni e gli spazi tra gli elementi.

## Layout e Posizionamento

### Display
- `block`: Occupa tutta la larghezza disponibile
- `inline`: Occupa solo lo spazio necessario, non accetta width/height
- `inline-block`: Come inline ma accetta width/height
- `none`: Rimuove l'elemento dal flusso della pagina

### Position
- `static`: Default, segue il flusso normale
- `relative`: Posizionato relativamente alla sua posizione normale
- `absolute`: Posizionato relativamente all'antenato posizionato più vicino
- `fixed`: Posizionato relativamente alla viewport
- `sticky`: Si comporta come relative fino a quando non raggiunge una soglia, poi diventa fixed

```css
.relativo {
  position: relative;
  top: 20px;
  left: 30px;
}

.assoluto {
  position: absolute;
  top: 0;
  right: 0;
}
```

**Perché è importante**: Il posizionamento ti permette di creare layout complessi e di posizionare elementi con precisione.

## Flexbox

Flexbox è un modello di layout unidimensionale progettato per organizzare elementi in riga o colonna.

### Proprietà del Container (Flex Container)
```css
.container {
  display: flex;
  flex-direction: row | row-reverse | column | column-reverse;
  flex-wrap: nowrap | wrap | wrap-reverse;
  justify-content: flex-start | flex-end | center | space-between | space-around | space-evenly;
  align-items: flex-start | flex-end | center | baseline | stretch;
  align-content: flex-start | flex-end | center | space-between | space-around | stretch;
}
```

### Proprietà degli Item (Flex Items)
```css
.item {
  order: 0; /* Default */
  flex-grow: 0; /* Default, non cresce */
  flex-shrink: 1; /* Default, può ridursi */
  flex-basis: auto; /* Default, dimensione iniziale */
  flex: 0 1 auto; /* Shorthand per grow, shrink, basis */
  align-self: auto | flex-start | flex-end | center | baseline | stretch;
}
```

**Perché è importante**: Flexbox semplifica enormemente la creazione di layout responsivi e di allineamenti che sarebbero complessi con i metodi tradizionali.

## Grid

CSS Grid è un sistema di layout bidimensionale che permette di organizzare elementi in righe e colonne.

### Proprietà del Container (Grid Container)
```css
.container {
  display: grid;
  grid-template-columns: 100px 200px 1fr;
  grid-template-rows: 50px auto 100px;
  grid-gap: 10px; /* o gap */
  grid-template-areas: 
    "header header header"
    "sidebar main main"
    "footer footer footer";
}
```

### Proprietà degli Item (Grid Items)
```css
.item {
  grid-column: 1 / 3; /* o grid-column-start e grid-column-end */
  grid-row: 1 / 2;
  grid-area: header; /* Corrisponde a un'area nominata */
}
```

**Perché è importante**: Grid ti permette di creare layout complessi e responsivi con meno codice e maggiore controllo rispetto ai metodi tradizionali.

## Responsive Design

### Media Queries
```css
/* Per schermi con larghezza massima di 768px */
@media (max-width: 768px) {
  .container {
    flex-direction: column;
  }
}

/* Per schermi con larghezza minima di 1200px */
@media (min-width: 1200px) {
  .container {
    max-width: 1140px;
  }
}
```

### Unità di Misura Responsive
- `%`: Percentuale relativa all'elemento genitore
- `vw`/`vh`: 1% della larghezza/altezza della viewport
- `em`: Relativa alla dimensione del font dell'elemento
- `rem`: Relativa alla dimensione del font dell'elemento root (html)

```css
.responsive-text {
  font-size: 1.2rem; /* Scala con le impostazioni dell'utente */
  margin: 1em; /* Proporzionale alla dimensione del testo */
  width: 80%; /* Percentuale della larghezza del genitore */
  max-width: 90vw; /* 90% della larghezza della viewport */
}
```

**Perché è importante**: Il responsive design è essenziale per creare siti web che funzionino bene su dispositivi di diverse dimensioni.

## Selettori Avanzati

### Selettori di Relazione
- `div p`: Seleziona tutti i `p` discendenti di `div`
- `div > p`: Seleziona tutti i `p` figli diretti di `div`
- `div + p`: Seleziona il primo `p` che segue immediatamente un `div`
- `div ~ p`: Seleziona tutti i `p` che seguono un `div`

### Selettori di Attributo
```css
[href] { /* Elementi con attributo href */}
[href="https://example.com"] { /* Valore esatto */}
[href^="https"] { /* Inizia con https */}
[href$=".pdf"] { /* Termina con .pdf */}
[href*="example"] { /* Contiene example */}
```

### Pseudo-classi strutturali
```css
:nth-child(odd) { /* Elementi dispari */}
:nth-child(even) { /* Elementi pari */}
:nth-child(3n+2) { /* Formula: ogni terzo elemento, partendo dal secondo */}
:first-of-type { /* Primo elemento di un tipo */}
:last-of-type { /* Ultimo elemento di un tipo */}
:empty { /* Elementi senza figli */}
```

**Perché è importante**: I selettori avanzati permettono di selezionare elementi specifici senza aggiungere classi HTML extra.

## Proprietà Visuali

### Colori
```css
.elemento {
  color: #ff0000; /* Hex */
  background-color: rgb(0, 255, 0); /* RGB */
  border-color: rgba(0, 0, 255, 0.5); /* RGBA con trasparenza */
  box-shadow: 0 0 10px hsl(0, 100%, 50%); /* HSL */
}
```

### Testo
```css
.testo {
  font-family: 'Arial', sans-serif;
  font-size: 16px;
  font-weight: bold;
  line-height: 1.5;
  text-align: center;
  text-decoration: underline;
  text-transform: uppercase;
  letter-spacing: 1px;
  word-spacing: 2px;
  text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.3);
}
```

### Bordi e Background
```css
.elemento {
  border: 1px solid black;
  border-radius: 5px;
  background-color: #f0f0f0;
  background-image: url('immagine.jpg');
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
}
```

**Perché è importante**: Le proprietà visuali definiscono l'aspetto estetico degli elementi, creando un'esperienza visiva coerente e attraente.

## Transizioni e Animazioni

### Transizioni
```css
.button {
  background-color: blue;
  transition: background-color 0.3s ease, transform 0.2s ease-in-out;
}

.button:hover {
  background-color: darkblue;
  transform: scale(1.05);
}
```

### Animazioni
```css
@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.elemento {
  animation: fadeIn 1s ease forwards;
}
```

**Perché è importante**: Transizioni e animazioni aggiungono dinamicità e feedback visivo, migliorando l'esperienza utente.

## Best Practices

### Organizzazione del Codice
- Segui una metodologia come BEM (Block Element Modifier)
- Organizza il CSS per componenti
- Usa variabili CSS per valori ricorrenti

```css
:root {
  --primary-color: #3498db;
  --secondary-color: #2ecc71;
  --text-color: #333;
  --spacing-unit: 8px;
}

.button {
  background-color: var(--primary-color);
  color: white;
  padding: calc(var(--spacing-unit) * 2);
}
```

### Performance
- Limita l'uso di selettori complessi
- Evita proprietà che causano reflow (come `width`, `height`, `top`)
- Preferisci `transform` e `opacity` per le animazioni

### Manutenibilità
- Commenta il codice CSS complesso
- Utilizza fogli di stile modulari
- Minimizza la specificità ove possibile
- Evita selettori universali (`*`) e regole troppo generiche

**Perché è importante**: Seguire le best practices rende il codice più performante, manutenibile e scalabile nel tempo.
