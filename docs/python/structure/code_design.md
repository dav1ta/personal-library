# Code Design

## Design by Contract

Design by Contract is a programming approach that focuses on enforcing rules and constraints during the communication of software components. It involves the use of contracts that define preconditions, postconditions, invariants, and side effects.

- Preconditions: Checks performed before running a function to ensure that the requirements are met.
- Postconditions: Checks performed after the execution of a function to validate if the expected result is achieved.
- Invariants: Rules or constraints that remain true throughout the execution of the code.
- Side Effects: Mentioned in the code, they describe any changes or actions that occur beyond the return value of a function.

## Defensive Programming

Defensive programming involves writing code that protects itself from invalid inputs or unexpected behavior. It includes error handling techniques such as:

- Value substitution: Using default values or environment variables (`os.getenv("DPORT", 5432)`).
- Error logging: Capturing and logging errors for debugging and analysis.
- Exception handling: Properly handling exceptions with well-defined scopes to reduce the impact of errors.

Best practices for error handling include avoiding traceback to end users, avoiding empty `except` blocks, and including the original exception for better debugging.

## Cohesion and Coupling

Cohesion and coupling are concepts related to how objects or components in a codebase depend on each other.

- Cohesion: Describes the degree to which a component or class focuses on a single responsibility or functionality. High cohesion means that a component is focused and has a clear purpose.
- Coupling: Refers to the interdependence between components or classes. High coupling indicates tight dependencies, which can lead to issues such as limited code reuse, ripple effects of changes, and a low level of abstraction.

## DRY and OAOO

DRY (Don't Repeat Yourself) and OAOO (Once and Only Once) are principles that promote code efficiency and maintainability.

- DRY: Encourages avoiding code duplication by abstracting common functionality into reusable components or functions.
- OAOO: Advocates for implementing a particular behavior or logic in a single place to ensure consistency and reduce the chance of introducing errors through duplicated code.

## YAGNI and KIS

- YAGNI: Stands for "You Ain't Gonna Need It." It advises developers to avoid over-engineering or adding unnecessary features to their codebase. Only implement what is needed at the present moment to avoid complexity and potential issues.
- KIS: Stands for "Keep It Simple." It emphasizes simplicity in design and implementation. When designing a software component, aim for the minimal solution that effectively solves the problem without introducing unnecessary complexity.

## EAFP and LBYL

- EAFP: Stands for "Easier to Ask Forgiveness than Permission." This programming approach suggests trying an operation and handling any resulting exceptions rather than checking for preconditions or permissions before executing the operation.
- LBYL: Stands for "Look Before You Leap." It involves checking preconditions or permissions before executing an operation to avoid exceptions or errors. An example is checking if a file exists before attempting to open it.

Example:

### EAFP
```python
try:
    with open(filename) as f:
        # Code for file processing
except FileNotFoundError as e:
    logger.error(e)
```
### LBYL
```python
if os.path.exists(filename):
    with open(filename) as f:
        # Code for file processing
```

