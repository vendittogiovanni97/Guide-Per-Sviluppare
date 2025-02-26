# Guida Completa a NestJS

## Introduzione
NestJS è un framework per la creazione di applicazioni server-side efficienti e scalabili in Node.js. È costruito con TypeScript e incorpora elementi di Programmazione Orientata agli Oggetti (OOP), Programmazione Funzionale (FP) e Programmazione Funzionale Reattiva (FRP).

## Installazione e Setup

### Prerequisiti
- Node.js (versione 12 o superiore)
- npm (Node Package Manager)

### Creazione Nuovo Progetto
```bash
# Installazione del CLI di NestJS
npm i -g @nestjs/cli

# Creazione nuovo progetto
nest new nome-progetto

# Avvio dell'applicazione in modalità sviluppo
npm run start:dev
```

## Concetti Fondamentali

### 1. Moduli
I moduli sono la struttura base di un'applicazione NestJS. Ogni applicazione ha almeno un modulo root.

```typescript
@Module({
  imports: [],      // Altri moduli importati
  controllers: [],  // Controller del modulo
  providers: [],    // Service e altri provider
  exports: []       // Provider da esportare
})
export class AppModule {}
```

### 2. Controllers
I controller gestiscono le richieste HTTP e definiscono i route dell'applicazione.

```typescript
@Controller('users')
export class UsersController {
  @Get()
  findAll(): string {
    return 'Lista di tutti gli utenti';
  }

  @Get(':id')
  findOne(@Param('id') id: string): string {
    return `Utente con ID ${id}`;
  }
}
```

### 3. Providers (Services)
I providers implementano la logica di business dell'applicazione.

```typescript
@Injectable()
export class UsersService {
  private users = [];

  findAll() {
    return this.users;
  }

  findOne(id: string) {
    return this.users.find(user => user.id === id);
  }
}
```

### 4. Pipes
Le pipes trasformano o validano i dati prima che raggiungano il controller.

```typescript
@Post()
createUser(@Body(ValidationPipe) createUserDto: CreateUserDto) {
  return this.usersService.create(createUserDto);
}
```

### 5. Guards
I guards determinano se una richiesta può essere gestita dal route handler.

```typescript
@Injectable()
export class AuthGuard implements CanActivate {
  canActivate(context: ExecutionContext): boolean {
    // Logica di autenticazione
    return true;
  }
}
```

## Database Integration

### MongoDB con Mongoose
```typescript
// app.module.ts
import { MongooseModule } from '@nestjs/mongoose';

@Module({
  imports: [
    MongooseModule.forRoot('mongodb://localhost/nest')
  ]
})
export class AppModule {}

// schema definizione
export class Cat {
  @Prop()
  name: string;

  @Prop()
  age: number;
}

export const CatSchema = SchemaFactory.createForClass(Cat);
```

## Middleware
I middleware sono funzioni che hanno accesso agli oggetti request e response.

```typescript
@Injectable()
export class LoggerMiddleware implements NestMiddleware {
  use(req: Request, res: Response, next: Function) {
    console.log('Request...');
    next();
  }
}
```

## Exception Handling
NestJS fornisce un layer integrato per la gestione delle eccezioni.

```typescript
// Exception personalizzata
export class UserNotFoundException extends HttpException {
  constructor() {
    super('User not found', HttpStatus.NOT_FOUND);
  }
}

// Uso nel controller
@Get(':id')
findOne(@Param('id') id: string) {
  const user = this.usersService.findOne(id);
  if (!user) {
    throw new UserNotFoundException();
  }
  return user;
}
```

## DTOs (Data Transfer Objects)
I DTOs definiscono la struttura dei dati che vengono scambiati tra client e server.

```typescript
export class CreateUserDto {
  @IsString()
  @IsNotEmpty()
  readonly name: string;

  @IsEmail()
  readonly email: string;

  @IsString()
  @MinLength(6)
  readonly password: string;
}
```

## Validation
NestJS usa class-validator per la validazione dei dati.

```typescript
// main.ts
async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  app.useGlobalPipes(new ValidationPipe());
  await app.listen(3000);
}
```

## Testing
NestJS supporta unit testing e e2e testing out of the box.

```typescript
// users.service.spec.ts
describe('UsersService', () => {
  let service: UsersService;

  beforeEach(async () => {
    const module = await Test.createTestingModule({
      providers: [UsersService],
    }).compile();

    service = module.get<UsersService>(UsersService);
  });

  it('should be defined', () => {
    expect(service).toBeDefined();
  });
});
```

## Best Practices

1. **Struttura del Progetto**
   - Organizzare il codice per feature/modulo
   - Mantenere i file correlati vicini
   - Utilizzare barrel files (index.ts) per l'esportazione

2. **Nomenclatura**
   - Utilizzare suffissi descrittivi (.service.ts, .controller.ts)
   - Seguire le convenzioni di NestJS per i nomi
   - Usare nomi descrittivi per classi e metodi

3. **Dependency Injection**
   - Utilizzare l'injection token corretto
   - Evitare dipendenze circolari
   - Preferire constructor injection

4. **Error Handling**
   - Utilizzare exception filters personalizzati
   - Gestire gli errori al livello appropriato
   - Fornire messaggi di errore significativi

## Deployment
```bash
# Build per produzione
npm run build

# Start in produzione
npm run start:prod
```

## Risorse Utili
- [Documentazione Ufficiale](https://docs.nestjs.com/)
- [Repository GitHub](https://github.com/nestjs/nest)
- [Gitter Community](https://gitter.im/nestjs/nestjs)

## Conclusione
NestJS fornisce un ecosistema robusto per lo sviluppo di applicazioni backend. La sua architettura modulare e l'uso di TypeScript lo rendono ideale per progetti enterprise e applicazioni scalabili.
