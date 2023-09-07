## LITERALS
0b101010         # Binary integer
0o52             # Octal integer
0x2a             # Hexadecimal integer



# Unpacking Sequences into Variables

Unpacking can be very useful for assigning multiple variables from a single sequence. Let's see some examples:

```python
p = (4,5)
x, y = p
print(x)
```

Output:
```
4
```

## Using _ as a Throwaway Variable

When unpacking, you can use `_` as a throwaway variable for certain values you're going to discard.

```python
data = ['ACME', 50, 91.1, (2012, 12, 21)]
name, shares, price, date = data
_, shares, _, date = data
```

Output:
```
'ACME'
```

## Unpacking N Elements

You can unpack elements flexibly using the `*` symbol:

```python
record = ('Dave', 'dave@example.com', '773-555-1212', '847-555-1212')
name, email, *phone_numbers = record
print(phone_numbers)
```

Output:
```
['773-555-1212', '847-555-1212']
```

## String Split Example

Strings can be split and unpacked easily:

```python
line = 'nobody:*:-2:-2:Unprivileged User:/var/empty:/usr/bin/false'
uname, *fields, homedir, sh = line.split(':')
print(uname)
```

Output:
```
'nobody'
```

# Working with Deques

`collections.deque` provides a double-ended queue that supports adding and removing elements from both ends in O(1) time.

```python
from collections import deque
q = deque(maxlen=3)
q.append(1)
q.append(2)
q.append(3)
q.append(4)
print(q)
```

Output:
```
deque([2, 3, 4])
```

## Appending to a Queue

```python
q.appendleft(4)
print(q)
```

Output:
```
deque([4, 2])
```

# Finding the Largest or Smallest N Items

The `heapq` module provides functions to find the N smallest or largest items.

```python
import heapq
# ... [rest of the code]
print(cheap)
print(expensive)
```



# 1.13 Sorting a List of Dictionaries by a common key

```python
rows = [
    {'fname': 'Brian', 'lname': 'Jones', 'uid': 1003},
    {'fname': 'David', 'lname': 'Beazley', 'uid': 1002},
    {'fname': 'John', 'lname': 'Cleese', 'uid': 1001},
    {'fname': 'Big', 'lname': 'Jones', 'uid': 1004}
]

from operator import itemgetter

rows_by_fname = sorted(rows, key=itemgetter('fname'))
rows_by_uid = sorted(rows, key=itemgetter('uid'))
print(rows_by_fname)
print(rows_by_uid)

rows_by_lfname = sorted(rows, key=itemgetter('lname','fname'))
print(rows_by_lfname)
```

# 1.15 Grouping Records Based on a Field

```python
rows = [
    ...
]
from operator import itemgetter
from itertools import groupby
...

for date, items in groupby(rows, key=itemgetter('date')):
    print(date)
    for i in items:
        print('    ', i)
```

# 1.16 Filtering List

```python
addresses = [
    ...
]
counts = [ ... ]

from itertools import compress
more5 = [n > 5 for n in counts]

list(compress(addresses, more5))
```

# 1.17 Subset of Dictionary

```python
prices = {
    ...
}

p1 = { key:value for key, value in prices.items() if value > 200 }
...
```

... [and so on, structuring each section with a Markdown header and enclosing the code in triple backticks for code blocks]

# 2.13. Aligning Text Strings

```python
text = 'Hello World'

text.ljust(20)
text.rjust(20)
...
```


# 3.12 Time Objects

Working with `datetime.timedelta`:

```python
from datetime import timedelta

a = timedelta(days=2, hours=6)
b = timedelta(hours=4.5)
c = a + b
print(c.days)  # 2
print(c.seconds)  # 37800
print(c.total_seconds())  # 210600.0
```

Working with `datetime.datetime`:

```python
from datetime import datetime

a = datetime(2012, 9, 23)
print(a + timedelta(days=10))  # 2012-10-03 00:00:00

b = datetime(2012, 12, 21)
d = b - a
print(d)  # datetime.timedelta(days=89)
```

# 3.13 Finding Last Occurrence of a Weekday

```python
from datetime import datetime, timedelta
from dateutil.relativedelta import relativedelta
from dateutil.rrule import *

weekdays = ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday']

def get_previous_byday(dayname, start_date=None):
    # ... [rest of the function here]
```

# 3.15 Convert String into Datetime

```python
from datetime import datetime

text = '2012-09-20'
y = datetime.strptime(text, '%Y-%m-%d')
print(y)  # datetime.datetime(2012, 9, 20, 0, 0)
```

# 4.1 Manually Consuming an Iterator

```python
with open('/etc/passwd') as f:
    # ... [rest of the code here]
```

# 4.3 Generators

```python
def frange(start, stop, increment):
    # ... [rest of the function here]
```

# 4.5 Reversed Iterator

```python
class Countdown:
    # ... [rest of the class here]
```

# 4.6 Generator Functions with Extra State

```python
from collections import deque

class linehistory:
    # ... [rest of the class here]
```

# 4.7 Taking Slice of an Iterator

```python
def count(n):
    # ... [rest of the function here]
```

# 4.8 User Database

```python
from itertools import dropwhile

with open('/etc/passwd') as f:
    # ... [rest of the code here]
```

# 4.9 Iterate All Possible Combinations

```python
items = ['a', 'b', 'c']
from itertools import permutations
# ... [rest of the code here]
```

# 4.11 Iterating Over Multiple Sequences Simultaneously

```python
xpts = [1,5,4,2,10,7]
ypts = [101, 78, 37, 15, 62, 99]
# ... [rest of the code here]
```

# 4.12 Using `itertools.chain`

```python
from itertools import chain

a = [1, 2, 3, 4]
b = ['x', 'y', 'z']
for x in chain(a, b):
    print(x)
```



