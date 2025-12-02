# Configuration: configparser, .env, and layering

## Layered Config Strategy

Order of precedence (highest first):
1) CLI args
2) Environment variables
3) Config file (INI/TOML/YAML)
4) Sensible defaults

---

## INI with `configparser`

```python
import configparser, os

cfg = configparser.ConfigParser()
cfg.read(["app.ini", "/etc/myapp/app.ini"])  # later files override earlier

host = cfg.get("server", "host", fallback="127.0.0.1")
port = cfg.getint("server", "port", fallback=8080)
debug = cfg.getboolean("app", "debug", fallback=False)
```

Example `app.ini`:
```ini
[server]
host = 0.0.0.0
port = 8000

[app]
debug = true
```

---

## .env Files and Environment Variables

```python
import os

def load_dotenv(path: str = ".env") -> None:
    try:
        with open(path, encoding="utf-8") as f:
            for line in f:
                line = line.strip()
                if not line or line.startswith("#"):
                    continue
                key, _, value = line.partition("=")
                os.environ.setdefault(key, value)
    except FileNotFoundError:
        pass

load_dotenv()

DB_URL = os.getenv("DB_URL", "sqlite:///app.db")
```

Use a library like `python-dotenv` for robust parsing if needed.

---

## Merge Layers

```python
import argparse, os, configparser

def load_config(argv=None):
    # defaults
    cfg = {
        "host": "127.0.0.1",
        "port": 8080,
        "debug": False,
    }

    # file
    cp = configparser.ConfigParser()
    cp.read(["app.ini"]) 
    cfg.update({
        "host": cp.get("server", "host", fallback=cfg["host"]),
        "port": cp.getint("server", "port", fallback=cfg["port"]),
        "debug": cp.getboolean("app", "debug", fallback=cfg["debug"]),
    })

    # env
    cfg["host"] = os.getenv("APP_HOST", cfg["host"])
    cfg["port"] = int(os.getenv("APP_PORT", cfg["port"]))
    cfg["debug"] = os.getenv("APP_DEBUG", str(cfg["debug"])) in {"1","true","True"}

    # cli
    p = argparse.ArgumentParser()
    p.add_argument("--host")
    p.add_argument("--port", type=int)
    p.add_argument("--debug", action="store_true")
    a = p.parse_args(argv)
    if a.host: cfg["host"] = a.host
    if a.port: cfg["port"] = a.port
    if a.debug: cfg["debug"] = True

    return cfg
```

---

## Tips

- Keep secrets in env; don’t commit them. Use `.env` for local dev only.
- Prefer TOML (`pyproject` style) for richer configs; see `modules/serialization.md` for reading TOML with `tomllib`.
- Document precedence and defaults in `--help` and README.

