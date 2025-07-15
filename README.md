# NLW Agents - Backend

## Tecnologias Utilizadas

- **Node.js** (runtime)
- **TypeScript** (linguagem)
- **Fastify** (`fastify`, `@fastify/cors`, `fastify-type-provider-zod`) - framework web
- **Drizzle ORM** (`drizzle-orm`, `drizzle-kit`, `drizzle-seed`) - ORM e migrations
- **PostgreSQL** (banco de dados, com extensão `pgvector`)
- **postgres** (driver para Node.js)
- **Zod** (validação de esquemas)
- **Docker** e **Docker Compose** (infraestrutura do banco)
- **Biome** (lint)
- **Ultracite** (configuração de lint)

## Estrutura do Projeto

- `src/` - Código-fonte principal
  - `server.ts` - Entrypoint HTTP
  - `db/` - Conexão, schema, migrations e seed do banco
  - `http/routes/` - Rotas HTTP
- `docker/` - Scripts de setup do banco
- `docker-compose.yml` - Orquestração do banco PostgreSQL

## Pré-requisitos

- Node.js 18+
- Docker e Docker Compose

## Passo a Passo para Deploy Local

### 1. Clone o repositório

```bash
git clone <repo-url>
cd server
```

### 2. Instale as dependências

```bash
npm install
```

### 3. Suba o banco de dados com Docker

```bash
docker compose up -d
```

Isso irá:

- Subir um container PostgreSQL com a extensão `pgvector`.
- Criar o banco `agents` com usuário e senha `docker`.
- Executar o script `docker/setup.sql` para garantir a extensão `vector`.

### 4. Configure as variáveis de ambiente

Crie um arquivo `.env` na raiz do projeto com o seguinte conteúdo:

```env
DATABASE_URL=postgresql://docker:docker@localhost:5432/agents
PORT=3333
```

### 5. Rode as migrations e o seed do banco

> O projeto utiliza Drizzle ORM para migrations e seed.

**(Opcional) Gere as migrations:**

```bash
npx drizzle-kit generate:pg
```

**Rode o seed para popular o banco:**

```bash
npm run db:seed
```

### 6. Inicie o servidor

```bash
npm run dev
```

O servidor estará disponível em `http://localhost:3333`.

## Endpoints Disponíveis

- `GET /health` - Health check
- `GET /rooms` - Lista as salas cadastradas

Exemplo de requisição (usando [client.http](./client.http)):

```http
GET http://localhost:3333/rooms
```

## Estrutura da Tabela `rooms`

| Campo       | Tipo      | Descrição           |
| ----------- | --------- | ------------------- |
| id          | uuid      | Identificador único |
| name        | text      | Nome da sala        |
| description | text      | Descrição           |
| created_at  | timestamp | Data de criação     |

## Scripts Úteis

- `npm run dev` - Inicia o servidor em modo desenvolvimento
- `npm run start` - Inicia o servidor em modo produção
- `npm run db:seed` - Reseta e popula o banco com dados fake

## Lint

O projeto utiliza [Biome](https://biomejs.dev/) para lint:

```bash
npx biome check .
```

## Observações

- O projeto utiliza TypeScript puro, sem transpilar para JS (Node suporta `.ts` nativamente com as flags usadas).
- O banco de dados é inicializado via Docker, mas pode ser usado um PostgreSQL local, desde que a URL de conexão seja ajustada.
- O seed cria 20 salas fake para testes.

---

Qualquer dúvida, abra uma issue!
