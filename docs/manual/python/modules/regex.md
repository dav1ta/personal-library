# Regular Expressions (`re`)

## Basics

```python
import re

pat = re.compile(r"^(?P<name>[A-Za-z_][A-Za-z0-9_]*)=(?P<value>.*)$")
m = pat.match("USER=alice")
if m:
    print(m.group("name"), m.group("value"))
```

---

## Flags and Verbose Mode

```python
pat = re.compile(r"""
    ^                  # start
    (?P<local>[^@]+)   # local part
    @
    (?P<domain>[^@]+)  # domain
    $                  # end
""", re.VERBOSE | re.IGNORECASE)
```

---

## Finditer and Substitution

```python
import re

for m in re.finditer(r"\b\d{4}-\d{2}-\d{2}\b", text):
    print(m.group(), m.span())

masked = re.sub(r"(?<=^|\s)\d{16}(?=\s|$)", "<CARD>", text)
```

---

## Performance Tips

- Precompile patterns you reuse.
- Prefer precise character classes and anchors to reduce backtracking.
- Use non-greedy `+?`/`*?` when appropriate.
- For very large inputs, consider `regex` third-party module for advanced features.

