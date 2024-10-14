Módulo 3: Criando Hooks Personalizados

Vamos seguir para o **Módulo 3** do curso, onde o foco será a criação de **hooks personalizados** (**Custom Hooks**) em **React com TypeScript**. Custom Hooks permitem encapsular lógica repetitiva e reutilizável, tornando seu código mais organizado e limpo.

---

## **Módulo 3: Criando Hooks Personalizados**

### **3.1. Introdução aos Custom Hooks**

**Hooks personalizados** são funções em React que seguem a convenção de nomeação começando com "use", e podem encapsular qualquer lógica de estado ou efeito que você quiser reutilizar entre diferentes componentes. A principal vantagem é a **reutilização de lógica de componentes** sem duplicação de código.

Um Custom Hook é uma função regular em JavaScript/TypeScript, mas com o poder de usar outros hooks nativos do React dentro dela.

---

### **3.2. Criando o Primeiro Custom Hook: `useFetch`**

Vamos começar com um exemplo prático, criando um hook personalizado para fazer requisições HTTP.

#### **Exemplo: `useFetch` para Requisições API**

Esse hook será responsável por lidar com requisições, retornando dados, status de carregamento e erros.

```tsx
import { useState, useEffect } from 'react';

// Tipos para os estados de retorno
type FetchState<T> = {
  data: T | null;
  loading: boolean;
  error: string | null;
};

function useFetch<T>(url: string): FetchState<T> {
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState<boolean>(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    const fetchData = async () => {
      try {
        const response = await fetch(url);
        if (!response.ok) {
          throw new Error('Erro na requisição');
        }
        const result = await response.json();
        setData(result);
      } catch (err: any) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };

    fetchData();
  }, [url]); // Atualiza a requisição quando a URL muda

  return { data, loading, error };
}

export default useFetch;
```

#### **Explicação:**
- O hook recebe a **URL** como parâmetro e retorna o estado da requisição, incluindo `data`, `loading`, e `error`.
- Ele utiliza `useState` para armazenar os dados e `useEffect` para disparar a requisição.

#### **Uso do Hook `useFetch` em um Componente**

Agora vamos usar esse hook em um componente real:

```tsx
import React from 'react';
import useFetch from './hooks/useFetch';
import { User } from './types';

const UsersList: React.FC = () => {
  const { data, loading, error } = useFetch<User[]>('https://randomuser.me/api/?results=5');

  if (loading) return <p>Carregando...</p>;
  if (error) return <p>Erro: {error}</p>;

  return (
    <ul>
      {data?.map((user, index) => (
        <li key={index}>
          {user.name.first} {user.name.last}
        </li>
      ))}
    </ul>
  );
};

export default UsersList;
```

#### **Explicação:**
- O `useFetch` é usado para buscar uma lista de usuários.
- O hook é tipado para lidar com a resposta como um array de `User`.

---

### **3.3. Criando um Hook para Gerenciamento de Formulários: `useForm`**

Formulários são comuns em praticamente todas as aplicações. Vamos criar um hook personalizado para simplificar o gerenciamento de estados de formulários.

#### **Exemplo: `useForm`**

Esse hook será responsável por gerenciar os valores e estados de campos em um formulário.

```tsx
import { useState, ChangeEvent } from 'react';

// Tipos para os valores do formulário
type FormValues = {
  [key: string]: any;
};

function useForm(initialValues: FormValues) {
  const [values, setValues] = useState<FormValues>(initialValues);

  const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
    const { name, value } = e.target;
    setValues({
      ...values,
      [name]: value,
    });
  };

  const resetForm = () => {
    setValues(initialValues);
  };

  return { values, handleChange, resetForm };
}

export default useForm;
```

#### **Explicação:**
- O hook `useForm` gerencia os valores dos campos de um formulário.
- Ele retorna o estado `values`, uma função `handleChange` para atualizar os valores e `resetForm` para limpar os campos.

#### **Uso do Hook `useForm` em um Componente**

Agora vamos aplicar o hook em um formulário real:

```tsx
import React from 'react';
import useForm from './hooks/useForm';

const MyForm: React.FC = () => {
  const { values, handleChange, resetForm } = useForm({ name: '', email: '' });

  const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    console.log('Formulário enviado:', values);
    resetForm();
  };

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label>Nome:</label>
        <input name="name" value={values.name} onChange={handleChange} />
      </div>
      <div>
        <label>Email:</label>
        <input name="email" value={values.email} onChange={handleChange} />
      </div>
      <button type="submit">Enviar</button>
    </form>
  );
};

export default MyForm;
```

#### **Explicação:**
- O hook `useForm` é usado para gerenciar os valores do formulário.
- `handleSubmit` lida com o envio do formulário e exibe os valores no console.

---

### **3.4. Criando um Hook para Contagem de Tempo: `useTimer`**

Vamos criar um hook para gerenciar contagens de tempo, ideal para cronômetros ou temporizadores.

#### **Exemplo: `useTimer`**

Esse hook gerenciará a lógica de um contador.

```tsx
import { useState, useEffect } from 'react';

function useTimer(initialValue: number) {
  const [time, setTime] = useState<number>(initialValue);
  const [isActive, setIsActive] = useState<boolean>(false);

  useEffect(() => {
    let interval: NodeJS.Timeout | null = null;

    if (isActive) {
      interval = setInterval(() => {
        setTime((prevTime) => prevTime + 1);
      }, 1000);
    } else if (!isActive && interval) {
      clearInterval(interval);
    }

    return () => {
      if (interval) clearInterval(interval);
    };
  }, [isActive]);

  const startTimer = () => setIsActive(true);
  const stopTimer = () => setIsActive(false);
  const resetTimer = () => setTime(0);

  return { time, startTimer, stopTimer, resetTimer, isActive };
}

export default useTimer;
```

#### **Explicação:**
- O hook `useTimer` gerencia a contagem de tempo, com funções para iniciar, parar e resetar o temporizador.
- Ele usa `setInterval` dentro de um `useEffect` para aumentar o valor a cada segundo.

#### **Uso do Hook `useTimer` em um Componente**

Agora vamos usar o `useTimer` em um cronômetro simples:

```tsx
import React from 'react';
import useTimer from './hooks/useTimer';

const Timer: React.FC = () => {
  const { time, startTimer, stopTimer, resetTimer, isActive } = useTimer(0);

  return (
    <div>
      <p>Tempo: {time} segundos</p>
      <button onClick={startTimer} disabled={isActive}>
        Iniciar
      </button>
      <button onClick={stopTimer} disabled={!isActive}>
        Pausar
      </button>
      <button onClick={resetTimer}>Resetar</button>
    </div>
  );
};

export default Timer;
```

#### **Explicação:**
- O hook `useTimer` é utilizado para controlar o temporizador, com botões para iniciar, pausar e resetar o tempo.

---

### **3.5. Criando um Hook de Persistência Local: `useLocalStorage`**

Esse hook será responsável por armazenar e recuperar dados do `localStorage` de maneira simplificada.

#### **Exemplo: `useLocalStorage`**

```tsx
import { useState } from 'react';

function useLocalStorage<T>(key: string, initialValue: T) {
  const [storedValue, setStoredValue] = useState<T>(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      console.error(error);
      return initialValue;
    }
  });

  const setValue = (value: T) => {
    try {
      setStoredValue(value);
      window.localStorage.setItem(key, JSON.stringify(value));
    } catch (error) {
      console.error(error);
    }
  };

  return [storedValue, setValue] as const;
}

export default useLocal

Storage;
```

#### **Uso em um Componente:**

```tsx
const CounterWithLocalStorage: React.FC = () => {
  const [count, setCount] = useLocalStorage<number>('count', 0);

  return (
    <div>
      <p>Contador: {count}</p>
      <button onClick={() => setCount(count + 1)}>Incrementar</button>
    </div>
  );
};
```

---

### **Conclusão do Módulo 3:**

Neste módulo, você aprendeu como criar hooks personalizados para diferentes finalidades, como requisições HTTP, gerenciamento de formulários, contagem de tempo e persistência no `localStorage`. O próximo módulo aprofundará a combinação de hooks para criar funcionalidades mais complexas.

