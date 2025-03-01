# Guia de Desenvolvimento

## Visão Geral

Este guia estabelece as práticas, padrões e convenções a serem seguidas no desenvolvimento da plataforma LMS. O objetivo é manter a consistência do código, facilitar a manutenção e garantir a qualidade do software.

## Padrões de Código

### Nomenclatura

1. **Arquivos e Diretórios**
   - Componentes React: PascalCase (ex: `UserProfile.tsx`)
   - Hooks: camelCase com prefixo "use" (ex: `useAuth.ts`)
   - Utilitários: camelCase (ex: `formatDate.ts`)
   - Testes: nome do arquivo + `.test.ts` (ex: `UserProfile.test.tsx`)

2. **Variáveis e Funções**
   - Variáveis: camelCase, nomes descritivos
   - Funções: camelCase, verbos que descrevem a ação
   - Constantes: UPPER_SNAKE_CASE
   - Interfaces/Types: PascalCase com prefixo I para interfaces

```typescript
// Bom
const userProfile = { ... };
function calculateTotalPrice() { ... }
const MAX_ATTEMPTS = 3;
interface IUserProfile { ... }

// Ruim
const user_profile = { ... };
function calc_price() { ... }
const maxAttempts = 3;
interface userProfile { ... }
```

### Estrutura de Componentes React

```typescript
// UserProfile.tsx
import React from 'react';
import styles from './UserProfile.module.css';

interface IUserProfileProps {
  user: IUser;
  onUpdate: (user: IUser) => void;
}

export const UserProfile: React.FC<IUserProfileProps> = ({ user, onUpdate }) => {
  // Estado local no topo
  const [isEditing, setIsEditing] = useState(false);
  
  // Hooks personalizados
  const { updateUser } = useUser();
  
  // Handlers de eventos
  const handleSubmit = async (data: IUserUpdateData) => {
    await updateUser(data);
    onUpdate(data);
  };
  
  // Renderização condicional em variáveis
  const renderEditForm = () => (
    <form onSubmit={handleSubmit}>
      {/* ... */}
    </form>
  );
  
  return (
    <div className={styles.container}>
      {isEditing ? renderEditForm() : renderProfile()}
    </div>
  );
};
```

### Estilização

1. **CSS Modules**
```typescript
// UserProfile.module.css
.container {
  padding: 1rem;
  border-radius: 0.5rem;
}

// UserProfile.tsx
import styles from './UserProfile.module.css';

<div className={styles.container}>
```

2. **Tailwind CSS**
```typescript
<div className="p-4 rounded-lg shadow-md hover:shadow-lg transition-shadow">
```

3. **Styled Components**
```typescript
const StyledButton = styled.button`
  background-color: ${props => props.variant === 'primary' ? 'blue' : 'gray'};
  padding: 0.5rem 1rem;
  border-radius: 0.25rem;
`;
```

## Gerenciamento de Estado

### Context API

Use para estados globais simples e temas:

```typescript
// ThemeContext.tsx
export const ThemeContext = createContext<IThemeContext>({
  theme: 'light',
  toggleTheme: () => {},
});

export const ThemeProvider: React.FC = ({ children }) => {
  const [theme, setTheme] = useState<'light' | 'dark'>('light');
  
  const toggleTheme = () => {
    setTheme(prev => prev === 'light' ? 'dark' : 'light');
  };
  
  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
};
```

### Redux Toolkit

Use para estados complexos e compartilhados:

```typescript
// courseSlice.ts
import { createSlice, PayloadAction } from '@reduxjs/toolkit';

const courseSlice = createSlice({
  name: 'courses',
  initialState,
  reducers: {
    setCourses: (state, action: PayloadAction<Course[]>) => {
      state.items = action.payload;
    },
    updateProgress: (state, action) => {
      const { courseId, progress } = action.payload;
      const course = state.items.find(c => c.id === courseId);
      if (course) {
        course.progress = progress;
      }
    }
  }
});
```

## Tratamento de Erros

### Frontend

```typescript
// Componente de erro global
export const ErrorBoundary: React.FC = ({ children }) => {
  const [hasError, setHasError] = useState(false);
  
  if (hasError) {
    return <ErrorPage />;
  }
  
  return children;
};

// Tratamento de erros em requisições
try {
  await api.post('/courses', courseData);
} catch (error) {
  if (error.response?.status === 401) {
    // Redirecionar para login
  } else {
    // Mostrar mensagem de erro genérica
    showErrorNotification(error.message);
  }
}
```

### Backend

```typescript
// Middleware de erro global
export const errorHandler: ErrorRequestHandler = (err, req, res, next) => {
  logger.error(err);
  
  if (err instanceof ValidationError) {
    return res.status(400).json({
      error: 'Dados inválidos',
      details: err.errors
    });
  }
  
  res.status(500).json({
    error: 'Erro interno do servidor'
  });
};
```

## Testes

### Testes Unitários

```typescript
// UserProfile.test.tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { UserProfile } from './UserProfile';

describe('UserProfile', () => {
  it('should render user information', () => {
    const user = {
      name: 'John Doe',
      email: 'john@example.com'
    };
    
    render(<UserProfile user={user} />);
    
    expect(screen.getByText(user.name)).toBeInTheDocument();
    expect(screen.getByText(user.email)).toBeInTheDocument();
  });
  
  it('should call onUpdate when form is submitted', async () => {
    const onUpdate = jest.fn();
    render(<UserProfile user={mockUser} onUpdate={onUpdate} />);
    
    fireEvent.click(screen.getByText('Editar'));
    fireEvent.change(screen.getByLabelText('Nome'), {
      target: { value: 'New Name' }
    });
    fireEvent.click(screen.getByText('Salvar'));
    
    expect(onUpdate).toHaveBeenCalledWith(expect.objectContaining({
      name: 'New Name'
    }));
  });
});
```

### Testes de Integração

```typescript
// courses.test.ts
describe('Courses API', () => {
  it('should create a new course', async () => {
    const token = await loginUser(adminCredentials);
    
    const response = await request(app)
      .post('/api/courses')
      .set('Authorization', `Bearer ${token}`)
      .send(mockCourseData);
      
    expect(response.status).toBe(201);
    expect(response.body).toHaveProperty('id');
  });
});
```

## Performance

### Otimizações React

1. **Memoização**
```typescript
// Componente pesado
const ExpensiveComponent = React.memo(({ data }) => {
  // Renderização complexa
});

// Callbacks
const handleClick = useCallback(() => {
  // Lógica do callback
}, [dependências]);

// Valores computados
const computedValue = useMemo(() => {
  return expensiveCalculation(props.data);
}, [props.data]);
```

2. **Code Splitting**
```typescript
const DashboardPage = lazy(() => import('./pages/Dashboard'));

function App() {
  return (
    <Suspense fallback={<Loading />}>
      <DashboardPage />
    </Suspense>
  );
}
```

### Otimizações Backend

1. **Caching**
```typescript
// Redis cache
export class CourseService {
  async getCourse(id: string) {
    // Tentar cache primeiro
    const cached = await redis.get(`course:${id}`);
    if (cached) {
      return JSON.parse(cached);
    }
    
    // Buscar do banco e cachear
    const course = await Course.findById(id);
    await redis.set(`course:${id}`, JSON.stringify(course), 'EX', 3600);
    return course;
  }
}
```

2. **Query Optimization**
```typescript
// Eager loading de relações
const course = await Course.findById(id)
  .populate('instructor')
  .populate('modules')
  .lean();
```

## Segurança

### Frontend

1. **XSS Prevention**
```typescript
// Sanitização de HTML
import DOMPurify from 'dompurify';

const sanitizedHtml = DOMPurify.sanitize(userProvidedHtml);
```

2. **CSRF Protection**
```typescript
// Adicionar token CSRF em requisições
api.interceptors.request.use(config => {
  config.headers['X-CSRF-Token'] = getCsrfToken();
  return config;
});
```

### Backend

1. **Input Validation**
```typescript
const createCourseSchema = z.object({
  title: z.string().min(3).max(100),
  price: z.number().min(0),
  description: z.string()
});

app.post('/courses', validateBody(createCourseSchema), async (req, res) => {
  // Corpo já validado
});
```

2. **Security Headers**
```typescript
app.use(helmet());
app.use(cors({
  origin: process.env.FRONTEND_URL,
  credentials: true
}));
```

## Documentação

### Comentários

```typescript
/**
 * Calcula o progresso total do usuário em um curso.
 * @param courseId - ID do curso
 * @param userId - ID do usuário
 * @returns Porcentagem de conclusão (0-100)
 * @throws {CourseNotFoundError} Se o curso não existir
 */
async function calculateProgress(courseId: string, userId: string): Promise<number> {
  // ...
}
```

### Documentação de API

```typescript
/**
 * @swagger
 * /courses/{id}:
 *   get:
 *     summary: Retorna detalhes de um curso
 *     parameters:
 *       - name: id
 *         in: path
 *         required: true
 *         schema:
 *           type: string
 *     responses:
 *       200:
 *         description: Detalhes do curso
 *       404:
 *         description: Curso não encontrado
 */
router.get('/courses/:id', courseController.getById);
```

## Git

### Commits

- Use commits atômicos
- Mensagens claras e descritivas
- Prefixos para categorizar:
  - feat: Nova funcionalidade
  - fix: Correção de bug
  - docs: Documentação
  - style: Formatação
  - refactor: Refatoração
  - test: Testes
  - chore: Tarefas de manutenção

### Branches

- main: Código em produção
- develop: Desenvolvimento
- feature/: Novas funcionalidades
- bugfix/: Correções
- release/: Preparação para release

### Pull Requests

- Descrição clara do que foi feito
- Referência para issues relacionadas
- Testes passando
- Code review por pelo menos um desenvolvedor

## CI/CD

### GitHub Actions

```yaml
name: CI

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main, develop ]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Setup Node.js
        uses: actions/setup-node@v2
        with:
          node-version: '18'
      - name: Install dependencies
        run: npm ci
      - name: Run tests
        run: npm test
      - name: Run linter
        run: npm run lint
```

## Monitoramento

### Logs

```typescript
const logger = winston.createLogger({
  level: 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.json()
  ),
  transports: [
    new winston.transports.File({ filename: 'error.log', level: 'error' }),
    new winston.transports.File({ filename: 'combined.log' })
  ]
});

// Uso
logger.info('Novo usuário registrado', {
  userId: user.id,
  email: user.email
});
```

### Métricas

```typescript
// Prometheus metrics
const requestDuration = new client.Histogram({
  name: 'http_request_duration_seconds',
  help: 'Duration of HTTP requests in seconds',
  labelNames: ['method', 'route', 'status']
});

app.use((req, res, next) => {
  const start = Date.now();
  res.on('finish', () => {
    const duration = Date.now() - start;
    requestDuration
      .labels(req.method, req.route?.path, res.statusCode)
      .observe(duration / 1000);
  });
  next();
});
```

## Review Checklist

### Code Review

- [ ] Código segue os padrões estabelecidos
- [ ] Testes adequados foram adicionados
- [ ] Documentação foi atualizada
- [ ] Não há código duplicado
- [ ] Tratamento de erros adequado
- [ ] Performance foi considerada
- [ ] Segurança foi considerada

### Pull Request

- [ ] Branch está atualizada com a main
- [ ] Commits são atômicos e bem descritos
- [ ] CI/CD passou sem erros
- [ ] Conflitos foram resolvidos
- [ ] Revisão de código foi feita
- [ ] Testes passaram localmente 