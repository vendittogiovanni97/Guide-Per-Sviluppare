# Guida Completa per Ottimizzare Visual Studio Code per la Programmazione

## 1. Installazione e Configurazione di Base
Se non hai ancora installato Visual Studio Code, puoi farlo con:
```sh
brew install --cask visual-studio-code
```

Apri VS Code e personalizzalo:
- Vai su **Code > Impostazioni > Tema** e scegli un tema scuro come *One Dark Pro* per ridurre l'affaticamento visivo.
- Attiva la sincronizzazione con il tuo account GitHub/Microsoft per salvare le impostazioni tra dispositivi.

## 2. Migliorare le Prestazioni di VS Code
Per progetti grandi, ottimizza le prestazioni:

### 🔹 Disabilita la Telemetria
```json
"telemetry.enableTelemetry": false
```

### 🔹 Aumenta la Memoria Disponibile
```sh
code --max-memory=4096
```

### 🔹 Escludi Cartelle Non Necessarie
```json
"files.watcherExclude": {
  "**/node_modules/**": true,
  "**/.git/**": true,
  "**/dist/**": true
}
```

## 3. Estensioni Essenziali

### 🔹 **Generale**
- **Settings Sync** → Sincronizza le impostazioni
- **Project Manager** → Passa velocemente tra progetti
- **Live Server** → Avvia un server locale per progetti frontend

### 🔹 **TypeScript e React**
- **ESLint** → Evidenzia errori nel codice
- **Prettier** → Formatta automaticamente il codice
- **Path Intellisense** → Suggerisce percorsi file
- **React Snippets** → Suggerimenti codice per React

### 🔹 **Backend (Node.js, Prisma, MySQL, MongoDB)**
- **REST Client** → Testa API direttamente in VS Code
- **Prisma** → Supporto per ORM Prisma
- **MongoDB for VS Code** → Gestione MongoDB
- **MySQL Database Client** → Gestione MySQL senza uscire da VS Code

## 4. Scorciatoie Utili
### 📌 Navigazione
- `Cmd + P` → Apri file rapidamente
- `Cmd + Shift + F` → Cerca in tutti i file
- `Cmd + B` → Mostra/nasconde la barra laterale

### ⚡ Modifica
- `Cmd + D` → Seleziona parola successiva uguale
- `Cmd + Shift + L` → Seleziona tutte le occorrenze
- `Cmd + /` → Commenta una riga

## 5. Configurare TypeScript in VS Code
Installa TypeScript globalmente:
```sh
npm install -g typescript
```

Inizializza un progetto:
```sh
tsc --init
```

Configura `tsconfig.json`:
```json
{
  "compilerOptions": {
    "target": "ES6",
    "module": "CommonJS",
    "strict": true,
    "outDir": "dist",
    "rootDir": "src"
  }
}
```

Compila e avvia il codice:
```sh
npm run build && npm start
```

Usa `ts-node` per eseguire TypeScript senza compilazione:
```sh
npm install -g ts-node
```
```sh
ts-node src/index.ts
```

## 6. Configurare un Debugger per TypeScript
1. Apri **Esegui e Debugga** (`Cmd + Shift + D`)
2. Clicca su **Crea un file launch.json** e seleziona **Node.js**
3. Modifica `launch.json`:
```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Debug TypeScript",
      "type": "node",
      "request": "launch",
      "program": "${workspaceFolder}/src/index.ts",
      "preLaunchTask": "tsc: build - tsconfig.json",
      "outFiles": ["${workspaceFolder}/dist/**/*.js"]
    }
  ]
}
```
4. Avvia il debug con **F5**! 🚀

## 7. Configurare Prisma ORM
1. Installa Prisma:
```sh
npm install @prisma/client
npx prisma init
```
2. Configura `schema.prisma`:
```prisma
datasource db {
  provider = "mysql"
  url      = env("DATABASE_URL")
}

generator client {
  provider = "prisma-client-js"
}

model User {
  id    Int     @id @default(autoincrement())
  name  String
  email String  @unique
}
```
3. Genera il client:
```sh
npx prisma generate
```

## 8. React e TypeScript
Installa React con TypeScript:
```sh
npx create-react-app my-app --template typescript
```
Aggiungi tipi ai componenti:
```tsx
type Props = {
  name: string;
};
const Hello: React.FC<Props> = ({ name }) => <h1>Hello, {name}!</h1>;
```

## 9. API REST con Express e TypeScript
```sh
npm install express @types/express
```
```ts
import express from 'express';
const app = express();
app.get('/', (req, res) => res.send('Hello TypeScript!'));
app.listen(3000, () => console.log('Server running'));
```

## 10. Alias per Aprire VS Code più Velocemente
Aggiungi un alias nel terminale:
```sh
alias code="open -a 'Visual Studio Code'"
```
Ora puoi digitare `code .` per aprire la cartella corrente in VS Code! 🎯

---
Ora hai un VS Code ottimizzato per TypeScript, Prisma, React e lo sviluppo backend! 🚀 Se vuoi approfondire qualcosa, fammelo sapere! 😃

