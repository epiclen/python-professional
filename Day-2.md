# 🐍 Python Bootcamp — Day 2
## 进阶语法：装饰器、生成器、迭代器、上下文管理器
*2026-05-22*
---
## 1. 迭代器协议
### 基础
```python
# 任何实现了 __iter__ 和 __next__ 的对象都是迭代器
class CountDown:
    def __init__(self, start):
        self.current = start
    def __iter__(self):
        return self
    def __next__(self):
        if self.current <= 0:
            raise StopIteration
        self.current -= 1
        return self.current + 1
for n in CountDown(5):
    print(n)  # 5, 4, 3, 2, 1
```
### 可迭代 vs 迭代器
```python
from collections.abc import Iterable, Iterator
# 可迭代：有 __iter__（可重复使用）
lst = [1, 2, 3]
isinstance(lst, Iterable)  # True
isinstance(lst, Iterator)  # False
for x in lst: pass
for x in lst: pass  # 没问题，重新开始
# 迭代器：有 __iter__ + __next__（一次性）
it = iter(lst)
isinstance(it, Iterator)  # True
for x in it: pass
for x in it: pass  # 空的！已经消耗完了
```
### reversed / enumerate / zip
```python
# zip 的星号技巧
columns = [("id", 1), ("name", "Leo"), ("age", 30)]
keys, values = zip(*columns)
print(keys)    # ("id", "name", "age")
print(values)  # (1, "Leo", 30)
# enumerate 指定起始值
for i, line in enumerate(lines, start=1):
    print(f"Line {i}: {line}")
```
---
## 2. 生成器
### yield 基础
```python
def fibonacci(limit):
    a, b = 0, 1
    while a < limit:
        yield a
        a, b = b, a + b
for n in fibonacci(100):
    print(n)  # 0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89
```
### 生成器表达式
```python
# 和列表推导式几乎一样，但惰性求值
import sys
list_comp = [x**2 for x in range(10000)]
gen_expr  = (x**2 for x in range(10000))
print(sys.getsizeof(list_comp))  # 85176 bytes
print(sys.getsizeof(gen_expr))   # 200 bytes 🚀
```
### yield from — 委托给子生成器
```python
def flatten(nested):
    for item in nested:
        if isinstance(item, (list, tuple)):
            yield from flatten(item)
        else:
            yield item
nested = [1, [2, [3, 4], 5], 6]
print(list(flatten(nested)))  # [1, 2, 3, 4, 5, 6]
```
### send / throw / close — 双向生成器
```python
def running_average():
    total = 0.0
    count = 0
    avg = None
    while True:
        new_val = yield avg
        if new_val is None:
            break
        total += new_val
        count += 1
        avg = total / count
avg_gen = running_average()
next(avg_gen)            # 启动，返回 None
print(avg_gen.send(10))  # 10.0
print(avg_gen.send(20))  # 15.0
print(avg_gen.send(30))  # 20.0
avg_gen.close()          # 关闭
```
---
## 3. 装饰器
### 基础 — 函数即对象
```python
def log_calls(func):
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__} with {args}, {kwargs}")
        result = func(*args, **kwargs)
        print(f"{func.__name__} returned {result}")
        return result
    return wrapper
@log_calls
def add(a, b):
    return a + b
add(3, 5)
# Calling add with (3, 5), {}
# add returned 8
```
### 保留元数据
```python
import functools
def log_calls(func):
    @functools.wraps(func)  # 维护 __name__, __doc__ 等
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__}")
        return func(*args, **kwargs)
    return wrapper
```
### 带参数装饰器（三层嵌套）
```python
def retry(max_attempts=3):
    def decorator(func):
        @functools.wraps(func)
        def wrapper(*args, **kwargs):
            for attempt in range(1, max_attempts + 1):
                try:
                    return func(*args, **kwargs)
                except Exception as e:
                    if attempt == max_attempts:
                        raise
                    print(f"Attempt {attempt} failed, retrying...")
            return None
        return wrapper
    return decorator
@retry(max_attempts=3)
def fetch_data(url):
    return requests.get(url)
```
### 类装饰器
```python
def singleton(cls):
    instances = {}
    @functools.wraps(cls)
    def get_instance(*args, **kwargs):
        if cls not in instances:
            instances[cls] = cls(*args, **kwargs)
        return instances[cls]
    return get_instance
@singleton
class Database:
    def __init__(self):
        print("Connecting...")
d1 = Database()  # Connecting...
d2 = Database()  # 不打印
print(d1 is d2)  # True
```
---
## 4. 上下文管理器
### 类实现
```python
class Timer:
    def __enter__(self):
        self.start = time.perf_counter()
        return self  # as 后面的值
    def __exit__(self, exc_type, exc_val, exc_tb):
        elapsed = time.perf_counter() - self.start
        print(f"Took {elapsed:.3f}s")
        return False  # False = 不吞异常，True = 吞
with Timer():
    expensive_operation()
```
### contextmanager 装饰器
```python
from contextlib import contextmanager
@contextmanager
def temporary_file(suffix=""):
    """创建临时文件，退出时自动删除"""
    import tempfile
    import os
    # __enter__
    fd, path = tempfile.mkstemp(suffix=suffix)
    try:
        yield path  # as 后面的值
    finally:
        # __exit__
        os.close(fd)
        os.remove(path)
with temporary_file(".txt") as tmp_path:
    with open(tmp_path, "w") as f:
        f.write("Hello")
    # 自动清理
```
### ExitStack — 动态上下文管理
```python
from contextlib import ExitStack
def process_files(files: list[str]):
    """不确定数量的上下文"""
    with ExitStack() as stack:
        handles = [
            stack.enter_context(open(f, "r"))
            for f in files
        ]
        # 所有文件同时打开，退出时自动关闭
        return [h.read() for h in handles]
```
---
## 5. 模块与包
### 绝对导入 vs 相对导入
```python
# 绝对导入（推荐）
from my_package.utils.helpers import parse_date
# 相对导入（仅在包内使用）
from . import models          # 当前包
from ..utils import helpers   # 父包
```
### `__all__` — 控制导出
```python
# my_module.py
__all__ = ["public_func", "PublicClass"]
def public_func(): pass
def _private_func(): pass  # 下划线开头 = 内部
# from my_module import *
# 只导入 public_func 和 PublicClass
```
### if __name__ == "__main__"
```python
def main():
    """脚本入口"""
    parser = argparse.ArgumentParser()
    parser.add_argument("--name")
    args = parser.parse_args()
    print(f"Hello {args.name}")
if __name__ == "__main__":
    main()
```
### 包结构规范
```
my_project/
├── src/
│   └── my_project/
│       ├── __init__.py      # 包标记 + 公共导出
│       ├── __main__.py       # python -m my_project
│       ├── cli.py
│       └── core.py
├── tests/
├── pyproject.toml
└── README.md
```
---
## 今日练习
1. **生成器实现 range**：自己实现一个 `my_range(start, stop, step)` 生成器
2. **性能装饰器**：写一个 `@memoize` 装饰器，缓存函数结果（注意处理可变参数）
3. **数据库上下文**：实现一个 `connection()` 上下文管理器，自动提交/回滚
4. **树形结构生成器**：用 yield from 实现一个递归目录遍历生成器
---
*明天预告：Day 3 — 中级核心（collections、itertools、functools 库、日志、错误处理模式）*
