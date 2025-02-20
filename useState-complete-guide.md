# Guida Completa useState Hook
## Una guida approfondita all'uso di useState in React

## Indice
- [Introduzione](#introduzione)
- [Sintassi e Funzionamento](#sintassi-e-funzionamento)
- [Casi d'Uso](#casi-duso)
- [Pattern Avanzati](#pattern-avanzati)
- [Gestione Errori Comuni](#gestione-errori-comuni)
- [Performance Optimization](#performance-optimization)
- [Best Practices](#best-practices)

## Introduzione

### Cos'è useState?
useState è uno dei Hook fondamentali di React che permette di aggiungere stato locale ai componenti funzionali. È la soluzione più semplice per gestire dati mutabili all'interno di un componente.

### Perché useState?
- Permette di gestire lo stato senza usare classi
- Rende i componenti più leggibili e mantenibili
- Facilita la logica di aggiornamento dello stato
- Integrazione perfetta con il sistema di rendering di React

## Sintassi e Funzionamento

### Sintassi Base
```javascript
const [state, setState] = useState(initialValue);
```

### Inizializzazione Lazy
```javascript
const [state, setState] = useState(() => {
  // Calcolo costoso o operazione complessa
  return heavyComputation();
});
```

### Tipi di Dati Supportati
```javascript
// Numeri
const [count, setCount] = useState(0);

// Stringhe
const [text, setText] = useState('');

// Booleani
const [isVisible, setIsVisible] = useState(false);

// Array
const [items, setItems] = useState([]);

// Oggetti
const [user, setUser] = useState({
  name: '',
  age: 0,
  email: ''
});

// Null o undefined
const [data, setData] = useState(null);
```

## Casi d'Uso

### 1. Form Management
```javascript
function RegistrationForm() {
  const [formData, setFormData] = useState({
    username: '',
    email: '',
    password: ''
  });

  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormData(prev => ({
      ...prev,
      [name]: value
    }));
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    // Gestione submit
    console.log(formData);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        name="username"
        value={formData.username}
        onChange={handleChange}
        placeholder="Username"
      />
      <input
        name="email"
        type="email"
        value={formData.email}
        onChange={handleChange}
        placeholder="Email"
      />
      <input
        name="password"
        type="password"
        value={formData.password}
        onChange={handleChange}
        placeholder="Password"
      />
      <button type="submit">Register</button>
    </form>
  );
}
```

### 2. Toggle Functionality
```javascript
function ToggleComponent() {
  const [isOn, setIsOn] = useState(false);
  const [settings, setSettings] = useState({
    notification: true,
    sound: false,
    darkMode: true
  });

  const toggleSetting = (setting) => {
    setSettings(prev => ({
      ...prev,
      [setting]: !prev[setting]
    }));
  };

  return (
    <div>
      <button onClick={() => setIsOn(!isOn)}>
        {isOn ? 'Turn Off' : 'Turn On'}
      </button>
      
      <div>
        {Object.entries(settings).map(([setting, value]) => (
          <label key={setting}>
            <input
              type="checkbox"
              checked={value}
              onChange={() => toggleSetting(setting)}
            />
            {setting}
          </label>
        ))}
      </div>
    </div>
  );
}
```

### 3. Lista Dinamica
```javascript
function DynamicList() {
  const [items, setItems] = useState([]);
  const [newItem, setNewItem] = useState('');

  const addItem = () => {
    if (!newItem.trim()) return;
    
    setItems(prev => [...prev, {
      id: Date.now(),
      text: newItem,
      completed: false
    }]);
    setNewItem('');
  };

  const removeItem = (id) => {
    setItems(prev => prev.filter(item => item.id !== id));
  };

  const toggleComplete = (id) => {
    setItems(prev => prev.map(item =>
      item.id === id
        ? { ...item, completed: !item.completed }
        : item
    ));
  };

  return (
    <div>
      <input
        value={newItem}
        onChange={(e) => setNewItem(e.target.value)}
        placeholder="Nuovo item"
      />
      <button onClick={addItem}>Aggiungi</button>

      <ul>
        {items.map(item => (
          <li key={item.id}>
            <input
              type="checkbox"
              checked={item.completed}
              onChange={() => toggleComplete(item.id)}
            />
            <span style={{
              textDecoration: item.completed ? 'line-through' : 'none'
            }}>
              {item.text}
            </span>
            <button onClick={() => removeItem(item.id)}>
              Elimina
            </button>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

## Pattern Avanzati

### 1. Gestione Stati Multipli Correlati
```javascript
function UserDashboard() {
  // Approccio 1: Stati separati
  const [username, setUsername] = useState('');
  const [email, setEmail] = useState('');
  const [preferences, setPreferences] = useState({});

  // Approccio 2: Stato oggetto unificato
  const [userData, setUserData] = useState({
    username: '',
    email: '',
    preferences: {}
  });

  // Updater function per modifiche specifiche
  const updateUserField = (field, value) => {
    setUserData(prev => ({
      ...prev,
      [field]: value
    }));
  };

  return (
    // Implementazione UI
  );
}
```

### 2. Stato Precedente e Computazioni
```javascript
function Counter() {
  const [count, setCount] = useState(0);

  // Incremento semplice
  const increment = () => {
    // ❌ Potenzialmente problematico con aggiornamenti multipli
    setCount(count + 1);
  };

  // Incremento sicuro usando lo stato precedente
  const safeIncrement = () => {
    // ✅ Sicuro per aggiornamenti multipli
    setCount(prev => prev + 1);
  };

  // Incremento con calcolo
  const incrementByCalc = () => {
    setCount(prev => {
      const newValue = prev + calculateIncrement(prev);
      return validateCount(newValue) ? newValue : prev;
    });
  };

  return (
    // Implementazione UI
  );
}
```

## Gestione Errori Comuni

### 1. Aggiornamenti Asincroni
```javascript
function AsyncStateExample() {
  const [data, setData] = useState(null);
  const [error, setError] = useState(null);
  const [loading, setLoading] = useState(false);

  const fetchData = async () => {
    setLoading(true);
    setError(null);
    
    try {
      const response = await fetch('api/data');
      const json = await response.json();
      setData(json);
    } catch (err) {
      setError(err.message);
    } finally {
      setLoading(false);
    }
  };

  return (
    <div>
      {loading && <p>Caricamento...</p>}
      {error && <p>Errore: {error}</p>}
      {data && <DisplayData data={data} />}
    </div>
  );
}
```

### 2. Aggiornamenti Batch
```javascript
function BatchUpdateExample() {
  const [count, setCount] = useState(0);
  const [total, setTotal] = useState(0);

  // ❌ Gli aggiornamenti potrebbero non essere immediati
  const incorrectUpdate = () => {
    setCount(count + 1);
    setTotal(count + 10); // Potrebbe usare il vecchio valore di count
  };

  // ✅ Gestione corretta degli aggiornamenti batch
  const correctUpdate = () => {
    setCount(prev => prev + 1);
    setTotal(prev => prev + 10);
  };

  return (
    // Implementazione UI
  );
}
```

## Performance Optimization

### 1. Memoizzazione dello Stato
```javascript
function OptimizedComponent() {
  // Per valori iniziali costosi
  const [expensiveState] = useState(() => {
    return heavyComputation();
  });

  // Per oggetti complessi
  const [complexState, setComplexState] = useState(() => ({
    data: processInitialData(),
    metadata: generateMetadata()
  }));

  return (
    // Implementazione UI
  );
}
```

### 2. Gestione Stati Grandi
```javascript
function LargeStateManager() {
  // Dividere stati grandi in parti più piccole
  const [pageInfo, setPageInfo] = useState({ current: 1, total: 10 });
  const [filters, setFilters] = useState({ category: '', search: '' });
  const [sortInfo, setSortInfo] = useState({ field: 'name', direction: 'asc' });

  // Updater functions specifiche
  const updateFilter = (filterName, value) => {
    setFilters(prev => ({
      ...prev,
      [filterName]: value
    }));
  };

  return (
    // Implementazione UI
  );
}
```

## Best Practices

### 1. Nominare gli Stati
- Usa nomi descrittivi per gli stati
- Usa prefissi come 'is', 'has' per booleani
- Usa plural/singular appropriatamente

```javascript
// ✅ Buoni nomi
const [isLoading, setIsLoading] = useState(false);
const [hasError, setHasError] = useState(false);
const [users, setUsers] = useState([]);
const [selectedUser, setSelectedUser] = useState(null);

// ❌ Nomi da evitare
const [data, setData] = useState([]);
const [flag, setFlag] = useState(false);
const [value, setValue] = useState('');
```

### 2. Organizzazione dello Stato
```javascript
function WellOrganizedComponent() {
  // Gruppa stati correlati
  const [formData, setFormData] = useState({
    name: '',
    email: '',
    password: ''
  });

  // Separa stati indipendenti
  const [isSubmitting, setIsSubmitting] = useState(false);
  const [showPassword, setShowPassword] = useState(false);

  // Gestisci validazione
  const [errors, setErrors] = useState({});

  return (
    // Implementazione UI
  );
}
```

### 3. Inizializzazione e Reset
```javascript
function StateResetExample() {
  const initialState = {
    user: null,
    preferences: {},
    settings: {
      theme: 'light',
      notifications: true
    }
  };

  const [appState, setAppState] = useState(initialState);

  // Reset completo
  const resetState = () => {
    setAppState(initialState);
  };

  // Reset parziale
  const resetUserOnly = () => {
    setAppState(prev => ({
      ...prev,
      user: null
    }));
  };

  return (
    // Implementazione UI
  );
}
```

### 4. Gestione Side Effects
```javascript
function StateWithEffects() {
  const [data, setData] = useState(null);

  // Gestione localStorage
  const saveToStorage = (newData) => {
    setData(newData);
    localStorage.setItem('data', JSON.stringify(newData));
  };

  // Gestione stato derivato
  const derivedValue = useMemo(() => {
    if (!data) return null;
    return processData(data);
  }, [data]);

  return (
    // Implementazione UI
  );
}
```

## Conclusione
useState è un Hook potente e flessibile che forma la base della gestione dello stato in React. La chiave per usarlo efficacemente sta nel:

1. Comprendere quando usare stati singoli vs oggetti
2. Gestire correttamente gli aggiornamenti asincroni
3. Ottimizzare le performance quando necessario
4. Seguire le best practices per la manutenibilità
5. Organizzare gli stati in modo logico e scalabile

