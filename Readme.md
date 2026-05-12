# EvoGo Docker

Ambiente Docker da EvoGo com os serviços de aplicacao e mensageria.

## Visao Geral

Este projeto possui dois arquivos Compose:

- `docker-compose.yaml`: perfil principal (producao/local sem exposicao de portas internas).
- `docker-compose.test.yaml`: perfil de testes/local com portas publicadas para acesso pelo host.

## Servicos

- `evolution-go`: API principal.
- `postgres`: banco de dados PostgreSQL 15.
- `rabbitmq`: broker AMQP.
- `nats`: broker NATS (com JetStream habilitado).

Observacao: este projeto nao utiliza Redis.

## Pre-requisitos

- Docker
- Docker Compose (plugin `docker compose`)

## Configuracao

Crie um arquivo `.env` na raiz do projeto com base no `.env.example`.

Variaveis principais:

- `POSTGRES_USER`
- `POSTGRES_PASSWORD`
- `RABBITMQ_DEFAULT_USER`
- `RABBITMQ_DEFAULT_PASS`
- `SERVER_PORT`
- `GLOBAL_API_KEY`
- Variaveis S3/MinIO (`S3_ENABLED`, `S3_ENDPOINT`, `S3_ACCESS_KEY`, `S3_SECRET_KEY`, `S3_REGION`, `S3_BUCKET`, `S3_USE_SSL`)

## Subir Ambiente

### Perfil principal

```bash
docker compose -f docker-compose.yaml up -d
```

### Perfil de teste

```bash
docker compose -f docker-compose.test.yaml up -d
```

## Parar Ambiente

### Perfil principal

```bash
docker compose -f docker-compose.yaml down
```

### Perfil de teste

```bash
docker compose -f docker-compose.test.yaml down
```

## Portas Expostas

No perfil principal (`docker-compose.yaml`), os servicos internos nao expoem portas no host.

No perfil de teste (`docker-compose.test.yaml`), as portas abaixo sao publicadas:

- Evolution Go: `8086:${SERVER_PORT}`
- PostgreSQL: `5438:5432`
- RabbitMQ AMQP: `5673:5672`
- RabbitMQ Management: `15673:15672`

## Healthchecks

- PostgreSQL: `pg_isready`
- RabbitMQ: `rabbitmqctl -q status`
- NATS: endpoint `http://localhost:8222/healthz`
- Evolution Go: endpoint `http://localhost:${SERVER_PORT}/health`

## Volumes

- `postgres_data`: dados do PostgreSQL
- `evolution_data`: dados da aplicacao
- `evolution_logs`: logs da aplicacao
