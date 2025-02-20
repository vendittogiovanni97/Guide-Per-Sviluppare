# Guida Completa useEffect Hook
## Una guida approfondita all'uso di useEffect in React

## Indice
- [Introduzione](#introduzione)
- [Sintassi e Funzionamento](#sintassi-e-funzionamento)
- [Array delle Dipendenze](#array-delle-dipendenze)
- [Cleanup Function](#cleanup-function)
- [Casi d'Uso Comuni](#casi-duso-comuni)
- [Pattern Avanzati](#pattern-avanzati)
- [Gestione Errori](#gestione-errori)
- [Best Practices](#best-practices)

## Introduzione

### Cos'è useEffect?
useEffect è un Hook fondamentale di React che permette di eseguire "effetti collaterali" nei componenti funzionali. È l'equivalente di diversi metodi del ciclo di vita nei componenti classe:
- componentDidMount
- componentDidUpdate
- componentWillUnmount

### Quando Usare useEffect?
- Chiamate API
- Sottoscrizioni a servizi esterni
- Manipolazioni DOM
- Event listeners
- Animazioni
- Logging
- Sincronizzazione con sistemi esterni

## Sintassi e Funzionamento

### Struttura Base
```javascript
useEffect(() => {
  // Codice dell'effetto
  
  return () => {
    // Cleanup (opzionale)
  };
}, [dependencies]); // Array delle dipendenze
```

### Timing di Esecuzione
```javascript
function TimingExample() {
  const [count, setCount] = useState(0);

  // Esegue ad ogni render
  useEffect(() => {
    console.log('Effetto eseguito');
  });

  // Esegue solo al mount
  useEffect(() => {
    console.log('Componente montato');
  }, []);

  // Esegue quando count cambia
  useEffect(() => {
    console.log('Count aggiornato:', count);
  }, [count]);

  return <button onClick={() => setCount(c => c + 1)}>Count: {count}</button>;
}
```

## Array delle Dipendenze

### 1. Dipendenze Vuote
```javascript
function MountEffect() {
  useEffect(() => {
    // Eseguito solo al mount
    console.log('Componente montato');

    return () => {
      // Eseguito solo all'unmount
      console.log('Componente smontato');
    };
  }, []); // Array vuoto = solo mount/unmount

  return <div>Mount Example</div>;
}
```

### 2. Dipendenze Multiple
```javascript
function MultiDependencies() {
  const [user, setUser] = useState(null);
  const [settings, setSettings] = useState({});

  useEffect(() => {
    // Eseguito quando user o settings cambiano
    console.log('User o settings aggiornati');
    updateUserPreferences(user, settings);
  }, [user, settings]); // Multiple dipendenze

  return <div>Dependencies Example</div>;
}
```

### 3. Gestione Dipendenze Oggetti
```javascript
function ObjectDependencies() {
  const [config, setConfig] = useState({ theme: 'light', lang: 'it' });

  // ❌ Potrebbe causare loop infiniti
  useEffect(() => {
    console.log('Config changed');
  }, [config]); // L'oggetto è sempre nuovo

  // ✅ Dipendenze corrette
  useEffect(() => {
    console.log('Config changed');
  }, [config.theme, config.lang]); // Dipendenze primitive

  return <div>Object Dependencies Example</div>;
}
```

## Cleanup Function

### 1. Event Listeners
```javascript
function EventListenerExample() {
  useEffect(() => {
    const handleResize = () => {
      console.log('Window resized');
    };

    window.addEventListener('resize', handleResize);

    // Cleanup: rimuove l'event listener
    return () => {
      window.removeEventListener('resize', handleResize);
    };
  }, []);

  return <div>Window Resize Listener</div>;
}
```

### 2. Subscriptions
```javascript
function SubscriptionExample() {
  const [data, setData] = useState(null);

  useEffect(() => {
    const subscription = dataSource.subscribe(
      newData => setData(newData)
    );

    // Cleanup: cancella la sottoscrizione
    return () => {
      subscription.unsubscribe();
    };
  }, []);

  return <div>Data: {JSON.stringify(data)}</div>;
}
```

### 3. Timers e Intervals
```javascript
function TimerExample() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const timer = setInterval(() => {
      setCount(c => c + 1);
    }, 1000);

    // Cleanup: cancella il timer
    return () => {
      clearInterval(timer);
    };
  }, []);

  return <div>Count: {count}</div>;
}
```

## Casi d'Uso Comuni

### 1. Fetch Data
```javascript
function DataFetching() {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    let isMounted = true;

    const fetchData = async () => {
      try {
        setLoading(true);
        const response = await fetch('https://api.example.com/data');
        const json = await response.json();
        
        if (isMounted) {
          setData(json);
          setError(null);
        }
      } catch (err) {
        if (isMounted) {
          setError(err.message);
          setData(null);
        }
      } finally {
        if (isMounted) {
          setLoading(false);
        }
      }
    };

    fetchData();

    return () => {
      isMounted = false;
    };
  }, []);

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;
  return <div>Data: {JSON.stringify(data)}</div>;
}
```

### 2. Form Validation
```javascript
function FormValidation() {
  const [email, setEmail] = useState('');
  const [isValid, setIsValid] = useState(false);
  const [errorMessage, setErrorMessage] = useState('');

  useEffect(() => {
    const timeoutId = setTimeout(() => {
      const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
      const valid = emailRegex.test(email);
      
      setIsValid(valid);
      setErrorMessage(valid ? '' : 'Email non valida');
    }, 500); // Debounce

    return () => {
      clearTimeout(timeoutId);
    };
  }, [email]);

  return (
    <div>
      <input
        type="email"
        value={email}
        onChange={e => setEmail(e.target.value)}
        className={isValid ? 'valid' : 'invalid'}
      />
      {errorMessage && <p>{errorMessage}</p>}
    </div>
  );
}
```

### 3. Sincronizzazione con localStorage
```javascript
function LocalStorageSync() {
  const [theme, setTheme] = useState(() => {
    return localStorage.getItem('theme') || 'light';
  });

  useEffect(() => {
    localStorage.setItem('theme', theme);
  }, [theme]);

  return (
    <button onClick={() => setTheme(t => t === 'light' ? 'dark' : 'light')}>
      Toggle Theme
    </button>
  );
}
```

## Pattern Avanzati

### 1. Debouncing e Throttling
```javascript
function SearchComponent() {
  const [query, setQuery] = useState('');
  const [results, setResults] = useState([]);

  // Debounced search
  useEffect(() => {
    const debounceTimeout = setTimeout(async () => {
      if (query.trim()) {
        const searchResults = await performSearch(query);
        setResults(searchResults);
      }
    }, 300);

    return () => {
      clearTimeout(debounceTimeout);
    };
  }, [query]);

  return (
    <div>
      <input
        value={query}
        onChange={e => setQuery(e.target.value)}
        placeholder="Search..."
      />
      <ul>
        {results.map(result => (
          <li key={result.id}>{result.title}</li>
        ))}
      </ul>
    </div>
  );
}
```

### 2. Polling
```javascript
function PollingComponent() {
  const [data, setData] = useState(null);
  const [isPolling, setIsPolling] = useState(true);

  useEffect(() => {
    if (!isPolling) return;

    const pollData = async () => {
      try {
        const response = await fetch('https://api.example.com/data');
        const newData = await response.json();
        setData(newData);
      } catch (error) {
        console.error('Polling error:', error);
      }
    };

    const intervalId = setInterval(pollData, 5000);

    return () => {
      clearInterval(intervalId);
    };
  }, [isPolling]);

  return (
    <div>
      <button onClick={() => setIsPolling(!isPolling)}>
        {isPolling ? 'Stop Polling' : 'Start Polling'}
      </button>
      <div>Data: {JSON.stringify(data)}</div>
    </div>
  );
}
```

### 3. Gestione Websocket
```javascript
function WebSocketComponent() {
  const [messages, setMessages] = useState([]);
  const [connected, setConnected] = useState(false);

  useEffect(() => {
    const ws = new WebSocket('ws://example.com');

    ws.onopen = () => {
      setConnected(true);
    };

    ws.onmessage = (event) => {
      setMessages(prev => [...prev, event.data]);
    };

    ws.onclose = () => {
      setConnected(false);
    };

    return () => {
      ws.close();
    };
  }, []);

  return (
    <div>
      <div>Status: {connected ? 'Connected' : 'Disconnected'}</div>
      <ul>
        {messages.map((msg, index) => (
          <li key={index}>{msg}</li>
        ))}
      </ul>
    </div>
  );
}
```

## Gestione Errori

### 1. Error Boundaries per useEffect
```javascript
function ErrorBoundaryExample() {
  const [error, setError] = useState(null);

  useEffect(() => {
    try {
      // Codice che potrebbe generare errori
      riskyOperation();
    } catch (err) {
      setError(err);
      // Log dell'errore
      logError(err);
    }
  }, []);

  if (error) {
    return <div>Si è verificato un errore: {error.message}</div>;
  }

  return <div>Component Content</div>;
}
```

### 2. Retry Logic
```javascript
function RetryExample() {
  const [data, setData] = useState(null);
  const [error, setError] = useState(null);
  const [retryCount, setRetryCount] = useState(0);

  useEffect(() => {
    const fetchWithRetry = async () => {
      try {
        const response = await fetch('https://api.example.com/data');
        const json = await response.json();
        setData(json);
        setError(null);
      } catch (err) {
        setError(err);
        if (retryCount < 3) {
          setTimeout(() => {
            setRetryCount(c => c + 1);
          }, 1000 * Math.pow(2, retryCount)); // Exponential backoff
        }
      }
    };

    fetchWithRetry();
  }, [retryCount]);

  return (
    <div>
      {error && <div>Error: {error.message}</div>}
      {data && <div>Data: {JSON.stringify(data)}</div>}
    </div>
  );
}
```

## Best Practices

### 1. Separazione degli Effetti
```javascript
function WellOrganizedComponent() {
  // ✅ Effetti separati per responsabilità
  useEffect(() => {
    // Gestione autenticazione
  }, [user]);

  useEffect(() => {
    // Gestione tema
  }, [theme]);

  useEffect(() => {
    // Gestione notifiche
  }, [notifications]);

  return <div>Well Organized Component</div>;
}
```

### 2. Evitare Loop Infiniti
```javascript
function AvoidInfiniteLoops() {
  const [data, setData] = useState([]);

  // ❌ Potenziale loop infinito
  useEffect(() => {
    setData([...data, 'new item']);
  }, [data]);

  // ✅ Versione corretta
  useEffect(() => {
    setData(prev => [...prev, 'new item']);
  }, []); // No dependencies

  return <div>Items: {data.length}</div>;
}
```

### 3. Gestione delle Race Conditions
```javascript
function RaceConditionExample() {
  const [id, setId] = useState(1);
  const [data, setData] = useState(null);

  useEffect(() => {
    let isCurrent = true;

    async function fetchData() {
      const response = await fetch(`https://api.example.com/data/${id}`);
      const json = await response.json();
      
      if (isCurrent) {
        setData(json);
      }
    }

    fetchData();

    return () => {
      isCurrent = false;
    };
  }, [id]);

  return <div>Data for ID {id}: {JSON.stringify(data)}</div>;
}
```

## Conclusione

useEffect è uno strumento potente che richiede:
1. Comprensione approfondita delle dipendenze
2. Gestione appropriata della cleanup function
3. Attenzione alle race conditions
4. Separazione delle responsabilità
5. Gestione corretta degli errori

Best Practices Chiave:
1. Mantieni gli effetti piccoli e focalizzati
2. Usa sempre le cleanup functions quando necessario
3. Gestisci correttamente le dipendenze
4. Evita loop infiniti
5. Implementa gestione degli errori robusta

