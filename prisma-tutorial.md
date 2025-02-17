# Tutorial Prisma: Sistema Biblioteca

Questo tutorial ti guiderà attraverso la creazione di un sistema di gestione biblioteca utilizzando Prisma. Esploreremo diversi tipi di relazioni e impareremo a gestire le migrazioni.

## Prerequisiti
- Node.js installato
- Conoscenza base di TypeScript
- Un database PostgreSQL

## Step 1: Setup Iniziale

1. Crea una nuova directory e inizializza il progetto:
```bash
mkdir biblioteca-prisma
cd biblioteca-prisma
npm init -y
```

2. Installa le dipendenze necessarie:
```bash
npm install prisma typescript @prisma/client @types/node
npm install -D ts-node
```

3. Inizializza Prisma:
```bash
npx prisma init
```

4. Configura il database URL nel file `.env`:
```
DATABASE_URL="postgresql://user:password@localhost:5432/biblioteca"
```

## Step 2: Prima Relazione 1:1 - Utente e Tessera Biblioteca

Modifica `schema.prisma`:

```prisma
model Utente {
  id          Int      @id @default(autoincrement())
  nome        String
  email       String   @unique
  tessera     Tessera?
}

model Tessera {
  id           Int      @id @default(autoincrement())
  numeroTessera String  @unique
  dataEmissione DateTime
  dataScadenza  DateTime
  utente       Utente   @relation(fields: [utenteId], references: [id])
  utenteId     Int      @unique
}
```

Esegui la prima migrazione:
```bash
npx prisma migrate dev --name init
```

## Step 3: Aggiungiamo Relazione 1:N - Utente e Prestiti

Aggiungi al tuo schema:

```prisma
model Utente {
  id          Int      @id @default(autoincrement())
  nome        String
  email       String   @unique
  tessera     Tessera?
  prestiti    Prestito[]
}

model Prestito {
  id            Int      @id @default(autoincrement())
  dataPrestito  DateTime
  dataRestituzione DateTime?
  utente        Utente   @relation(fields: [utenteId], references: [id])
  utenteId      Int
  libro         Libro    @relation(fields: [libroId], references: [id])
  libroId       Int
}

model Libro {
  id          Int       @id @default(autoincrement())
  titolo      String
  isbn        String    @unique
  prestiti    Prestito[]
}
```

Crea la migrazione:
```bash
npx prisma migrate dev --name aggiungi_prestiti
```

## Step 4: Relazione N:N - Libri e Autori

Aggiungi i modelli per la relazione many-to-many:

```prisma
model Libro {
  id          Int       @id @default(autoincrement())
  titolo      String
  isbn        String    @unique
  prestiti    Prestito[]
  autori      AutoriSuLibri[]
}

model Autore {
  id          Int       @id @default(autoincrement())
  nome        String
  cognome     String
  libri       AutoriSuLibri[]
}

model AutoriSuLibri {
  libro       Libro    @relation(fields: [libroId], references: [id])
  libroId     Int
  autore      Autore   @relation(fields: [autoreId], references: [id])
  autoreId    Int
  @@id([libroId, autoreId])
}
```

Crea la migrazione:
```bash
npx prisma migrate dev --name aggiungi_autori
```

## Step 5: Migrazione Customizzata - Aggiungere Campi Calcolati

1. Genera una nuova migrazione:
```bash
npx prisma migrate dev --create-only --name aggiungi_stato_prestito
```

2. Modifica il file di migrazione generato:

```sql
-- Aggiungiamo un tipo ENUM per lo stato del prestito
CREATE TYPE "StatoPrestito" AS ENUM ('IN_CORSO', 'RESTITUITO', 'IN_RITARDO');

-- Aggiungiamo la colonna stato
ALTER TABLE "Prestito" ADD COLUMN "stato" "StatoPrestito";

-- Aggiorniamo i record esistenti per prestiti restituiti
UPDATE "Prestito" 
SET stato = 'RESTITUITO'
WHERE dataRestituzione IS NOT NULL;

-- Aggiorniamo i record per prestiti in ritardo
UPDATE "Prestito"
SET stato = 'IN_RITARDO'
WHERE dataRestituzione IS NULL 
AND dataPrestito + INTERVAL '30 days' < CURRENT_DATE;

-- Aggiorniamo i record per prestiti in corso
UPDATE "Prestito"
SET stato = 'IN_CORSO'
WHERE stato IS NULL;

-- Creiamo una vista per monitorare facilmente i prestiti
CREATE VIEW prestiti_status AS
SELECT 
    p.id,
    u.nome as nome_utente,
    l.titolo as titolo_libro,
    p.dataPrestito,
    p.dataRestituzione,
    p.stato,
    CASE 
        WHEN p.dataRestituzione IS NULL THEN 
            CURRENT_DATE - p.dataPrestito
        ELSE
            p.dataRestituzione - p.dataPrestito
    END as giorni_prestito
FROM "Prestito" p
JOIN "Utente" u ON p.utenteId = u.id
JOIN "Libro" l ON p.libroId = l.id;
```

3. Aggiorna lo schema Prisma come prima.

# Query
## Relazione 1:1 (Utente-Tessera)

1. Trova tutti gli utenti che hanno una tessera valida (non scaduta)
2. Trova tutte le tessere che scadranno nei prossimi 30 giorni
3. Trova tutti gli utenti senza tessera
4. Trova l'utente associato a uno specifico numero tessera
5. Mostra per ogni tessera: numero, nome utente e giorni mancanti alla scadenza

## Relazione 1:N (Utente-Prestiti)

1. Trova tutti i prestiti attivi di un utente specifico
2. Trova tutti gli utenti che hanno almeno un prestito in corso
3. Trova i libri che non sono mai stati presi in prestito
4. Trova l'utente che ha effettuato più prestiti
5. Trova tutti i prestiti che durano da più di 14 giorni
6. Mostra il numero di prestiti per ogni utente
7. Trova l'ultimo prestito di ogni utente
8. Per ogni utente, mostra il libro preso in prestito più a lungo

## Relazione N:N (Libri-Autori)

1. Trova tutti i libri di un autore specifico
2. Trova tutti gli autori che hanno scritto più di 3 libri
3. Trova tutti i libri che hanno più di un autore
4. Trova tutte le coppie di autori che hanno collaborato insieme
5. Trova i libri che sono stati scritti da un autore specifico E sono attualmente in prestito
6. Trova l'autore con più libri prestati
7. Per ogni autore, mostra il numero medio di prestiti dei suoi libri
8. Trova gli autori che hanno libri in comune con un autore specifico