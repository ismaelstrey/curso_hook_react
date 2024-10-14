Vamos para o **Módulo 4** do curso, onde iremos abordar a **composição de hooks personalizados** e como eles podem ser usados juntos para criar funcionalidades mais complexas em uma aplicação React.

---

## **Módulo 4: Composição de Hooks Personalizados**

### **4.1. O que é Composição de Hooks?**

A composição de hooks permite combinar diferentes hooks personalizados para reutilizar e compartilhar lógica entre componentes. Isso é útil para manter o código organizado e modular. Você pode usar múltiplos hooks em um componente para encapsular diferentes partes da lógica de negócios.

---

### **4.2. Criando um Hook Composto: `useUser`**

Vamos criar um hook composto que combina `useFetch` e `useLocalStorage` para gerenciar dados de usuários de uma API e armazená-los localmente. 

#### **Exemplo: `useUser`**

Esse hook buscará os dados do usuário de uma API e os armazenará no `localStorage`.

```tsx
import useFetch from './useFetch';
import useLocalStorage from './useLocalStorage';
import { User } from '../types';

const useUser = (url: string) => {
  const { data, loading, error } = useFetch<User[]>(url);
  const [storedUsers, setStoredUsers] = useLocalStorage<User[]>('users', []);

  // Armazena os dados buscados localmente se não houver erros
  const saveUsers = () => {
    if (data) {
      setStoredUsers(data);
    }
  };

  // Execute a função de salvar quando os dados mudam
  if (data) {
    saveUsers();
  }

  return { users: storedUsers, loading, error };
};

export default useUser;
```

#### **Explicação:**
- O hook `useUser` usa `useFetch` para buscar dados da API e `useLocalStorage` para armazená-los.
- Se a busca for bem-sucedida, os dados são salvos no `localStorage`.

#### **Uso do Hook `useUser` em um Componente**

Vamos usar o `useUser` em um componente para exibir a lista de usuários.

```tsx
import React from 'react';
import useUser from './hooks/useUser';
import { User } from './types';

const UserList: React.FC = () => {
  const { users, loading, error } = useUser('https://randomuser.me/api/?results=5');

  if (loading) return <p>Carregando...</p>;
  if (error) return <p>Erro: {error}</p>;

  return (
    <ul>
      {users.map((user, index) => (
        <li key={index}>
          {user.name.first} {user.name.last}
        </li>
      ))}
    </ul>
  );
};

export default UserList;
```

---

### **4.3. Combinando Hooks para Formulários Complexos**

Agora, vamos criar um formulário que usa vários hooks personalizados juntos. Neste exemplo, combinaremos `useForm`, `useLocalStorage` e `useTimer` para um formulário que salva automaticamente os dados a cada X segundos.

#### **Exemplo: Formulário Auto-Salvável**

```tsx
import React, { useEffect } from 'react';
import useForm from './hooks/useForm';
import useLocalStorage from './hooks/useLocalStorage';
import useTimer from './hooks/useTimer';

const AutoSaveForm: React.FC = () => {
  const { values, handleChange } = useForm({ name: '', email: '' });
  const [savedValues, setSavedValues] = useLocalStorage('formValues', values);
  const { time, startTimer, resetTimer } = useTimer(0);

  // Atualiza os valores salvos a cada 5 segundos
  useEffect(() => {
    if (time > 0 && time % 5 === 0) {
      setSavedValues(values);
    }
  }, [time, values, setSavedValues]);

  return (
    <div>
      <h2>Formulário Auto-Salvável</h2>
      <form>
        <div>
          <label>Nome:</label>
          <input name="name" value={values.name} onChange={handleChange} />
        </div>
        <div>
          <label>Email:</label>
          <input name="email" value={values.email} onChange={handleChange} />
        </div>
      </form>
      <button onClick={startTimer}>Iniciar Auto-Salvamento</button>
      <p>Tempo: {time} segundos</p>
      <p>Valores Salvos: {JSON.stringify(savedValues)}</p>
    </div>
  );
};

export default AutoSaveForm;
```

#### **Explicação:**
- O componente `AutoSaveForm` utiliza `useForm` para gerenciar os campos do formulário, `useLocalStorage` para persistir os dados e `useTimer` para contar o tempo.
- O hook `useEffect` verifica a cada 5 segundos e salva os dados do formulário no `localStorage`.

---

### **4.4. Criando um Hook para Lidar com Notificações: `useNotifications`**

Vamos criar um hook que permita gerenciar notificações de maneira simples, utilizando a composição com o hook `useTimer`.

#### **Exemplo: `useNotifications`**

```tsx
import { useState, useEffect } from 'react';

type Notification = {
  id: number;
  message: string;
};

function useNotifications() {
  const [notifications, setNotifications] = useState<Notification[]>([]);
  const [nextId, setNextId] = useState(0);

  const addNotification = (message: string) => {
    setNotifications((prev) => [...prev, { id: nextId, message }]);
    setNextId((prev) => prev + 1);
  };

  const removeNotification = (id: number) => {
    setNotifications((prev) => prev.filter((note) => note.id !== id));
  };

  useEffect(() => {
    const timeout = setInterval(() => {
      if (notifications.length) {
        removeNotification(notifications[0].id);
      }
    }, 3000); // Remove a notificação após 3 segundos

    return () => clearInterval(timeout);
  }, [notifications]);

  return { notifications, addNotification };
}

export default useNotifications;
```

#### **Uso do Hook `useNotifications` em um Componente**

```tsx
import React from 'react';
import useNotifications from './hooks/useNotifications';

const NotificationComponent: React.FC = () => {
  const { notifications, addNotification } = useNotifications();

  return (
    <div>
      <button onClick={() => addNotification('Nova Notificação!')}>Adicionar Notificação</button>
      <ul>
        {notifications.map((note) => (
          <li key={note.id}>{note.message}</li>
        ))}
      </ul>
    </div>
  );
};

export default NotificationComponent;
```

---

### **Conclusão do Módulo 4:**

Neste módulo, você aprendeu a criar hooks personalizados compostos, combinando funcionalidades de diferentes hooks para criar uma lógica mais complexa e reutilizável. Você também viu exemplos práticos de como aplicar a composição de hooks em um projeto.

### **Próximos passos:**

No próximo módulo, abordaremos **testes de hooks personalizados** para garantir que eles funcionem como esperado e possam ser integrados em aplicações reais com segurança.