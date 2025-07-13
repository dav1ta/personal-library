## Understanding `types.MethodType` in Python

### What is `types.MethodType`?

`types.MethodType` is a function from Python’s built-in `types` module. It allows you to **bind a function to an instance** as a method, so that the instance is automatically passed as `self` when you call the method.

**Signature:**
```python
types.MethodType(function, instance)
```

---

### Why use `types.MethodType`?

Suppose you want to add a new method to an instance at runtime. If you assign a function directly, it won’t behave like a regular method—`self` won’t be passed automatically.

#### Example: Assigning Directly (Does NOT work)

```python
class MyClass:
    def __init__(self, value):
        self.value = value

def custom_method(self, x):
    return self.value + x

obj = MyClass(10)
obj.new_method = custom_method

# This will raise a TypeError!
obj.new_method(5)
```
**Error:**  
`TypeError: custom_method() missing 1 required positional argument: 'self'`

---

#### Example: Using `types.MethodType` (Works!)

```python
import types

class MyClass:
    def __init__(self, value):
        self.value = value

def custom_method(self, x):
    return self.value + x

obj = MyClass(10)
obj.new_method = types.MethodType(custom_method, obj)

print(obj.new_method(5))  # Output: 15
```

---

### When Should You Use This?

- **Dynamic behavior:** When you need to add instance-specific methods at runtime.
- **Plugin/extension systems:** When functionality is added dynamically.
- **Monkey-patching:** For altering behavior during testing or debugging.

For static methods or class-wide additions, define methods directly in the class, use class decorators, or metaclasses.

---

### TL;DR

- **Direct assignment:** Does NOT bind `self`.
- **`types.MethodType`:** Binds the function as a proper method.

---

**Tip:**  
Experiment in a Python REPL to see the difference in behavior!

If you want a snippet for using this with lambdas, `functools`, or more advanced metaprogramming, just ask!



## Classic Python Closure Bug: Late Binding When Attaching Methods

### ❌ Buggy Example: Late Binding in Loop

```python
import types

tools = []

class DummyTool:
    def __init__(self, name):
        self.name = name
        self._arun = lambda **kwargs: f"{self.name} original: {kwargs}"

# Create two tools
for n in ("search", "fetch_content"):
    tools.append(DummyTool(n))

def patch_all_tools_arun_buggy(tools):
    for tool in tools:
        original_arun = tool._arun
        async def smart_adapter(self, *args, **kwargs):
            # Always uses the last value of original_arun!
            return await original_arun(**kwargs)
        tool._arun = types.MethodType(smart_adapter, tool)
    return tools

patch_all_tools_arun_buggy(tools)
# Now both tools' _arun will point to the last original_arun (fetch_content)
```

**Result:**  
Calling `tools[0]._arun({})` and `tools[1]._arun({})` will both use the `fetch_content` logic.  
This is because `original_arun` is late-bound in the closure!

---

### ✅ Corrected Example: Capture with Default Argument

```python
import types

def patch_all_tools_arun_fixed(tools):
    for tool in tools:
        original_arun = tool._arun
        async def smart_adapter(self, *args, original_func=original_arun, **kwargs):
            return await original_func(**kwargs)
        tool._arun = types.MethodType(smart_adapter, tool)
    return tools

# Now each tool's _arun uses the correct original function!
patch_all_tools_arun_fixed(tools)
```
**Result:**  
Each tool's `_arun` works as expected, calling its own original method.

---

### 🧠 Learning: Why This Happens

- **Late binding in closures:**  
  Functions defined inside a loop "capture" variables by reference, not by value.  
  By the time the function is called, the loop variable might have changed.
- **Fix:**  
  Use a default argument (`original_func=original_arun`) to capture the current value for each iteration.

**Tip:** This pattern is crucial to remember for dynamic method patching, decorators in loops, and any place where you use closures with loop variables.
