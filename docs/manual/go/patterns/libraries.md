# Common Libraries (API, WebSockets, Data, etc)

Practical, commonly used choices. Prefer the standard library unless you need more.

## HTTP APIs and Routing
Libraries and patterns for building HTTP APIs and routing.

- net/http (stdlib)
- chi (router + middleware)
- gin, echo (web frameworks)
- fiber (web framework on fasthttp)

## WebSockets
Libraries and patterns for WebSocket support.

- gorilla/websocket
- coder/websocket
- gobwas/ws (low-level)

## Database and SQL
Libraries and patterns for database access and SQL.

- database/sql (stdlib)
- pgx (Postgres driver + toolkit)
- go-redis (Redis client)

## Migrations
Schema migration tools and workflows.

- golang-migrate
- goose

## Background Jobs / Workflows
Libraries and patterns for background job processing and workflows.

- asynq (Redis-backed jobs)
- Temporal (workflow engine)

## Config
Config libraries and patterns commonly used in Go services.

- viper
- koanf
- envconfig
- godotenv (load .env in dev)

## Logging and Observability
Options for logging, metrics, and tracing.

- slog (stdlib)
- zerolog
- OpenTelemetry Go

## Messaging / Eventing
Libraries and patterns for messaging and eventing.

- Watermill

Next: [Overview](../modules/overview.md)
