# Environments and `pip`

## Create and Activate a Virtual Environment

```bash
python3 -m venv .venv
source .venv/bin/activate   # Windows: .venv\\Scripts\\activate
python -m pip install --upgrade pip
```

Deactivate with `deactivate`.

---

## Install Dependencies

```bash
pip install -r requirements.txt

# Save current environment
pip freeze > requirements.txt
```

Editable install for a local package:
```bash
pip install -e .
```

---

## `pyproject.toml` Basics

Modern builds use `pyproject.toml`.

```toml
[project]
name = "myapp"
version = "0.1.0"
description = "Demo"
readme = "README.md"
requires-python = ">=3.9"
dependencies = [
  "requests>=2",
]

[build-system]
requires = ["setuptools>=68", "wheel"]
build-backend = "setuptools.build_meta"
```

Build and install locally:
```bash
python -m pip install --upgrade build
python -m build
pip install dist/myapp-0.1.0-py3-none-any.whl
```

---

## Tips

- Pin direct dependencies in `requirements.txt`; use constraints for shared pins.
- Keep one venv per project; avoid global installs.
- Use `pip install --require-hashes -r requirements.txt` for reproducibility when feasible.

