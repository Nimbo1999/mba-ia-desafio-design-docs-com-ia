# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Escopo

- `docs/` guarda a documentação do projeto. As skills em `.claude/skills/` trazem instruções sobre como escrever e estruturar cada documento de design do projeto.

## Stack

- **Aplicação:** Order Management System (OMS), uma API HTTP REST com Express 4. As rotas ficam em `/api/v1`, além de `GET /health`.
- **Linguagem:** TypeScript 5.6 em ESM (`"type": "module"`), Node >= 20. Os imports relativos usam a extensão `.js`.
- **Dependências principais:** express, zod (validação de entrada e de env), jsonwebtoken (JWT), bcrypt, pino e pino-http (logs), uuid.
- **Banco:** MySQL 8, que sobe localmente com `docker-compose.yml`.
- **ORM:** Prisma 5.22 (`prisma/schema.prisma`).
- **Migrações:** Prisma Migrate (`prisma/migrations/`). Exige `SHADOW_DATABASE_URL`.
- **Testes:** Vitest e supertest, como testes de integração contra um MySQL real.
- **Infra, IaC e CI/CD:** não existem. Não há `.github/`, Dockerfile da aplicação, Terraform nem provedor de nuvem configurado.
- A aplicação não tem filas, eventos, workers nem notificações externas.

## Comandos

```bash
docker compose up -d            # sobe o MySQL local
cp .env.example .env
npm run db:migrate              # prisma migrate dev
npm run db:seed
npm run dev                     # tsx watch com .env
npm run build && npm start      # compila para dist/ e executa
npm run lint                    # eslint
npm run format                  # prettier
npm test                        # vitest run (precisa do MySQL de pé)
npx vitest run tests/orders.test.ts          # um arquivo
npx vitest run -t "creates an order"         # um teste pelo nome
```

Os testes rodam em série (`singleFork`, sem paralelismo entre arquivos). O `tests/setup.ts` apaga todas as tabelas antes de cada teste.

## Arquitetura

- **Montagem da aplicação:** a injeção de dependências é manual. Em `src/app.ts`, `buildControllers()` cria Repository → Service → Controller para cada módulo, e `buildApp()` registra os middlewares, o router e o `errorMiddleware`. O `src/server.ts` faz o bootstrap e o shutdown gracioso (SIGINT/SIGTERM).
- **Módulos** (`src/modules/<nome>/`): auth, users, customers, products e orders. Cada módulo tem:
  - `*.routes.ts`: aplica `authenticate`, `requireRole` e `validate({ body, query, params })`
  - `*.controller.ts`: handlers em arrow function, com `try`/`next(err)`
  - `*.service.ts`: regra de negócio
  - `*.repository.ts`: acesso ao Prisma
  - `*.schemas.ts`: schemas Zod

  Os routers são registrados em `src/routes/index.ts`.
- **Pedidos:**
  - A máquina de estados fica em `src/modules/orders/order.status.ts`: PENDING → PAID → PROCESSING → SHIPPED → DELIVERED. CANCELLED é alcançável a partir de PENDING, PAID ou PROCESSING.
  - `OrderService.changeStatus` (`order.service.ts`) roda inteiro dentro de uma `prisma.$transaction`. Valida a transição, debita o estoque em PENDING→PAID, repõe o estoque ao cancelar a partir de PAID ou PROCESSING, atualiza o status e grava `OrderStatusHistory`.
  - `create` também é transacional. Gera o `orderNumber` (`ORD-000001`) pela tabela `OrderNumberSequence`.
- **Erros:**
  - `AppError(message, statusCode, errorCode, details)` fica em `src/shared/errors/app-error.ts`. As subclasses HTTP ficam em `http-errors.ts`.
  - Os códigos de erro são strings em UPPER_SNAKE, como `NOT_FOUND`, `INVALID_STATUS_TRANSITION` e `INSUFFICIENT_STOCK`.
  - `src/middlewares/error.middleware.ts` centraliza o tratamento e responde `{ error: { code, message, details? } }`. Também trata `ZodError` e os erros Prisma P2002 (409) e P2025 (404).
- **Autenticação:** `authenticate` (JWT Bearer) e `requireRole('ADMIN' | 'OPERATOR')` ficam em `src/middlewares/auth.middleware.ts`.
- **Logs:** o logger Pino fica em `src/shared/logger/index.ts`. Ele mascara authorization, password e token, e usa pino-pretty em dev. `request-logger.middleware.ts` propaga e devolve o `X-Request-Id`.
- **Variáveis de ambiente:** `src/config/env.ts` valida as variáveis com Zod e encerra o processo se alguma for inválida. Toda variável nova precisa entrar nesse schema.
- **Paginação:** use `paginated()` de `src/shared/http/response.ts`, que devolve `{ data, pagination }`.
