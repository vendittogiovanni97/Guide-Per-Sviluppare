# Guida Completa TypeScript: Classi, Types ed Enum

## Indice
1. [Tipi Base](#tipi-base)
2. [Interfacce](#interfacce)
3. [Types](#types)
4. [Classi](#classi)
5. [Enum](#enum)
6. [Generics](#generics)
7. [Utility Types](#utility-types)
8. [Best Practices](#best-practices)

## Tipi Base

TypeScript fornisce diversi tipi primitivi:

```typescript
// Tipi primitivi
let stringa: string = "Hello";
let numero: number = 42;
let booleano: boolean = true;
let nullo: null = null;
let indefinito: undefined = undefined;
let qualsiasi: any = "qualsiasi cosa";
let sconosciuto: unknown = 4;
let vuoto: void = undefined;
let never: never; // Non può mai avere un valore

// Array
let numeri: number[] = [1, 2, 3];
let stringhe: Array<string> = ["a", "b", "c"];

// Tuple
let tupla: [string, number] = ["età", 25];

// Union Types
let stringaONumero: string | number = "42";
```

## Interfacce

Le interfacce definiscono la struttura degli oggetti:

```typescript
interface Persona {
  nome: string;
  età: number;
  indirizzo?: string; // Proprietà opzionale
  readonly id: number; // Proprietà di sola lettura
}

interface Dipendente extends Persona {
  stipendio: number;
  ruolo: string;
}

// Implementazione
const dipendente: Dipendente = {
  nome: "Mario",
  età: 30,
  id: 1,
  stipendio: 30000,
  ruolo: "Sviluppatore"
};
```

## Types

I Types sono simili alle interfacce ma offrono maggiore flessibilità:

```typescript
// Type Alias
type StringOrNumber = string | number;

// Intersection Types
type Admin = Persona & { permessi: string[] };

// Mapped Types
type Readonly<T> = {
  readonly [P in keyof T]: T[P];
};

// Conditional Types
type IsString<T> = T extends string ? true : false;

// Template Literal Types
type EmailLocale = `${string}@${string}.${string}`;
```

## Classi

TypeScript supporta la programmazione orientata agli oggetti:

```typescript
abstract class Veicolo {
  protected constructor(
    protected readonly marca: string,
    private _modello: string
  ) {}

  abstract avvia(): void;

  // Getter
  get modello(): string {
    return this._modello;
  }

  // Setter
  set modello(value: string) {
    this._modello = value;
  }
}

class Auto extends Veicolo {
  constructor(
    marca: string,
    modello: string,
    private _numeroDiPorte: number
  ) {
    super(marca, modello);
  }

  avvia(): void {
    console.log(`Avvio ${this.marca} ${this.modello}`);
  }

  // Metodo statico
  static isAuto(veicolo: any): veicolo is Auto {
    return veicolo instanceof Auto;
  }
}

// Implementazione di interfacce
interface VeicoloElettrico {
  carica(): void;
}

class AutoElettrica extends Auto implements VeicoloElettrico {
  constructor(
    marca: string,
    modello: string,
    numeroDiPorte: number,
    private capacitàBatteria: number
  ) {
    super(marca, modello, numeroDiPorte);
  }

  carica(): void {
    console.log("Ricarica in corso...");
  }
}
```

## Enum

Gli enum permettono di definire un insieme di costanti nominate:

```typescript
// Enum numerico
enum Direzione {
  Su,      // 0
  Giù,     // 1
  Sinistra,// 2
  Destra   // 3
}

// Enum string
enum TipoUtente {
  Admin = "ADMIN",
  User = "USER",
  Guest = "GUEST"
}

// Enum constant
const enum StatusCode {
  OK = 200,
  NotFound = 404,
  Error = 500
}

// Enum calcolato
enum FileAccess {
  None = 0,
  Read = 1 << 0,    // 1
  Write = 1 << 1,   // 2
  ReadWrite = Read | Write
}
```

## Generics

I generics permettono di creare componenti riutilizzabili:

```typescript
// Funzione generica
function identità<T>(arg: T): T {
  return arg;
}

// Interfaccia generica
interface Contenitore<T> {
  valore: T;
}

// Classe generica
class CodaGenerica<T> {
  private dati: T[] = [];

  push(elemento: T): void {
    this.dati.push(elemento);
  }

  pop(): T | undefined {
    return this.dati.shift();
  }
}

// Constraints
interface ConNome {
  nome: string;
}

function stampaNome<T extends ConNome>(oggetto: T): void {
  console.log(oggetto.nome);
}
```

## Utility Types

TypeScript fornisce diversi utility types incorporati:

```typescript
// Partial - Rende tutte le proprietà opzionali
interface Todo {
  titolo: string;
  descrizione: string;
}

function aggiornaTodo(todo: Todo, campiDaAggiornare: Partial<Todo>) {
  return { ...todo, ...campiDaAggiornare };
}

// Required - Rende tutte le proprietà obbligatorie
interface Props {
  a?: number;
  b?: string;
}

const obj: Required<Props> = { a: 5, b: "string" };

// Record - Crea un tipo con un insieme di proprietà
type PageInfo = {
  titolo: string;
};

type Page = "home" | "about" | "contact";

const nav: Record<Page, PageInfo> = {
  home: { titolo: "Home" },
  about: { titolo: "About" },
  contact: { titolo: "Contact" }
};

// Pick - Seleziona un sottoinsieme di proprietà
interface Todo {
  titolo: string;
  descrizione: string;
  completato: boolean;
}

type TodoPreview = Pick<Todo, "titolo" | "completato">;

// Omit - Rimuove proprietà specifiche
type TodoSenzaDescrizione = Omit<Todo, "descrizione">;
```

## Best Practices

1. **Usa strict mode**
```typescript
// tsconfig.json
{
  "compilerOptions": {
    "strict": true
  }
}
```

2. **Evita any**
```typescript
// ❌ Sbagliato
function processo(data: any) {
  return data.length;
}

// ✅ Corretto
function processo<T extends { length: number }>(data: T) {
  return data.length;
}
```

3. **Usa type inference quando possibile**
```typescript
// ❌ Ridondante
let messaggio: string = "Hello";

// ✅ Corretto
let messaggio = "Hello"; // TypeScript inferisce string
```

4. **Usa interfacce per oggetti pubblici API**
```typescript
// Definisce un contratto chiaro
interface UserAPI {
  getUser(id: string): Promise<User>;
  updateUser(id: string, data: Partial<User>): Promise<User>;
}
```

5. **Usa type guards per narrowing**
```typescript
function isString(value: unknown): value is string {
  return typeof value === "string";
}

function processo(value: unknown) {
  if (isString(value)) {
    // TypeScript sa che value è una string
    return value.toUpperCase();
  }
}
```

