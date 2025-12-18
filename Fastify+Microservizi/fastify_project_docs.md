# 📘 Documentazione progetto Fastify Modulare

Questa documentazione raccoglie tutte le funzionalità, rotte, plugin e decisioni implementative nel progetto backend **Fastify + TypeScript**.

---

## ✅ Stack iniziale

- **Fastify**: framework web veloce e minimale
- **TypeScript**: tipizzazione forte e sviluppo robusto
- **Zod**: validazione dati lato server
- **@fastify/jwt**: gestione token JWT
- **@fastify/swagger**: documentazione API automatica
- **@fastify/autoload**: caricamento automatico dei moduli
- **Dotenv**: gestione variabili ambientali

---

## 📁 Struttura del progetto

```
src/
├── server.ts            # Entrypoint dell'app
├── app.ts               # Caricamento plugin + moduli
├── plugins/             # Plugin Fastify (JWT, Swagger, Zod)
│   ├── jwt.ts
│   ├── swagger.ts
│   └── validator.ts
├── modules/             # Moduli organizzati per dominio
│   └── user/            # Modulo utenti (esempio)
│       ├── index.ts
│       ├── routes.ts
│       ├── controller.ts
│       └── schema.ts
```

---

## 🔐 Autenticazione JWT

- Plugin: `@fastify/jwt`
- Secret salvato in `.env`
- Middleware `fastify.authenticate` disponibile per proteggere rotte

### 📌 Rotta protetta esempio

```ts
fastify.get('/me', { preHandler: [fastify.authenticate] }, async (req, res) => {
  return req.user;
});
```

---

## 📃 Documentazione Swagger

- Accessibile su `http://localhost:3000/docs`
- Auto-generata tramite `@fastify/swagger` e `@fastify/swagger-ui`
- Schema Zod convertito in `openapi()` (con `zod-to-openapi` se aggiunto)

---

## ✅ Validazione con Zod

- Ogni modulo definisce il proprio `schema.ts`
- In `controller.ts` viene usato `.parse()` o `.safeParse()`

### Esempio:

```ts
const data = userSchema.parse(req.body);
```

---

## 🧪 Modulo `user`

- `GET /users`: lista utenti
- `POST /users`: crea utente con validazione Zod

---

## 🛣️ Roadmap futura (da implementare)

-

---

## 🧠 Note personali (puoi aggiungere qui)

-

---

> Aggiorna questa documentazione man mano che aggiungi funzionalità!

