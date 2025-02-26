# Guida Completa Vite.js

## Indice
1. [Setup Iniziale](#setup-iniziale)
2. [Struttura del Progetto](#struttura-del-progetto)
3. [Componenti](#componenti)
4. [Routing](#routing)
5. [Gestione degli Asset](#gestione-degli-asset)
6. [Stili e CSS](#stili-e-css)
7. [Configurazione Avanzata](#configurazione-avanzata)
8. [Ottimizzazioni](#ottimizzazioni)
9. [Deploy](#deploy)
10. [Testing](#testing)

## Setup Iniziale

### Creazione di un nuovo progetto

```bash
# Usando NPM
npm create vite@latest my-app -- --template react-ts

# Usando Yarn
yarn create vite my-app --template react-ts

# Usando PNPM
pnpm create vite my-app --template react-ts
```

### Template Disponibili
- vanilla
- vanilla-ts
- vue
- vue-ts
- react
- react-ts
- preact
- preact-ts
- lit
- lit-ts
- svelte
- svelte-ts

### Struttura Base del package.json
```json
{
  "name": "my-app",
  "private": true,
  "version": "0.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "preview": "vite preview"
  },
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0"
  },
  "devDependencies": {
    "@types/react": "^18.2.0",
    "@types/react-dom": "^18.2.0",
    "@vitejs/plugin-react": "^4.0.0",
    "typescript": "^5.0.0",
    "vite": "^4.0.0"
  }
}
```

## Struttura del Progetto

```plaintext
my-app/
├── node_modules/
├── public/
│   └── vite.svg
├── src/
│   ├── assets/
│   ├── components/
│   ├── pages/
│   ├── styles/
│   ├── App.tsx
│   ├── main.tsx
│   └── vite-env.d.ts
├── index.html
├── package.json
├── tsconfig.json
├── tsconfig.node.json
└── vite.config.ts
```

### Configurazione TypeScript (tsconfig.json)
```json
{
  "compilerOptions": {
    "target": "ESNext",
    "useDefineForClassFields": true,
    "lib": ["DOM", "DOM.Iterable", "ESNext"],
    "allowJs": false,
    "skipLibCheck": true,
    "esModuleInterop": false,
    "allowSyntheticDefaultImports": true,
    "strict": true,
    "forceConsistentCasingInFileNames": true,
    "module": "ESNext",
    "moduleResolution": "Node",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx",
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"]
    }
  },
  "include": ["src"],
  "references": [{ "path": "./tsconfig.node.json" }]
}
```

## Componenti

### Componente Base
```tsx
// src/components/Button.tsx
import { FC, ButtonHTMLAttributes } from 'react';
import './Button.css';

interface ButtonProps extends ButtonHTMLAttributes<HTMLButtonElement> {
  variant?: 'primary' | 'secondary';
}

export const Button: FC<ButtonProps> = ({ 
  children, 
  variant = 'primary', 
  ...props 
}) => {
  return (
    <button 
      className={`button ${variant}`} 
      {...props}
    >
      {children}
    </button>
  );
};
```

### Composizione di Componenti
```tsx
// src/components/Card.tsx
import { FC, ReactNode } from 'react';
import './Card.css';

interface CardProps {
  title: string;
  children: ReactNode;
}

export const Card: FC<CardProps> = ({ title, children }) => {
  return (
    <div className="card">
      <div className="card-header">
        <h2>{title}</h2>
      </div>
      <div className="card-body">
        {children}
      </div>
    </div>
  );
};
```

## Routing

### Setup del Router
```bash
npm install react-router-dom
```

```tsx
// src/App.tsx
import { BrowserRouter, Routes, Route } from 'react-router-dom';
import { Home } from './pages/Home';
import { About } from './pages/About';
import { Layout } from './components/Layout';

export const App = () => {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Layout />}>
          <Route index element={<Home />} />
          <Route path="about" element={<About />} />
        </Route>
      </Routes>
    </BrowserRouter>
  );
};
```

### Layout Component
```tsx
// src/components/Layout.tsx
import { FC } from 'react';
import { Outlet, Link } from 'react-router-dom';
import { Navbar } from './Navbar';
import { Footer } from './Footer';

export const Layout: FC = () => {
  return (
    <div className="layout">
      <Navbar />
      <main>
        <Outlet />
      </main>
      <Footer />
    </div>
  );
};
```

## Gestione degli Asset

### Importazione di Immagini
```tsx
// Importazione statica
import logo from '@/assets/logo.png';

// Importazione dinamica
const imageUrl = new URL('@/assets/image.png', import.meta.url).href;
```

### SVG come Componenti
```tsx
// vite.config.ts
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import svgr from 'vite-plugin-svgr';

export default defineConfig({
  plugins: [react(), svgr()]
});
```

```tsx
// Uso
import { ReactComponent as Logo } from './logo.svg';
```

## Stili e CSS

### CSS Modules
```css
/* Button.module.css */
.button {
  padding: 0.5rem 1rem;
  border-radius: 4px;
}

.primary {
  background: blue;
  color: white;
}

.secondary {
  background: gray;
  color: white;
}
```

```tsx
import styles from './Button.module.css';

export const Button = ({ variant = 'primary' }) => {
  return (
    <button className={`${styles.button} ${styles[variant]}`}>
      Click me
    </button>
  );
};
```

### Tailwind CSS Setup
```bash
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

```javascript
// tailwind.config.js
export default {
  content: [
    "./index.html",
    "./src/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

```css
/* index.css */
@tailwind base;
@tailwind components;
@tailwind utilities;
```

## Configurazione Avanzata

### Alias dei Path
```typescript
// vite.config.ts
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import path from 'path';

export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
    },
  },
});
```

### Variabili d'Ambiente
```plaintext
# .env
VITE_API_URL=https://api.example.com
```

```typescript
// Uso
const apiUrl = import.meta.env.VITE_API_URL;
```

### Proxy Setup
```typescript
// vite.config.ts
export default defineConfig({
  server: {
    proxy: {
      '/api': {
        target: 'http://localhost:3000',
        changeOrigin: true,
        rewrite: (path) => path.replace(/^\/api/, '')
      }
    }
  }
});
```

## Ottimizzazioni

### Code Splitting
```typescript
// Lazy loading di componenti
import { lazy, Suspense } from 'react';

const HeavyComponent = lazy(() => import('./HeavyComponent'));

function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <HeavyComponent />
    </Suspense>
  );
}
```

### Bundle Analyzer
```bash
npm install -D rollup-plugin-visualizer
```

```typescript
// vite.config.ts
import { visualizer } from 'rollup-plugin-visualizer';

export default defineConfig({
  plugins: [
    react(),
    visualizer({
      open: true,
      filename: 'dist/stats.html',
    }),
  ],
});
```

### Performance Optimization
```typescript
// vite.config.ts
export default defineConfig({
  build: {
    target: 'esnext',
    minify: 'terser',
    rollupOptions: {
      output: {
        manualChunks: {
          vendor: ['react', 'react-dom'],
          // Altri chunk personalizzati
        },
      },
    },
  },
});
```

## Deploy

### Build per Produzione
```bash
npm run build
```

### Preview della Build
```bash
npm run preview
```

### Configurazione per diversi ambienti
```typescript
// vite.config.ts
export default defineConfig(({ command, mode }) => {
  const env = loadEnv(mode, process.cwd(), '');
  
  return {
    define: {
      __API_URL__: JSON.stringify(env.VITE_API_URL),
    },
    build: {
      outDir: 'dist',
      assetsDir: 'assets',
      sourcemap: mode === 'development',
    },
  };
});
```

## Testing

### Vitest Setup
```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom
```

```typescript
// vite.config.ts
/// <reference types="vitest" />
import { defineConfig } from 'vite';

export default defineConfig({
  test: {
    globals: true,
    environment: 'jsdom',
    setupFiles: './src/test/setup.ts',
  },
});
```

### Esempio di Test
```typescript
// Button.test.tsx
import { render, screen } from '@testing-library/react';
import { Button } from './Button';

describe('Button', () => {
  it('renders with correct text', () => {
    render(<Button>Click me</Button>);
    expect(screen.getByText('Click me')).toBeInTheDocument();
  });

  it('applies correct variant class', () => {
    render(<Button variant="secondary">Click me</Button>);
    const button = screen.getByText('Click me');
    expect(button).toHaveClass('secondary');
  });
});
```

### Testing Setup
```typescript
// src/test/setup.ts
import '@testing-library/jest-dom';
import { expect, afterEach } from 'vitest';
import { cleanup } from '@testing-library/react';
import matchers from '@testing-library/jest-dom/matchers';

expect.extend(matchers);

afterEach(() => {
  cleanup();
});
```
