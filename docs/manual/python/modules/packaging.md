# Packaging and Publishing

## Build Config: `pyproject.toml`

```toml
[project]
name = "myapp"
version = "0.1.0"
readme = "README.md"
requires-python = ">=3.9"
dependencies = ["requests>=2"]

[project.scripts]
myapp = "myapp.__main__:cli"

[build-system]
requires = ["setuptools>=68", "wheel"]
build-backend = "setuptools.build_meta"
```

---

## Build Wheel and SDist

```bash
python -m pip install --upgrade build
python -m build
ls dist/
```

---

## Publish to PyPI/TestPyPI

```bash
python -m pip install --upgrade twine

# TestPyPI first
twine upload --repository testpypi dist/*

# Then PyPI
twine upload dist/*
```

Use API tokens; never commit credentials.

---

## Editable Installs for Dev

```bash
pip install -e .
```

---

## Tips

- Pin minimum versions in `dependencies`; avoid overly tight upper bounds.
- Include `py.typed` for typed libraries; ship type info.
- Add `LICENSE`, classifiers, and `long_description` metadata for good packaging hygiene.

