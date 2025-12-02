# Logging in Python: Practical Patterns

## Quick Start

```python
import logging

logger = logging.getLogger("app")
logger.setLevel(logging.INFO)

handler = logging.StreamHandler()
handler.setFormatter(logging.Formatter(
    fmt="%(asctime)s %(levelname)s %(name)s %(message)s",
    datefmt="%Y-%m-%d %H:%M:%S",
))

logger.addHandler(handler)

logger.info("service started", extra={"user": "alice"})
```

Tips:
- Always create a module-level logger via `getLogger(__name__)` in libraries.
- Avoid `basicConfig` in libraries; use it only in apps/entrypoints.

---

## dictConfig with Console + Rotating File

```python
import logging, logging.config

LOGGING = {
    "version": 1,
    "disable_existing_loggers": False,
    "formatters": {
        "plain": {
            "format": "%(asctime)s %(levelname)s %(name)s %(message)s",
            "datefmt": "%H:%M:%S",
        },
    },
    "handlers": {
        "console": {
            "class": "logging.StreamHandler",
            "formatter": "plain",
            "level": "INFO",
        },
        "rotating_file": {
            "class": "logging.handlers.RotatingFileHandler",
            "filename": "app.log",
            "maxBytes": 5 * 1024 * 1024,
            "backupCount": 5,
            "formatter": "plain",
            "level": "INFO",
        },
    },
    "root": {
        "handlers": ["console", "rotating_file"],
        "level": "INFO",
    },
}

logging.config.dictConfig(LOGGING)
logging.getLogger(__name__).info("configured via dictConfig")
```

---

## Structured Context and JSON-ish Output

```python
import json, logging

class JsonFormatter(logging.Formatter):
    def format(self, record: logging.LogRecord) -> str:
        data = {
            "time": self.formatTime(record, "%Y-%m-%dT%H:%M:%S"),
            "level": record.levelname,
            "name": record.name,
            "msg": record.getMessage(),
        }
        # Merge context from `extra` (appears as attributes on record)
        for k, v in record.__dict__.items():
            if k not in ("args", "msg", "levelno", "levelname", "name", "pathname", "filename", "module", "exc_info", "exc_text", "stack_info", "lineno", "funcName", "created", "msecs", "relativeCreated", "thread", "threadName", "processName", "process"):
                data[k] = v
        return json.dumps(data)

logger = logging.getLogger("svc")
handler = logging.StreamHandler()
handler.setFormatter(JsonFormatter())
logger.addHandler(handler)
logger.setLevel(logging.INFO)

logger.info("user login", user="alice", ip="10.0.0.5")
```

Notes:
- Prefer passing context with `extra` or keyword attributes (as above) instead of string concatenation.
- Use `LoggerAdapter` to attach persistent context like `request_id`.

---

## Good Practices

- Levels: use `DEBUG` for diagnostics, `INFO` for key events, `WARNING` for recoverable issues, `ERROR` for failures, `CRITICAL` for process-threatening.
- Do not log secrets or PII. Prefer IDs and hashes.
- For libraries: never configure handlers by default; respect the application’s config.
- Use `exc_info=True` to include tracebacks on exceptions.

