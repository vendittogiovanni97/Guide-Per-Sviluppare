# 🚀 Guida Completa a React Router con TypeScript

## 📌 Introduzione

React Router è la libreria ufficiale per la gestione delle rotte in un'applicazione React. Permette di navigare tra diverse pagine o componenti senza ricaricare l'intera applicazione. In questa guida vedremo come utilizzare **React Router con TypeScript**, creando rotte, parametri dinamici, protezione delle pagine e navigazione programmatica.

---

## 📦 **Installazione di React Router**

Per iniziare, installiamo la libreria **React Router DOM**:

```sh
npm install react-router-dom
```

Se stai usando TypeScript, installa anche i tipi di React Router:

```sh
npm install @types/react-router-dom --save-dev
```

---

## 🏗 **Configurare React Router in un Progetto TypeScript**

Creiamo un'applicazione base con più pagine.

### **1️⃣ Definizione delle Pagine**

Creiamo alcune pagine semplici:

📂 **src/pages/Home.tsx**
```tsx
const Home: React.FC = () => {
  return <h1>🏠 Home Page</h1>;
};

export default Home;
```

📂 **src/pages/About.tsx**
```tsx
const About: React.FC = () => {
  return <h1>ℹ️ About Page</h1>;
};

export default About;
```

📂 **src/pages/NotFound.tsx**
```tsx
const NotFound: React.FC = () => {
  return <h1>❌ Pagina non trovata</h1>;
};

export default NotFound;
```

---

### **2️⃣ Configurazione delle Rotte**

Creiamo un file dedicato alla gestione delle rotte.

📂 **src/App.tsx**
```tsx
import React from "react";
import { BrowserRouter as Router, Routes, Route } from "react-router-dom";
import Home from "./pages/Home";
import About from "./pages/About";
import NotFound from "./pages/NotFound";

const App: React.FC = () => {
  return (
    <Router>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="*" element={<NotFound />} />
      </Routes>
    </Router>
  );
};

export default App;
```

> 🔹 `BrowserRouter` avvolge l'app per gestire la navigazione.
> 🔹 `Routes` contiene tutte le rotte.
> 🔹 `Route` definisce le singole pagine con `path` e `element`.

---

## 🔄 **Navigazione tra Pagine**

Per navigare tra le pagine, usiamo il componente `Link`.

📂 **src/components/Navbar.tsx**
```tsx
import { Link } from "react-router-dom";

const Navbar: React.FC = () => {
  return (
    <nav>
      <ul>
        <li><Link to="/">Home</Link></li>
        <li><Link to="/about">About</Link></li>
      </ul>
    </nav>
  );
};

export default Navbar;
```

Ora includiamo la `Navbar` in **App.tsx**:

```tsx
import Navbar from "./components/Navbar";

const App: React.FC = () => {
  return (
    <Router>
      <Navbar />
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="*" element={<NotFound />} />
      </Routes>
    </Router>
  );
};
```

---

## 🔢 **Rotte con Parametri Dinamici**

Creiamo una pagina dinamica con un **ID utente**:

📂 **src/pages/Profile.tsx**
```tsx
import { useParams } from "react-router-dom";

type ProfileParams = {
  id: string;
};

const Profile: React.FC = () => {
  const { id } = useParams<ProfileParams>();
  return <h1>👤 Profilo Utente: {id}</h1>;
};

export default Profile;
```

Aggiungiamo la rotta nel nostro `App.tsx`:

```tsx
<Route path="/profile/:id" element={<Profile />} />
```

Ora possiamo visitare `http://localhost:3000/profile/42` e vedremo `👤 Profilo Utente: 42`.

---

## 🔐 **Rotte Protette (Protected Routes)**

Spesso alcune pagine devono essere accessibili solo agli utenti autenticati. Creiamo un wrapper per proteggere le rotte.

📂 **src/components/ProtectedRoute.tsx**
```tsx
import { Navigate } from "react-router-dom";

type ProtectedRouteProps = {
  children: JSX.Element;
};

const isAuthenticated = () => {
  return !!localStorage.getItem("authToken");
};

const ProtectedRoute: React.FC<ProtectedRouteProps> = ({ children }) => {
  return isAuthenticated() ? children : <Navigate to="/" />;
};

export default ProtectedRoute;
```

Utilizziamo `ProtectedRoute` per proteggere la pagina Profilo:

```tsx
<Route path="/profile/:id" element={<ProtectedRoute><Profile /></ProtectedRoute>} />
```

Se l'utente non è autenticato, viene reindirizzato alla home.

---

## 📡 **Navigazione Programmata con `useNavigate`**

Possiamo navigare tra le pagine in modo programmatico.

📂 **src/pages/Login.tsx**
```tsx
import { useNavigate } from "react-router-dom";

const Login: React.FC = () => {
  const navigate = useNavigate();

  const handleLogin = () => {
    localStorage.setItem("authToken", "12345");
    navigate("/profile/42");
  };

  return (
    <div>
      <h1>🔑 Login Page</h1>
      <button onClick={handleLogin}>Login</button>
    </div>
  );
};

export default Login;
```

Ora aggiungiamo la rotta in `App.tsx`:

```tsx
<Route path="/login" element={<Login />} />
```

Dopo il login, l'utente viene reindirizzato al proprio profilo.

---

## 🎯 **Conclusione**

Abbiamo visto come configurare **React Router con TypeScript**, gestire rotte dinamiche, proteggere pagine e navigare in modo programmatico. Ora puoi implementare queste funzionalità nel tuo progetto! 🚀

