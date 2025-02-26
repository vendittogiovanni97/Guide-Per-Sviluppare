const focusInput = () => {
    inputRef.current?.focus();
  };

  // Video control
  const playVideo = () => {
    videoRef.current?.play();
  };

  // Form handling
  const resetForm = () => {
    formRef.current?.reset();
  };

  return (
    <div>
      <input ref={inputRef} type="text" />
      <button onClick={focusInput}>Focus Input</button>

      <video ref={videoRef} src="video.mp4" />
      <button onClick={playVideo}>Play Video</button>

      <form ref={formRef}>
        <input type="text" name="username" />
        <button type="button" onClick={resetForm}>
          Reset Form
        </button>
      </form>
    </div>
  );
}
```

### 2. Gestione Timer e Intervalli
```javascript
function TimerExample() {
  const timerRef = useRef(null);
  const intervalRef = useRef(null);
  const [count, setCount] = useState(0);

  const startTimer = () => {
    // Pulizia timer esistente
    if (timerRef.current) {
      clearTimeout(timerRef.current);
    }

    timerRef.current = setTimeout(() => {
      console.log('Timer completato!');
    }, 3000);
  };

  const startInterval = () => {
    if (intervalRef.current) {
      clearInterval(intervalRef.current);
    }

    intervalRef.current = setInterval(() => {
      setCount(c => c + 1);
    }, 1000);
  };

  // Pulizia al unmount
  useEffect(() => {
    return () => {
      if (timerRef.current) clearTimeout(timerRef.current);
      if (intervalRef.current) clearInterval(intervalRef.current);
    };
  }, []);

  return (
    <div>
      <button onClick={startTimer}>Start Timer</button>
      <button onClick={startInterval}>Start Interval</button>
      <p>Count: {count}</p>
    </div>
  );
}
```

### 3. Memorizzazione Valori Precedenti
```javascript
function PreviousValueExample() {
  const [count, setCount] = useState(0);
  const prevCountRef = useRef();

  useEffect(() => {
    prevCountRef.current = count;
  }, [count]);

  const increment = () => {
    setCount(c => c + 1);
  };

  return (
    <div>
      <p>Current: {count}</p>
      <p>Previous: {prevCountRef.current}</p>
      <button onClick={increment}>Increment</button>
    </div>
  );
}
```

## Pattern Avanzati

### 1. Callback Refs
```javascript
function CallbackRefExample() {
  const [dimensions, setDimensions] = useState({ width: 0, height: 0 });

  const measuredRef = useCallback(node => {
    if (node !== null) {
      setDimensions({
        width: node.getBoundingClientRect().width,
        height: node.getBoundingClientRect().height
      });
    }
  }, []);

  return (
    <div>
      <div ref={measuredRef}>
        Measured Element
      </div>
      <p>Width: {dimensions.width}px</p>
      <p>Height: {dimensions.height}px</p>
    </div>
  );
}
```

### 2. Gestione Animazioni
```javascript
function AnimationExample() {
  const elementRef = useRef(null);
  const animationRef = useRef(null);

  const startAnimation = () => {
    let start;

    const animate = (timestamp) => {
      if (!start) start = timestamp;
      const progress = timestamp - start;

      // Animazione
      const element = elementRef.current;
      if (element) {
        element.style.transform = `translateX(${Math.min(progress / 10, 200)}px)`;
      }

      if (progress < 2000) {
        animationRef.current = requestAnimationFrame(animate);
      }
    };

    animationRef.current = requestAnimationFrame(animate);
  };

  const stopAnimation = () => {
    if (animationRef.current) {
      cancelAnimationFrame(animationRef.current);
    }
  };

  useEffect(() => {
    return () => {
      if (animationRef.current) {
        cancelAnimationFrame(animationRef.current);
      }
    };
  }, []);

  return (
    <div>
      <div ref={elementRef} style={{ width: 50, height: 50, background: 'red' }} />
      <button onClick={startAnimation}>Start Animation</button>
      <button onClick={stopAnimation}>Stop Animation</button>
    </div>
  );
}
```

## Integrazione con Altri Hooks

### 1. useEffect e useRef
```javascript
function IntegrationExample() {
  const [isRunning, setIsRunning] = useState(false);
  const countRef = useRef(0);
  const intervalRef = useRef(null);

  useEffect(() => {
    if (isRunning) {
      intervalRef.current = setInterval(() => {
        countRef.current += 1;
        console.log(`Count: ${countRef.current}`);
      }, 1000);
    }

    return () => {
      if (intervalRef.current) {
        clearInterval(intervalRef.current);
      }
    };
  }, [isRunning]);

  return (
    <button onClick={() => setIsRunning(!isRunning)}>
      {isRunning ? 'Stop' : 'Start'}
    </button>
  );
}
```

### 2. useState e useRef Insieme
```javascript
function CombinedExample() {
  const [count, setCount] = useState(0);
  const lastUpdateTime = useRef(Date.now());

  const incrementWithTimestamp = () => {
    const now = Date.now();
    const timeSinceLastUpdate = now - lastUpdateTime.current;
    
    setCount(c => c + 1);
    console.log(`Time since last update: ${timeSinceLastUpdate}ms`);
    
    lastUpdateTime.current = now;
  };

  return (
    <button onClick={incrementWithTimestamp}>
      Increment ({count})
    </button>
  );
}
```

## Best Practices

### 1. Quando Usare useRef vs useState
```javascript
function BestPracticesExample() {
  // ✅ Use useState for render-triggering values
  const [visible, setVisible] = useState(false);

  // ✅ Use useRef for values that shouldn't trigger renders
  const mountTimeRef = useRef(Date.now());
  const clickCountRef = useRef(0);

  // ✅ Use useRef for DOM elements
  const buttonRef = useRef(null);

  return (
    <div>
      <button
        ref={buttonRef}
        onClick={() => {
          clickCountRef.current += 1;
          setVisible(!visible);
        }}
      >
        Toggle
      </button>
    </div>
  );
}
```

### 2. Pulizia e Gestione delle Risorse
```javascript
function CleanupExample() {
  const resourceRef = useRef(null);

  useEffect(() => {
    // Inizializzazione risorsa
    resourceRef.current = initializeResource();

    return () => {
      // Pulizia risorsa
      if (resourceRef.current) {
        resourceRef.current.cleanup();
        resourceRef.current = null;
      }
    };
  }, []);

  return <div>Resource Management Example</div>;
}
```

## Problemi Comuni e Soluzioni

### 1. Accesso al DOM Troppo Presto
```javascript
function DOMAccessSolution() {
  const elementRef = useRef(null);

  // ❌ Potrebbe non funzionare
  const incorrectAccess = () => {
    elementRef.current.focus();
  };

  // ✅ Accesso sicuro
  const correctAccess = () => {
    if (elementRef.current) {
      elementRef.current.focus();
    }
  };

  return <input ref={elementRef} />;
}
```

### 2. Gestione Eventi con Ref
```javascript
function EventHandlingExample() {
  const eventRef = useRef(null);

  // ✅ Gestione sicura degli eventi
  const handleEvent = useCallback(() => {
    if (eventRef.current) {
      // Gestione evento
    }
  }, []);

  useEffect(() => {
    const currentRef = eventRef.current;

    if (currentRef) {
      currentRef.addEventListener('custom-event', handleEvent);
    }

    return () => {
      if (currentRef) {
        currentRef.removeEventListener('custom-event', handleEvent);
      }
    };
  }, [handleEvent]);

  return <div ref={eventRef}>Event Target</div>;
}
```

## Conclusione

useRef è uno strumento potente per:
1. Accesso diretto al DOM
2. Mantenimento di valori tra i render
3. Gestione di timer e animazioni
4. Memorizzazione di valori precedenti

Best Practices Chiave:
1. Usa useRef per valori che non devono causare re-render
2. Implementa sempre controlli di sicurezza
3. Gestisci correttamente la pulizia delle risorse
4. Combina con altri hooks quando necessario
5. Evita l'accesso diretto al DOM quando possibile

