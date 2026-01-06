# PostgreSQL Installation (Quick)

## Linux (Debian/Ubuntu)
```bash
sudo apt update
sudo apt install postgresql postgresql-contrib
psql --version
```

## macOS (Homebrew)
```bash
brew install postgresql
brew services start postgresql
psql --version
```

## Windows
- Use the official installer and keep defaults unless you know you need changes.
- Confirm `psql` is available in PATH.

## Docker
```bash
docker run --name pg \
  -e POSTGRES_PASSWORD=secret \
  -p 5432:5432 \
  -d postgres
```

## First Login
```bash
psql -U postgres
```

## Next Steps
- [Configuration](configuration.md)
- [Performance](../advanced/performance.md)
