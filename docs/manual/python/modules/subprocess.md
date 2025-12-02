# Subprocess: Safe and Practical Usage

## Quick Commands with `run`

```python
import subprocess as sp

# Simple command; raises on non-zero exit
sp.run(["echo", "hello"], check=True)

# Capture output as text
res = sp.run(["python", "-V"], check=True, capture_output=True, text=True)
print(res.stdout.strip())

# Timeout and stderr capture
try:
    sp.run(["sleep", "10"], timeout=2, check=True)
except sp.TimeoutExpired:
    print("command timed out")
```

Guidelines:
- Always pass a list of args; avoid `shell=True` unless needed.
- Use `check=True` so failures raise and don't go unnoticed.
- Use `text=True` (or `encoding=`) to get `str` instead of `bytes`.

---

## Streaming with `Popen`

```python
import subprocess as sp

with sp.Popen(
    ["python", "-u", "-c", "import sys, time; [print(i) or time.sleep(0.2) for i in range(5)]"],
    stdout=sp.PIPE,
    stderr=sp.PIPE,
    text=True,
) as proc:
    for line in proc.stdout:
        print("OUT:", line.rstrip())
    code = proc.wait()
    if code != 0:
        err = proc.stderr.read()
        raise RuntimeError(f"failed with {code}: {err}")
```

Notes:
- Use `-u` for unbuffered Python child output; other programs may need flags to disable buffering.
- Iterate `proc.stdout` for line-by-line processing; call `flush()` in the child when needed.

---

## Bidirectional I/O

```python
import subprocess as sp

proc = sp.Popen(["python", "-u", "-c", "print(input())"], stdin=sp.PIPE, stdout=sp.PIPE, text=True)
out, _ = proc.communicate("PING\n", timeout=2)
print(out)
```

---

## Errors and Return Codes

```python
import subprocess as sp

try:
    sp.run(["bash", "-lc", "exit 3"], check=True)
except sp.CalledProcessError as e:
    print(e.returncode)  # 3
    print(e.cmd)
```

---

## Security Tips

- Avoid `shell=True`; if you must, sanitize inputs and prefer fixed command templates.
- Use explicit timeouts for external commands.
- Limit environment exposure via `env={...}` and `cwd=...` when appropriate.

