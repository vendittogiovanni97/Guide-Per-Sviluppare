# Guida Completa agli Strumenti per Sviluppatori

## NPM (Node Package Manager)

### Comandi Base di Inizializzazione
```bash
# Inizializza un nuovo progetto Node.js, creando package.json
npm init

# Inizializza progetto con risposte predefinite (modalità yes)
npm init -y
```

### Installazione Librerie

#### Installazioni Standard
```bash
# Installa libreria Express (framework web per Node.js)
npm install express

# Installa gestore per funzioni asincrone con Express
npm install express-async-handler

# Installa driver per MySQL
npm install mysql2
```

#### Installazioni in Development
```bash
# Installa dipendenze solo per ambiente di sviluppo
npm install --save-dev typescript

# Esempio di installazione che non verrà inclusa in produzione
```

## TypeScript

### Comandi Principali
```bash
# Converte file TypeScript in JavaScript
npx tsc

# Esegue file TypeScript direttamente senza conversione
npx ts-node

# Inizializza configurazione TypeScript
npx tsc init
```

### Configurazione TypeScript (tsconfig.json)
```json
{
  "compilerOptions": {
    "module": "commonjs",
    "esModuleInterop": true,
    "target": "es6",
    "noImplicitAny": true,
    "moduleResolution": "node",
    "sourceMap": true,
    "outDir": "./dist",
    "baseUrl": "./src",
    "paths": {
      "*": [
        "node_modules/*",
        "src/types/*"
      ]
    }
  },
  "include": ["src/**/*"]
}
```

## React con Vite

### Installazione e Avvio
```bash
# Crea nuovo progetto React con TypeScript usando Vite
npm create vite react -- --template=react-ts

# Avvia server di sviluppo
npm run dev
```

## Prisma (ORM per Database)

### Installazione
```bash
# Installa Prisma come dipendenza di sviluppo
npm install prisma --save-dev

# Installa client Prisma per interazione con database
npm install @prisma/client

# Installa Prisma globalmente
npm install prisma -g
```

### Comandi Principali
```bash
# Inizializza configurazione Prisma
npx prisma init

# Migra e aggiorna schema del database
npx prisma migrate dev

# Apre interfaccia web per gestione database
npx prisma studio
```

### Configurazione Database
Nel file `.env`:
```
DATABASE_URL="mysql://username:password@localhost:3306/nome_database"
```

## Permessi Database MySQL

### Concessione Permessi
```sql
GRANT CREATE, ALTER, DROP, REFERENCES, 
       INSERT, SELECT, UPDATE, DELETE, INDEX 
ON *.* TO 'unilink'@'%';

FLUSH PRIVILEGES;
```

## Gestione Dipendenze e Comandi Aggiuntivi

### Flag Comuni npm
```bash
# Installa dipendenze solo per sviluppo
--save-dev

# Installa pacchetto globalmente
-g

# Installa con risposta automatica yes
-y
```

## Best Practice per Sviluppatori

1. **Gestione Dipendenze**
   - Mantieni aggiornate le dipendenze del progetto
   - Usa `package-lock.json` per versioni consistenti
   - Controlla regolarmente vulnerabilità con `npm audit`

2. **Configurazione Progetto**
   - Crea sempre un `.gitignore`
   - Escludi cartelle come `node_modules`
   - Documenta la configurazione del progetto

3. **Ambiente di Sviluppo**
   - Usa gestori di versioni come `nvm`
   - Mantieni ambienti di sviluppo, staging e produzione separati
   - Utilizza variabili d'ambiente per configurazioni sensibili

4. **Qualità del Codice**
   - Implementa lint e formatter (ESLint, Prettier)
   - Usa TypeScript per tipizzazione statica
   - Scrivi test unitari e di integrazione

## Risorse Utili

- Documentazione NPM: https://docs.npmjs.com/
- Guida TypeScript: https://www.typescriptlang.org/docs/
- Documentazione Prisma: https://www.prisma.io/docs/
- Guida React: https://react.dev/

## Note Finali

Questa guida è un punto di partenza. Ogni progetto ha esigenze specifiche, quindi adatta questi strumenti e pratiche alle tue necessità.

**Buon Coding!** 🚀👩‍💻👨‍💻


Altre istruzioni:
Step 2: Aggiungere validazioni con Zod
Zod è una libreria perfetta per validare i dati in ingresso. Installiamola:

bash
Copia
Modifica
npm install zod
E creiamo una validazione per gli utenti in un file separato:

📌 src/middlewares/userValidation.ts

typescript
Copia
Modifica
import { z } from "zod";

export const userSchema = z.object({
  name: z.string().min(2, "Il nome deve avere almeno 2 caratteri"),
  email: z.string().email("L'email non è valida"),
  password: z.string().min(6, "La password deve avere almeno 6 caratteri"),
});