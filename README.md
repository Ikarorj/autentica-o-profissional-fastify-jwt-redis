# 🔐 API de Autenticação com Fastify, JWT e Redis

Uma implementação educativa e profissional de um fluxo de autenticação moderno usando Fastify, JWT (Access + Refresh tokens) e Redis (para sessão server-side). O projeto demonstra geração e renovação de tokens, controle de sessão com TTL, e invalidação de sessão no logout.

---

## Índice

- [Recursos](#recursos)
- [Tecnologias](#tecnologias)
- [Estrutura do projeto](#estrutura-do-projeto)
- [Pré-requisitos](#pré-requisitos)
- [Instalação e execução](#instalação-e-execução)
- [Variáveis de ambiente](#variáveis-de-ambiente)
- [Endpoints principais](#endpoints-principais)
- [Exemplos (cURL)](#exemplos-curl)
- [Fluxo de autenticação](#fluxo-de-autenticação)
- [Observações de segurança e finalidade](#observações-de-segurança-e-finalidade)

---

## Recursos

- Login com Access Token (curta duração)
- Geração e uso de Refresh Token (longa duração)
- Armazenamento da sessão (Access Token) no Redis com TTL
- Rotas protegidas com validação de JWT e sessão no Redis
- Renovação de sessão via Refresh Token
- Logout com invalidação de sessão no Redis
- Tratamento de erros e boas práticas básicas de segurança

---

## Tecnologias

- Node.js
- TypeScript
- [Fastify](https://www.fastify.io/)
- JWT (`jsonwebtoken`)
- Redis (via Docker para desenvolvimento)
- `bcryptjs` (hash de senhas)
- Docker (para execução do Redis)

---

## Estrutura do projeto

src/
├── controllers/
│   └── authController.ts
├── routes/
│   └── authRoutes.ts
├── services/
│   └── tokenServices.ts
├── redis/
│   └── clienteRedis.ts
├── types/
│   └── user.ts
├── users.json
├── server.ts
.env

---

## Pré-requisitos

- Node.js (recomendado versão 16+)
- npm ou yarn
- Docker (apenas para executar o Redis em ambiente de desenvolvimento)

---

## Instalação e execução

1. Clone o repositório
```bash
git clone https://github.com/Ikarorj/autentica-o-profissional-fastify-jwt-redis.git
cd autentica-o-profissional-fastify-jwt-redis
```

2. Instale as dependências
```bash
npm install
```

3. Crie o arquivo `.env` na raiz do projeto com as variáveis descritas abaixo.

4. Inicie o Redis (opcional — necessário se `USE_REDIS=true`)
```bash
docker run -d --name redis-auth -p 6379:6379 redis
# Verifique se está rodando:
docker ps
```

5. Execute a aplicação
```bash
npm run dev
```

A API estará disponível em: http://localhost:3000 (ou na porta definida em `.env`)

---

## Variáveis de ambiente

Exemplo mínimo de `.env`:
```
PORT=3000

ACCESS_SECRET=access-secret
REFRESH_SECRET=refresh-secret

ACCESS_TTL_SECONDS=30

USE_REDIS=true
REDIS_URL=redis://127.0.0.1:6379
```

- PORT: porta onde a API será exposta.
- ACCESS_SECRET: segredo para assinar o Access Token.
- REFRESH_SECRET: segredo para assinar o Refresh Token.
- ACCESS_TTL_SECONDS: tempo de vida do Access Token (em segundos) e TTL sincronizado no Redis.
- USE_REDIS: habilita o uso de Redis para sessões (true/false).
- REDIS_URL: URL de conexão com o Redis.

---

## Endpoints principais

- POST /auth/login  
  Realiza o login do usuário e retorna `accessToken` e `refreshToken`.

  Body:
  ```json
  {
    "email": "aluno@ifpi.edu.br",
    "password": "123456"
  }
  ```

  Resposta:
  ```json
  {
    "accessToken": "...",
    "refreshToken": "..."
  }
  ```

- GET /auth/protected  
  Rota protegida que exige:
  - Authorization: Bearer <accessToken>
  - Token válido e sessão ativa no Redis

  Header:
  ```
  Authorization: Bearer <accessToken>
  ```

- POST /auth/refresh  
  Renova a sessão usando o `refreshToken`.

  Body:
  ```json
  {
    "refreshToken": "..."
  }
  ```

- POST /auth/logout  
  Encerra (invalida) a sessão do usuário removendo a chave do Redis.

  Header:
  ```
  Authorization: Bearer <accessToken>
  ```

---

## Exemplos (cURL)

Login:
```bash
curl -X POST http://localhost:3000/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"aluno@ifpi.edu.br","password":"123456"}'
```

Acessar rota protegida:
```bash
curl http://localhost:3000/auth/protected \
  -H "Authorization: Bearer <accessToken>"
```

Refresh Token:
```bash
curl -X POST http://localhost:3000/auth/refresh \
  -H "Content-Type: application/json" \
  -d '{"refreshToken":"<refreshToken>"}'
```

Logout:
```bash
curl -X POST http://localhost:3000/auth/logout \
  -H "Authorization: Bearer <accessToken>"
```

---

## Fluxo de autenticação (resumo)

1. Geração de tokens
   - Implementação principal: `src/services/tokenServices.ts`  
   - Ao autenticar credenciais válidas, são criados `accessToken` e `refreshToken`.

2. Sessão no Redis
   - Chave: `token:<userId>` (exemplo)  
   - TTL sincronizado com `ACCESS_TTL_SECONDS` — o Redis mantém a sessão server-side.

3. Validação de rota protegida
   - Verifica JWT (assinatura e expiração)  
   - Verifica presença/consistência do token no Redis

4. Renovação (refresh)
   - `refreshToken` válido gera novo `accessToken` e atualiza o TTL no Redis

5. Logout
   - Remove a chave do Redis, invalidando a sessão imediatamente

---

## Observações de segurança e finalidade

- Este projeto tem caráter educacional, para demonstrar conceitos de autenticação moderna e controle de sessão server-side.
- Em produção:
  - Use segredos fortes e armazenamento seguro (ex.: vaults ou variáveis de ambiente gerenciadas).
  - Considere usar HTTPS, proteção contra CSRF onde aplicável, rate limiting e monitoramento.
  - Ajuste políticas de expiração e rotação de refresh tokens conforme requisitos de segurança.
  - Garanta validação e sanitização de entradas de usuário.

---

Se quiser, eu posso:
- Converter este README em um arquivo pronto para commit no repositório,
- Adicionar badges, ou
- Expandir a seção de arquitetura com diagramas e exemplos de payloads mais detalhados.
