# Import Hooks + AST Transforms

Instrument modules at import time by rewriting their AST.

## Goal

Inject entry/exit tracing into functions for modules under a chosen package prefix without changing source files.

---

## AST Transformer

```python
import ast

class TraceTransformer(ast.NodeTransformer):
    def visit_FunctionDef(self, node: ast.FunctionDef):
        self.generic_visit(node)
        enter = ast.parse(f"print('→ {node.name}')").body[0]
        exit_ = ast.parse(f"print('← {node.name}')").body[0]
        node.body = [enter, *node.body, exit_]
        return node
```

---

## Meta Path Finder/Loader

```python
import importlib.abc, importlib.util, importlib.machinery, sys, types

PREFIX = "instrumented"

class Loader(importlib.abc.Loader):
    def __init__(self, spec):
        self.spec = spec

    def create_module(self, spec):
        return None  # default module creation

    def exec_module(self, module: types.ModuleType):
        src = self.spec.loader.get_source(self.spec.name)
        tree = ast.parse(src, filename=self.spec.origin)
        tree = TraceTransformer().visit(tree)
        ast.fix_missing_locations(tree)
        code = compile(tree, self.spec.origin, "exec")
        exec(code, module.__dict__)

class Finder(importlib.abc.MetaPathFinder):
    def find_spec(self, fullname, path, target=None):
        if not fullname.startswith(PREFIX + "."):
            return None
        # Delegate to default finder to locate the real module
        real_name = fullname[len(PREFIX) + 1:]
        spec = importlib.machinery.PathFinder.find_spec(real_name, path)
        if spec and spec.loader and getattr(spec.loader, 'get_source', None):
            new = importlib.util.spec_from_loader(fullname, Loader(spec), origin=spec.origin)
            new.submodule_search_locations = spec.submodule_search_locations
            return new
        return None

sys.meta_path.insert(0, Finder())
```

Usage:

```python
# Suppose you have a real package "pkg" with modules.
# Import the same modules via the virtual prefix to instrument:
import instrumented.pkg.module as M
M.some_function()  # prints → some_function ... ← some_function
```

Notes:
- This approach preserves the original module as a different import path; aliasing can avoid confusion.
- Extend the transformer to wrap with `try/finally`, add timers, or inject guards.

---

## Decorator Injection Variant

Instead of inserting print nodes, add a decorator to each function and provide the decorator in a bootstrapped prelude.

```python
TRACE_DECORATOR = """
def __trace(fn):
    import time, functools
    @functools.wraps(fn)
    def wrapper(*a, **k):
        t0 = time.perf_counter()
        try:
            return fn(*a, **k)
        finally:
            dt = time.perf_counter() - t0
            print(f"{fn.__name__} took {dt:.3f}s")
    return wrapper
"""

class Decorate(ast.NodeTransformer):
    def visit_FunctionDef(self, node):
        self.generic_visit(node)
        node.decorator_list.append(ast.Name(id='__trace', ctx=ast.Load()))
        return node
```

Prepend `TRACE_DECORATOR` to the module body before compile; or insert into the module dict before `exec`.

