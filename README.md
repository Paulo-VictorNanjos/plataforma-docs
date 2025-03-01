# Plataforma de Atendimento e Cursos Online (LMS)

## Visão Geral

Esta plataforma é um sistema completo de gerenciamento de aprendizado (LMS) com foco em cursos online e atendimento personalizado. O sistema permite a criação, gestão e consumo de conteúdo educacional, com recursos avançados de gamificação, avaliação de conhecimento e desenvolvimento de soft skills relacionadas a ERPs.

## Estrutura do Projeto

O projeto está dividido em duas partes principais:

- **Frontend**: Interface de usuário construída com React e TypeScript
- **Backend**: API RESTful construída com Node.js, Express e TypeScript

### Tecnologias Utilizadas

#### Frontend
- **Linguagem**: TypeScript
- **Framework**: React
- **Build**: Vite
- **Gerenciamento de Estado**: Redux Toolkit / Context API
- **Estilização**: Tailwind CSS / Styled Components
- **Requisições HTTP**: Axios
- **Validação de Formulários**: React Hook Form + Zod
- **Notificações**: SweetAlert2

#### Backend
- **Linguagem**: TypeScript
- **Framework**: Node.js + Express.js
- **Banco de Dados**: MongoDB (ou PostgreSQL)
- **Autenticação**: Firebase Auth / JWT
- **Upload de Arquivos**: AWS S3 / Firebase Storage
- **Gerenciamento de Tarefas**: BullMQ / Agenda.js

## Funcionalidades Principais

### Autenticação
- Login e Registro com Firebase Auth / JWT
- Recuperação de senha
- Controle de acesso por função (Admin, Professor, Aluno)

### Dashboard do Aluno
- Listagem de cursos matriculados
- Progresso do curso
- Certificados emitidos
- Ranqueamento de aluno com XP

### Gerenciamento de Cursos
- Criar, editar e excluir cursos
- Estrutura hierárquica de Curso > Módulos > Aulas
- Upload de vídeos
- Provas e avaliações

### Portal do Professor
- Criar e gerenciar turmas
- Acompanhar progresso dos alunos
- Criar questionários e materiais complementares

### Admin Dashboard
- Gerenciar usuários
- Gerenciar cursos, módulos e aulas
- Monitoramento de provas e avaliações
- Relatórios de uso

### Fórum e Comunidade
- Fórum de discussão
- Sistema de perguntas e respostas
- Upvotes e destaque para melhores respostas

### Gamificação
- Sistema de XP e Níveis (Júnior, Pleno, Sênior)
- Badges e conquistas
- Progresso baseado em provas concluídas

### Dashboards Avançados
- Relatórios detalhados sobre progresso dos alunos
- Comparação de desempenho entre turmas
- Insights baseados em IA

### Certificados Personalizados
- Geração automática de certificados com QR Code
- Personalização para empresas

### Soft Skills Relacionadas ao ERP
- Avaliação de habilidades no contexto do ERP
- Relatórios de pontos fortes e fracos
- Recomendações personalizadas de cursos

### Notificações
- Notificações em tempo real com Firebase
- Mensagens para alunos e professores

## Estrutura de Diretórios

### Frontend
```
lms-frontend/
 |-- src/
 |   |-- components/ (Componentes reutilizáveis)
 |   |-- pages/ (Páginas principais)
 |   |-- hooks/ (Custom Hooks)
 |   |-- services/ (Serviços de API)
 |   |-- context/ (Gerenciamento de estado)
 |   |-- types/ (Tipagens TypeScript)
 |   |-- utils/ (Funções auxiliares)
 |-- public/
 |-- .env
 |-- package.json
 |-- tsconfig.json
```

### Backend
```
lms-backend/
 |-- src/
 |   |-- controllers/ (Lógica de negócios)
 |   |-- models/ (Modelos do banco de dados)
 |   |-- routes/ (Definição de rotas)
 |   |-- middlewares/ (Autenticação, logs, etc.)
 |   |-- services/ (Serviços auxiliares)
 |   |-- utils/ (Funções auxiliares)
 |   |-- config/ (Configuração do ambiente)
 |-- .env
 |-- package.json
 |-- tsconfig.json
```

## Boas Práticas e Organização do Código

### Código Limpo (Clean Code)
- Nomes descritivos para variáveis e funções
- Funções pequenas com responsabilidade única (SRP)
- Evitar código duplicado (DRY)
- TypeScript para garantir segurança no código

### Organização da Arquitetura
- Frontend: Componentização reutilizável
- Backend: Arquitetura MVC
- Middlewares para autenticação, logs e validações
- Separação de lógica de negócios em serviços

### Segurança
- Autenticação segura com Firebase Auth ou JWT
- Proteção contra ataques CSRF e XSS
- Validação de entradas
- Logs e auditoria de ações administrativas

### Testes
- Testes unitários e de integração com Jest e Testing Library
- Cobertura mínima para funcionalidades críticas

## Documentação Adicional

Para informações mais detalhadas, consulte:

- [Documentação do Frontend](./docs/frontend.md)
- [Documentação do Backend](./docs/backend.md)
- [Guia de API](./docs/api-guide.md)
- [Guia de Instalação](./docs/installation.md)
