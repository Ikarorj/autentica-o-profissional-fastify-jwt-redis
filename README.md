# 🔐 API de Autenticação com Fastify, JWT e Redis

Este projeto implementa um fluxo completo de autenticação utilizando **Fastify**, **JWT (Access Token + Refresh Token)** e **Redis** (executado via Docker) para gerenciamento de sessão com **TTL**.

O objetivo é demonstrar, de forma prática, conceitos vistos em sala de aula sobre autenticação, controle de sessão, renovação de tokens e invalidação manual.

---

## 🚀 Tecnologias Utilizadas

- Node.js
- TypeScript
- Fastify
- JWT (jsonwebtoken)
- Redis
- Docker
- bcryptjs

---

## 🎯 Funcionalidades

- Login com Access Token (curta duração)
- Geração de Refresh Token (longa duração)
- Armazenamento do Access Token no Redis com TTL
- Validação de token e sessão em rotas protegidas
- Renovação de sessão via Refresh Token
- Logout com invalidação de sessão
- Tratamento de erros e boas práticas de segurança

---

## 📦 Estrutura do Projeto

src/
├── controllers/
│ └── authController.ts
├── routes/
│ └── authRoutes.ts
├── services/
│ └── tokenServices.ts
├── redis/
│ └── clienteRedis.ts
├── types/
│ └── user.ts
├── users.json
├── server.ts
.env

yaml
Copiar código

---

## ⚙️ Configuração do Ambiente

### 1️⃣ Clonar o repositório

```bash
git clone https://github.com/seu-usuario/api-auth-fastify-redis.git
cd api-auth-fastify-redis
2️⃣ Instalar dependências
bash
Copiar código
npm install
3️⃣ Criar o arquivo .env
env
Copiar código
PORT=3000

ACCESS_SECRET=access-secret
REFRESH_SECRET=refresh-secret

ACCESS_TTL_SECONDS=30

USE_REDIS=true
REDIS_URL=redis://127.0.0.1:6379
4️⃣ Subir o Redis com Docker
bash
Copiar código
docker run -d --name redis-auth -p 6379:6379 redis
Verifique se está rodando:

bash
Copiar código
docker ps
5️⃣ Rodar a aplicação
bash
Copiar código
npm run dev
A API estará disponível em:

arduino
Copiar código
http://localhost:3000
📌 Endpoints
🔐 POST /auth/login
Realiza o login do usuário e gera os tokens.

Body:

json
Copiar código
{
  "email": "aluno@ifpi.edu.br",
  "password": "123456"
}
Resposta:

json
Copiar código
{
  "accessToken": "...",
  "refreshToken": "..."
}
🔒 GET /auth/protected
Rota protegida que valida:

Access Token (JWT)

Sessão ativa no Redis

Header:

makefile
Copiar código
Authorization: Bearer <accessToken>
🔁 POST /auth/refresh
Renova a sessão utilizando o Refresh Token.

Body:

json
Copiar código
{
  "refreshToken": "..."
}
🚪 POST /auth/logout
Encerra a sessão do usuário.

Header:

makefile
Copiar código
Authorization: Bearer <accessToken>
🧪 Exemplos de Uso (cURL)
Login
bash
Copiar código
curl -X POST http://localhost:3000/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"aluno@ifpi.edu.br","password":"123456"}'
Rota protegida
bash
Copiar código
curl http://localhost:3000/auth/protected \
  -H "Authorization: Bearer <accessToken>"
Refresh Token
bash
Copiar código
curl -X POST http://localhost:3000/auth/refresh \
  -H "Content-Type: application/json" \
  -d '{"refreshToken":"<refreshToken>"}'
Logout
bash
Copiar código
curl -X POST http://localhost:3000/auth/logout \
  -H "Authorization: Bearer <accessToken>"
🧠 Fluxo de Autenticação
🔑 Geração de Tokens
Implementação: src/services/tokenServices.ts

🗄️ Sessão no Redis
Chave: token:<userId>

TTL sincronizado com o Access Token

✅ Validação
JWT válido

Token presente no Redis

🔁 Renovação
Refresh Token válido

Novo Access Token gerado

Redis atualizado

❌ Logout
Token removido do Redis

Sessão invalidada

📚 Observações
Projeto com fins educacionais para demonstrar:

Autenticação moderna

Controle de sessão server-side

Uso de Redis como cache

Boas práticas com JWT
