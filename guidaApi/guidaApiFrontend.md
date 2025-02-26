# Guida Completa: Consumare API in Frontend con TypeScript

Questa guida ti mostrerà come chiamare API REST in un'applicazione frontend utilizzando **TypeScript**, **fetch**, e librerie avanzate come **Axios** e **React Query**.

---

## 📊 1. Chiamare API con Fetch

### 🔹 Funzione base con Fetch
```ts
const fetchData = async (): Promise<any> => {
  try {
    const response = await fetch('https://jsonplaceholder.typicode.com/posts');
    if (!response.ok) {
      throw new Error('Errore nella richiesta');
    }
    return await response.json();
  } catch (error) {
    console.error('Errore:', error);
  }
};
```

### 🔹 Utilizzo in un componente React
```tsx
import { useEffect, useState } from 'react';

const PostList = () => {
  const [posts, setPosts] = useState<any[]>([]);

  useEffect(() => {
    fetchData().then(data => setPosts(data));
  }, []);

  return (
    <ul>
      {posts.map(post => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  );
};

export default PostList;
```

---

## 🚀 2. Usare Axios per gestire API

### 🔹 Installare Axios
```sh
npm install axios
```

### 🔹 Creare una funzione API con Axios
```ts
import axios from 'axios';

const api = axios.create({
  baseURL: 'https://jsonplaceholder.typicode.com',
  headers: {
    'Content-Type': 'application/json',
  },
});

export const getPosts = async () => {
  try {
    const response = await api.get('/posts');
    return response.data;
  } catch (error) {
    console.error('Errore API:', error);
    throw error;
  }
};
```

### 🔹 Usare Axios in un componente
```tsx
import { useEffect, useState } from 'react';
import { getPosts } from '@/api';

const PostList = () => {
  const [posts, setPosts] = useState<any[]>([]);

  useEffect(() => {
    getPosts().then(setPosts).catch(console.error);
  }, []);

  return (
    <ul>
      {posts.map(post => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  );
};

export default PostList;
```

---

## 🌟 4. WebSocket in TypeScript per connessioni in tempo reale

### 🔹 Installare WebSocket
```sh
npm install ws
```

### 🔹 Configurare un Server WebSocket
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

### 🔹 Connessione WebSocket in React
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

---

Questa guida copre tutto ciò che ti serve per lavorare con API in **TypeScript** nel frontend, dalle chiamate con Fetch e Axios fino a React Query e WebSocket. Se vuoi approfondire qualche argomento, fammelo sapere! 🚀

