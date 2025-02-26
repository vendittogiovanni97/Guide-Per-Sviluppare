# Comandi NPM per Setup Progetti

## Inizializzazione Progetti

### Progetto Base
```bash
# Inizializzazione base
npm init
npm init -y  # Versione automatica

# Package.json con Git
npm init -y && git init (da bash, powershall non prende doppio &&)
```

### TypeScript Base
```bash
# Installazione TypeScript
npm install -D typescript @types/node
npm install -D ts-node

# Inizializzazione TypeScript
npx tsc --init

# Setup completo TypeScript + Node
npm install -D typescript @types/node ts-node ts-node-dev
```

### Vite
```bash
# React + TypeScript
npm create vite@latest my-app -- --template react-ts

# React
npm create vite@latest my-app -- --template react

# Vue + TypeScript
npm create vite@latest my-app -- --template vue-ts

# Vanilla + TypeScript
npm create vite@latest my-app -- --template vanilla-ts
```

## Database e ORM

### Prisma
```bash
# Installazione Prisma
npm install -D prisma
npm install @prisma/client

# Inizializzazione Prisma
npx prisma init

# Comandi utili Prisma
npx prisma generate
npx prisma db push
npx prisma migrate dev
npx prisma studio
```

## Backend Development

### Express + TypeScript
```bash
# Setup base
npm install express
npm install -D @types/express

# Setup completo
npm install express cors helmet
npm install -D @types/express @types/cors @types/node typescript ts-node
```

### Nodemon
```bash
# Installazione base
npm install -D nodemon

# Con ts-node
npm install -D nodemon ts-node

# Setup consigliato per sviluppo
npm install -D nodemon ts-node-dev
```

### Environment Variables
```bash
# dotenv base
npm install dotenv

# Con type checking
npm install dotenv-safe
npm install -D @types/dotenv-safe
```

### WebSocket
```bash
# ws (WebSocket base)
npm install ws
npm install -D @types/ws

# Socket.io
npm install socket.io
npm install -D @types/socket.io

# Client Socket.io
npm install socket.io-client
npm install -D @types/socket.io-client
```

## Frontend Development

### React
```bash
# Create React App + TypeScript
npx create-react-app my-app --template typescript

# Routing
npm install react-router-dom
npm install -D @types/react-router-dom

# State Management
npm install @reduxjs/toolkit react-redux
npm install -D @types/react-redux
```

### Styling e UI
```bash
# Tailwind CSS
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p

# Styled Components
npm install styled-components
npm install -D @types/styled-components

# Material UI
npm install @mui/material @emotion/react @emotion/styled
npm install @mui/icons-material
```

## Code Quality

### Prettier
```bash
# Installazione base
npm install -D prettier

# Con ESLint
npm install -D prettier eslint-config-prettier

# Setup consigliato
npm install -D prettier eslint eslint-config-prettier eslint-plugin-prettier
```

### ESLint
```bash
# Setup base
npm install -D eslint

# TypeScript ESLint
npm install -D eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin

# Setup React
npm install -D eslint-plugin-react eslint-plugin-react-hooks
```

### Testing
```bash
# Jest
npm install -D jest @types/jest ts-jest

# React Testing Library
npm install -D @testing-library/react @testing-library/jest-dom @testing-library/user-event

# Vitest (per Vite)
npm install -D vitest
```

## Utility Packages

### Validazione
```bash
# Zod
npm install zod

# Joi
npm install joi
npm install -D @types/joi

# Yup
npm install yup
```

### Date e Time
```bash
# Date-fns
npm install date-fns

# Moment.js
npm install moment

# Day.js
npm install dayjs
```

### HTTP Client
```bash
# Axios
npm install axios
npm install -D @types/axios

# Fetch wrapper
npm install cross-fetch
```

### Logging
```bash
# Winston
npm install winston

# Morgan (HTTP logging)
npm install morgan
npm install -D @types/morgan

# Pino
npm install pino
npm install -D @types/pino
```

### Security
```bash
# Basic security
npm install helmet cors cookie-parser

# Authentication
npm install jsonwebtoken bcrypt
npm install -D @types/jsonwebtoken @types/bcrypt

# Session handling
npm install express-session
npm install -D @types/express-session
```

### File Upload
```bash
# Multer
npm install multer
npm install -D @types/multer

# FormData
npm install form-data
npm install -D @types/form-data
```

## Scripts Package.json Consigliati

```json
{
  "scripts": {
    "start": "node dist/index.js",
    "dev": "nodemon src/index.ts",
    "build": "tsc",
    "lint": "eslint . --ext .ts,.tsx",
    "format": "prettier --write \"src/**/*.{ts,tsx}\"",
    "test": "jest",
    "test:watch": "jest --watch",
    "prisma:generate": "prisma generate",
    "prisma:push": "prisma db push",
    "prisma:studio": "prisma studio"
  }
}
```
