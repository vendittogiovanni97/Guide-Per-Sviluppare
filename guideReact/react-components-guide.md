# Guida Completa React Components e Props
## Pattern, Best Practices e Tecniche Avanzate

## Indice
- [Tipologie di Componenti](#tipologie-di-componenti)
- [Props e Loro Gestione](#props-e-loro-gestione)
- [Component Composition](#component-composition)
- [Pattern Avanzati](#pattern-avanzati)
- [Performance Optimization](#performance-optimization)
- [Best Practices](#best-practices)

## Tipologie di Componenti

### 1. Functional Components
```javascript
// Componente Base
function SimpleComponent() {
  return <div>Hello World</div>;
}

// Con Props
function GreetingComponent({ name, role = 'User' }) {
  return (
    <div>
      Hello, {name}! 
      You are a {role}
    </div>
  );
}

// Con Children
function Container({ children, className }) {
  return (
    <div className={`container ${className}`}>
      {children}
    </div>
  );
}
```

### 2. Componenti Specializzati

```javascript
// Presentational Component
function UserCard({ user }) {
  return (
    <div className="card">
      <img src={user.avatar} alt={user.name} />
      <h3>{user.name}</h3>
      <p>{user.bio}</p>
    </div>
  );
}

// Container Component
function UserCardContainer({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetchUser(userId)
      .then(data => {
        setUser(data);
        setLoading(false);
      });
  }, [userId]);

  if (loading) return <div>Loading...</div>;
  return <UserCard user={user} />;
}

// Layout Component
function PageLayout({ header, sidebar, main, footer }) {
  return (
    <div className="layout">
      <header>{header}</header>
      <div className="content">
        <aside>{sidebar}</aside>
        <main>{main}</main>
      </div>
      <footer>{footer}</footer>
    </div>
  );
}
```

## Props e Loro Gestione

### 1. Props Destructuring e Default Values
```javascript
function UserProfile({
  name,
  age = 25,
  role = 'User',
  isAdmin = false,
  onProfileUpdate,
  children
}) {
  return (
    <div className="profile">
      <h2>{name}</h2>
      <p>Age: {age}</p>
      <p>Role: {role}</p>
      {isAdmin && <AdminControls />}
      {children}
      <button onClick={onProfileUpdate}>Update Profile</button>
    </div>
  );
}
```

### 2. Props Validation
```javascript
import PropTypes from 'prop-types';

function ProductCard({ 
  name, 
  price, 
  description, 
  inStock 
}) {
  return (
    <div className="product-card">
      <h3>{name}</h3>
      <p>{description}</p>
      <p>Price: ${price}</p>
      <p>Status: {inStock ? 'In Stock' : 'Out of Stock'}</p>
    </div>
  );
}

ProductCard.propTypes = {
  name: PropTypes.string.isRequired,
  price: PropTypes.number.isRequired,
  description: PropTypes.string,
  inStock: PropTypes.bool.isRequired
};

ProductCard.defaultProps = {
  description: 'No description available',
  inStock: false
};
```

### 3. Props Spreading e Forwarding
```javascript
// Props Spreading
function Button({ className, ...props }) {
  return (
    <button 
      className={`btn ${className}`}
      {...props}
    />
  );
}

// Props Forwarding
const Input = React.forwardRef((props, ref) => {
  return <input ref={ref} {...props} />;
});
```

## Component Composition

### 1. Composition vs Inheritance
```javascript
// ❌ Using Inheritance
class SpecialButton extends Button {
  render() {
    return <button className="special">{this.props.children}</button>;
  }
}

// ✅ Using Composition
function SpecialButton({ children, ...props }) {
  return (
    <Button className="special" {...props}>
      {children}
    </Button>
  );
}
```

### 2. Compound Components
```javascript
function Tabs({ children }) {
  const [activeTab, setActiveTab] = useState(0);

  return (
    <TabsContext.Provider value={{ activeTab, setActiveTab }}>
      {children}
    </TabsContext.Provider>
  );
}

function TabList({ children }) {
  return <div className="tab-list">{children}</div>;
}

function Tab({ index, children }) {
  const { activeTab, setActiveTab } = useContext(TabsContext);
  
  return (
    <button 
      className={activeTab === index ? 'active' : ''}
      onClick={() => setActiveTab(index)}
    >
      {children}
    </button>
  );
}

function TabPanel({ index, children }) {
  const { activeTab } = useContext(TabsContext);
  
  if (activeTab !== index) return null;
  return <div className="tab-panel">{children}</div>;
}

// Usage
function App() {
  return (
    <Tabs>
      <TabList>
        <Tab index={0}>Tab 1</Tab>
        <Tab index={1}>Tab 2</Tab>
      </TabList>
      <TabPanel index={0}>Content 1</TabPanel>
      <TabPanel index={1}>Content 2</TabPanel>
    </Tabs>
  );
}
```

### 3. Render Props
```javascript
function Toggle({ render }) {
  const [on, setOn] = useState(false);

  return render({
    on,
    toggle: () => setOn(!on)
  });
}

// Usage
function App() {
  return (
    <Toggle
      render={({ on, toggle }) => (
        <div>
          <button onClick={toggle}>
            {on ? 'ON' : 'OFF'}
          </button>
          {on && <div>Content visible when ON</div>}
        </div>
      )}
    />
  );
}
```

## Pattern Avanzati

### 1. Higher-Order Components (HOC)
```javascript
// HOC per aggiungere funzionalità di loading
function withLoading(WrappedComponent) {
  return function WithLoadingComponent({ isLoading, ...props }) {
    if (isLoading) return <div>Loading...</div>;
    return <WrappedComponent {...props} />;
  };
}

// Usage
const UserListWithLoading = withLoading(UserList);

function App() {
  return <UserListWithLoading isLoading={true} users={[]} />;
}
```

### 2. Custom Hooks Pattern
```javascript
function useFormInput(initialValue) {
  const [value, setValue] = useState(initialValue);
  
  const handleChange = (e) => {
    setValue(e.target.value);
  };

  return {
    value,
    onChange: handleChange
  };
}

// Usage
function LoginForm() {
  const username = useFormInput('');
  const password = useFormInput('');

  return (
    <form>
      <input type="text" {...username} />
      <input type="password" {...password} />
    </form>
  );
}
```

### 3. Context + Reducer Pattern
```javascript
// Context
const TodoContext = React.createContext();

// Reducer
function todoReducer(state, action) {
  switch (action.type) {
    case 'ADD_TODO':
      return [...state, action.payload];
    case 'REMOVE_TODO':
      return state.filter(todo => todo.id !== action.payload);
    default:
      return state;
  }
}

// Provider Component
function TodoProvider({ children }) {
  const [todos, dispatch] = useReducer(todoReducer, []);

  return (
    <TodoContext.Provider value={{ todos, dispatch }}>
      {children}
    </TodoContext.Provider>
  );
}

// Consumer Component
function TodoList() {
  const { todos, dispatch } = useContext(TodoContext);

  return (
    <ul>
      {todos.map(todo => (
        <li key={todo.id}>
          {todo.text}
          <button
            onClick={() => dispatch({
              type: 'REMOVE_TODO',
              payload: todo.id
            })}
          >
            Delete
          </button>
        </li>
      ))}
    </ul>
  );
}
```

## Performance Optimization

### 1. React.memo
```javascript
const MemoizedComponent = React.memo(function MyComponent(props) {
  return (
    <div>
      {/* Expensive rendering */}
    </div>
  );
});

// Con custom comparison
const MemoizedCustomComponent = React.memo(
  function MyComponent(props) {
    return <div>{props.value}</div>;
  },
  (prevProps, nextProps) => {
    return prevProps.value === nextProps.value;
  }
);
```

### 2. useMemo e useCallback
```javascript
function ExpensiveComponent({ data, onItemSelect }) {
  // Memoize expensive calculations
  const processedData = useMemo(() => {
    return data.map(item => expensiveProcess(item));
  }, [data]);

  // Memoize callbacks
  const handleSelect = useCallback((item) => {
    onItemSelect(item.id);
  }, [onItemSelect]);

  return (
    <div>
      {processedData.map(item => (
        <div key={item.id} onClick={() => handleSelect(item)}>
          {item.name}
        </div>
      ))}
    </div>
  );
}
```

### 3. Lazy Loading
```javascript
const LazyComponent = React.lazy(() => import('./LazyComponent'));

function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <LazyComponent />
    </Suspense>
  );
}
```

## Best Practices

### 1. Componenti Riutilizzabili
```javascript
// ✅ Componente generico e riutilizzabile
function Card({
  title,
  subtitle,
  image,
  children,
  className,
  onClick
}) {
  return (
    <div 
      className={`card ${className}`}
      onClick={onClick}
    >
      {image && (
        <img src={image} alt={title} className="card-image" />
      )}
      <div className="card-content">
        {title && <h3 className="card-title">{title}</h3>}
        {subtitle && <p className="card-subtitle">{subtitle}</p>}
        {children}
      </div>
    </div>
  );
}
```

### 2. Separazione delle Responsabilità
```javascript
// ✅ Componente di presentazione
function UserProfile({ user, onEdit }) {
  return (
    <div className="profile">
      <h2>{user.name}</h2>
      <p>{user.bio}</p>
      <button onClick={onEdit}>Edit Profile</button>
    </div>
  );
}

// ✅ Componente container
function UserProfileContainer({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    fetchUser(userId).then(setUser);
  }, [userId]);

  const handleEdit = async (updates) => {
    await updateUser(userId, updates);
    setUser(prev => ({ ...prev, ...updates }));
  };

  if (!user) return <div>Loading...</div>;

  return <UserProfile user={user} onEdit={handleEdit} />;
}
```

### 3. Error Boundaries
```javascript
class ErrorBoundary extends React.Component {
  state = { hasError: false, error: null };

  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }

  componentDidCatch(error, errorInfo) {
    logErrorToService(error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return <div>Something went wrong!</div>;
    }

    return this.props.children;
  }
}

// Usage
function App() {
  return (
    <ErrorBoundary>
      <MyComponent />
    </ErrorBoundary>
  );
}
```

### 4. Testing
```javascript
import { render, fireEvent, screen } from '@testing-library/react';

// Test Component
function Counter() {
  const [count, setCount] = useState(0);
  
  return (
    <div>
      <span>Count: {count}</span>
      <button onClick={() => setCount(c => c + 1)}>
        Increment
      </button>
    </div>
  );
}

// Tests
describe('Counter', () => {
  test('renders initial count', () => {
    render(<Counter />);
    expect(screen.getByText('Count: 0')).toBeInTheDocument();
  });

  test('increments count when clicked', () => {
    render(<Counter />);
    fireEvent.click(screen.getByText('Increment'));
    expect(screen.getByText('Count: 1')).toBeInTheDocument();
  });
});
```

## Conclusione

Per migliorare nell'uso di React, è fondamentale:

1. Comprendere e applicare i vari pattern dei componenti
2. Gestire correttamente le props e la loro validazione
3. Utilizzare la composition invece dell'inheritance
4. Implementare ottimizzazioni delle performance quando necessario
5. Seguire le best practices per la struttura dei componenti
6. Mantenere una chiara separazione delle responsabilità
7. Implementare test appropriati

Best Practices Chiave:
1. Mantieni i componenti piccoli e focalizzati
2. Usa la composizione per la riusabilità
3. Implementa la validazione delle props
4. Ottimizza le performance quando necessario
5. Segui il principio di responsabilità unica
6. Testa i tuoi componenti
7. Usa Error Boundaries per la gestione degli errori

