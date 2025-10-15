

```python
payload = {'key1': 'value1', 'key2': 'value2'}
r = requests.get('https://httpbin.org/get', params=payload)
r.json()
```


```python
# =========================
# 1) Context managers
# =========================

# a) Class-based context manager
class FileCM:
    def __init__(self, path, mode):
        self.path, self.mode, self.f = path, mode, None
    def __enter__(self):
        self.f = open(self.path, self.mode, encoding="utf-8")
        return self.f
    def __exit__(self, exc_type, exc, tb):
        if self.f:
            self.f.close()
        # return False to propagate exceptions
        return False

with FileCM("demo.txt", "w") as fh:
    fh.write("hello\n")

# b) @contextmanager-style
from contextlib import contextmanager

@contextmanager
def temp_value(obj, attr, new):
    old = getattr(obj, attr)
    setattr(obj, attr, new)
    try:
        yield obj
    finally:
        setattr(obj, attr, old)

class C: 
    x = 1

c = C()
with temp_value(C, "x", 99):
    assert C.x == 99
assert C.x == 1


# =========================
# 2) Decorators with args
# =========================

import time
import functools

# you need an outer function if you want to use args
def timing(label="timing", *, rounds=1):
    def deco(fn):
        @functools.wraps(fn)
        def wrap(*a, **kw):
            t0 = time.perf_counter()
            out = None
            for _ in range(rounds):
                out = fn(*a, **kw)
            dt = time.perf_counter() - t0
            print(f"[{label}] {fn.__name__}: {dt:.6f}s over {rounds} run(s)")
            return out
        return wrap
    return deco

@timing("fib", rounds=1)
def fib(n):
    return 1 if n < 2 else fib(n-1) + fib(n-2)

# print(fib(5))


# =========================
# 3) Iterators and generators
# =========================

# a) Iterator class
class MyRange:
    def __init__(self, start, stop=None, step=1):
        if stop is None:
            start, stop = 0, start
        self.cur, self.stop, self.step = start, stop, step
    def __iter__(self):
        return self
    def __next__(self):
        if (self.step > 0 and self.cur >= self.stop) or (self.step < 0 and self.cur <= self.stop):
            raise StopIteration
        v = self.cur
        self.cur += self.step
        return v

# list(MyRange(3)) -> [0,1,2]

# b) Generator function
def gen_squares(n):
    for i in range(n):
        yield i*i

# list(gen_squares(5)) -> [0,1,4,9,16]

# c) 'yield from' delegation
def flatten(list_of_lists):
    for sub in list_of_lists:
        # delegate to sub-iterator
        yield from sub

# list(flatten([[1,2],[3],[4,5]])) -> [1,2,3,4,5]


# =========================
# 4) Coroutines + 'yield from' details
# =========================

# a) Modern asyncio coroutines
import asyncio

async def subtask(x):
    await asyncio.sleep(0.01)
    return x * 2

async def main_asyncio():
    # run concurrently
    results = await asyncio.gather(subtask(1), subtask(2), subtask(3))
    return results

# asyncio.run(main_asyncio())

# b) Generator-based coroutine (educational)
#   'yield from' drives a subgenerator; 'types.coroutine' adapts to 'await'
import types

@types.coroutine
def legacy_sleep(dt):
    # Placeholder; real event loop would handle this
    yield ("sleep", dt)
    return None

def legacy_coro():
    # generator-based "coroutine"
    yield from legacy_sleep(0.01)
    return 42


# =========================
# 5) ThreadPool/ProcessPool and 'asyncio.run' blocking semantics
# =========================

# Key facts:
# - ThreadPoolExecutor/ProcessPoolExecutor within 'with' uses shutdown(wait=True) at exit.
#   Exiting the 'with' blocks until all submitted tasks finish (unless you manually shutdown(wait=False)).
# - Calling future.result() blocks until that future completes.
# - asyncio.run(coro) blocks the current thread until 'coro' completes; it also cancels remaining pending tasks in that created loop before closing.

from concurrent.futures import ThreadPoolExecutor, ProcessPoolExecutor
import time

def work(t):
    time.sleep(t)
    return t

def demo_executors():
    # Blocking on result():
    with ThreadPoolExecutor(max_workers=2) as ex:
        f = ex.submit(work, 0.2)
        _ = f.result()  # blocks until done

    # Even without calling result(), exiting 'with' waits for submitted tasks:
    with ThreadPoolExecutor(max_workers=2) as ex:
        ex.submit(work, 0.2)  # no .result()
        # exiting the 'with' waits here

    # Same semantics for ProcessPoolExecutor:
    with ProcessPoolExecutor(max_workers=2) as ex:
        futs = [ex.submit(work, 0.1), ex.submit(work, 0.2)]
        # exiting waits for both

# demo_executors()

# asyncio.run blocks the current thread until done:
async def main_run_block_demo():
    await asyncio.sleep(0.05)
    return "done"

# asyncio.run(main_run_block_demo())  # blocks until "done"


# =========================
# 6) PyTorch __call__ mechanics
# =========================

# torch.nn.Module overrides __call__ to:
#  - run pre-hooks
#  - dispatch to .forward(*args, **kwargs)
#  - run post-hooks
# This enables modules to be called like functions.

# Minimal demonstration:
try:
    import torch
    import torch.nn as nn
    import torch.nn.functional as F

    class MyLinear(nn.Module):
        def __init__(self, in_f, out_f):
            super().__init__()
            self.w = nn.Parameter(torch.randn(out_f, in_f))
            self.b = nn.Parameter(torch.zeros(out_f))

        def forward(self, x):
            return x @ self.w.T + self.b

    m = MyLinear(3, 2)
    x = torch.randn(4, 3)
    y = m(x)          # calls m.__call__ -> m.forward(x)
    # print(y.shape)  # torch.Size([4, 2])
except Exception:
    pass

```