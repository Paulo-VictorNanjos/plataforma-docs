# Guia da API

## Visão Geral

A API da plataforma LMS segue os princípios REST e utiliza JSON para comunicação. Todas as requisições devem ser autenticadas (exceto rotas públicas) e incluir o token JWT no header de autorização.

## Base URL

```
http://localhost:3000/api/v1
```

## Autenticação

### Login

```http
POST /auth/login
Content-Type: application/json

{
  "email": "usuario@exemplo.com",
  "password": "senha123"
}
```

Resposta:
```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "id": "123",
    "name": "Usuário Exemplo",
    "email": "usuario@exemplo.com",
    "role": "student"
  }
}
```

### Registro

```http
POST /auth/register
Content-Type: application/json

{
  "name": "Novo Usuário",
  "email": "novo@exemplo.com",
  "password": "senha123",
  "role": "student"
}
```

## Cursos

### Listar Cursos

```http
GET /courses
Authorization: Bearer <token>
```

Parâmetros de Query:
- `page`: Número da página (default: 1)
- `limit`: Itens por página (default: 10)
- `search`: Termo de busca
- `status`: Status do curso (draft, published, archived)

Resposta:
```json
{
  "data": [
    {
      "id": "1",
      "title": "Curso de React",
      "description": "Aprenda React do zero",
      "instructor": {
        "id": "123",
        "name": "Professor"
      },
      "thumbnail": "https://exemplo.com/thumb.jpg",
      "price": 99.90,
      "rating": 4.5,
      "studentsCount": 150
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 10,
    "total": 45,
    "pages": 5
  }
}
```

### Detalhes do Curso

```http
GET /courses/:id
Authorization: Bearer <token>
```

Resposta:
```json
{
  "id": "1",
  "title": "Curso de React",
  "description": "Aprenda React do zero",
  "instructor": {
    "id": "123",
    "name": "Professor",
    "bio": "Especialista em React"
  },
  "modules": [
    {
      "id": "m1",
      "title": "Introdução",
      "lessons": [
        {
          "id": "l1",
          "title": "Bem-vindo",
          "duration": 300,
          "type": "video"
        }
      ]
    }
  ],
  "price": 99.90,
  "rating": 4.5,
  "reviews": [
    {
      "id": "r1",
      "user": {
        "name": "Aluno"
      },
      "rating": 5,
      "comment": "Excelente curso!"
    }
  ]
}
```

### Criar Curso

```http
POST /courses
Authorization: Bearer <token>
Content-Type: application/json

{
  "title": "Novo Curso",
  "description": "Descrição do curso",
  "price": 99.90,
  "modules": [
    {
      "title": "Módulo 1",
      "lessons": [
        {
          "title": "Lição 1",
          "content": "Conteúdo da lição",
          "duration": 300
        }
      ]
    }
  ]
}
```

## Usuários

### Perfil do Usuário

```http
GET /users/me
Authorization: Bearer <token>
```

Resposta:
```json
{
  "id": "123",
  "name": "Usuário",
  "email": "usuario@exemplo.com",
  "role": "student",
  "level": 5,
  "xp": 1500,
  "badges": [
    {
      "id": "b1",
      "name": "Iniciante",
      "icon": "badge-url.png"
    }
  ],
  "enrolledCourses": [
    {
      "id": "c1",
      "title": "Curso",
      "progress": 45
    }
  ]
}
```

### Atualizar Perfil

```http
PUT /users/me
Authorization: Bearer <token>
Content-Type: application/json

{
  "name": "Novo Nome",
  "bio": "Nova bio"
}
```

## Matrículas

### Matricular em Curso

```http
POST /enrollments
Authorization: Bearer <token>
Content-Type: application/json

{
  "courseId": "123"
}
```

### Progresso do Curso

```http
GET /enrollments/:courseId/progress
Authorization: Bearer <token>
```

Resposta:
```json
{
  "courseId": "123",
  "progress": 45,
  "completedLessons": [
    {
      "lessonId": "l1",
      "completedAt": "2024-03-01T12:00:00Z"
    }
  ],
  "quizzes": [
    {
      "quizId": "q1",
      "score": 85,
      "completedAt": "2024-03-01T12:30:00Z"
    }
  ]
}
```

## Fórum

### Criar Tópico

```http
POST /forum/topics
Authorization: Bearer <token>
Content-Type: application/json

{
  "title": "Dúvida sobre React",
  "content": "Como funciona o useEffect?",
  "courseId": "123"
}
```

### Listar Tópicos

```http
GET /forum/topics
Authorization: Bearer <token>
```

Parâmetros de Query:
- `courseId`: Filtrar por curso
- `search`: Termo de busca
- `page`: Número da página
- `limit`: Itens por página

## Upload de Arquivos

### Upload de Mídia

```http
POST /upload/media
Authorization: Bearer <token>
Content-Type: multipart/form-data

file: <arquivo>
```

Resposta:
```json
{
  "url": "https://storage.exemplo.com/media/123.mp4",
  "type": "video/mp4",
  "size": 1024000
}
```

## Webhooks

### Notificações de Pagamento

```http
POST /webhooks/payment
Content-Type: application/json
X-Webhook-Secret: <secret>

{
  "type": "payment.success",
  "data": {
    "paymentId": "123",
    "courseId": "456",
    "userId": "789",
    "amount": 99.90
  }
}
```

## Códigos de Erro

| Código | Descrição |
|--------|-----------|
| 400 | Requisição inválida |
| 401 | Não autorizado |
| 403 | Proibido |
| 404 | Não encontrado |
| 422 | Erro de validação |
| 429 | Muitas requisições |
| 500 | Erro interno |

## Limites de Taxa

- 100 requisições por minuto por IP
- 1000 requisições por hora por usuário
- 50 uploads por hora por usuário

## Boas Práticas

1. **Autenticação**
   - Sempre envie o token JWT no header Authorization
   - Renove o token antes de expirar
   - Armazene tokens de forma segura

2. **Requisições**
   - Use compressão gzip para payloads grandes
   - Implemente retry com backoff exponencial
   - Cache respostas quando apropriado

3. **Upload de Arquivos**
   - Verifique o tipo e tamanho antes do upload
   - Use upload multipart para arquivos grandes
   - Implemente retry para uploads interrompidos

4. **Tratamento de Erros**
   - Sempre verifique o código de status
   - Implemente tratamento de erro global
   - Log de erros com contexto adequado

## Exemplos de Código

### JavaScript/TypeScript (Axios)

```typescript
import axios from 'axios';

const api = axios.create({
  baseURL: 'http://localhost:3000/api/v1',
  timeout: 10000,
});

// Interceptor para token
api.interceptors.request.use(config => {
  const token = localStorage.getItem('token');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

// Exemplo de uso
async function getCourses() {
  try {
    const response = await api.get('/courses', {
      params: {
        page: 1,
        limit: 10,
        status: 'published'
      }
    });
    return response.data;
  } catch (error) {
    console.error('Erro ao buscar cursos:', error);
    throw error;
  }
}
```

### Python (Requests)

```python
import requests

class LMSApi:
    def __init__(self, base_url, token):
        self.base_url = base_url
        self.headers = {
            'Authorization': f'Bearer {token}',
            'Content-Type': 'application/json'
        }
    
    def get_courses(self, page=1, limit=10):
        response = requests.get(
            f'{self.base_url}/courses',
            headers=self.headers,
            params={'page': page, 'limit': limit}
        )
        response.raise_for_status()
        return response.json()
    
    def enroll_course(self, course_id):
        response = requests.post(
            f'{self.base_url}/enrollments',
            headers=self.headers,
            json={'courseId': course_id}
        )
        response.raise_for_status()
        return response.json()
```

## Websockets

Para funcionalidades em tempo real, use a conexão WebSocket:

```javascript
const ws = new WebSocket('ws://localhost:3000/ws');

ws.onmessage = (event) => {
  const data = JSON.parse(event.data);
  switch (data.type) {
    case 'notification':
      showNotification(data.message);
      break;
    case 'progress_update':
      updateProgress(data.courseId, data.progress);
      break;
  }
};
```

## Segurança

1. **HTTPS**
   - Todas as requisições em produção devem usar HTTPS
   - Certificados válidos e atualizados

2. **Tokens**
   - JWTs com tempo de expiração curto
   - Refresh tokens para renovação segura
   - Revogação de tokens quando necessário

3. **Rate Limiting**
   - Limites por IP e por usuário
   - Backoff exponencial em caso de erro

4. **Validação**
   - Sanitização de inputs
   - Validação de tipos e formatos
   - Prevenção contra injeção

## Monitoramento

1. **Métricas**
   - Tempo de resposta
   - Taxa de erro
   - Uso de recursos

2. **Logs**
   - Nível de log apropriado
   - Contexto suficiente
   - Rotação de logs

3. **Alertas**
   - Latência alta
   - Erros frequentes
   - Uso excessivo de recursos

## Versionamento

A API usa versionamento no path (/v1). Mudanças incompatíveis serão lançadas em novas versões. 