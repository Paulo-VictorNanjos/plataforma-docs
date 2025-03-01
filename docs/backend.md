# Documentação do Backend

## Visão Geral

O backend da plataforma LMS é construído com Node.js e Express, utilizando TypeScript para garantir maior segurança e manutenibilidade do código. A API RESTful fornece todos os serviços necessários para o funcionamento da plataforma de ensino, incluindo autenticação, gerenciamento de cursos, gamificação e integração com serviços externos.

## Tecnologias Principais

- **Node.js**: Ambiente de execução JavaScript
- **Express.js**: Framework web para Node.js
- **TypeScript**: Superset tipado de JavaScript
- **MongoDB/PostgreSQL**: Banco de dados
- **Firebase Auth/JWT**: Autenticação
- **AWS S3/Firebase Storage**: Armazenamento de arquivos
- **BullMQ/Agenda.js**: Gerenciamento de filas e tarefas
- **Jest**: Testes automatizados
- **Swagger/OpenAPI**: Documentação da API

## Estrutura de Diretórios

```
lms-backend/
 |-- src/
 |   |-- config/
 |   |   |-- database.ts
 |   |   |-- auth.ts
 |   |   |-- storage.ts
 |   |   |-- queue.ts
 |   |-- controllers/
 |   |   |-- auth.controller.ts
 |   |   |-- course.controller.ts
 |   |   |-- user.controller.ts
 |   |   |-- forum.controller.ts
 |   |-- models/
 |   |   |-- user.model.ts
 |   |   |-- course.model.ts
 |   |   |-- lesson.model.ts
 |   |   |-- forum.model.ts
 |   |-- routes/
 |   |   |-- auth.routes.ts
 |   |   |-- course.routes.ts
 |   |   |-- user.routes.ts
 |   |   |-- forum.routes.ts
 |   |-- middlewares/
 |   |   |-- auth.middleware.ts
 |   |   |-- error.middleware.ts
 |   |   |-- validation.middleware.ts
 |   |   |-- upload.middleware.ts
 |   |-- services/
 |   |   |-- auth.service.ts
 |   |   |-- course.service.ts
 |   |   |-- user.service.ts
 |   |   |-- email.service.ts
 |   |   |-- storage.service.ts
 |   |-- utils/
 |   |   |-- validators.ts
 |   |   |-- formatters.ts
 |   |   |-- errors.ts
 |   |-- types/
 |   |   |-- express.d.ts
 |   |   |-- models.d.ts
 |   |-- app.ts
 |   |-- server.ts
 |-- tests/
 |   |-- unit/
 |   |-- integration/
 |-- .env
 |-- package.json
 |-- tsconfig.json
 |-- jest.config.js
```

## Modelos de Dados

### Usuário
```typescript
interface User {
  id: string;
  name: string;
  email: string;
  password: string;
  role: 'admin' | 'teacher' | 'student';
  level: number;
  xp: number;
  badges: Badge[];
  courses: Course[];
  createdAt: Date;
  updatedAt: Date;
}
```

### Curso
```typescript
interface Course {
  id: string;
  title: string;
  description: string;
  thumbnail: string;
  instructor: User;
  modules: Module[];
  students: User[];
  price: number;
  rating: number;
  status: 'draft' | 'published' | 'archived';
  createdAt: Date;
  updatedAt: Date;
}
```

## APIs e Rotas

### Autenticação
```typescript
// auth.routes.ts
router.post('/register', authController.register);
router.post('/login', authController.login);
router.post('/forgot-password', authController.forgotPassword);
router.post('/reset-password', authController.resetPassword);
router.get('/me', authMiddleware, authController.getProfile);
```

### Cursos
```typescript
// course.routes.ts
router.get('/courses', courseController.list);
router.get('/courses/:id', courseController.getById);
router.post('/courses', authMiddleware, courseController.create);
router.put('/courses/:id', authMiddleware, courseController.update);
router.delete('/courses/:id', authMiddleware, courseController.delete);
```

## Middlewares

### Autenticação
```typescript
// auth.middleware.ts
export const authMiddleware = async (req: Request, res: Response, next: NextFunction) => {
  try {
    const token = req.headers.authorization?.split(' ')[1];
    if (!token) {
      throw new UnauthorizedError('Token não fornecido');
    }
    
    const decoded = verifyToken(token);
    req.user = await UserModel.findById(decoded.userId);
    next();
  } catch (error) {
    next(new UnauthorizedError('Token inválido'));
  }
};
```

## Serviços

### Upload de Arquivos
```typescript
// storage.service.ts
export class StorageService {
  async uploadFile(file: Express.Multer.File): Promise<string> {
    const s3 = new AWS.S3();
    const key = `uploads/${Date.now()}-${file.originalname}`;
    
    await s3.upload({
      Bucket: process.env.AWS_BUCKET_NAME,
      Key: key,
      Body: file.buffer,
      ContentType: file.mimetype,
    }).promise();
    
    return `https://${process.env.AWS_BUCKET_NAME}.s3.amazonaws.com/${key}`;
  }
}
```

## Validação de Dados

Utilizamos Zod para validação de dados:

```typescript
// validators.ts
const createCourseSchema = z.object({
  title: z.string().min(3).max(100),
  description: z.string().min(10),
  price: z.number().min(0),
  modules: z.array(z.object({
    title: z.string(),
    lessons: z.array(z.object({
      title: z.string(),
      content: z.string(),
      duration: z.number()
    }))
  }))
});
```

## Tratamento de Erros

```typescript
// error.middleware.ts
export const errorHandler = (
  err: Error,
  req: Request,
  res: Response,
  next: NextFunction
) => {
  if (err instanceof ApiError) {
    return res.status(err.statusCode).json({
      status: 'error',
      message: err.message
    });
  }

  return res.status(500).json({
    status: 'error',
    message: 'Erro interno do servidor'
  });
};
```

## Testes

### Testes Unitários
```typescript
// course.service.test.ts
describe('CourseService', () => {
  it('should create a new course', async () => {
    const courseData = {
      title: 'Curso de Test',
      description: 'Descrição do curso',
      price: 99.90
    };

    const course = await courseService.create(courseData);
    expect(course).toHaveProperty('id');
    expect(course.title).toBe(courseData.title);
  });
});
```

## Segurança

### Headers de Segurança
```typescript
// security.middleware.ts
app.use(helmet());
app.use(cors({
  origin: process.env.FRONTEND_URL,
  credentials: true
}));
```

### Rate Limiting
```typescript
// rate-limit.middleware.ts
const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutos
  max: 100 // limite de 100 requisições por IP
});
```

## Monitoramento e Logs

### Winston Logger
```typescript
// logger.ts
const logger = winston.createLogger({
  level: 'info',
  format: winston.format.json(),
  transports: [
    new winston.transports.File({ filename: 'error.log', level: 'error' }),
    new winston.transports.File({ filename: 'combined.log' })
  ]
});
```

## Integração com Serviços Externos

### Email
```typescript
// email.service.ts
export class EmailService {
  async sendWelcomeEmail(user: User) {
    await this.transporter.sendMail({
      to: user.email,
      subject: 'Bem-vindo à Plataforma',
      template: 'welcome',
      context: {
        name: user.name
      }
    });
  }
}
```

## Variáveis de Ambiente

```env
# .env.example
NODE_ENV=development
PORT=3000
DATABASE_URL=mongodb://localhost:27017/lms
JWT_SECRET=your-secret-key
AWS_ACCESS_KEY_ID=your-access-key
AWS_SECRET_ACCESS_KEY=your-secret-key
AWS_BUCKET_NAME=your-bucket-name
```

## Scripts de Desenvolvimento

```json
{
  "scripts": {
    "dev": "ts-node-dev --respawn src/server.ts",
    "build": "tsc",
    "start": "node dist/server.js",
    "test": "jest",
    "migrate": "prisma migrate dev",
    "seed": "ts-node prisma/seed.ts"
  }
}
```

## Boas Práticas

1. **Arquitetura Limpa**
   - Separação clara de responsabilidades
   - Injeção de dependências
   - Princípios SOLID

2. **Segurança**
   - Sanitização de inputs
   - Proteção contra ataques comuns
   - Autenticação e autorização robustas

3. **Performance**
   - Caching de dados
   - Otimização de consultas
   - Compressão de respostas

4. **Manutenibilidade**
   - Código bem documentado
   - Testes automatizados
   - Logs detalhados

5. **Escalabilidade**
   - Arquitetura stateless
   - Filas para processamento assíncrono
   - Cache distribuído 