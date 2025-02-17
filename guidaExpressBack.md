# Guida Completa: Creare API con Express e TypeScript

Questa guida ti aiuterà a configurare e sviluppare un'API REST utilizzando **Express** e **TypeScript**.

---

## 📌 1. Configurare il Progetto

### 🔹 Inizializzare un nuovo progetto
```sh
mkdir my-express-api && cd my-express-api
npm init -y
```

### 🔹 Installare le dipendenze necessarie
```sh
npm install express dotenv cors
npm install -D typescript ts-node @types/node @types/express nodemon
```

### 🔹 Creare il file di configurazione TypeScript
Aggiungi un file `tsconfig.json` alla root del progetto:
```json
{
  "compilerOptions": {
    "outDir": "./dist",
    "rootDir": "./src",
    "moduleResolution": "node",
    "target": "ES6",
    "module": "CommonJS",
    "strict": true
  }
}
```

---

## 🚀 2. Creare il Server Express

### 🔹 Struttura base del progetto
```
my-express-api/
│── src/
│   ├── server.ts
│   ├── routes/
│   │   ├── user.routes.ts
│   ├── controllers/
│   │   ├── user.controller.ts
│   ├── middlewares/
│   ├── models/
│── tsconfig.json
│── package.json
```

### 🔹 Creare il file `server.ts`
```ts
import express from 'express';
import dotenv from 'dotenv';
import cors from 'cors';

dotenv.config();
const app = express();
const PORT = process.env.PORT || 5000;

app.use(cors());
app.use(express.json());

app.get('/', (req, res) => {
  res.send('API is running...');
});

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

---

## 🔗 3. Creare le API REST

### 🔹 Definire le Route degli Utenti
**File:** `src/routes/user.routes.ts`
```ts
import { Router } from 'express';
import { getUsers } from '../controllers/user.controller';

const router = Router();
router.get('/users', getUsers);
export default router;
```

### 🔹 Creare il Controller degli Utenti
**File:** `src/controllers/user.controller.ts`
```ts
import { Request, Response } from 'express';

export const getUsers = (req: Request, res: Response) => {
  res.json({ users: [{ id: 1, name: "John Doe" }] });
};
```

### 🔹 Importare le Route nel Server
Modifica `server.ts` per includere le route degli utenti:
```ts
import userRoutes from './routes/user.routes';
app.use('/api', userRoutes);
```

---

## 🛠 4. Debug e Avvio del Server

### 🔹 Configurare `nodemon` per lo sviluppo
Aggiungi questo script in `package.json`:
```json
"scripts": {
  "dev": "nodemon --ext ts --exec ts-node src/server.ts"
}
```
Avvia il server in modalità sviluppo:
```sh
npm run dev
```

### 🔹 Debug con VS Code
Crea `.vscode/launch.json`:
```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Debug API Express",
      "type": "node",
      "request": "launch",
      "program": "${workspaceFolder}/src/server.ts",
      "outFiles": ["${workspaceFolder}/dist/**/*.js"],
      "preLaunchTask": "tsc: build - tsconfig.json"
    }
  ]
}
```

Ora puoi eseguire il debug premendo **F5** su VS Code. 🚀

---

Questa guida ti permette di creare e gestire un'API REST con **Express** e **TypeScript** in modo efficiente. Se vuoi approfondire qualche aspetto, fammelo sapere! 😊

