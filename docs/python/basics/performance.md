# Performance Tips in Python

Python is easy to use but can be slow for some tasks. Here are some tips to improve performance:

## Use Built-in Functions
Built-in functions like `map`, `filter`, and comprehensions are faster than manual loops.

## Avoid Unnecessary Computation
Cache results, use memoization, and avoid repeated work.

## Use Efficient Data Structures
Choose the right data structure for the job (e.g., `set` for membership tests).

## Profile Your Code
Use `cProfile` or `timeit` to find bottlenecks.

```python
import timeit
print(timeit.timeit('sum(range(100))'))
```

## Use Libraries
- `numpy` for numerical work
- `pandas` for data analysis
- `multiprocessing` for parallelism

## Example: List Comprehension vs. Loop
```python
# Slower
result = []
for i in range(1000):
    result.append(i * 2)
# Faster
result = [i * 2 for i in range(1000)]
```

## Resources
- [Python performance tips](https://docs.python.org/3/howto/profile.html) 