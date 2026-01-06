# Config and Secrets (5 Layers)

Use a clear precedence order so config is predictable.

## The 5 Layers (low -> high priority)
Config precedence from low to high priority sources.

1. Defaults in code
2. Config file (optional)
3. Environment variables
4. Flags / CLI overrides
5. Runtime overrides (secret stores, feature flags, etc)

Keep it boring: deterministic, validated, and easy to debug.

## Minimal Standard-Lib Loader
Small config loader using only the standard library.

```go
type Config struct {
    Addr     string
    DBURL    string
    LogLevel string
}

func LoadConfig() (Config, error) {
    cfg := Config{
        Addr:     ":8080",
        LogLevel: "info",
    }

    if v := os.Getenv("ADDR"); v != "" {
        cfg.Addr = v
    }
    if v := os.Getenv("DB_URL"); v != "" {
        cfg.DBURL = v
    }
    if v := os.Getenv("LOG_LEVEL"); v != "" {
        cfg.LogLevel = v
    }

    addr := flag.String("addr", cfg.Addr, "listen address")
    logLevel := flag.String("log-level", cfg.LogLevel, "log level")
    flag.Parse()

    cfg.Addr = *addr
    cfg.LogLevel = *logLevel

    if cfg.DBURL == "" {
        return Config{}, errors.New("DB_URL required")
    }
    return cfg, nil
}
```

## Secrets
Handle secrets securely and avoid leaking them.

- Never commit secrets to git.
- Prefer environment variables or mounted files (Kubernetes secrets).
- Separate secret values from non-secret config.

Example secret from file:
```go
secret, err := os.ReadFile("/var/run/secrets/api_key")
if err != nil {
    return err
}
apiKey := strings.TrimSpace(string(secret))
```

## When You Need a Library
If the config surface grows (multiple files, env + flags + overrides), use a config helper.
See [Common Libraries](libraries.md) for options.

Next: [Background Jobs](background_jobs.md)
