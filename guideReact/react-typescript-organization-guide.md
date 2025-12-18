# Guida all'Organizzazione dei Progetti React con TypeScript

## Indice
1. [Struttura delle Cartelle](#struttura-delle-cartelle)
2. [Tipizzazione in React con TypeScript](#tipizzazione-in-react-con-typescript)
3. [Best Practices e Consigli](#best-practices-e-consigli)

## Struttura delle Cartelle

### Principi di Base per Organizzare un Progetto React

La struttura delle cartelle in un progetto React non è solo una questione di ordine, ma influisce direttamente su:
- **Manutenibilità**: Quanto è facile trovare e modificare il codice
- **Scalabilità**: Come il progetto crescerà nel tempo
- **Collaborazione**: Quanto facilmente altri sviluppatori possono orientarsi
- **Riutilizzo**: Come facilitare il riuso dei componenti

### Approcci alla Struttura delle Cartelle

Esistono diversi approcci, ma i principali sono:

#### 1. Struttura per Tipo (Type-Based)

```
src/
├── components/
│   ├── Button.tsx
│   ├── Card.tsx
│   └── Modal.tsx
├── pages/
│   ├── Home.tsx
│   ├── About.tsx
│   └── Contact.tsx
├── hooks/
│   ├── useAuth.ts
│   └── useFetch.ts
├── contexts/
│   ├── AuthContext.tsx
│   └── ThemeContext.tsx
├── utils/
│   ├── formatting.ts
│   └── validation.ts
├── services/
│   ├── api.ts
│   └── storage.ts
└── types/
    ├── user.ts
    └── product.ts
```

**Pro**:
- Facile da capire inizialmente
- Chiara separazione tra tipi di file diversi

**Contro**:
- Con la crescita del progetto, le cartelle diventano troppo grandi
- Difficile capire quali componenti sono correlati

#### 2. Struttura per Caratteristica/Modulo (Feature-Based)

```
src/
├── features/
│   ├── auth/
│   │   ├── components/
│   │   │   ├── LoginForm.tsx
│   │   │   └── RegisterForm.tsx
│   │   ├── hooks/
│   │   │   └── useAuth.ts
│   │   ├── services/
│   │   │   └── authService.ts
│   │   ├── types/
│   │   │   └── auth.types.ts
│   │   └── authSlice.ts
│   ├── products/
│   │   └── ...
│   └── checkout/
│       └── ...
├── shared/
│   ├── components/
│   │   ├── Button.tsx
│   │   └── Card.tsx
│   ├── hooks/
│   │   └── useFetch.ts
│   └── utils/
│       └── formatting.ts
└── app/
    ├── layouts/
    │   └── MainLayout.tsx
    ├── routes.tsx
    └── store.ts
```

**Pro**:
- Migliore scalabilità
- Code colocation (codice correlato è vicino)
- Facilita il lavoro su funzionalità specifiche

**Contro**:
- Più complesso all'inizio
- Potenziale duplicazione di codice se non gestito correttamente

#### 3. Approccio Ibrido (Consigliato)

```
src/
├── components/
│   ├── common/
│   │   ├── Button/
│   │   │   ├── Button.tsx
│   │   │   ├── Button.test.tsx
│   │   │   └── Button.module.css
│   │   └── Card/
│   │       └── ...
│   ├── layouts/
│   │   └── MainLayout/
│   │       └── ...
│   └── features/
│       ├── auth/
│       │   ├── LoginForm/
│       │   │   └── ...
│       │   └── RegisterForm/
│       │       └── ...
│       └── products/
│           └── ...
├── pages/
│   ├── Home/
│   │   ├── Home.tsx
│   │   └── components/
│   │       └── HeroSection.tsx
│   └── About/
│       └── ...
├── hooks/
├── contexts/
├── services/
├── utils/
└── types/
```

**Pro**:
- Combina i vantaggi di entrambi gli approcci
- Permette una struttura più flessibile
- Facilita sia la navigazione che la colocation

**Contro**:
- Richiede più pianificazione e disciplina

### Considerazioni Importanti

1. **Coesione**: Files che cambiano insieme dovrebbero stare vicini
2. **Principio di Responsabilità Unica**: Ogni file/componente dovrebbe avere un solo motivo per cambiare
3. **Astrazione**: Componenti comuni vanno astratti e resi riutilizzabili
4. **Consistenza**: Qualunque struttura scegli, mantienila consistente

### Come Scegliere la Struttura Giusta

La scelta dipende da:
- **Dimensione del progetto**: Progetti piccoli possono funzionare bene con Type-Based
- **Complessità**: Progetti complessi beneficiano di Feature-Based
- **Team**: Team più grandi lavorano meglio con Feature-Based
- **Durata**: Progetti a lungo termine necessitano di una struttura più scalabile

### Struttura dei File dei Componenti

Per ogni componente significativo, considera una struttura del tipo:

```
ComponentName/
├── ComponentName.tsx    // Il componente React principale
├── ComponentName.test.tsx    // Test del componente
├── ComponentName.module.css  // Stili (o .scss)
├── types.ts             // Tipi/interfacce specifiche del componente
├── utils.ts             // Funzioni di utilità specifiche
└── index.ts             // File barrel per l'export
```

Il file index.ts permette import più puliti:

```typescript
// index.ts
export { default } from './ComponentName';
export * from './types';
```

## Tipizzazione in React con TypeScript

### Perché la Tipizzazione è Importante

1. **Prevenzione degli errori**: Cattura errori a tempo di compilazione
2. **Auto-documentazione**: Il codice spiega sé stesso
3. **Intellisense e autocompletamento**: Miglior supporto dell'IDE
4. **Refactoring più sicuro**: Modifiche con maggior sicurezza
5. **Contratti chiari**: Definisce chiaramente quali props un componente accetta

### Tipizzare i Componenti React

#### Componenti Funzionali

```typescript
// Definizione di un'interfaccia per le props
interface ButtonProps {
  text: string;
  onClick: () => void;
  variant?: 'primary' | 'secondary';
  disabled?: boolean;
  className?: string;
}

// Componente funzionale tipizzato
const Button: React.FC<ButtonProps> = ({ 
  text, 
  onClick, 
  variant = 'primary', 
  disabled = false,
  className = ''
}) => {
  return (
    <button
      className={`button ${variant} ${className}`}
      onClick={onClick}
      disabled={disabled}
    >
      {text}
    </button>
  );
};

export default Button;
```

#### Approccio Alternativo (senza React.FC)

```typescript
// Spesso preferito per maggior chiarezza sui children
interface ButtonProps {
  text: string;
  onClick: () => void;
  variant?: 'primary' | 'secondary';
  disabled?: boolean;
  className?: string;
  children?: React.ReactNode;
}

// Componente con tipo di ritorno esplicito
const Button = ({ 
  text, 
  onClick, 
  variant = 'primary', 
  disabled = false,
  className = '',
  children
}: ButtonProps): JSX.Element => {
  return (
    <button
      className={`button ${variant} ${className}`}
      onClick={onClick}
      disabled={disabled}
    >
      {text}
      {children}
    </button>
  );
};

export default Button;
```

### Tipizzazione dei Children

```typescript
interface CardProps {
  title: string;
  children: React.ReactNode;  // Può contenere qualsiasi elemento React valido
}

const Card = ({ title, children }: CardProps): JSX.Element => {
  return (
    <div className="card">
      <h2>{title}</h2>
      <div className="card-content">
        {children}
      </div>
    </div>
  );
};
```

### Tipizzazione degli Eventi

```typescript
interface InputProps {
  value: string;
  onChange: (event: React.ChangeEvent<HTMLInputElement>) => void;
  onBlur?: (event: React.FocusEvent<HTMLInputElement>) => void;
  onKeyDown?: (event: React.KeyboardEvent<HTMLInputElement>) => void;
}

const Input = ({ value, onChange, onBlur, onKeyDown }: InputProps): JSX.Element => {
  return (
    <input
      value={value}
      onChange={onChange}
      onBlur={onBlur}
      onKeyDown={onKeyDown}
    />
  );
};
```

### Utilizzo di Generics

```typescript
interface ListProps<T> {
  items: T[];
  renderItem: (item: T) => React.ReactNode;
}

// Il componente è generico e può accettare qualsiasi tipo di array
function List<T>({ items, renderItem }: ListProps<T>): JSX.Element {
  return (
    <ul>
      {items.map((item, index) => (
        <li key={index}>{renderItem(item)}</li>
      ))}
    </ul>
  );
}

// Utilizzo
interface User {
  id: number;
  name: string;
}

const UserList = () => {
  const users: User[] = [
    { id: 1, name: "Alice" },
    { id: 2, name: "Bob" }
  ];
  
  return (
    <List<User>
      items={users}
      renderItem={(user) => <span>{user.name}</span>}
    />
  );
};
```

### Tipizzare Hooks Personalizzati

```typescript
// Hook per gestire il form
function useForm<T extends Record<string, any>>(initialValues: T) {
  const [values, setValues] = useState<T>(initialValues);
  
  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const { name, value } = e.target;
    setValues(prev => ({ ...prev, [name]: value }));
  };
  
  const reset = () => {
    setValues(initialValues);
  };
  
  return { values, handleChange, reset };
}

// Utilizzo
const LoginForm = () => {
  const { values, handleChange, reset } = useForm({
    username: '',
    password: ''
  });
  
  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    console.log(values);
    reset();
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        name="username"
        value={values.username}
        onChange={handleChange}
      />
      <input
        type="password"
        name="password"
        value={values.password}
        onChange={handleChange}
      />
      <button type="submit">Login</button>
    </form>
  );
};
```

### Type vs Interface

```typescript
// Type (tipo)
type ButtonVariant = 'primary' | 'secondary' | 'tertiary';

type ButtonProps = {
  text: string;
  variant: ButtonVariant;
  onClick: () => void;
};

// Interface (interfaccia)
interface User {
  id: number;
  name: string;
  email: string;
}

interface AdminUser extends User {
  permissions: string[];
}
```

**Quando usare Type**:
- Per unioni o intersezioni: `type Result = Success | Error`
- Per tipi primitivi rinominati: `type ID = string`
- Per tuple: `type Coordinate = [number, number]`
- Quando serve mappare tipi esistenti

**Quando usare Interface**:
- Per definire la forma di un oggetto
- Quando prevedi di estendere l'interfaccia
- Per API pubbliche (più facilmente estendibili)

### Creare File di Tipi Dedicati

Per tipi/interfacce riutilizzabili, crea file dedicati:

```typescript
// types/user.ts
export interface User {
  id: number;
  name: string;
  email: string;
  role: 'admin' | 'user';
}

export interface UserProfile extends User {
  avatar: string;
  bio: string;
}
```

Poi importali dove necessario:

```typescript
// components/UserCard.tsx
import { User } from '../types/user';

interface UserCardProps {
  user: User;
}

const UserCard = ({ user }: UserCardProps): JSX.Element => {
  // ...
};
```

## Best Practices e Consigli

### Struttura del Progetto

1. **Inizia semplice e rifattorizza**: Non sovra-ingegnerizzare all'inizio
2. **Mantieni i componenti piccoli**: Componenti con singola responsabilità
3. **Raggruppa per contesto**: Files correlati dovrebbero stare vicini
4. **Usa "barrel files"** (index.ts) per semplificare gli import
5. **Documenta le decisioni architetturali**: Un file README.md per spiegare la struttura

### Tipizzazione

1. **Non usare `any`**: È una scappatoia che elimina i vantaggi di TypeScript
2. **Usa tipi espliciti**: Evita l'inferenza di tipo per le props dei componenti
3. **Centralizza i tipi comuni**: Crea file types.ts per tipi riutilizzabili
4. **Usa i tipi di libreria**: Sfrutta i tipi forniti da React, Redux, ecc.
5. **Considera l'utilizzo di utility types**:
   - `Partial<T>`: Tutti i campi di T diventano opzionali
   - `Required<T>`: Tutti i campi di T diventano obbligatori
   - `Pick<T, K>`: Prende solo alcune proprietà K da T
   - `Omit<T, K>`: Omette alcune proprietà K da T
   - `ReturnType<T>`: Estrae il tipo di ritorno di una funzione

### Esempio Pratico di Organizzazione di un Progetto

Per un'applicazione di e-commerce:

```
src/
├── components/
│   ├── common/
│   │   ├── Button/
│   │   ├── Input/
│   │   └── Modal/
│   └── features/
│       ├── auth/
│       │   ├── LoginForm/
│       │   └── SignupForm/
│       └── products/
│           ├── ProductCard/
│           └── ProductList/
├── pages/
│   ├── Home/
│   ├── ProductDetails/
│   └── Checkout/
├── features/
│   ├── auth/
│   │   ├── authSlice.ts
│   │   └── authService.ts
│   └── cart/
│       ├── cartSlice.ts
│       └── cartService.ts
├── hooks/
│   ├── useFetch.ts
│   └── useLocalStorage.ts
├── contexts/
│   ├── AuthContext.tsx
│   └── ThemeContext.tsx
├── utils/
│   ├── formatting.ts
│   └── validation.ts
├── services/
│   ├── api.ts
│   └── storage.ts
├── types/
│   ├── product.ts
│   └── user.ts
└── app/
    ├── store.ts
    └── routes.tsx
```

### Esempio di Componente Ben Tipizzato

```typescript
// types/product.ts
export interface Product {
  id: string;
  name: string;
  price: number;
  description: string;
  imageUrl: string;
  category: string;
  inStock: boolean;
}

// components/features/products/ProductCard/types.ts
import { Product } from '../../../../types/product';

export interface ProductCardProps {
  product: Product;
  onAddToCart: (productId: string) => void;
  isInCart?: boolean;
  variant?: 'compact' | 'detailed';
}

// components/features/products/ProductCard/ProductCard.tsx
import React from 'react';
import { ProductCardProps } from './types';
import './ProductCard.module.css';

const ProductCard: React.FC<ProductCardProps> = ({
  product,
  onAddToCart,
  isInCart = false,
  variant = 'detailed'
}) => {
  const handleAddToCart = () => {
    onAddToCart(product.id);
  };
  
  return (
    <div className={`product-card ${variant}`}>
      <img src={product.imageUrl} alt={product.name} />
      <h3>{product.name}</h3>
      
      {variant === 'detailed' && (
        <p className="description">{product.description}</p>
      )}
      
      <div className="price">${product.price.toFixed(2)}</div>
      
      <button 
        onClick={handleAddToCart}
        disabled={!product.inStock || isInCart}
        className={isInCart ? 'in-cart' : ''}
      >
        {isInCart ? 'In Cart' : product.inStock ? 'Add to Cart' : 'Out of Stock'}
      </button>
    </div>
  );
};

export default ProductCard;

// components/features/products/ProductCard/index.ts
export { default } from './ProductCard';
export * from './types';
```

### Tipizzare lo State Management (esempio Redux)

```typescript
// features/cart/types.ts
import { Product } from '../../types/product';

export interface CartItem extends Product {
  quantity: number;
}

export interface CartState {
  items: CartItem[];
  isLoading: boolean;
  error: string | null;
}

// features/cart/cartSlice.ts
import { createSlice, PayloadAction } from '@reduxjs/toolkit';
import { CartState, CartItem } from './types';

const initialState: CartState = {
  items: [],
  isLoading: false,
  error: null
};

const cartSlice = createSlice({
  name: 'cart',
  initialState,
  reducers: {
    addToCart: (state, action: PayloadAction<Product>) => {
      const existingItem = state.items.find(item => item.id === action.payload.id);
      
      if (existingItem) {
        existingItem.quantity += 1;
      } else {
        state.items.push({ ...action.payload, quantity: 1 });
      }
    },
    // altri reducers...
  }
});
```
