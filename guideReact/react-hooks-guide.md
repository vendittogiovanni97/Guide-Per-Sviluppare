# Guida Completa React Hooks
## useState, useRef e useEffect: Teoria ed Esempi Pratici

## Indice
- [useState](#usestate)
- [useRef](#useref)
- [useEffect](#useeffect)

## useState

### Cos'è useState?
`useState` è un Hook fondamentale di React che permette di aggiungere lo state ai componenti funzionali. Prima dell'introduzione degli Hooks, lo state poteva essere utilizzato solo nei componenti classe.

### Sintassi Base
```javascript
const [state, setState] = useState(initialValue);
```

- `state`: il valore corrente dello state
- `setState`: la funzione per aggiornare lo state
- `initialValue`: il valore iniziale dello state

### Quando Usarlo?
- Quando hai bisogno di gestire dati che cambiano nel tempo
- Per gestire input form
- Per toggle di stati (es: mostrare/nascondere elementi)
- Per contatori e altri valori dinamici

### Esempio Pratico Commentato
```javascript
import React, { useState } from 'react';

function Contatore() {
  // Inizializziamo lo state 'count' a 0
  const [count, setCount] = useState(0);
  
  // Gestiamo anche un input text come esempio
  const [nome, setNome] = useState('');

  return (
    <div>
      {/* Utilizzo base per un contatore */}
      <p>Conteggio: {count}</p>
      <button onClick={() => setCount(count + 1)}>
        Incrementa
      </button>

      {/* Esempio con form input */}
      <input 
        value={nome}
        onChange={(e) => setNome(e.target.value)}
        placeholder="Inserisci il tuo nome"
      />
      <p>Nome inserito: {nome}</p>
    </div>
  );
}
```

### Best Practices
1. Usa nomi descrittivi per le variabili state
2. Separa logicamente gli stati diversi
3. Non usare useState per dati che non triggherano re-render
4. Usa la versione funzionale di setState quando il nuovo stato dipende dal precedente:
```javascript
setCount(prevCount => prevCount + 1)
```

## useRef

### Cos'è useRef?
`useRef` è un Hook che permette di:
1. Mantenere valori mutabili che non causano re-render
2. Accedere direttamente a elementi DOM

### Sintassi Base
```javascript
const refContainer = useRef(initialValue);
```

### Quando Usarlo?
- Per accedere direttamente a elementi DOM
- Per mantenere valori che cambiano ma non devono causare re-render
- Per memorizzare valori precedenti
- Per gestire timer e intervalli

### Esempio Pratico Commentato
```javascript
import React, { useRef, useEffect } from 'react';

function InputFocus() {
  // Creiamo un ref per l'input
  const inputRef = useRef(null);
  
  // Creiamo un ref per un contatore che non causa re-render
  const clickCount = useRef(0);

  useEffect(() => {
    // Focus automatico sull'input al mount
    inputRef.current.focus();
  }, []);

  const handleClick = () => {
    // Incrementiamo il contatore senza causare re-render
    clickCount.current += 1;
    console.log(`Clicks: ${clickCount.current}`);
  };

  return (
    <div>
      <input 
        ref={inputRef}
        type="text" 
        placeholder="Focus automatico"
      />
      <button onClick={handleClick}>
        Conta Click
      </button>
    </div>
  );
}
```

### Best Practices
1. Non abusare di useRef per aggirare il sistema di rendering di React
2. Usalo principalmente per accessi DOM necessari
3. Documenta bene l'uso di ref mutabili nel codice
4. Evita di modificare .current durante il rendering

## useEffect

### Cos'è useEffect?
`useEffect` è un Hook che permette di eseguire effetti collaterali nei componenti funzionali. È l'equivalente di diversi metodi lifecycle dei componenti classe (componentDidMount, componentDidUpdate, componentWillUnmount).

### Sintassi Base
```javascript
useEffect(() => {
  // Effetto da eseguire
  return () => {
    // Cleanup (opzionale)
  };
}, [dependencies]); // Array delle dipendenze
```

### Quando Usarlo?
1. Per operazioni dopo il rendering:
   - Chiamate API
   - Sottoscrizioni a servizi
   - Manipolazioni DOM
2. Per pulizia prima dell'unmounting
3. Per reazioni a cambiamenti di props o state

### Esempio Pratico Commentato
```javascript
import React, { useState, useEffect } from 'react';

function DataFetcher() {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    // Dichiariamo una funzione asincrona per il fetch
    const fetchData = async () => {
      try {
        // Simuliamo una chiamata API
        const response = await fetch('https://api.example.com/data');
        const json = await response.json();
        setData(json);
        setLoading(false);
      } catch (err) {
        setError(err.message);
        setLoading(false);
      }
    };

    fetchData();

    // Cleanup function
    return () => {
      // Pulizia (es: annullare richieste pendenti)
      setData(null);
    };
  }, []); // Array vuoto = esegui solo al mount

  // Esempio di effect con dipendenze
  useEffect(() => {
    document.title = `Dati: ${data ? 'Caricati' : 'In caricamento'}`;
  }, [data]); // Si riesegue quando data cambia

  if (loading) return <div>Caricamento...</div>;
  if (error) return <div>Errore: {error}</div>;
  return <div>Dati: {JSON.stringify(data)}</div>;
}
```

### Best Practices
1. Separare gli effect per responsabilità
2. Includere tutte le dipendenze necessarie
3. Implementare sempre cleanup quando necessario
4. Evitare loop infiniti controllando le dipendenze
5. Usare ESLint plugin per hooks

## Confronto tra gli Hooks

### useState vs useRef
- **useState**: 
  - Causa re-render quando cambia
  - Mantiene i dati tra i render
  - Ideale per dati che influenzano l'UI
- **useRef**:
  - Non causa re-render
  - Mantiene i dati tra i render
  - Ideale per valori che non influenzano l'UI

### Quando Usare Quale?
1. **useState**:
   - Dati che devono essere visualizzati
   - Stati che influenzano il rendering
   - Form inputs
   - Toggle states

2. **useRef**:
   - Riferimenti DOM
   - Valori che cambiano frequentemente
   - Timer/Intervalli
   - Valori precedenti

3. **useEffect**:
   - Setup/Cleanup di sottoscrizioni
   - Fetch di dati
   - Manipolazioni DOM
   - Logging
   - Integrazioni con librerie esterne

## Conclusione
Gli Hooks sono strumenti potenti che permettono di gestire lo stato e gli effetti collaterali nei componenti funzionali React. La chiave per usarli efficacemente è:

1. Comprendere il loro scopo specifico
2. Seguire le best practices
3. Scegliere l'Hook giusto per ogni caso d'uso
4. Mantenere il codice pulito e organizzato

## Risorse Aggiuntive
- [React Hooks Documentation](https://reactjs.org/docs/hooks-intro.html)
- [React useEffect Documentation](https://reactjs.org/docs/hooks-effect.html)
- [React useRef Documentation](https://reactjs.org/docs/hooks-reference.html#useref)
