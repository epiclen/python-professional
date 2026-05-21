# 🐍 Python Bootcamp — Day 1
## 语言基础速通（针对有编程经验者）
*2026-05-21*

> 你有多年编程经验且接触过 Python，Day 1 不讲"变量是什么"。直接按 Python 和其他语言差异最大的点来，快速建立完整的 Python 心智模型。

---

## 一、Python 的"哲学"——先理解为什么

打开 Python 终端执行 `import this`，会看到 Tim Peters 写的《Python 之禅》：

```
Beautiful is better than ugly.
Explicit is better than implicit.
Simple is better than complex.
Complex is better than complicated.
Readability counts.
```

这不仅仅是鸡汤——它直接影响 API 设计。对比一下：

```python
# 不 Pythonic（像 Java 翻译过来）
def calculate_value(a, b, c):
    list_object = new_list()
    list_object.append(a)
    return_value = 0
    for i in list_object:
        return_value = return_value + i
    return return_value

# Pythonic
def calculate(a, b, c):
    return sum([a, b, c])
```

**Python 的核心理念：** 代码首先是给人读的，其次才是给机器执行的。

---

## 二、核心概念速览

### 1. 动态强类型

与 Java 的静态类型和 JavaScript 的弱类型都不同，Python 是**动态强类型**：

```python
# 动态：变量不需要声明类型
x = 42          # int
x = "hello"     # 现在变成 str（合法）

# 强：不会隐式类型转换
"answer: " + 42     # ❌ TypeError: can only concatenate str (not "int") to str
"answer: " + str(42)  # ✅ 要显式转

# 跟 JS 对比（弱类型）
# "5" + 3  = "53"
# "5" - 3  = 2
# Python 不会做这种事
```

### 2. 缩进即块结构

Python 没有 `{}`，用缩进定义代码块：

```python
# 一致的缩进 = 正确的代码
for i in range(3):
    if i % 2 == 0:
        print(f"{i} is even")  # 这行属于 if
    else:
        print(f"{i} is odd")   # 这行属于 else
print("done")  # 这行在 for 外面
```

**标准约定：** 4 个空格，永远不要用 Tab 混着空格。大部分编辑器会自动把 Tab 转成空格。

### 3. 一切皆对象

在 Python 中，函数、类、甚至类型本身都是对象：

```python
def hello():
    return "hello"

hello.custom_attr = 42  # ✅ 函数可以动态加属性
print(hello.__name__)   # "hello"

# 函数可以传给变量、作为参数、作为返回值
f = hello               # 赋值
print(f())              # "hello"

def call_twice(func):
    return func() + func()

print(call_twice(hello))  # "hellohello"
```

对"一切皆对象"的深入理解，是打通 Python 高级主题（装饰器、元类、描述器）的关键。

---

## 三、数据类型详解

### int — 任意精度整数

```python
# 不会溢出，可以算到内存不够为止
a = 2 ** 1000
print(a)
# 10715086071862673209484250490600018105614048117055336074437503883703510511249361224931983788156958581275946729175531468251871452856923140435984577574698574803934567774824230985421074605062371141877954182153046474983581941267398767559165543946077062914571196477686542167660429831652624386837205668069376

# 进制
print(0b1010)    # 10（二进制）
print(0o12)      # 10（八进制）
print(0xA)       # 10（十六进制）
print(bin(10))   # "0b1010"
print(hex(10))   # "0xa"
```

### float — 底层是 C double

```python
# 浮点精度问题（跟所有语言一样）
print(0.1 + 0.2)            # 0.30000000000000004
print(0.1 + 0.2 == 0.3)     # False ❌

# 正确比较方式
print(abs(0.1 + 0.2 - 0.3) < 1e-9)  # True
print(round(0.1 + 0.2, 2) == 0.3)   # True

# Decimal 精确小数
from decimal import Decimal
print(Decimal("0.1") + Decimal("0.2"))  # 0.3
```

### complex — 复数（很少语言原生支持）

```python
c = 3 + 4j
print(c.real)       # 3.0
print(c.imag)       # 4.0
print(abs(c))       # 5.0（模）
print(c.conjugate()) # (3-4j)
```

### bool — int 的子类

```python
# True = 1, False = 0
print(True + True)   # 2
print(False * 10)    # 0

# 隐式布尔转换（记这个列表，超常用）
# 以下值被当作 False：
#   None, False, 0, 0.0, "", [], (), {}, set(), range(0)
if []:      # 不会执行
    print("never")
if [1]:     # 会执行
    print("always")

# 所以不用写：
if len(items) > 0:
# 直接写：
if items:
```

### None — 空值（类似于 null/nil）

```python
x = None
print(x is None)  # True ✅（用 is 比较，不用 ==）
print(x == None)  # 也可以但不推荐
```

---

## 四、字符串

### f-string — Python 3.6 最实用的特性

```python
name = "Leo"
age = 30

# 基础
print(f"Name: {name}, Age: {age}")

# 表达式
print(f"Next year: {age + 1}")

# 格式化
pi = 3.1415926
print(f"Pi: {pi:.2f}")         # Pi: 3.14
print(f"Pi: {pi:.4f}")         # Pi: 3.1416
print(f"Pi: {pi:010.3f}")      # Pi: 000003.142

# 对齐
print(f"|{'left':<10}|")       # |left      |
print(f"|{'right':>10}|")      # |     right|
print(f"|{'center':^10}|")     # |  center  |

# 百分比
print(f"Rate: {0.856:.1%}")    # Rate: 85.6%

# 逗号分隔（3.6+）
print(f"{1234567:,}")          # 1,234,567
```

### 字符串不可变

```python
s = "hello"
# s[0] = "H"     # ❌ TypeError: 'str' object does not support item assignment
s = "H" + s[1:]  # ✅ 只能重新创建
```

### join 而不是 +=

```python
# ❌ O(n²) — 每次 + 创建新字符串
result = ""
for s in words:
    result += s + ", "

# ✅ O(n) — 一次性拼接
result = ", ".join(words)
```

### 常用方法

```python
text = "  Hello, World!  "

# 修剪
text.strip()       # "Hello, World!"
text.lstrip()      # "Hello, World!  "
text.rstrip()      # "  Hello, World!"

# 分割与合并
"a,b,c".split(",")      # ['a', 'b', 'c']
"a\nb\nc".splitlines()  # ['a', 'b', 'c']

# 查找
text.find("World")     # 8（索引）
text.find("Python")    # -1（没找到）
"World" in text        # True（成员检查，最常用）

# 替换
text.replace("World", "Python")

# 判断
"abc123".isalpha()     # False
"abc".isalpha()        # True
"123".isdigit()        # True

# 大小写
"hello".upper()        # "HELLO"
"HELLO".lower()        # "hello"
"hello world".title()  # "Hello World"
```

---

## 五、容器类型

### 列表（list）— 最常用的容器

```python
# 创建
nums = [1, 2, 3, 4, 5]
mixed = [1, "hello", 3.14, [1, 2]]  # 可以放不同类型

# 索引和切片
nums[0]        # 1
nums[-1]       # 5（倒数第一个）
nums[1:3]      # [2, 3]（左闭右开）
nums[::2]      # [1, 3, 5]（步进）
nums[::-1]     # [5, 4, 3, 2, 1]（反转）

# 修改
nums.append(6)       # [1,2,3,4,5,6]
nums.extend([7, 8])  # [1,2,3,4,5,6,7,8]
nums.insert(0, 0)    # [0,1,2,3,4,5,6,7,8]
nums.pop()           # 删除并返回最后一个
nums.pop(0)          # 删除并返回索引 0
nums.remove(3)       # 删除第一个值为 3 的元素
```

**列表推导式 — Python 标志性语法：**

```python
# 基本
squares = [x**2 for x in range(10)]
# 等价于：
squares = []
for x in range(10):
    squares.append(x**2)

# 条件过滤
evens = [x for x in range(20) if x % 2 == 0]

# 嵌套推导
matrix = [[i*3 + j for j in range(3)] for i in range(3)]
# [[0,1,2],[3,4,5],[6,7,8]]

# 展开嵌套列表
flat = [x for row in matrix for x in row]
# [0,1,2,3,4,5,6,7,8]
```

### 元组（tuple）— 不可变列表

```python
# 创建
point = (3, 4)
single = (1,)   # 注意逗号！(1) 是 int

# 解包
x, y = point
# 变量交换不需要临时变量
a, b = b, a

# 星号解包
first, *middle, last = [1, 2, 3, 4, 5]
# first=1, middle=[2,3,4], last=5

# 元组可哈希 → 能用做字典键
d = {(1, 2): "point_a"}
```

### 集合（set）— 无序不重复

```python
s = {1, 2, 3, 3, 2}
print(s)        # {1, 2, 3}（自动去重）

s.add(4)
s.discard(99)   # 删除，不存在也不报错
s.remove(99)    # ❌ 不存在会报 KeyError

# 集合运算
a = {1, 2, 3}
b = {2, 3, 4}

a & b    # {2, 3}          交集
a | b    # {1, 2, 3, 4}    并集
a - b    # {1}             差集
a ^ b    # {1, 4}          对称差集

# O(1) 查找 — 比列表快得多
if item in large_set:   # O(1)
if item in large_list:  # O(n)
```

### 字典（dict）— 关联数组

```python
user = {
    "name": "Leo",
    "age": 30,
    "skills": ["Python", "Go"],
}

# 安全取值
user.get("name")           # "Leo"
user.get("salary", 0)      # 0（key 不存在返回默认值）
user["salary"]             # ❌ KeyError

# setdefault — key 不存在才设值
user.setdefault("level", "junior")
user.setdefault("level", "senior")  # 不会覆盖

# 更新/合并
d1 = {"a": 1, "b": 2}
d2 = {"b": 3, "c": 4}
d1.update(d2)    # d1 = {"a":1, "b":3, "c":4}
d3 = d1 | d2     # 3.9+ 合并运算符

# 遍历
for key in user:
    print(key, user[key])

for key, value in user.items():
    print(f"{key}={value}")

# 字典推导
squares = {x: x**2 for x in range(5)}
# {0:0, 1:1, 2:4, 3:9, 4:16}
```

---

## 六、控制流

### for-else / while-else

这是 Python 独有的，其他语言没有。`else` 在循环**没有 break** 时执行：

```python
# 找素数
def is_prime(n):
    for i in range(2, int(n**0.5) + 1):
        if n % i == 0:
            print(f"{n} is divisible by {i}")
            break
    else:
        print(f"{n} is prime")
        return True
    return False

# 实战：找可用端口
for port in [8080, 8081, 8082]:
    if check_port(port):
        print(f"Using port {port}")
        break
else:
    raise RuntimeError("No available port")
```

### 海象操作符 :=（3.8+）

在一行内同时赋值和用值，避免重复代码：

```python
# 场景 1：if 条件中
# 之前
data = get_data()
if len(data) > 10:
    print(f"Got {len(data)} items")

# 之后
if (n := len(data)) > 10:
    print(f"Got {n} items")  # n 在 if 块里也能用

# 场景 2：while 循环
# 之前
line = file.readline()
while line:
    process(line)
    line = file.readline()

# 之后
while line := file.readline():
    process(line)

# 场景 3：列表推导式
# 筛选变换后非 None 的结果
results = [y for x in data if (y := transform(x)) is not None]
```

### match-case（3.10+）

Python 的 switch-case，但不止于此。它可以**模式匹配**——即匹配*结构*而不是值：

```python
# 基础——替代 if-elif
def http_status(code):
    match code:
        case 200:
            return "OK"
        case 301 | 302:  # 多个值用 | 连接
            return "Redirect"
        case 404:
            return "Not Found"
        case _:          # 默认分支
            return "Unknown"

# 进阶——匹配结构
def process_command(cmd):
    match cmd:
        # ("quit", ...) 结构
        case ("quit",):
            return "bye"
        
        # ("move", x, y) 结构 + guard 条件
        case ("move", x, y) if 0 <= x <= 100 and 0 <= y <= 100:
            return f"Moving to ({x}, {y})"
        
        # 字典匹配
        case {"type": "user", "name": name, "age": age}:
            return f"User {name}, {age}"
        
        # 列表匹配
        case [int() as x, int() as y]:
            return f"Coordinates: {x}, {y}"
        
        case _:
            return "Unknown command"

# 比 switch 强的地方：
# 1. 匹配结构，不仅仅是数值
# 2. 支持 guard（if 条件）
# 3. 自动解构赋值
# 4. | 连接多个模式
```

---

## 七、函数

### 参数种类（这是 Python 独有特色）

```python
def complex_func(
    a,                # 位置参数
    /,                # 分隔符：前面只能位置传参
    b,                # 位置或关键字
    *,                # 分隔符：后面只能关键字传参
    c,                # 仅关键字参数
    d=42,             # 默认值参数
    **kwargs          # 关键字参数收集（打包成 dict）
):
    print(a, b, c, d, kwargs)

# 正确调用
complex_func(1, 2, c=3, d=4, extra="hello")
# 1 2 3 4 {'extra': 'hello'}

# 错调用
# complex_func(1, 2, 3, 4, extra="hello")  # ❌ c 不能位置传
# complex_func(a=1, b=2, c=3)                # ❌ a 不能关键字传
```

### 可变参数

```python
# *args — 任意数量位置参数
def sum_all(*numbers):
    return sum(numbers)

print(sum_all(1, 2, 3))       # 6
print(sum_all(1, 2, 3, 4, 5)) # 15

# **kwargs — 任意数量关键字参数
def build_url(base, **params):
    query = "&".join(f"{k}={v}" for k, v in params.items())
    return f"{base}?{query}"

print(build_url("http://api.com", name="Leo", age=30))
# http://api.com?name=Leo&age=30
```

### 闭包与工厂函数

```python
def make_counter(start=0):
    count = [start]  # 用列表模拟可变闭包（不可变类型需要用容器）
    
    def increment(step=1):
        count[0] += step
        return count[0]
    
    def reset():
        count[0] = start
    
    return increment, reset

inc, reset = make_counter(10)
print(inc())    # 11
print(inc(5))   # 16
reset()
print(inc())    # 11
```

### 可变默认值陷阱 — 经典面试题

```python
# ❌ 大坑
def add_item(item, items=[]):
    items.append(item)
    return items

print(add_item(1))      # [1]
print(add_item(2))      # [1, 2]——不是 [2]！
print(add_item(3))      # [1, 2, 3]

# 原因：默认值在函数定义时求值一次，之后每次都改同一个列表
# 打印默认值看看：
print(add_item.__defaults__)  # ([1, 2, 3],)

# ✅ 正确做法
def add_item(item, items=None):
    if items is None:
        items = []
    items.append(item)
    return items
```

### lambda

```python
# 单行匿名函数
square = lambda x: x ** 2
print(square(5))  # 25

# 常用搭配
pairs = [(1, "b"), (2, "a"), (3, "c")]
pairs.sort(key=lambda x: x[1])  # 按第二个元素排序

sorted([-3, 1, -2, 4], key=lambda x: abs(x))
# [1, -2, -3, 4]
```

---

## 八、异常处理

### try-except-else-finally

```python
try:
    result = risky_operation()
except ValueError as e:          # 捕获特定异常
    print(f"Value error: {e}")
except (IOError, OSError) as e:  # 捕获多个异常
    print(f"IO error: {e}")
except Exception:                # 捕获所有（少用）
    print("Something wrong")
    raise                        # 重新抛出
else:
    print(f"Success: {result}")  # 没异常才执行（比放在 try 里更清晰）
finally:
    cleanup()                    # 无论是否有异常都执行
```

### 自定义异常

```python
class AppError(Exception):
    """应用异常基类"""
    pass

class ValidationError(AppError):
    def __init__(self, field: str, message: str):
        self.field = field
        self.message = message
        super().__init__(f"{field}: {message}")

# 使用
raise ValidationError("email", "Invalid format")
```

### raise ... from（异常链）

```python
def load_config(path):
    try:
        with open(path) as f:
            return json.load(f)
    except FileNotFoundError as e:
        raise ConfigError(f"Config not found: {path}") from e
    # from e 保留了原始异常信息，方便调试
```

---

## 九、文件 I/O

### with 语句（上下文管理器）

```python
# 不需要手动 close——with 退出时自动关闭
with open("data.txt", "r", encoding="utf-8") as f:
    content = f.read()       # 整个文件
    lines = f.readlines()    # 按行列表

# 大文件逐行读取
with open("large.log", "r") as f:
    for line in f:           # 惰性读取，不会一次加载整个文件
        process(line)
```

### pathlib（Python 3.4+）— 比 os.path 好 10 倍

```python
from pathlib import Path

# 路径操作
p = Path("data/config.json")
p.parent           # data/
p.stem             # config
p.suffix           # .json
p.name             # config.json

# 检查
p.exists()         # True/False
p.is_file()
p.is_dir()

# 读写（最常用）
p.read_text()                  # 读为字符串
p.write_text("hello")          # 写入字符串
p.read_bytes()                 # 读为字节
p.write_bytes(b"\x00\x01")     # 写入字节

# 遍历
for py_file in Path("src").rglob("*.py"):
    print(py_file)

# 创建目录
Path("data/logs").mkdir(parents=True, exist_ok=True)
```

---

## 十、模块与 import

```python
# 三种 import 方式
import os                    # os 命名空间可用
from pathlib import Path     # Path 直接可用
from datetime import datetime as dt  # 别名

# 绝对 vs 相对
# 推荐绝对导入
from my_package.utils.helpers import parse_date

# 模块也是对象
import math
print(math.__name__)   # "math"
print(math.pi)         # 3.14159...
```

### if __name__ == "__main__"

```python
# 当文件被直接运行时 __name__ = "__main__"
# 当文件被 import 时 __name__ = 模块名
def main():
    print("Running directly")

if __name__ == "__main__":
    main()
```

---

## 今日练习（动手才能记住）

1. **海象操作符练习**：有一段数据 `data = [1, 2, 3, 4, 5, 6, 7, 8]`，用 `:=` 在 if 条件中计算长度，同时输出 "长度大于 5" 或 "长度 <= 5"

2. **match-case 解析器**：用 match-case 写一个函数，解析下面这种命令格式：
   ```python
   commands = [
       ("set", "theme", "dark"),
       ("get", "config"),
       ("delete", "user", 42),
       {"cmd": "quit"},
   ]
   ```
   对每种命令打印不同的输出。

3. **字典处理**：给一个学生成绩字典 `{"Alice": 85, "Bob": 92, "Charlie": 78, "David": 95}`，用字典推导式筛选出成绩 >= 90 的学生，输出他们的名字和大写格式。

4. **文件处理**：用 pathlib 在当前目录下找所有 `.txt` 文件，读取每个文件的第一行，输出到控制台。

---

**明天预告：Day 2 — 进阶语法**
- 装饰器（@语法糖、带参数装饰器、类装饰器）
- 生成器（yield、yield from、生成器表达式）
- 迭代器（可迭代 vs 迭代器、自定义迭代器）
- 上下文管理器（with、contextmanager）
- 包的导入机制和项目结构

> 如果你还没准备好明天的节奏，现在告诉我，我可以调整内容深度。
