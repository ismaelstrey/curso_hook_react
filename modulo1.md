Módulo 1: Introdução ao React Hooks e TypeScript
Para o Módulo 1, que é uma introdução aos React Hooks e TypeScript, vamos criar um exemplo prático simples, utilizando os hooks básicos como `useState` e `useEffect`, e aplicar a tipagem correta em TypeScript. O exemplo será uma aplicação que consome dados de uma API e exibe uma lista de usuários.

### Objetivo:
- Entender como usar `useState` e `useEffect` em uma aplicação React com TypeScript.
- Consumir uma API e tipar corretamente os dados da resposta.
- Lidar com estados como `loading` e `error`.

### Exemplo prático: Lista de usuários

---

### **Passo 1: Configurando o projeto**
Primeiro, configure o ambiente com **React** e **TypeScript**. Supondo que você já tenha um projeto React com TypeScript configurado, caso não tenha, crie um projeto com o seguinte comando:

```bash
npx create-react-app my-users-app --template typescript
```

Depois de instalar as dependências, navegue até o diretório do projeto:

```bash
cd my-users-app
```

---

### **Passo 2: Criando o componente `UserList`**

O componente `UserList` vai consumir dados de uma API pública (exemplo: [Random User API](https://randomuser.me/)) e exibir uma lista de usuários na tela.

#### **1. Definir o tipo de dados retornados pela API**

Como estamos usando **TypeScript**, é importante definir o tipo de dado esperado da API. Crie um arquivo `types.ts` para manter as definições de tipo:

```ts
// src/types.ts

export interface User {
  name: {
    first: string;
    last: string;
  };
  email: string;
  picture: {
    thumbnail: string;
  };
}
```

Isso define que cada usuário terá um nome, um email e uma imagem (`thumbnail`).

---

#### **2. Criar o componente `UserList.tsx`**

Agora crie o componente `UserList.tsx` dentro da pasta `src/components`.

```tsx
// src/components/UserList.tsx

import React, { useEffect, useState } from 'react';
import { User } from '../types';

const UserList: React.FC = () => {
  // Estado para armazenar os dados da API
  const [users, setUsers] = useState<User[]>([]);
  const [loading, setLoading] = useState<boolean>(true);
  const [error, setError] = useState<string | null>(null);

  // useEffect para consumir a API ao montar o componente
  useEffect(() => {
    const fetchUsers = async () => {
      try {
        const response = await fetch('https://randomuser.me/api/?results=5');
        if (!response.ok) {
          throw new Error('Erro ao buscar usuários');
        }
        const data = await response.json();
        setUsers(data.results); // Definindo os usuários no estado
      } catch (error) {
        setError(error.message);
      } finally {
        setLoading(false);
      }
    };

    fetchUsers();
  }, []);

  if (loading) {
    return <p>Carregando...</p>;
  }

  if (error) {
    return <p>Erro: {error}</p>;
  }

  return (
    <div>
      <h2>Lista de Usuários</h2>
      <ul>
        {users.map((user, index) => (
          <li key={index}>
            <img src={user.picture.thumbnail} alt={`${user.name.first} ${user.name.last}`} />
            <p>{user.name.first} {user.name.last}</p>
            <p>{user.email}</p>
          </li>
        ))}
      </ul>
    </div>
  );
};

export default UserList;
```

#### **Explicação do código:**

- **useState**: 
  - `users`: armazena a lista de usuários retornada pela API.
  - `loading`: controla o estado de carregamento enquanto os dados são buscados.
  - `error`: armazena uma mensagem de erro caso a requisição falhe.
  
- **useEffect**:
  - Faz a chamada à API assim que o componente for montado.
  - Dentro da função `fetchUsers`, a requisição é feita para a API do [randomuser.me](https://randomuser.me/api/).
  - Os dados são armazenados no estado `users` se a requisição for bem-sucedida. Caso contrário, o estado `error` é atualizado com uma mensagem.

---

### **Passo 3: Exibindo o componente na aplicação**

Agora você pode importar e usar o componente `UserList` no seu arquivo `App.tsx`:

```tsx
// src/App.tsx

import React from 'react';
import './App.css';
import UserList from './components/UserList';

const App: React.FC = () => {
  return (
    <div className="App">
      <h1>Aplicação de Lista de Usuários</h1>
      <UserList />
    </div>
  );
};

export default App;
```

---

### **Passo 4: Rodar a aplicação**

Execute a aplicação para ver o resultado:

```bash
npm start
```

A página deverá exibir uma lista de usuários com seus nomes, emails e miniaturas das fotos. 

---

### **Conceitos aprendidos:**
- **useState**: Gerenciamos estados para usuários, loading e erros.
- **useEffect**: Utilizamos para realizar uma operação assíncrona ao montar o componente.
- **TypeScript**: Tipamos corretamente os estados e os dados retornados da API, garantindo maior segurança e previsibilidade no código.

Esse exemplo abrange conceitos básicos de `useState` e `useEffect`, tipagem em TypeScript e consumo de API, conforme o proposto no Módulo 1.