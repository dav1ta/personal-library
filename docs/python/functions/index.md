




## RECURSION


current limit sys.getrecursionlimit() default is 1000
set limit sys.setrecursionlimit()

## LAMBDA FUNCTIONS
```python
x = 2
f = lambda y: x * y
x = 3
g = lambda y: x * y

```
print(f(10))       # --> prints 30
print(g(10))       # --> prints 30
#TODO late binding

## INNER FUNCTIONS
nonlocal cannot be used to refer to a global variable—it must reference a local variable in an outer scope. Thus, if a function is assigning to a global, you should still use the global declaration

Use of nested functions and nonlocal declarations is not a common programming style. For example, inner functions have no outside visibility, which can complicate testing and debugging. Nevertheless, nested functions are sometimes useful for breaking

## INSPECTION
```
f.__name__
Function name
f.__qualname__
Fully qualified name (if nested)
f.__module__
Name of module in which defined
f.__doc__
Documentation string
f.__annotations__
Type hints
f.__globals__
Dictionary that is the global namespace
f.__closure__
Closure variables (if any)
f.__code__

```
## CHECK  FUNCTION PARAMETERS

```python
import inspect
def func(x: int, y:float, debug=False) -> float:
    pass
sig = inspect.signature(func)
assert inspect.signature(func1) == inspect.signature(func2)

```

## GET CURRENT FRAME LOCALS
```python
def spam(x, y):
    z = x + y
    grok(z)
def grok(a):
    b = a * 10
    # outputs: {'a':5, 'b':50 }
    print(inspect.currentframe().f_locals)
```

```
f.f_back
Previous stack frame (toward the caller)
f.f_code
Code object being executed
f.f_locals
Dictionary of local variables (locals())
f.f_globals
Dictionary used for global variables (globals())
f.f_builtins
Dictionary used for built-in names
f.f_lineno
Line number
f.f_lasti
Current instruction. This is an index into the bytecode string of f_code.
f.f_trace
Function called at start of each source code line
```
