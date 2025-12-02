# Python Modules and Packages

## Introduction

Modules and packages are fundamental to organizing Python code. This guide covers module creation, importing, packaging, and best practices.

## Basic Module Concepts

### Module Definition
```python
# mymodule.py
def hello():
    print("Hello, World!")

# Module-level variables
VERSION = "1.0.0"
```

### Module Importing
```python
# Basic import
import mymodule
mymodule.hello()

# Import specific items
from mymodule import hello, VERSION

# Import with alias
import mymodule as mm
from mymodule import hello as greet
```

### Module Caching
```python
# Modules are cached after first import
import mymodule  # First import
import mymodule  # Uses cached version

# Force reload
import importlib
importlib.reload(mymodule)
```

## Package Structure

### Basic Package
```
mypackage/
    __init__.py
    module1.py
    module2.py
    subpackage/
        __init__.py
        module3.py
```

### Package Initialization
```python
# mypackage/__init__.py
from .module1 import function1
from .module2 import Class1

__all__ = ['function1', 'Class1']
```

### Relative Imports
```python
# Inside subpackage/module3.py
from ..module1 import function1
from . import module4
```

## Module Search Path

### Python Path
```python
import sys

# Add directory to search path
sys.path.append('/path/to/modules')

# Environment variable
# PYTHONPATH=/path/to/modules python script.py
```

### Package Execution
```python
# Run package as script
# python -m mypackage

# Package structure for execution
myapp/
    __init__.py
    main.py
    module1.py
    module2.py
```

## Circular Imports

### Problem
```python
# moda.py
import modb

def func_a():
    modb.func_b()

class Base:
    pass

# modb.py
import moda

def func_b():
    print('B')

class Child(moda.Base):
    pass
```

### Solution
```python
# moda.py
class Base:
    pass

def func_a():
    from . import modb
    modb.func_b()

# modb.py
from . import moda

def func_b():
    print('B')

class Child(moda.Base):
    pass
```

## Package Namespace Control

### Exports
```python
# module.py
__all__ = ['public_function', 'PublicClass']

def public_function():
    pass

def _private_function():
    pass

class PublicClass:
    pass

# __init__.py
from .module import *
```

### Namespace Management
```python
# graphics/__init__.py
from .graph2d.plot2d import Plot2D
from .graph3d.plot3d import Plot3D

__all__ = ['Plot2D', 'Plot3D']
```

## Module Attributes

### Common Attributes
```python
import mymodule

print(mymodule.__name__)      # Module name
print(mymodule.__doc__)       # Documentation string
print(mymodule.__file__)      # Filename
print(mymodule.__package__)   # Package name
print(mymodule.__path__)      # Package subdirectories
```

## Best Practices

1. Use `__init__.py` files for packages
2. Control exports with `__all__`
3. Avoid circular imports
4. Use relative imports within packages
5. Keep modules focused and small
6. Document module purpose and usage
7. Use proper package structure
8. Consider namespace pollution