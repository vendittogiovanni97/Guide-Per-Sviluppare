# Guida Completa a Redux in React

## Indice
1. [Introduzione a Redux](#introduzione-a-redux)
2. [Concetti Fondamentali](#concetti-fondamentali)
3. [Redux con React](#redux-con-react)
4. [Organizzazione dei File](#organizzazione-dei-file)
5. [Implementazione Pratica](#implementazione-pratica)
6. [Best Practices](#best-practices)
7. [Esempi Avanzati](#esempi-avanzati)

## Introduzione a Redux

Redux è una libreria per la gestione dello stato in applicazioni JavaScript, particolarmente utile con React. È stata creata da Dan Abramov e si basa sui principi del pattern Flux e dell'architettura Elm.

### Perché usare Redux?

- **Stato Centralizzato**: mantiene tutto lo stato dell'applicazione in un unico store
- **Prevedibilità**: cambiamenti di stato avvengono solo attraverso azioni esplicite
- **Debugging Facilitato**: tracciamento delle modifiche allo stato
- **Middleware**: supporto per funzionalità come gestione di effetti collaterali (API calls)
- **Time Travel Debugging**: possibilità di "viaggiare nel tempo" tra diversi stati

## Concetti Fondamentali

### 1. Store

Lo store è un oggetto che contiene lo stato dell'intera applicazione. È l'unica fonte di verità e può essere accessibile da qualsiasi componente.

```javascript
import { createStore } from 'redux';
import rootReducer from './reducers';

const store = createStore(rootReducer);
```

### 2. Actions

Le actions sono oggetti JavaScript che descrivono cosa è successo. Hanno sempre una proprietà `type` e possono includere dati aggiuntivi.

```javascript
// Action creator
const addTodo = (text) => {
  return {
    type: 'ADD_TODO',
    payload: {
      text,
      completed: false
    }
  };
};
```

### 3. Reducers

I reducers sono funzioni pure che specificano come lo stato cambia in risposta a un'action. Prendono lo stato corrente e un'action, e restituiscono un nuovo stato.

```javascript
const initialState = {
  todos: []
};

const todoReducer = (state = initialState, action) => {
  switch (action.type) {
    case 'ADD_TODO':
      return {
        ...state,
        todos: [...state.todos, action.payload]
      };
    default:
      return state;
  }
};
```

### 4. Dispatch

Il metodo `dispatch` è il modo in cui invii le actions allo store. Quando chiami `dispatch` con un'action, lo store esegue il reducer con l'action corrente e lo stato corrente.

```javascript
store.dispatch(addTodo('Imparare Redux'));
```

## Redux con React

Per utilizzare Redux con React, utilizziamo `react-redux`, che fornisce componenti e hook per connettere i componenti React allo store Redux.

### Provider

Il componente `Provider` rende lo store disponibile a tutti i componenti annidati:

```jsx
import { Provider } from 'react-redux';
import store from './store';

function App() {
  return (
    <Provider store={store}>
      <TodoApp />
    </Provider>
  );
}
```

### Hooks di React-Redux

Modern React-Redux utilizza hook per connettere i componenti allo store:

#### useSelector

Seleziona dati dallo stato:

```jsx
import { useSelector } from 'react-redux';

function TodoList() {
  const todos = useSelector(state => state.todos);
  
  return (
    <ul>
      {todos.map(todo => (
        <li key={todo.id}>{todo.text}</li>
      ))}
    </ul>
  );
}
```

#### useDispatch

Accede alla funzione `dispatch` dello store:

```jsx
import { useDispatch } from 'react-redux';
import { addTodo } from './actions';

function AddTodo() {
  const [text, setText] = useState('');
  const dispatch = useDispatch();
  
  const handleSubmit = e => {
    e.preventDefault();
    if (!text.trim()) return;
    dispatch(addTodo(text));
    setText('');
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <input value={text} onChange={e => setText(e.target.value)} />
      <button type="submit">Add Todo</button>
    </form>
  );
}
```

## Organizzazione dei File

Una buona struttura di cartelle aiuta a mantenere il codice organizzato e scalabile:

```
src/
├── components/       # Componenti React
├── store/            # Tutto ciò che riguarda Redux
│   ├── index.js      # Configurazione dello store
│   ├── actions/      # Action creators
│   │   ├── index.js  # Punto di ingresso per tutte le actions
│   │   ├── todoActions.js
│   │   └── userActions.js
│   ├── reducers/     # Reducers
│   │   ├── index.js  # Combina tutti i reducers
│   │   ├── todoReducer.js
│   │   └── userReducer.js
│   ├── types/        # Costanti delle action types
│   │   └── index.js
│   ├── selectors/    # Funzioni per selezionare dati specifici dallo state
│   │   └── index.js
│   └── middleware/   # Middleware personalizzati
│       └── index.js
├── services/         # API calls, etc.
└── utils/            # Utilità generiche
```

### Feature-First Approach (Alternativa)

In progetti più grandi, l'organizzazione per feature può essere più efficiente:

```
src/
├── features/         # Organizzato per feature
│   ├── todos/
│   │   ├── components/
│   │   ├── actions.js
│   │   ├── reducer.js
│   │   ├── selectors.js
│   │   └── types.js
│   └── users/
│       ├── components/
│       ├── actions.js
│       ├── reducer.js
│       ├── selectors.js
│       └── types.js
├── store/
│   ├── index.js      # Configurazione dello store
│   └── rootReducer.js # Combina i reducers di tutte le features
└── components/       # Componenti condivisi
```

### Ducks Pattern (Redux Modules)

Il pattern Ducks combina actions, reducer e types relativi a una feature in un singolo file:

```
src/
├── store/
│   ├── index.js
│   └── ducks/        # Ogni "duck" contiene tutto ciò che serve per una feature
│       ├── todos.js  # Actions, reducer, e types per todos
│       └── users.js  # Actions, reducer, e types per users
└── components/
```

## Implementazione Pratica

Vediamo un esempio pratico completo:

### 1. Configurazione dello Store

```javascript
// src/store/index.js
import { createStore, applyMiddleware, compose } from 'redux';
import thunk from 'redux-thunk';
import rootReducer from './reducers';

const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ || compose;

const store = createStore(
  rootReducer,
  composeEnhancers(applyMiddleware(thunk))
);

export default store;
```

### 2. Definizione dei Types

```javascript
// src/store/types/index.js
export const ADD_TODO = 'ADD_TODO';
export const TOGGLE_TODO = 'TOGGLE_TODO';
export const DELETE_TODO = 'DELETE_TODO';
export const SET_FILTER = 'SET_FILTER';
```

### 3. Creazione delle Actions

```javascript
// src/store/actions/todoActions.js
import { ADD_TODO, TOGGLE_TODO, DELETE_TODO } from '../types';

export const addTodo = (text) => ({
  type: ADD_TODO,
  payload: {
    id: Date.now(),
    text,
    completed: false
  }
});

export const toggleTodo = (id) => ({
  type: TOGGLE_TODO,
  payload: { id }
});

export const deleteTodo = (id) => ({
  type: DELETE_TODO,
  payload: { id }
});
```

### 4. Implementazione del Reducer

```javascript
// src/store/reducers/todoReducer.js
import { ADD_TODO, TOGGLE_TODO, DELETE_TODO } from '../types';

const initialState = {
  todos: []
};

const todoReducer = (state = initialState, action) => {
  switch (action.type) {
    case ADD_TODO:
      return {
        ...state,
        todos: [...state.todos, action.payload]
      };
    
    case TOGGLE_TODO:
      return {
        ...state,
        todos: state.todos.map(todo =>
          todo.id === action.payload.id
            ? { ...todo, completed: !todo.completed }
            : todo
        )
      };
    
    case DELETE_TODO:
      return {
        ...state,
        todos: state.todos.filter(todo => todo.id !== action.payload.id)
      };
    
    default:
      return state;
  }
};

export default todoReducer;
```

### 5. Combinare i Reducers

```javascript
// src/store/reducers/index.js
import { combineReducers } from 'redux';
import todoReducer from './todoReducer';
import filterReducer from './filterReducer';

const rootReducer = combineReducers({
  todos: todoReducer,
  filter: filterReducer
});

export default rootReducer;
```

### 6. Selectors

```javascript
// src/store/selectors/index.js
export const getTodos = state => state.todos.todos;
export const getVisibleTodos = state => {
  const todos = getTodos(state);
  const filter = state.filter;
  
  switch (filter) {
    case 'ALL':
      return todos;
    case 'COMPLETED':
      return todos.filter(todo => todo.completed);
    case 'ACTIVE':
      return todos.filter(todo => !todo.completed);
    default:
      return todos;
  }
};
```

### 7. Utilizzo in un Componente React

```jsx
// src/components/TodoList.js
import React from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { toggleTodo, deleteTodo } from '../store/actions/todoActions';
import { getVisibleTodos } from '../store/selectors';

const TodoList = () => {
  const todos = useSelector(getVisibleTodos);
  const dispatch = useDispatch();
  
  return (
    <ul>
      {todos.map(todo => (
        <li
          key={todo.id}
          style={{ textDecoration: todo.completed ? 'line-through' : 'none' }}
        >
          <span onClick={() => dispatch(toggleTodo(todo.id))}>
            {todo.text}
          </span>
          <button onClick={() => dispatch(deleteTodo(todo.id))}>
            Elimina
          </button>
        </li>
      ))}
    </ul>
  );
};

export default TodoList;
```

## Best Practices

1. **Immutabilità**: Non modificare direttamente lo stato nei reducers
2. **Normalizzazione**: Strutturare i dati come un database, evitando duplicati
3. **Granularità delle Actions**: Azioni atomiche che descrivono un singolo cambiamento
4. **Selectors**: Usare selectors per accedere ai dati dello store
5. **Redux Toolkit**: Considerare l'uso di Redux Toolkit per ridurre il boilerplate

## Esempi Avanzati

### Redux Thunk per Operazioni Asincrone

```javascript
// Action creator con thunk
export const fetchTodos = () => {
  return async (dispatch) => {
    dispatch({ type: 'FETCH_TODOS_REQUEST' });
    
    try {
      const response = await fetch('/api/todos');
      const data = await response.json();
      
      dispatch({
        type: 'FETCH_TODOS_SUCCESS',
        payload: data
      });
    } catch (error) {
      dispatch({
        type: 'FETCH_TODOS_FAILURE',
        error: error.message
      });
    }
  };
};

// Utilizzo in un componente
const dispatch = useDispatch();

useEffect(() => {
  dispatch(fetchTodos());
}, [dispatch]);
```

### Redux Toolkit (Approccio Moderno)

Redux Toolkit semplifica l'utilizzo di Redux riducendo il boilerplate:

```javascript
// store.js
import { configureStore } from '@reduxjs/toolkit';
import todoReducer from './features/todos/todoSlice';

export const store = configureStore({
  reducer: {
    todos: todoReducer
  }
});

// features/todos/todoSlice.js
import { createSlice } from '@reduxjs/toolkit';

const todoSlice = createSlice({
  name: 'todos',
  initialState: { list: [] },
  reducers: {
    addTodo: (state, action) => {
      state.list.push({
        id: Date.now(),
        text: action.payload,
        completed: false
      });
    },
    toggleTodo: (state, action) => {
      const todo = state.list.find(todo => todo.id === action.payload);
      if (todo) {
        todo.completed = !todo.completed;
      }
    }
  }
});

export const { addTodo, toggleTodo } = todoSlice.actions;
export default todoSlice.reducer;

// Component.js
import React from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { addTodo, toggleTodo } from './features/todos/todoSlice';

function TodoApp() {
  const todos = useSelector(state => state.todos.list);
  const dispatch = useDispatch();
  
  // Utilizzo
  dispatch(addTodo('Imparare Redux Toolkit'));
}
```

### Redux Saga (Alternativa a Thunk)

```javascript
// sagas.js
import { call, put, takeEvery } from 'redux-saga/effects';

function* fetchTodosSaga() {
  try {
    const data = yield call(api.fetchTodos);
    yield put({ type: 'FETCH_TODOS_SUCCESS', payload: data });
  } catch (error) {
    yield put({ type: 'FETCH_TODOS_FAILURE', error: error.message });
  }
}

function* rootSaga() {
  yield takeEvery('FETCH_TODOS_REQUEST', fetchTodosSaga);
}

export default rootSaga;
```

## Conclusioni

Redux offre un potente pattern per la gestione dello stato in applicazioni React. Sebbene richieda un certo setup iniziale, i benefici di avere uno stato prevedibile e una chiara separazione delle responsabilità superano i costi, specialmente in applicazioni di medie e grandi dimensioni.

Il dispatch è il cuore di Redux, permettendo di inviare azioni in modo intuitivo e predicibile a tutto il sistema. Con una buona organizzazione dei file e seguendo le best practices, Redux può rendere la tua applicazione più manutenibile e robusta nel lungo periodo.ß