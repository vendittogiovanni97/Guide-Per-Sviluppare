# Autenticazione e Gestione Sessioni in TypeScript con Zod

## Indice
1. [Setup del Progetto](#setup-del-progetto)
2. [Schema Validazione con Zod](#schema-validazione-con-zod)
3. [Modelli Database](#modelli-database)
4. [Autenticazione JWT](#autenticazione-jwt)
5. [Gestione Sessioni](#gestione-sessioni)
6. [Middleware](#middleware)
7. [Controller](#controller)
8. [Rate Limiting](#rate-limiting)
9. [Security Best Practices](#security-best-practices)

## Setup del Progetto

### Dipendenze Necessarie
```bash
npm init -y
npm install express zod jsonwebtoken bcrypt express-session
npm install -D typescript @types/express @types/node @types/jsonwebtoken @types/bcrypt @types/express-session
```

### Configurazione TypeScript
```json
// tsconfig.json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "outDir": "./dist",
    "rootDir": "./src",
    "moduleResolution": "node"
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules"]
}
```

## Schema Validazione con Zod

```typescript
// src/schemas/auth.schema.ts
import { z } from 'zod';

export const registerSchema = z.object({
  email: z.string().email('Email non valida'),
  password: z.string()
    .min(8, 'La password deve essere di almeno 8 caratteri')
    .regex(/[A-Z]/, 'La password deve contenere almeno una lettera maiuscola')
    .regex(/[0-9]/, 'La password deve contenere almeno un numero')
    .regex(/[^A-Za-z0-9]/, 'La password deve contenere almeno un carattere speciale'),
  name: z.string().min(2, 'Il nome deve essere di almeno 2 caratteri'),
});

export const loginSchema = z.object({
  email: z.string().email('Email non valida'),
  password: z.string().min(1, 'Password richiesta'),
});

export type RegisterInput = z.infer<typeof registerSchema>;
export type LoginInput = z.infer<typeof loginSchema>;
```

## Modelli Database

```typescript
// src/models/user.model.ts
import { z } from 'zod';

export const UserSchema = z.object({
  id: z.string().uuid(),
  email: z.string().email(),
  password: z.string(),
  name: z.string(),
  role: z.enum(['user', 'admin']),
  createdAt: z.date(),
  updatedAt: z.date(),
});

export type User = z.infer<typeof UserSchema>;

// src/models/session.model.ts
export const SessionSchema = z.object({
  id: z.string().uuid(),
  userId: z.string().uuid(),
  token: z.string(),
  expiresAt: z.date(),
  createdAt: z.date(),
});

export type Session = z.infer<typeof SessionSchema>;
```

## Autenticazione JWT

```typescript
// src/utils/jwt.ts
import jwt from 'jsonwebtoken';
import { z } from 'zod';

const JWT_SECRET = process.env.JWT_SECRET || 'your-secret-key';

const JWTPayloadSchema = z.object({
  userId: z.string(),
  email: z.string(),
  role: z.string(),
});

type JWTPayload = z.infer<typeof JWTPayloadSchema>;

export const generateToken = (payload: JWTPayload): string => {
  return jwt.sign(payload, JWT_SECRET, { expiresIn: '1d' });
};

export const verifyToken = (token: string): JWTPayload => {
  try {
    const decoded = jwt.verify(token, JWT_SECRET);
    const result = JWTPayloadSchema.safeParse(decoded);
    
    if (!result.success) {
      throw new Error('Invalid token payload');
    }
    
    return result.data;
  } catch (error) {
    throw new Error('Invalid token');
  }
};
```

## Gestione Sessioni

```typescript
// src/services/session.service.ts
import { Session, SessionSchema } from '../models/session.model';
import { redis } from '../config/redis';

export class SessionService {
  private readonly PREFIX = 'session:';
  private readonly EXPIRATION = 24 * 60 * 60; // 24 ore in secondi

  async createSession(userId: string, token: string): Promise<Session> {
    const session: Session = {
      id: crypto.randomUUID(),
      userId,
      token,
      expiresAt: new Date(Date.now() + this.EXPIRATION * 1000),
      createdAt: new Date(),
    };

    const validatedSession = SessionSchema.parse(session);
    
    await redis.setex(
      `${this.PREFIX}${session.id}`,
      this.EXPIRATION,
      JSON.stringify(validatedSession)
    );

    return validatedSession;
  }

  async getSession(sessionId: string): Promise<Session | null> {
    const sessionData = await redis.get(`${this.PREFIX}${sessionId}`);
    
    if (!sessionData) return null;

    const session = SessionSchema.safeParse(JSON.parse(sessionData));
    return session.success ? session.data : null;
  }

  async deleteSession(sessionId: string): Promise<void> {
    await redis.del(`${this.PREFIX}${sessionId}`);
  }
}
```

## Middleware

```typescript
// src/middleware/auth.middleware.ts
import { Request, Response, NextFunction } from 'express';
import { verifyToken } from '../utils/jwt';
import { SessionService } from '../services/session.service';

const sessionService = new SessionService();

export const authenticate = async (
  req: Request,
  res: Response,
  next: NextFunction
) => {
  try {
    const token = req.headers.authorization?.split(' ')[1];
    
    if (!token) {
      return res.status(401).json({ message: 'Token non fornito' });
    }

    const payload = verifyToken(token);
    const session = await sessionService.getSession(payload.userId);

    if (!session || session.token !== token) {
      return res.status(401).json({ message: 'Sessione non valida' });
    }

    req.user = payload;
    next();
  } catch (error) {
    res.status(401).json({ message: 'Non autorizzato' });
  }
};

// src/middleware/validate.middleware.ts
import { Request, Response, NextFunction } from 'express';
import { AnyZodObject } from 'zod';

export const validate = (schema: AnyZodObject) => {
  return async (req: Request, res: Response, next: NextFunction) => {
    try {
      await schema.parseAsync({
        body: req.body,
        query: req.query,
        params: req.params,
      });
      next();
    } catch (error) {
      res.status(400).json(error);
    }
  };
};
```

## Controller

```typescript
// src/controllers/auth.controller.ts
import { Request, Response } from 'express';
import bcrypt from 'bcrypt';
import { UserService } from '../services/user.service';
import { SessionService } from '../services/session.service';
import { generateToken } from '../utils/jwt';
import { loginSchema, registerSchema } from '../schemas/auth.schema';

export class AuthController {
  constructor(
    private userService: UserService,
    private sessionService: SessionService
  ) {}

  async register(req: Request, res: Response) {
    try {
      const validatedData = registerSchema.parse(req.body);
      
      const existingUser = await this.userService.findByEmail(validatedData.email);
      if (existingUser) {
        return res.status(400).json({ message: 'Email già registrata' });
      }

      const hashedPassword = await bcrypt.hash(validatedData.password, 10);
      const user = await this.userService.create({
        ...validatedData,
        password: hashedPassword,
      });

      const token = generateToken({
        userId: user.id,
        email: user.email,
        role: user.role,
      });

      await this.sessionService.createSession(user.id, token);

      res.status(201).json({
        token,
        user: {
          id: user.id,
          email: user.email,
          name: user.name,
        },
      });
    } catch (error) {
      res.status(400).json(error);
    }
  }

  async login(req: Request, res: Response) {
    try {
      const validatedData = loginSchema.parse(req.body);
      
      const user = await this.userService.findByEmail(validatedData.email);
      if (!user) {
        return res.status(401).json({ message: 'Credenziali non valide' });
      }

      const isPasswordValid = await bcrypt.compare(
        validatedData.password,
        user.password
      );

      if (!isPasswordValid) {
        return res.status(401).json({ message: 'Credenziali non valide' });
      }

      const token = generateToken({
        userId: user.id,
        email: user.email,
        role: user.role,
      });

      await this.sessionService.createSession(user.id, token);

      res.json({
        token,
        user: {
          id: user.id,
          email: user.email,
          name: user.name,
        },
      });
    } catch (error) {
      res.status(400).json(error);
    }
  }

  async logout(req: Request, res: Response) {
    try {
      const userId = req.user.userId;
      await this.sessionService.deleteSession(userId);
      res.json({ message: 'Logout effettuato con successo' });
    } catch (error) {
      res.status(500).json({ message: 'Errore durante il logout' });
    }
  }
}
```

## Rate Limiting

```typescript
// src/middleware/rateLimiter.middleware.ts
import rateLimit from 'express-rate-limit';
import RedisStore from 'rate-limit-redis';
import { redis } from '../config/redis';

export const loginLimiter = rateLimit({
  store: new RedisStore({
    client: redis,
    prefix: 'rate-limit:login:',
  }),
  windowMs: 15 * 60 * 1000, // 15 minuti
  max: 5, // limite di 5 tentativi
  message: 'Troppi tentativi di login. Riprova tra 15 minuti.',
});

export const apiLimiter = rateLimit({
  store: new RedisStore({
    client: redis,
    prefix: 'rate-limit:api:',
  }),
  windowMs: 60 * 1000, // 1 minuto
  max: 100, // limite di 100 richieste
  message: 'Troppe richieste. Riprova tra un minuto.',
});
```

## Security Best Practices

```typescript
// src/config/security.ts
import helmet from 'helmet';
import { Express } from 'express';
import cors from 'cors';
import { z } from 'zod';

const SecurityConfigSchema = z.object({
  cors: z.object({
    origin: z.union([z.string(), z.array(z.string())]),
    methods: z.array(z.string()),
    credentials: z.boolean(),
  }),
  csrf: z.boolean(),
  rateLimit: z.object({
    windowMs: z.number(),
    max: z.number(),
  }),
});

type SecurityConfig = z.infer<typeof SecurityConfigSchema>;

export const configSecurity = (app: Express, config: SecurityConfig) => {
  // Helmet per headers di sicurezza
  app.use(helmet());

  // CORS
  app.use(cors(config.cors));

  // CSRF Protection se abilitata
  if (config.csrf) {
    const csrf = require('csurf');
    app.use(csrf({ cookie: true }));
  }

  // Cookie sicuri
  app.use(session({
    secret: process.env.SESSION_SECRET || 'secure-secret',
    name: 'sessionId',
    cookie: {
      secure: process.env.NODE_ENV === 'production',
      httpOnly: true,
      sameSite: 'strict',
      maxAge: 24 * 60 * 60 * 1000 // 24 ore
    },
    resave: false,
    saveUninitialized: false
  }));

  // XSS Protection
  app.use(helmet.xssFilter());

  // Content Security Policy
  app.use(
    helmet.contentSecurityPolicy({
      directives: {
        defaultSrc: ["'self'"],
        scriptSrc: ["'self'"],
        styleSrc: ["'self'", "'unsafe-inline'"],
        imgSrc: ["'self'", 'data:', 'https:'],
        connectSrc: ["'self'"],
        fontSrc: ["'self'"],
        objectSrc: ["'none'"],
        mediaSrc: ["'self'"],
        frameSrc: ["'none'"],
      },
    })
  );
};
```

### Esempio di Implementazione Completa

```typescript
// src/index.ts
import express from 'express';
import { configSecurity } from './config/security';
import { apiLimiter, loginLimiter } from './middleware/rateLimiter.middleware';
import { authenticate } from './middleware/auth.middleware';
import { validate } from './middleware/validate.middleware';
import { AuthController } from './controllers/auth.controller';
import { UserService } from './services/user.service';
import { SessionService } from './services/session.service';

const app = express();

// Configurazione sicurezza
configSecurity(app, {
  cors: {
    origin: process.env.ALLOWED_ORIGINS?.split(',') || 'http://localhost:3000',
    methods: ['GET', 'POST', 'PUT', 'DELETE'],
    credentials: true,
  },
  csrf: true,
  rateLimit: {
    windowMs: 15 * 60 * 1000,
    max: 100,
  },
});

// Servizi
const userService = new UserService();
const sessionService = new SessionService();
const authController = new AuthController(userService, sessionService);

// Routes
app.post(
  '/api/auth/register',
  validate(registerSchema),
  (req, res) => authController.register(req, res)
);

app.post(
  '/api/auth/login',
  loginLimiter,
  validate(loginSchema),
  (req, res) => authController.login(req, res)
);

app.post(
  '/api/auth/logout',
  authenticate,
  (req, res) =>