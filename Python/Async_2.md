# 🐍 Python - Async Comprehension

## 📚 Introduction

In modern Python, asynchronous programming is a powerful paradigm, especially for I/O-bound operations. Beyond basic coroutines and async functions, Python also supports **asynchronous generators** and **async comprehensions**, allowing developers to write highly efficient and readable asynchronous code.

This course focuses on:
- Writing asynchronous generators
- Using async comprehensions
- Measuring concurrent execution time
- Proper type annotations

---

## 🔄 Asynchronous Generators

### 🧠 What is an Async Generator?
An **asynchronous generator** is similar to a normal generator, except that it allows you to `yield` values **asynchronously** using `async def` and `yield` statements.

It enables **lazy evaluation** of asynchronous sequences. Each iteration can await a coroutine (e.g., waiting on data from a server or a delay).

### 🔧 Syntax

```python
import asyncio
import random

async def async_generator():
    for _ in range(10):
        await asyncio.sleep(1)
        yield random.uniform(0, 10)
```

- `await asyncio.sleep(1)`: Asynchronously waits one second
- `yield random.uniform(...)`: Yields a float between 0 and 10

---

## 🧼 Async For Loop

To consume the values yielded by an async generator:

```python
async def print_yielded_values():
    result = []
    async for value in async_generator():
        result.append(value)
    print(result)
```

Note the use of `async for`, which is required when iterating over an async generator.

---

## 🧮 Async Comprehension

### 🧠 What is Async Comprehension?

**Async comprehension** allows you to collect values from an async generator using a single-line expression, like a list comprehension.

### 🔧 Syntax

```python
async def async_comprehension():
    return [i async for i in async_generator()]
```

- `[i async for i in async_generator()]` will asynchronously iterate over values and collect them into a list.

### ✅ Advantages

- Cleaner and more readable than manual iteration
- Preserves async behavior
- Perfect for streaming, pagination, and event-based patterns

---

## 🧪 Parallel Execution with `asyncio.gather`

### 🧠 Concept

You can run multiple coroutines **in parallel** using `asyncio.gather()`.

### 🔧 Example: Measure Runtime

```python
import asyncio
import time

async def measure_runtime():
    start = time.time()
    await asyncio.gather(
        async_comprehension(),
        async_comprehension(),
        async_comprehension(),
        async_comprehension()
    )
    return time.time() - start
```

Even though each comprehension takes ~10 seconds due to 10 x 1s sleeps, all 4 comprehensions **run concurrently**, resulting in a **total runtime close to 10s** (not 40s).

---

## 🧠 Why Use Async Generators and Comprehensions?

| Feature              | Benefit                                                                 |
|---------------------|-------------------------------------------------------------------------|
| Lazy evaluation      | Useful for streaming / large datasets                                  |
| Concurrent execution | Speeds up I/O-bound operations                                          |
| Readability          | Cleaner code with comprehensions                                        |
| Memory efficiency    | No need to store all intermediate values at once                        |

---

## 📌 Type Annotations for Async Generators

```python
from typing import AsyncGenerator
import random
import asyncio

async def async_generator() -> AsyncGenerator[float, None]:
    for _ in range(10):
        await asyncio.sleep(1)
        yield random.uniform(0, 10)
```

- `AsyncGenerator[float, None]`: Yields float values, receives nothing

---

## ✅ Summary

- Use `async def` + `yield` for asynchronous generators
- Use `async for` or `[x async for x in ...]` for clean data gathering
- Combine with `asyncio.gather()` for concurrent execution
- Annotate properly with `AsyncGenerator`, `Coroutine`, etc.
- Useful for I/O-bound tasks: APIs, DB queries, file streaming, etc.

---

## 📚 Additional Resources

- [PEP 530 – Asynchronous Comprehensions](https://peps.python.org/pep-0530/)
- [Async Generators in Python Docs](https://docs.python.org/3/whatsnew/3.6.html#pep-525-asynchronous-generators)
- [Typing Async Generators](https://docs.python.org/3/library/typing.html#typing.AsyncGenerator)
