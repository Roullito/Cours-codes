
# 🌀 Python - Asynchronous Execution: Full Course

## 🎯 Introduction
Asynchronous programming is a paradigm that allows multiple tasks to execute concurrently without blocking the main program. In Python, the `asyncio` module along with `async` and `await` keywords form the backbone of asynchronous execution. This model is crucial for building efficient I/O-bound applications such as web scrapers, API clients, or real-time applications.

---

## 🧠 Core Concepts

### ✅ Coroutine
A coroutine is a special type of function that can pause and resume its execution. Declared with `async def`, it runs asynchronously using an event loop.

```python
async def greet():
    print("Hello")
```

### ✅ Event Loop
The event loop is responsible for executing asynchronous tasks. It manages scheduling and ensures that tasks yield control efficiently.

```python
import asyncio
asyncio.run(greet())
```

### ✅ async and await
- `async`: Defines a coroutine.
- `await`: Pauses the coroutine until the awaited task is completed.

```python
async def example():
    await asyncio.sleep(1)
    print("Done sleeping")
```

---

## ⏱ Sequential vs Concurrent Execution

### Sequential Example

```python
import asyncio

async def say_hello():
    await asyncio.sleep(1)
    print("Hello")

async def say_world():
    await asyncio.sleep(1)
    print("World")

async def main():
    await say_hello()
    await say_world()

asyncio.run(main())  # Takes ~2 seconds
```

### Concurrent Example

```python
async def main():
    await asyncio.gather(say_hello(), say_world())

asyncio.run(main())  # Takes ~1 second
```

---

## 🌐 Real-world Example with aiohttp

```python
import aiohttp
import asyncio

async def fetch_url(url):
    async with aiohttp.ClientSession() as session:
        async with session.get(url) as response:
            return await response.text()

async def main():
    urls = ["https://example.com", "https://example.org"]
    tasks = [fetch_url(url) for url in urls]
    results = await asyncio.gather(*tasks)
    for url, content in zip(urls, results):
        print(f"{url}: {len(content)} bytes")

asyncio.run(main())
```

---

## ⚖️ Coroutines vs Threads

| Feature            | Coroutines                          | Threads                               |
|-------------------|-------------------------------------|----------------------------------------|
| Control flow       | Cooperative (`await`)               | Preemptive (OS-controlled)             |
| Best for           | I/O-bound tasks                     | CPU-bound tasks                        |
| Memory footprint   | Very lightweight                    | High (context switching, memory)       |
| Concurrency issues | Minimal                             | Race conditions, deadlocks possible    |
| Parallelism        | Single-threaded (not parallel)      | Multi-core capable (parallelism)       |

---

## 🧪 Measuring Performance

```python
import time

def measure_time(n, max_delay):
    start = time.time()
    asyncio.run(wait_n(n, max_delay))
    end = time.time()
    return (end - start) / n
```

---

## 🧱 Task Creation and Scheduling

### Create a Task

```python
async def delayed():
    await asyncio.sleep(1)
    return 1

task = asyncio.create_task(delayed())
await task  # Waits until completion
```

---

## 📦 Modules Used

- `asyncio`: Core asynchronous programming library.
- `aiohttp`: Async HTTP client for web scraping, APIs.
- `random`: To simulate varied delay or behavior.

---

## 🧰 Use Cases

- Chat apps
- Real-time dashboards
- Data ingestion pipelines
- Web crawlers
- Gaming servers

---

## 🧵 Summary

| Concept       | Summary                                                 |
|---------------|----------------------------------------------------------|
| Coroutine     | Async function capable of suspending and resuming       |
| Event Loop    | Central dispatcher for scheduling tasks                 |
| await         | Pauses execution until awaited coroutine completes      |
| asyncio.gather| Run multiple coroutines concurrently                    |
| create_task   | Schedule coroutine as a task for concurrent execution   |

---

## 📚 Resources
- [asyncio Documentation](https://docs.python.org/3/library/asyncio.html)
- [Real Python - Async in Python](https://realpython.com/async-io-python/)
- [Holberton Project Page - Async Execution](https://github.com/holbertonschool-web_back_end)

---

## ✅ Requirements Reminder (Holberton)
- Python 3.9 on Ubuntu 20.04
- Files must be executable and type-annotated
- `#!/usr/bin/env python3` as first line
- Respect PEP8 with `pycodestyle 2.5.x`

---

**🧠 Pro tip**: Use asynchronous programming for I/O-heavy workloads to gain efficiency, scalability, and non-blocking performance!
