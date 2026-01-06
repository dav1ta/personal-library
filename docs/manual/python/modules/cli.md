# Building CLIs with `argparse`

## Minimal CLI

```python
import argparse

def main(argv=None):
    p = argparse.ArgumentParser(prog="app", description="Demo CLI")
    p.add_argument("input")
    p.add_argument("--verbose", "-v", action="count", default=0)
    args = p.parse_args(argv)
    print(args.input, args.verbose)

if __name__ == "__main__":
    main()
```

---

## Subcommands

```python
import argparse, os

def build_parser():
    p = argparse.ArgumentParser(prog="app")
    sub = p.add_subparsers(dest="cmd", required=True)

    p_greet = sub.add_parser("greet", help="Say hello")
    p_greet.add_argument("name")
    p_greet.add_argument("--upper", action="store_true")

    p_sum = sub.add_parser("sum", help="Sum numbers")
    p_sum.add_argument("nums", type=int, nargs="+")

    return p

def main(argv=None):
    p = build_parser()
    a = p.parse_args(argv)
    if a.cmd == "greet":
        s = f"Hello, {a.name}!"
        print(s.upper() if a.upper else s)
    elif a.cmd == "sum":
        print(sum(a.nums))

if __name__ == "__main__":
    main()
```

---

## Types, Choices, and Defaults

```python
import argparse, os

p = argparse.ArgumentParser()
p.add_argument("--mode", choices=["dev", "prod"], default=os.getenv("APP_MODE", "dev"))
p.add_argument("--ratio", type=float, default=0.1)
p.add_argument("--tag", action="append", default=[])  # repeatable
args = p.parse_args([])
```

---

## Tips

- Use `subparsers` for multiple actions; keep each subparser focused.
- Use environment variables for defaults with `os.getenv`.
- Prefer clear `help=` strings and sensible defaults.
- Consider `click`/`typer` for ergonomics; keep `argparse` for zero-deps.

Next: [Logging](logging.md)
