# Common Libraries (API, WebSockets, Data, etc)

Practical, commonly used choices. Prefer the standard library unless you need more.

## HTTP APIs and Routing
- net/http (stdlib)
- chi (router + middleware)
- gin, echo (web frameworks)
- fiber (web framework on fasthttp)

## WebSockets
- gorilla/websocket
- coder/websocket
- gobwas/ws (low-level)

## Database and SQL
- database/sql (stdlib)
- pgx (Postgres driver + toolkit)
- go-redis (Redis client)

## Migrations
- golang-migrate
- goose

## Background Jobs / Workflows
- asynq (Redis-backed jobs)
- Temporal (workflow engine)

## Config
- viper
- koanf
- envconfig
- godotenv (load .env in dev)

## Logging and Observability
- slog (stdlib)
- zerolog
- OpenTelemetry Go

## Messaging / Eventing
- Watermill
