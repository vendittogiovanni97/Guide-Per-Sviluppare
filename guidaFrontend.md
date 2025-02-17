# Guida Completa per Sviluppo Frontend con Vite, TypeScript, Material UI e WebSocket

## 1. Creare un Progetto con Vite e TypeScript

Installa **Vite** e configura un progetto con TypeScript:

```sh
npm create vite@latest my-app --template react-ts
cd my-app
npm install
```

Avvia il server di sviluppo:

```sh
npm run dev
```

## 2. Installare Material UI

```sh
npm install @mui/material @emotion/react @emotion/styled
```

Aggiungi un tema personalizzato:

```tsx
import { createTheme, ThemeProvider } from '@mui/material/styles';
import CssBaseline from '@mui/material/CssBaseline';
import { blue, pink } from '@mui/material/colors';

const theme = createTheme({
  palette: {
    primary: blue,
    secondary: pink,
  },
});

const App = () => (
  <ThemeProvider theme={theme}>
    <CssBaseline />
    <h1>Hello Vite + MUI</h1>
  </ThemeProvider>
);
```

## 3. Ottimizzare Vite per il Progetto

Modifica `vite.config.ts` per supportare alias e ottimizzazione:

```ts
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import path from 'path';

export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: {
      '@': path.resolve(__dirname, 'src'),
    },
  },
});
```

Ora puoi importare componenti con `@/component` invece di `../../component`.

## 4. Configurare WebSocket con Vite

Installa WebSocket nativo in TypeScript:

```sh
npm install ws
```

Server WebSocket in `server.ts`:

```ts
import { WebSocketServer } from 'ws';

const wss = new WebSocketServer({ port: 8080 });

wss.on('connection', ws => {
  ws.send('Benvenuto nel WebSocket Server!');
  ws.on('message', message => {
    console.log(`Messaggio ricevuto: ${message}`);
  });
});
```

Client WebSocket in React:

```tsx
import { useEffect } from 'react';

const WebSocketComponent = () => {
  useEffect(() => {
    const ws = new WebSocket('ws://localhost:8080');
    ws.onmessage = event => console.log('Messaggio dal server:', event.data);
    return () => ws.close();
  }, []);
  return <h2>WebSocket Attivo</h2>;
};
```

## 5. Aggiungere Zustand per la Gestione dello Stato

Installiamo Zustand:

```sh
npm install zustand
```

Creiamo uno store globale:

```ts
import create from 'zustand';

type Store = {
  count: number;
  increment: () => void;
};

export const useStore = create<Store>((set) => ({
  count: 0,
  increment: () => set((state) => ({ count: state.count + 1 })),
}));
```

Usiamo lo store nel componente:

```tsx
import { useStore } from '@/store';

const Counter = () => {
  const { count, increment } = useStore();
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={increment}>Incrementa</button>
    </div>
  );
};
```

## 6. Ottimizzare Performance con React Query

Installiamo React Query:

```sh
npm install @tanstack/react-query
```

Configuriamo un client globale:

```tsx
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';

const queryClient = new QueryClient();

const App = () => (
  <QueryClientProvider client={queryClient}>
    <MyComponent />
  </QueryClientProvider>
);
```

## 7. Configurare un Debugger con Vite e TypeScript

1. Apri **Esegui e Debugga** (`Cmd + Shift + D`)
2. Clicca su **Crea un file launch.json** e seleziona **Vite**
3. Modifica `launch.json`:

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Debug Vite App",
      "type": "chrome",
      "request": "launch",
      "url": "http://localhost:5173",
      "webRoot": "${workspaceFolder}/src"
    }
  ]
}
```

4. Avvia il debug con **F5**! 🚀

## 8. Alias per Aprire VS Code più Velocemente

Aggiungi un alias nel terminale:

```sh
alias code="open -a 'Visual Studio Code'"
```

Ora puoi digitare `code .` per aprire la cartella corrente in VS Code! 🎯

---

Ora hai un ambiente di sviluppo **Vite + TypeScript + MUI + WebSocket** super ottimizzato! 🚀 Se vuoi approfondire qualcosa, fammelo sapere! 😃

