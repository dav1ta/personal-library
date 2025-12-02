# Serialization: JSON, CSV, TOML, Pickle

## JSON: Safe Defaults + Custom Types

```python
import json, dataclasses, datetime as dt

def default(o):
    if dataclasses.is_dataclass(o):
        return dataclasses.asdict(o)
    if isinstance(o, (dt.datetime, dt.date)):
        return o.isoformat()
    raise TypeError(f"Unserializable: {type(o)!r}")

s = json.dumps(obj, default=default, separators=(",", ":"))  # compact
obj2 = json.loads(s)
```

Tips:
- Prefer `separators` for compact output; `indent=2` for human-readable.
- Avoid `NaN`/`Infinity`; use `allow_nan=False` to catch them.

---

## JSON Lines (ndjson)

```python
import json
with open("events.jsonl", "w", encoding="utf-8") as f:
    for ev in events:
        f.write(json.dumps(ev, ensure_ascii=False) + "\n")
```

---

## CSV: DictReader/DictWriter

```python
import csv

rows = [
    {"name": "alice", "age": 30},
    {"name": "bob", "age": 28},
]

with open("people.csv", "w", newline="", encoding="utf-8") as f:
    w = csv.DictWriter(f, fieldnames=["name", "age"])
    w.writeheader()
    w.writerows(rows)

with open("people.csv", newline="", encoding="utf-8") as f:
    r = csv.DictReader(f)
    for row in r:
        print(row["name"], int(row["age"]))
```

---

## TOML: Read with `tomllib` (3.11+)

```python
import tomllib

with open("pyproject.toml", "rb") as f:
    data = tomllib.load(f)
print(data["project"]["name"])
```

For older Python, use `tomli`.

---

## Pickle: Be Careful

```python
import pickle

# Do not unpickle untrusted data (code execution risk)
blob = pickle.dumps(obj, protocol=pickle.HIGHEST_PROTOCOL)
obj2 = pickle.loads(blob)
```

Prefer JSON/TOML/CSV for interoperability; only use pickle for trusted, Python-only data.

