# Working with Dates and Times in Python

Python's `datetime` module provides classes for manipulating dates and times.

## Importing datetime
```python
import datetime
now = datetime.datetime.now()
print(now)
```

## Creating Dates and Times
```python
from datetime import date, time, datetime

d = date(2023, 1, 1)
t = time(12, 30)
dt = datetime(2023, 1, 1, 12, 30)
```

## Formatting Dates
```python
now = datetime.now()
print(now.strftime('%Y-%m-%d %H:%M:%S'))
```

## Parsing Strings
```python
from datetime import datetime
s = '2023-01-01 12:30:00'
dt = datetime.strptime(s, '%Y-%m-%d %H:%M:%S')
```

## Timedelta
```python
from datetime import timedelta

tomorrow = now + timedelta(days=1)
```

## Resources
- [datetime docs](https://docs.python.org/3/library/datetime.html) 