# Datetime & Timezones

## Naive vs Aware

```python
from datetime import datetime, timezone

naive = datetime.now()               # naive (no tzinfo)
aware = datetime.now(timezone.utc)   # aware (UTC tzinfo)
```

Always use aware datetimes for storage and comparisons.

---

## Localize and Convert with `zoneinfo` (3.9+)

```python
from datetime import datetime
from zoneinfo import ZoneInfo

ny = ZoneInfo("America/New_York")
berlin = ZoneInfo("Europe/Berlin")

dt = datetime(2024, 3, 10, 1, 30, tzinfo=ny)  # pre-DST gap edge
dt2 = dt.astimezone(berlin)
```

Create datetimes with tzinfo attached; avoid naive local times around DST changes.

---

## Parsing/Formatting

```python
from datetime import datetime, timezone

dt = datetime.fromisoformat("2024-06-07T12:34:56+00:00")
s = dt.astimezone(timezone.utc).isoformat()

# Custom format
fmt = dt.strftime("%Y-%m-%d %H:%M:%S %Z")
```

---

## Durations and Rounding

```python
from datetime import datetime, timedelta, timezone

start = datetime.now(timezone.utc)
# ... work ...
elapsed = datetime.now(timezone.utc) - start

# Round down to minute granularity
rounded = start.replace(second=0, microsecond=0)
```

---

## Tips

- Store in UTC, display in user’s local timezone.
- Prefer `zoneinfo` over third-party when possible.
- Beware DST gaps and folds; see `datetime.fold` for ambiguous times.

Next: [Code Design](../advanced/code_design.md)
