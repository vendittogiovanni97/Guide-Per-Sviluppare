# Guida ai Principali Pacchetti npm per lo Sviluppo

Questa guida contiene una raccolta dei pacchetti npm più utili per il frontend e il backend, con una breve spiegazione su come utilizzarli.

---

## 📌 1. Gestione del Progetto
### 🔹 npm, pnpm e yarn
- **npm**: Il package manager di default per Node.js.
  ```sh
  npm install -g npm
  ```
- **pnpm**: Alternativa veloce e ottimizzata.
  ```sh
  npm install -g pnpm
  ```
- **yarn**: Alternativa popolare con gestione avanzata dei pacchetti.
  ```sh
  npm install -g yarn
  ```

---

## 🚀 2. Framework e Librerie Frontend
### 🔹 React
```sh
npm install react react-dom
```
Libreria per creare UI dinamiche.

### 🔹 Next.js
```sh
npm install next
```
Framework React con SSR e API Routes.

### 🔹 Vite
```sh
npm create vite@latest my-app --template react-ts
```
Build tool ultrarapido per React, Vue e altri framework.

---

## 🎨 3. UI e Styling
### 🔹 Material UI
```sh
npm install @mui/material @emotion/react @emotion/styled
```
Componenti UI pronti all’uso.

### 🔹 Tailwind CSS
```sh
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```
Framework utility-first per lo styling.

### 🔹 Styled Components
```sh
npm install styled-components
```
CSS-in-JS per React.

---

## 🔧 4. Gestione dello Stato
### 🔹 Redux Toolkit
```sh
npm install @reduxjs/toolkit react-redux
```
Gestione dello stato centralizzata.

### 🔹 Zustand
```sh
npm install zustand
```
Gestione dello stato semplice e scalabile.

### 🔹 React Query
```sh
npm install @tanstack/react-query
```
Gestione avanzata del fetching di dati.

---

## 🔥 5. Backend e Database
### 🔹 Express.js
```sh
npm install express
```
Framework per creare API veloci.

### 🔹 Prisma ORM
```sh
npm install @prisma/client
```
ORM per TypeScript e Node.js con supporto a MySQL, PostgreSQL, SQLite.

### 🔹 Mongoose
```sh
npm install mongoose
```
ODM per MongoDB.

---

## 🔗 6. WebSocket e Real-time
### 🔹 ws (Server WebSocket)
```sh
npm install ws
```
Gestione WebSocket con Node.js.

### 🔹 socket.io
```sh
npm install socket.io
```
WebSocket con funzionalità avanzate.

---

## 🛠 7. Strumenti di Sviluppo
### 🔹 nodemon
```sh
npm install -g nodemon
```
Ricarica automaticamente il server quando ci sono modifiche al codice.

### 🔹 ESLint + Prettier
```sh
npm install -D eslint prettier eslint-config-prettier eslint-plugin-prettier
```
Linting e formattazione automatica del codice.

### 🔹 Debug con VS Code e TypeScript
Aggiungere questo in `.vscode/launch.json`:
```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Debug Node.js",
      "type": "node",
      "request": "launch",
      "program": "${workspaceFolder}/src/index.ts",
      "outFiles": ["${workspaceFolder}/dist/**/*.js"]
    }
  ]
}
```
Avvia il debug con **F5**! 🚀

---

Questa guida ti aiuterà a scegliere i pacchetti npm giusti per i tuoi progetti. Se vuoi approfondire qualcosa, fammelo sapere! 😊

