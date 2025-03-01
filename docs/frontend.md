# Documentação do Frontend

## Visão Geral

O frontend da plataforma LMS é construído com React e TypeScript, utilizando Vite como ferramenta de build. A interface é projetada para ser responsiva, acessível e oferecer uma experiência de usuário intuitiva para alunos, professores e administradores.

## Tecnologias Principais

- **React**: Biblioteca para construção de interfaces de usuário
- **TypeScript**: Superset tipado de JavaScript
- **Vite**: Ferramenta de build rápida para desenvolvimento moderno
- **Redux Toolkit / Context API**: Gerenciamento de estado global
- **Tailwind CSS / Styled Components**: Estilização
- **Axios**: Cliente HTTP para requisições à API
- **React Hook Form + Zod**: Validação de formulários
- **SweetAlert2**: Notificações e alertas

## Estrutura de Diretórios

```
lms-frontend/
 |-- src/
 |   |-- components/
 |   |   |-- common/
 |   |   |   |-- Button/
 |   |   |   |-- Card/
 |   |   |   |-- Input/
 |   |   |   |-- Modal/
 |   |   |   |-- Notification/
 |   |   |-- layout/
 |   |   |   |-- Header/
 |   |   |   |-- Sidebar/
 |   |   |   |-- Footer/
 |   |   |-- dashboard/
 |   |   |-- courses/
 |   |   |-- forum/
 |   |   |-- auth/
 |   |-- pages/
 |   |   |-- Home/
 |   |   |-- Login/
 |   |   |-- Register/
 |   |   |-- Dashboard/
 |   |   |-- CourseDetails/
 |   |   |-- AdminPanel/
 |   |   |-- TeacherPortal/
 |   |   |-- Forum/
 |   |   |-- Profile/
 |   |-- hooks/
 |   |   |-- useAuth.ts
 |   |   |-- useCourses.ts
 |   |   |-- useNotification.ts
 |   |   |-- useProgress.ts
 |   |-- services/
 |   |   |-- api.ts
 |   |   |-- auth.service.ts
 |   |   |-- courses.service.ts
 |   |   |-- forum.service.ts
 |   |   |-- user.service.ts
 |   |-- context/
 |   |   |-- AuthContext.tsx
 |   |   |-- NotificationContext.tsx
 |   |   |-- ThemeContext.tsx
 |   |-- store/ (Redux)
 |   |   |-- index.ts
 |   |   |-- slices/
 |   |   |   |-- authSlice.ts
 |   |   |   |-- courseSlice.ts
 |   |   |   |-- userSlice.ts
 |   |-- types/
 |   |   |-- auth.types.ts
 |   |   |-- course.types.ts
 |   |   |-- user.types.ts
 |   |   |-- forum.types.ts
 |   |-- utils/
 |   |   |-- formatters.ts
 |   |   |-- validators.ts
 |   |   |-- storage.ts
 |   |   |-- constants.ts
 |   |-- App.tsx
 |   |-- main.tsx
 |-- public/
 |   |-- assets/
 |   |   |-- images/
 |   |   |-- icons/
 |-- .env
 |-- package.json
 |-- tsconfig.json
 |-- vite.config.ts
 |-- tailwind.config.js
```

## Componentes Principais

### Componentes Comuns

Componentes reutilizáveis em toda a aplicação:

- **Button**: Botões personalizados com variantes (primário, secundário, etc.)
- **Card**: Containers para exibição de informações
- **Input**: Campos de entrada com validação
- **Modal**: Janelas modais para ações específicas
- **Notification**: Componente para exibir notificações

### Componentes de Layout

- **Header**: Barra de navegação superior
- **Sidebar**: Menu lateral com navegação principal
- **Footer**: Rodapé da aplicação

### Componentes Específicos

- **CourseCard**: Exibe informações resumidas de um curso
- **ProgressBar**: Mostra o progresso do aluno em um curso
- **ForumPost**: Exibe uma postagem no fórum
- **BadgeDisplay**: Mostra as conquistas do usuário
- **RankingCard**: Exibe o nível e XP do aluno

## Páginas Principais

### Autenticação

- **Login**: Página de login com opções de recuperação de senha
- **Register**: Cadastro de novos usuários
- **ForgotPassword**: Recuperação de senha

### Dashboard do Aluno

- **Dashboard**: Visão geral dos cursos e progresso
- **CourseList**: Lista de cursos matriculados
- **Certificates**: Certificados emitidos
- **Ranking**: Posição do aluno no ranking

### Cursos

- **CourseDetails**: Detalhes de um curso específico
- **ModuleView**: Visualização de um módulo do curso
- **LessonPlayer**: Player de vídeo para aulas
- **Quiz**: Interface para realização de provas

### Portal do Professor

- **TeacherDashboard**: Visão geral para professores
- **ClassManagement**: Gerenciamento de turmas
- **StudentProgress**: Acompanhamento do progresso dos alunos
- **ContentCreation**: Criação de conteúdo e questionários

### Admin Dashboard

- **AdminDashboard**: Visão geral para administradores
- **UserManagement**: Gerenciamento de usuários
- **CourseManagement**: Gerenciamento de cursos
- **Reports**: Relatórios de uso e desempenho

### Fórum e Comunidade

- **ForumHome**: Página principal do fórum
- **TopicView**: Visualização de um tópico específico
- **CreatePost**: Criação de nova postagem
- **SearchResults**: Resultados de busca no fórum

## Gerenciamento de Estado

### Redux Toolkit

Utilizado para gerenciar o estado global da aplicação, especialmente para:

- Autenticação e dados do usuário
- Dados de cursos e progresso
- Configurações da aplicação

### Context API

Utilizado para estados mais específicos:

- Tema da aplicação (dark/light mode)
- Notificações
- Estado de autenticação

## Hooks Personalizados

- **useAuth**: Gerencia autenticação e permissões
- **useCourses**: Acesso e manipulação de dados de cursos
- **useProgress**: Acompanhamento do progresso do aluno
- **useNotification**: Exibição de notificações

## Serviços de API

- **api.ts**: Configuração base do Axios
- **auth.service.ts**: Serviços de autenticação
- **courses.service.ts**: Serviços relacionados a cursos
- **user.service.ts**: Serviços relacionados a usuários
- **forum.service.ts**: Serviços do fórum

## Validação de Formulários

Utilizamos React Hook Form em conjunto com Zod para validação de formulários:

```typescript
// Exemplo de schema de validação com Zod
const loginSchema = z.object({
  email: z.string().email('Email inválido'),
  password: z.string().min(6, 'A senha deve ter pelo menos 6 caracteres')
});

// Uso com React Hook Form
const { register, handleSubmit, formState: { errors } } = useForm({
  resolver: zodResolver(loginSchema)
});
```

## Estilização

### Tailwind CSS

Utilizado para estilização rápida e consistente:

```jsx
<button className="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">
  Entrar
</button>
```

### Styled Components

Utilizado para componentes com estilos mais complexos:

```jsx
const StyledButton = styled.button`
  background-color: ${props => props.primary ? 'var(--primary-color)' : 'white'};
  color: ${props => props.primary ? 'white' : 'var(--primary-color)'};
  padding: 0.5rem 1rem;
  border-radius: 4px;
  transition: all 0.3s ease;
  
  &:hover {
    opacity: 0.9;
    transform: translateY(-2px);
  }
`;
```

## Rotas

Utilizamos React Router para gerenciamento de rotas:

```jsx
<Routes>
  <Route path="/" element={<Home />} />
  <Route path="/login" element={<Login />} />
  <Route path="/register" element={<Register />} />
  
  {/* Rotas protegidas */}
  <Route element={<ProtectedRoute />}>
    <Route path="/dashboard" element={<Dashboard />} />
    <Route path="/courses/:id" element={<CourseDetails />} />
    <Route path="/profile" element={<Profile />} />
  </Route>
  
  {/* Rotas de admin */}
  <Route element={<AdminRoute />}>
    <Route path="/admin/*" element={<AdminPanel />} />
  </Route>
  
  {/* Rotas de professor */}
  <Route element={<TeacherRoute />}>
    <Route path="/teacher/*" element={<TeacherPortal />} />
  </Route>
  
  <Route path="*" element={<NotFound />} />
</Routes>
```

## Autenticação

A autenticação é gerenciada através do Firebase Auth ou JWT:

```typescript
// Exemplo de hook de autenticação
export const useAuth = () => {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  
  useEffect(() => {
    // Firebase Auth
    const unsubscribe = firebase.auth().onAuthStateChanged(user => {
      setUser(user);
      setLoading(false);
    });
    
    return () => unsubscribe();
  }, []);
  
  const login = async (email, password) => {
    try {
      await firebase.auth().signInWithEmailAndPassword(email, password);
      return true;
    } catch (error) {
      console.error(error);
      return false;
    }
  };
  
  const logout = async () => {
    try {
      await firebase.auth().signOut();
      return true;
    } catch (error) {
      console.error(error);
      return false;
    }
  };
  
  return { user, loading, login, logout };
};
```

## Notificações

Utilizamos SweetAlert2 para notificações:

```typescript
// Exemplo de hook de notificação
export const useNotification = () => {
  const showSuccess = (message) => {
    Swal.fire({
      title: 'Sucesso!',
      text: message,
      icon: 'success',
      confirmButtonText: 'OK'
    });
  };
  
  const showError = (message) => {
    Swal.fire({
      title: 'Erro!',
      text: message,
      icon: 'error',
      confirmButtonText: 'OK'
    });
  };
  
  return { showSuccess, showError };
};
```

## Testes

Utilizamos Jest e Testing Library para testes:

```typescript
// Exemplo de teste de componente
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { LoginForm } from './LoginForm';

describe('LoginForm', () => {
  test('should display validation errors when form is submitted with empty fields', async () => {
    render(<LoginForm onSubmit={jest.fn()} />);
    
    // Submeter o formulário vazio
    userEvent.click(screen.getByRole('button', { name: /entrar/i }));
    
    // Verificar se as mensagens de erro são exibidas
    expect(await screen.findByText(/email é obrigatório/i)).toBeInTheDocument();
    expect(screen.getByText(/senha é obrigatória/i)).toBeInTheDocument();
  });
});
```

## Boas Práticas

- **Componentização**: Componentes pequenos e reutilizáveis
- **Tipagem**: Uso consistente de TypeScript para evitar erros
- **Separação de Responsabilidades**: Cada componente com uma única responsabilidade
- **Hooks Personalizados**: Encapsulamento de lógica complexa em hooks
- **Lazy Loading**: Carregamento sob demanda para melhorar performance
- **Memoização**: Uso de useMemo e useCallback para otimização
- **Acessibilidade**: Seguir práticas de WCAG para acessibilidade

## Integração com Backend

A comunicação com o backend é feita através do Axios:

```typescript
// Configuração base do Axios
import axios from 'axios';

const api = axios.create({
  baseURL: import.meta.env.VITE_API_URL,
  timeout: 10000,
  headers: {
    'Content-Type': 'application/json'
  }
});

// Interceptor para adicionar token de autenticação
api.interceptors.request.use(config => {
  const token = localStorage.getItem('token');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

// Interceptor para tratamento de erros
api.interceptors.response.use(
  response => response,
  error => {
    if (error.response && error.response.status === 401) {
      // Redirecionar para login se token expirado
      localStorage.removeItem('token');
      window.location.href = '/login';
    }
    return Promise.reject(error);
  }
);

export default api;
```

## Gamificação

A interface de gamificação inclui:

- Exibição de XP e nível do aluno
- Badges e conquistas
- Progresso visual em cursos
- Ranking de alunos

## Responsividade

A interface é totalmente responsiva, adaptando-se a diferentes tamanhos de tela:

```jsx
// Exemplo de componente responsivo com Tailwind
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
  {courses.map(course => (
    <CourseCard key={course.id} course={course} />
  ))}
</div>
```

## Acessibilidade

Seguimos as diretrizes WCAG para acessibilidade:

- Uso adequado de elementos semânticos HTML
- Atributos ARIA quando necessário
- Contraste adequado de cores
- Suporte a navegação por teclado
- Textos alternativos para imagens 