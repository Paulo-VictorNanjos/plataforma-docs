# Guia de Instalação

Este guia fornece instruções detalhadas para configurar o ambiente de desenvolvimento da plataforma LMS.

## Pré-requisitos

- Node.js (v18 ou superior)
- npm ou yarn
- MongoDB
- Git

## Configuração do Ambiente

### 1. Clone o Repositório

```bash
git clone https://github.com/seu-usuario/lms-platform.git
cd lms-platform
```

### 2. Configuração do Backend

1. Entre no diretório do backend:
```bash
cd backend
```

2. Instale as dependências:
```bash
npm install
# ou
yarn install
```

3. Configure as variáveis de ambiente:
```bash
cp .env.example .env
```

4. Edite o arquivo `.env` com suas configurações:
```env
NODE_ENV=development
PORT=3000
DATABASE_URL=mongodb://localhost:27017/lms
JWT_SECRET=seu-secret-key
AWS_ACCESS_KEY_ID=sua-access-key
AWS_SECRET_ACCESS_KEY=sua-secret-key
AWS_BUCKET_NAME=seu-bucket-name
```

5. Execute as migrações do banco de dados:
```bash
npm run migrate
# ou
yarn migrate
```

6. (Opcional) Execute o seed do banco de dados:
```bash
npm run seed
# ou
yarn seed
```

7. Inicie o servidor de desenvolvimento:
```bash
npm run dev
# ou
yarn dev
```

### 3. Configuração do Frontend

1. Em outro terminal, entre no diretório do frontend:
```bash
cd frontend
```

2. Instale as dependências:
```bash
npm install
# ou
yarn install
```

3. Configure as variáveis de ambiente:
```bash
cp .env.example .env
```

4. Edite o arquivo `.env`:
```env
VITE_API_URL=http://localhost:3000
VITE_FIREBASE_API_KEY=sua-api-key
VITE_FIREBASE_AUTH_DOMAIN=seu-auth-domain
VITE_FIREBASE_PROJECT_ID=seu-project-id
```

5. Inicie o servidor de desenvolvimento:
```bash
npm run dev
# ou
yarn dev
```

## Configurações Adicionais

### Configuração do Firebase (se aplicável)

1. Crie um projeto no [Firebase Console](https://console.firebase.google.com)
2. Ative a Autenticação e selecione os provedores desejados
3. Copie as credenciais do projeto para o arquivo `.env`

### Configuração do AWS S3 (se aplicável)

1. Crie um bucket S3 na [AWS Console](https://console.aws.amazon.com/s3)
2. Configure as permissões CORS do bucket:
```json
[
  {
    "AllowedHeaders": ["*"],
    "AllowedMethods": ["GET", "POST", "PUT", "DELETE"],
    "AllowedOrigins": ["http://localhost:5173"],
    "ExposeHeaders": []
  }
]
```

### Configuração do MongoDB

1. Instale o MongoDB localmente ou use MongoDB Atlas
2. Crie um banco de dados chamado 'lms'
3. Atualize a URL de conexão no arquivo `.env`

## Verificação da Instalação

### Backend

1. Verifique se o servidor está rodando:
```bash
curl http://localhost:3000/api/health
```

2. Verifique os logs:
```bash
tail -f logs/combined.log
```

### Frontend

1. Acesse http://localhost:5173 no navegador
2. Verifique se a página inicial carrega corretamente
3. Tente fazer login com as credenciais de teste:
   - Email: admin@example.com
   - Senha: password123

## Solução de Problemas

### Problemas Comuns

1. **Erro de conexão com o banco de dados**
   - Verifique se o MongoDB está rodando
   - Confirme a URL de conexão no `.env`
   - Verifique as permissões do usuário do banco

2. **Erro no frontend**
   - Limpe o cache do navegador
   - Delete node_modules e reinstale
   - Verifique a URL da API no `.env`

3. **Erro de CORS**
   - Verifique as configurações de CORS no backend
   - Confirme se as origens permitidas estão corretas

### Comandos Úteis

```bash
# Limpar cache do npm
npm cache clean --force

# Verificar portas em uso
netstat -ano | findstr :3000

# Logs do MongoDB
tail -f /var/log/mongodb/mongod.log

# Reiniciar serviços
npm run dev --reset-cache
```

## Ambiente de Produção

Para configurar o ambiente de produção, siga estas etapas adicionais:

1. Configure variáveis de ambiente para produção
2. Configure um servidor nginx como proxy reverso
3. Configure PM2 para gerenciamento de processos
4. Configure certificados SSL
5. Configure backups automáticos do banco de dados

## Suporte

Em caso de problemas:

1. Consulte a [documentação completa](./README.md)
2. Verifique as [issues no GitHub](https://github.com/seu-usuario/lms-platform/issues)
3. Entre em contato com a equipe de desenvolvimento

## Próximos Passos

Após a instalação:

1. Explore a [documentação da API](./api-guide.md)
2. Revise as [boas práticas de desenvolvimento](./development-guide.md)
3. Configure seu ambiente de teste
4. Familiarize-se com o fluxo de trabalho do Git 