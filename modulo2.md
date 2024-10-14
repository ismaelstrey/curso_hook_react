Vamos seguir para o **Módulo 2** do curso sobre **React Hooks e TypeScript**, onde aprofundaremos no uso dos hooks nativos do React. Aqui, exploraremos o uso de hooks como `useState`, `useEffect`, `useReducer`, `useRef`, `useContext`, `useCallback`, e `useMemo`, com exemplos práticos e tipagem adequada em **TypeScript**.

---

## **Módulo 2: Hooks Nativos e Suas Tipagens**

### **2.1. `useState`: Tipagem e Boas Práticas**

O hook `useState` é um dos mais básicos e amplamente usados no React. Ele permite gerenciar o estado de variáveis dentro de componentes funcionais.

#### **Exemplo 1: Contador Simples com `useState`**

Vamos criar um contador simples e tipá-lo corretamente em **TypeScript**:

```tsx
import React, { useState } from 'react';

const Counter: React.FC = () => {
  // Tipando o estado como número
  const [count, setCount] = useState<number>(0);

  return (
    <div>
      <p>Contagem: {count}</p>
      <button onClick={() => setCount(count + 1)}>Incrementar</button>
      <button onClick={() => setCount(count - 1)}>Decrementar</button>
    </div>
  );
};

export default Counter;
```

#### **Explicação:**
- O estado `count` é tipado como `number` e inicializado com o valor `0`.
- As funções de incremento e decremento atualizam o valor de `count` diretamente.
  
#### **Exemplo 2: Trabalhando com Arrays no `useState`**

Agora, vamos criar um exemplo onde trabalhamos com arrays no `useState`.

```tsx
import React, { useState } from 'react';

const TodoList: React.FC = () => {
  // Estado tipado como array de strings
  const [todos, setTodos] = useState<string[]>([]);

  const addTodo = () => {
    const newTodo = `Tarefa ${todos.length + 1}`;
    setTodos([...todos, newTodo]);
  };

  return (
    <div>
      <button onClick={addTodo}>Adicionar Tarefa</button>
      <ul>
        {todos.map((todo, index) => (
          <li key={index}>{todo}</li>
        ))}
      </ul>
    </div>
  );
};

export default TodoList;
```

#### **Explicação:**
- O estado `todos` é tipado como um array de strings (`string[]`).
- A função `addTodo` cria uma nova tarefa e a adiciona ao array de todos.

---

### **2.2. `useEffect`: Efeitos Colaterais com Tipagem**

O `useEffect` é utilizado para lidar com efeitos colaterais, como requisições API, assinaturas e manipulação do DOM. Ele pode ser disparado sempre que o componente for renderizado ou quando uma variável específica mudar.

#### **Exemplo: Requisição API com `useEffect`**

Vamos aprimorar o exemplo do Módulo 1 para adicionar tipagem correta e controle de dependências no `useEffect`:

```tsx
import React, { useState, useEffect } from 'react';
import { User } from './types';

const UserList: React.FC = () => {
  const [users, setUsers] = useState<User[]>([]);
  const [loading, setLoading] = useState<boolean>(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    const fetchUsers = async () => {
      try {
        const response = await fetch('https://randomuser.me/api/?results=5');
        if (!response.ok) throw new Error('Erro ao buscar usuários');
        const data = await response.json();
        setUsers(data.results);
      } catch (error) {
        setError(error.message);
      } finally {
        setLoading(false);
      }
    };

    fetchUsers();
  }, []); // [] garante que o efeito rodará apenas na montagem

  return (
    <div>
      {loading ? <p>Carregando...</p> : null}
      {error ? <p>Erro: {error}</p> : null}
      <ul>
        {users.map((user, index) => (
          <li key={index}>{user.name.first} {user.name.last}</li>
        ))}
      </ul>
    </div>
  );
};

export default UserList;
```

#### **Explicação:**
- O `useEffect` dispara a requisição API somente uma vez, ao montar o componente (com a dependência `[]`).
- A função `fetchUsers` é assíncrona e lida com o estado de `loading`, `error`, e `users`.

---

### **2.3. `useReducer`: Estados Complexos**

O `useReducer` é útil para gerenciar estados mais complexos, especialmente quando várias ações afetam o estado.

#### **Exemplo: Carrinho de Compras com `useReducer`**

Vamos criar um exemplo básico de um carrinho de compras usando `useReducer`:

```tsx
import React, { useReducer } from 'react';

// Definindo tipos para ações
type ActionType = 
  | { type: 'add', item: string }
  | { type: 'remove', index: number };

// Estado inicial
const initialCart: string[] = [];

// Redutor
const cartReducer = (state: string[], action: ActionType): string[] => {
  switch (action.type) {
    case 'add':
      return [...state, action.item];
    case 'remove':
      return state.filter((_, i) => i !== action.index);
    default:
      return state;
  }
};

const Cart: React.FC = () => {
  const [cart, dispatch] = useReducer(cartReducer, initialCart);

  return (
    <div>
      <button onClick={() => dispatch({ type: 'add', item: 'Produto A' })}>
        Adicionar Produto A
      </button>
      <button onClick={() => dispatch({ type: 'add', item: 'Produto B' })}>
        Adicionar Produto B
      </button>
      <ul>
        {cart.map((item, index) => (
          <li key={index}>
            {item}
            <button onClick={() => dispatch({ type: 'remove', index })}>
              Remover
            </button>
          </li>
        ))}
      </ul>
    </div>
  );
};

export default Cart;
```

#### **Explicação:**
- O `useReducer` gerencia o estado do carrinho de compras (`cart`).
- As ações `add` e `remove` são tipadas, garantindo que o TypeScript valide os tipos ao despachar as ações.

---

### **2.4. Outros Hooks Nativos**

#### **2.4.1. `useRef`**

O hook `useRef` permite criar referências mutáveis que persistem entre renderizações. Ele é útil para acessar diretamente elementos DOM ou armazenar valores que não disparam renderizações.

```tsx
import React, { useRef } from 'react';

const FocusInput: React.FC = () => {
  const inputRef = useRef<HTMLInputElement>(null);

  const focusInput = () => {
    inputRef.current?.focus();
  };

  return (
    <div>
      <input ref={inputRef} type="text" />
      <button onClick={focusInput}>Focar no input</button>
    </div>
  );
};

export default FocusInput;
```

#### **2.4.2. `useContext`**

O `useContext` permite compartilhar dados entre componentes sem precisar passar props manualmente.

```tsx
import React, { createContext, useContext } from 'react';

// Criando o contexto
const ThemeContext = createContext<string>('light');

const ChildComponent: React.FC = () => {
  const theme = useContext(ThemeContext);
  return <p>O tema atual é {theme}</p>;
};

const ParentComponent: React.FC = () => {
  return (
    <ThemeContext.Provider value="dark">
      <ChildComponent />
    </ThemeContext.Provider>
  );
};

export default ParentComponent;
```

---

### **2.5. Otimização com `useCallback` e `useMemo`**

#### **2.5.1. `useCallback`: Memorizando Funções**

O `useCallback` é usado para memorizar funções e evitar que elas sejam recriadas em todas as renderizações, o que pode melhorar a performance em componentes que passam funções como props.

```tsx
import React, { useState, useCallback } from 'react';

const CounterWithCallback: React.FC = () => {
  const [count, setCount] = useState<number>(0);

  const increment = useCallback(() => {
    setCount((prevCount) => prevCount + 1);
  }, []);

  return (
    <div>
      <p>Contagem: {count}</p>
      <button onClick={increment}>Incrementar</button>
    </div>
  );
};

export default CounterWithCallback;
```

#### **2.5.2. `useMemo`: Memorizando Valores**

O `useMemo` é usado para memorizar o resultado de cálculos caros ou operações que não devem ser repetidas em cada renderização.

```tsx
import React, { useState, useMemo } from 'react';

const ExpensiveCalculation: React.FC = () => {
  const [

number, setNumber] = useState<number>(0);
  
  const calculate = (n: number) => {
    console.log('Calculando...');
    return n * 2;
  };

  const memoizedResult = useMemo(() => calculate(number), [number]);

  return (
    <div>
      <p>Resultado: {memoizedResult}</p>
      <button onClick={() => setNumber(number + 1)}>Incrementar</button>
    </div>
  );
};

export default ExpensiveCalculation;
```

---

### **Conclusão do Módulo 2:**
Neste módulo, aprendemos como trabalhar com hooks nativos como `useState`, `useEffect`, `useReducer`, `useRef`, `useContext`, `useCallback`, e `useMemo`, tipando corretamente cada um em TypeScript. Esse conhecimento é fundamental para o desenvolvimento de componentes complexos e otimizados.

**Próximos passos**: No Módulo 3, vamos abordar **hooks personalizados (custom hooks)**, que permitem encapsular a lógica de componentes e reutilizá-la em diferentes partes da aplicação.