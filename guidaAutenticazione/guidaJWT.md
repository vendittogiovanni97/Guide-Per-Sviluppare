# JSON Web Token (JWT) - Guida Completa

## 📌 Introduzione

Il **JSON Web Token (JWT)** è uno standard aperto (**RFC 7519**) utilizzato per la creazione di **token di autenticazione** sicuri e compatti.
È molto utilizzato nelle applicazioni web per garantire la sicurezza delle API, evitando l’uso delle sessioni tradizionali lato server.

---

## 🎯 **Cos'è un JWT?**

Un **JWT** è una stringa compatta che rappresenta in modo sicuro delle **informazioni (claims)** tra due parti, tipicamente un **client** e un **server**.

### **Struttura di un JWT**
Un token JWT è composto da **tre parti** separate da un punto (`.`):  

```
HEADER.PAYLOAD.SIGNATURE
```

Esempio di JWT:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOiIxMjM0NTY3ODkwIiwicm9sZSI6InVzZXIiLCJpYXQiOjE2ODQ5ODk1NjAsImV4cCI6MTY4NDk5MzE2MH0.4Bvvb4jEdXl1kVDFOHR6lzoU1bmdbu_PcbqR3eQ6MXY
```

Ogni parte ha un ruolo specifico:

### **1️⃣ Header** (Intestazione)
Contiene le informazioni sull’algoritmo di firma e sul tipo di token.

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```
- `alg`: algoritmo di firma, es. **HS256** (HMAC SHA-256).
- `typ`: tipo di token (JWT).

### **2️⃣ Payload** (Dati contenuti nel token)
Contiene le **informazioni (claims)** che vogliamo trasmettere.

```json
{
  "userId": "1234567890",
  "role": "user",
  "iat": 1684989560,
  "exp": 1684993160
}
```
- `userId`: ID dell’utente.
- `role`: Ruolo dell’utente nel sistema.
- `iat` (issued at): Data di emissione del token (Unix timestamp).
- `exp` (expiration): Data di scadenza del token (Unix timestamp).

### **3️⃣ Signature** (Firma del Token)
La firma garantisce l’integrità del token.

Formula:

```
HMACSHA256(
  Base64UrlEncode(header) + "." + Base64UrlEncode(payload),
  secret
)
```

---

## 🔐 **Come Funziona JWT?**

1️⃣ **Autenticazione:**
- L’utente invia username e password.
- Il server verifica le credenziali e genera un **JWT**.
- Il token viene restituito al client.

2️⃣ **Uso del Token:**
- Il client include il token in ogni richiesta HTTP.
- Il server verifica il token prima di concedere l’accesso.

3️⃣ **Scadenza e Rinnovo:**
- Il token ha una durata (`exp`).
- Quando scade, il client deve rinnovarlo.

---

## 🚀 **Utilizzo di JWT in Express.js**

### **1️⃣ Installare le dipendenze**

```sh
npm install express jsonwebtoken dotenv cors
```

### **2️⃣ Creazione del Token**

```javascript
const express = require('express');
const jwt = require('jsonwebtoken');
const app = express();
const SECRET_KEY = "supersegreta";

app.use(express.json());

app.post('/login', (req, res) => {
    const user = { id: 1, username: "Mario" };
    const token = jwt.sign(user, SECRET_KEY, { expiresIn: "1h" });
    res.json({ token });
});

app.listen(3000, () => console.log("Server avviato su porta 3000"));
```

### **3️⃣ Middleware per la Verifica del Token**

```javascript
const authenticateJWT = (req, res, next) => {
    const token = req.header("Authorization")?.split(" ")[1];
    if (!token) return res.status(401).json({ message: "Accesso negato" });

    jwt.verify(token, SECRET_KEY, (err, user) => {
        if (err) return res.status(403).json({ message: "Token non valido" });
        req.user = user;
        next();
    });
};

app.get("/private", authenticateJWT, (req, res) => {
    res.json({ message: "Accesso autorizzato", user: req.user });
});
```

---

## ⚡ **Utilizzo di JWT in NestJS**

### **1️⃣ Installare le dipendenze**

```sh
npm install @nestjs/jwt @nestjs/passport passport passport-jwt
```

### **2️⃣ Configurazione del Modulo Auth**

```typescript
import { Module } from '@nestjs/common';
import { JwtModule } from '@nestjs/jwt';
import { AuthService } from './auth.service';
import { PassportModule } from '@nestjs/passport';
import { JwtStrategy } from './jwt.strategy';

@Module({
  imports: [
    PassportModule,
    JwtModule.register({
      secret: 'supersegreta',
      signOptions: { expiresIn: '1h' },
    }),
  ],
  providers: [AuthService, JwtStrategy],
})
export class AuthModule {}
```

### **3️⃣ Creazione del Token in AuthService**

```typescript
import { Injectable } from '@nestjs/common';
import { JwtService } from '@nestjs/jwt';

@Injectable()
export class AuthService {
  constructor(private jwtService: JwtService) {}

  async login(user: any) {
    const payload = { username: user.username, sub: user.userId };
    return {
      access_token: this.jwtService.sign(payload),
    };
  }
}
```

### **4️⃣ Middleware per la Verifica**

```typescript
import { Injectable } from '@nestjs/common';
import { PassportStrategy } from '@nestjs/passport';
import { ExtractJwt, Strategy } from 'passport-jwt';

@Injectable()
export class JwtStrategy extends PassportStrategy(Strategy) {
  constructor() {
    super({
      jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
      ignoreExpiration: false,
      secretOrKey: 'supersegreta',
    });
  }

  async validate(payload: any) {
    return { userId: payload.sub, username: payload.username };
  }
}
```

---

## 🎯 **Conclusione**

Il **JWT** è un sistema di autenticazione leggero, scalabile e sicuro. È ampiamente usato in API REST per autenticare utenti senza mantenere sessioni lato server.

Se vuoi approfondire ulteriormente, prova ad implementarlo in un tuo progetto! 🚀

