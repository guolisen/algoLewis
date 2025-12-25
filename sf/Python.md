[TOC]

## BFS

### 433 · 岛屿的个数
```
from typing import (
    List,
)

class Solution:
    """
    @param grid: a boolean 2D matrix
    @return: an integer
    """
    def isInBound(self, x, y, m, n):
        if x < 0 or y < 0 or x >= m or y >= n:
            return False
        return True
    def bfs(self, grid, x, y):
        m = len(grid)
        n = len(grid[0])
        dx = [1,-1,0,0]
        dy = [0,0,-1,1]
        q = collections.deque([(x, y)])
        while len(q) != 0:
            curX, curY = q.popleft()
            for d in range(4):
                nx = curX + dx[d]
                ny = curY + dy[d]
                if not self.isInBound(nx, ny, m, n) or not grid[nx][ny]:
                    continue
                q.append((nx, ny))
                grid[nx][ny] = False

    def num_islands(self, grid: List[List[bool]]) -> int:
        m = len(grid)
        if m == 0:
            return 0
        n = len(grid[0])
        res = 0
        for i in range(m):
            for j in range(n):
                if grid[i][j]:
                    res += 1
                    self.bfs(grid, i, j)
        return res
```

## 二分
### 457 · 经典二分查找问题
```
class Solution:
    def findPosition(self, nums, target):
        if len(nums) == 0:
            return -1
        l, r = 0, len(nums) - 1
        while l < r:
            mid = (l + r) / 2
            if nums[mid] >= target:
                r = mid
            else:
                l = mid + 1
        if nums[l] == target:
            return l
        return -1
```
##  数组初始化
```
611 · 骑士的最短路线
visit = [[False] * n for _ in range(m)]
127 · 拓扑排序
de = [g for g in graph if indegree[g] == 0]
indegree = {x : 0 for x in graph}

dp = [False for _ in range(m)]
dp = [[False] * n for _ in range(m)]
dp = [[[False] * p for _ in range(n)] for _ in range(m)]
```
## collections

名称	功能
namedtuple	用于创建具有命名字段的 tuple 子类的 factory 函数 (具名元组)
deque	类似 list 的容器，两端都能实现快速 append 和 pop (双端队列)
ChainMap	类似 dict 的类，用于创建多个映射的单视图
Counter	用于计算 hashable 对象的 dict 子类 (可哈希对象计数)
OrderedDict	记住元素添加顺序的 dict 子类 (有序字典)
defaultdict	dict 子类调用 factory 函数来提供缺失值
UserDict	包装 dict 对象以便于 dict 的子类化
UserList	包装 list 对象以便于 list 的子类化
UserString	包装 string 对象以便于 string 的子类化Python `collections` 模块详细代码示例



### 1. namedtuple - 命名元组

```python
from collections import namedtuple

# 创建命名元组类型
Employee = namedtuple('Employee', ['name', 'id', 'title', 'salary'])

# 实例化
emp1 = Employee('John Doe', 12345, 'Software Engineer', 85000)
emp2 = Employee('Jane Smith', 54321, 'Data Scientist', 92000)

# 访问字段
print(emp1.name)      # 输出: John Doe
print(emp2.title)     # 输出: Data Scientist

# 转换为字典
emp_dict = emp1._asdict()
print(emp_dict)       # 输出: {'name': 'John Doe', 'id': 12345, ...}

# 替换字段值
emp1 = emp1._replace(salary=90000)
print(emp1.salary)    # 输出: 90000
```

### 2. deque - 双端队列

```python
from collections import deque

# 创建双端队列
d = deque(['a', 'b', 'c'])

# 添加元素
d.append('d')         # 右侧添加
d.appendleft('z')     # 左侧添加
print(d)              # 输出: deque(['z', 'a', 'b', 'c', 'd'])

# 移除元素
right_item = d.pop()  # 移除并返回右侧元素
left_item = d.popleft() # 移除并返回左侧元素
print(right_item)     # 输出: 'd'
print(left_item)      # 输出: 'z'

# 旋转队列
d.rotate(1)           # 向右旋转1位
print(d)              # 输出: deque(['c', 'a', 'b'])
d.rotate(-1)          # 向左旋转1位
print(d)              # 输出: deque(['a', 'b', 'c'])

# 限制队列长度
limited_d = deque(maxlen=3)
for i in range(5):
    limited_d.append(i)
print(limited_d)      # 输出: deque([2, 3, 4], maxlen=3)
```

### 3. Counter - 计数器

```python
from collections import Counter

# 创建计数器
words = ['apple', 'banana', 'apple', 'orange', 'banana', 'apple']
word_counts = Counter(words)

print(word_counts)        # 输出: Counter({'apple': 3, 'banana': 2, 'orange': 1})
print(word_counts['apple'])  # 输出: 3

# 更新计数器
word_counts.update(['apple', 'kiwi'])
print(word_counts)        # 输出: Counter({'apple': 4, 'banana': 2, 'orange': 1, 'kiwi': 1})

# 获取最常见元素
print(word_counts.most_common(2))  # 输出: [('apple', 4), ('banana', 2)]

# 数学运算
c1 = Counter(a=3, b=1)
c2 = Counter(a=1, b=2)
print(c1 + c2)           # 输出: Counter({'a': 4, 'b': 3})
print(c1 - c2)           # 输出: Counter({'a': 2})
print(c1 & c2)           # 输出: Counter({'a': 1, 'b': 1}) (交集)
print(c1 | c2)           # 输出: Counter({'a': 3, 'b': 2}) (并集)
```

### 4. defaultdict - 默认字典

```python
from collections import defaultdict

# 创建默认值为0的字典
dd_int = defaultdict(int)
words = ['apple', 'banana', 'apple']
for word in words:
    dd_int[word] += 1
print(dd_int)            # 输出: defaultdict(<class 'int'>, {'apple': 2, 'banana': 1})

# 默认值为列表的字典
dd_list = defaultdict(list)
data = [('a', 1), ('b', 2), ('a', 3), ('b', 4)]
for key, value in data:
    dd_list[key].append(value)
print(dd_list)           # 输出: defaultdict(<class 'list'>, {'a': [1, 3], 'b': [2, 4]})

# 自定义默认值工厂函数
def default_factory():
    return {'count': 0, 'total': 0}

dd_custom = defaultdict(default_factory)
dd_custom['a']['count'] += 1
dd_custom['a']['total'] += 10
print(dd_custom)         # 输出: defaultdict(<function default_factory...>, {'a': {'count': 1, 'total': 10}})
```

### 5. OrderedDict - 有序字典

```python
from collections import OrderedDict

# 创建有序字典
od = OrderedDict()
od['a'] = 1
od['b'] = 2
od['c'] = 3
print(od)                # 输出: OrderedDict([('a', 1), ('b', 2), ('c', 3)])

# 保持插入顺序
od['d'] = 4
print(list(od.keys()))   # 输出: ['a', 'b', 'c', 'd']

# 移动元素到末尾
od.move_to_end('a')
print(list(od.keys()))   # 输出: ['b', 'c', 'd', 'a']

# 弹出元素
last_item = od.popitem(last=True)  # LIFO顺序
first_item = od.popitem(last=False) # FIFO顺序
print(last_item)         # 输出: ('a', 4)
print(first_item)        # 输出: ('b', 2)

# 有序字典相等性测试
od1 = OrderedDict([('a', 1), ('b', 2)])
od2 = OrderedDict([('b', 2), ('a', 1)])
print(od1 == od2)        # 输出: False (顺序不同)
```

### 6. ChainMap - 链式映射

```python
from collections import ChainMap

# 创建ChainMap
dict1 = {'a': 1, 'b': 2}
dict2 = {'b': 3, 'c': 4}
chain = ChainMap(dict1, dict2)

# 查找键 (搜索顺序: dict1 -> dict2)
print(chain['a'])        # 输出: 1 (来自dict1)
print(chain['b'])        # 输出: 2 (来自dict1)
print(chain['c'])        # 输出: 4 (来自dict2)

# 更新操作只影响第一个映射
chain['c'] = 5           # 更新dict1
print(dict1)             # 输出: {'a': 1, 'b': 2, 'c': 5}
print(dict2)             # 输出: {'b': 3, 'c': 4} (未改变)

# 添加新映射
dict3 = {'d': 6}
new_chain = chain.new_child(dict3)
print(new_chain['d'])    # 输出: 6

# 获取所有键 (可能有重复)
print(list(chain.keys()))  # 输出: ['a', 'b', 'c']

# 获取所有值 (可能有重复)
print(list(chain.values()))  # 输出: [1, 2, 5, 3, 4]
```


## heap
4 · 丑数 II
```
import heapq

class Solution:
    """
    @param n: An integer
    @return: return a  integer as description.
    """
    def nthUglyNumber(self, n):
        heap = []
        heapq.heappush(heap, 1)

        seen = set()
        seen.add(1)

        factors = [2, 3, 5]

        curr_ugly = 1
        
        for _ in range(n):
            # 每次弹出当前最小丑数
            curr_ugly = heapq.heappop(heap)
            # 生成新的丑数
            for f in factors:
                new_ugly = curr_ugly * f
                if new_ugly not in seen:
                    seen.add(new_ugly)
                    heapq.heappush(heap, new_ugly)
        return curr_ugly
```

## list反向遍历

```
lst = [1, 2, 3, 4, 5]
for item in reversed(lst):
    print(item)

lst = [1, 2, 3, 4, 5]
for item in lst[::-1]:
    print(item)

lst = [1, 2, 3, 4, 5]
i = len(lst) - 1
while i >= 0:
    print(lst[i])
    i -= 1

lst = [1, 2, 3, 4, 5]
for i in range(len(lst)-1, -1, -1):
    print(lst[i])
```

## Python dict()
在 Python 中，字典（`dict`）是一种键值对（`key-value`）数据结构，用于存储和快速查找数据。判断某个 `key` 是否存在于字典中，有以下几种常用方法：


### 一、字典的基本使用（快速回顾）
先简单了解字典的创建和基本操作，方便理解后续内容：
```python
# 创建字典
my_dict = {
    "name": "Alice",
    "age": 30,
    "city": "New York"
}

# 访问值（通过 key）
print(my_dict["name"])  # 输出：Alice

# 添加/修改键值对
my_dict["email"] = "alice@example.com"  # 添加新 key
my_dict["age"] = 31  # 修改已有 key 的值
```


### 二、判断 key 是否存在的 3 种方法

#### 1. 使用 `in` 关键字（推荐）
`in` 是最简洁直观的方法，直接判断 `key` 是否在字典的键集合中，返回 `True` 或 `False`。

```python
my_dict = {"name": "Alice", "age": 30}

# 判断 "name" 是否存在
print("name" in my_dict)  # 输出：True

# 判断 "email" 是否存在
print("email" in my_dict)  # 输出：False
```

- 原理：`key in dict` 等价于 `key in dict.keys()`（`dict.keys()` 返回所有键的集合）。
- 优势：简洁、高效，时间复杂度为 O(1)（字典的核心优势）。


#### 2. 使用 `dict.get()` 方法
`get(key, default)` 方法用于获取 `key` 对应的值，若 `key` 不存在则返回 `default`（默认值为 `None`）。可通过判断返回值是否为默认值来间接判断 `key` 是否存在。

```python
my_dict = {"name": "Alice", "age": 30}

# 若 key 不存在，返回 None（默认）
if my_dict.get("email") is None:
    print("'email' 不存在")  # 输出：'email' 不存在

# 自定义默认值，更明确区分
if my_dict.get("email", "不存在") == "不存在":
    print("'email' 不存在")  # 输出：'email' 不存在
```

- 注意：若 `key` 存在但对应的值恰好是 `None`，则无法用 `get(key) is None` 判断（会误判），此时推荐用 `in` 关键字。


#### 3. 使用 `dict.has_key()` 方法（不推荐）
这是 Python 2 中的方法，在 Python 3 中已被移除，**不建议使用**，仅作了解：
```python
# Python 2 中可用，Python 3 中会报错
my_dict = {"name": "Alice"}
print(my_dict.has_key("name"))  # Python 2 输出：True
```
### 三、总结
- **推荐用法**：`key in dict`（简洁、高效、无歧义）。
- **场景选择**：
  - 仅判断存在性：用 `in`。
  - 判断存在性的同时需要获取值：用 `get()`（如 `value = my_dict.get(key, default_value)`）。

例如，实际开发中常见写法：
```python
my_dict = {"name": "Alice", "age": 30}
key = "email"

if key in my_dict:
    print(f"{key} 存在，值为：{my_dict[key]}")
else:
    print(f"{key} 不存在")
```

### 删除dict元素

```
my_dict = {"name": "Alice", "age": 30, "city": "New York"}

# 删除 key 为 "age" 的元素
del my_dict["age"]
print(my_dict)  # 输出：{'name': 'Alice', 'city': 'New York'}
```



## Python `set()` 使用详解

`set()` 是 Python 中的集合数据类型，用于存储**唯一、无序**的元素集合。以下是 `set` 的详细用法：

### 1. 创建集合

```python
# 方法1：使用 set() 函数
empty_set = set()          # 创建空集合
numbers = set([1, 2, 3])   # 从列表创建
chars = set("hello")       # 从字符串创建 {'h', 'e', 'l', 'o'}

# 方法2：使用花括号（不能创建空集合）
fruits = {'apple', 'banana', 'orange'}
```

⚠️ 注意：`{}` 创建的是空字典，不是空集合，空集合必须用 `set()`

### 2. 基本操作

#### 添加元素
```python
s = {1, 2, 3}
s.add(4)        # 添加单个元素 {1, 2, 3, 4}
s.update([5, 6]) # 添加多个元素 {1, 2, 3, 4, 5, 6}
```

#### 删除元素
```python
s = {1, 2, 3, 4, 5}
s.remove(3)     # 移除元素，不存在会报错
s.discard(10)   # 移除元素，不存在不会报错
popped = s.pop() # 随机移除并返回一个元素
s.clear()       # 清空集合
```

### 3. 集合运算

```python
a = {1, 2, 3, 4}
b = {3, 4, 5, 6}

# 并集
print(a | b)    # {1, 2, 3, 4, 5, 6}
print(a.union(b))

# 交集
print(a & b)    # {3, 4}
print(a.intersection(b))

# 差集
print(a - b)    # {1, 2}
print(a.difference(b))

# 对称差集（仅在其中一个集合中的元素）
print(a ^ b)    # {1, 2, 5, 6}
print(a.symmetric_difference(b))
```

### 4. 集合关系判断

```python
x = {1, 2}
y = {1, 2, 3}

print(x <= y)   # True，x是y的子集
print(x.issubset(y))

print(y >= x)   # True，y是x的超集
print(y.issuperset(x))

print(x.isdisjoint({5, 6}))  # True，没有共同元素
```

### 5. 集合推导式

```python
# 创建1-10的平方集合
squares = {x**2 for x in range(1, 11)}
# {1, 4, 9, 16, 25, 36, 49, 64, 81, 100}

# 从字符串创建不重复字母的大写集合
unique_letters = {c.upper() for c in "hello world" if c.isalpha()}
# {'H', 'E', 'L', 'O', 'W', 'R', 'D'}
```

### 6. 常用方法

```python
s = {1, 2, 3}

# 长度
print(len(s))   # 3

# 检查存在
print(2 in s)   # True

# 复制集合
s_copy = s.copy()
```

### 实际应用示例

1. **去重**：
```python
lst = [1, 2, 2, 3, 4, 4, 4]
unique = list(set(lst))  # [1, 2, 3, 4]
```

2. **查找共同元素**：
```python
developers = {'John', 'Jane', 'Jack'}
designers = {'Jane', 'Jack', 'Jill'}
team = developers & designers  # {'Jane', 'Jack'}
```

3. **快速成员检查**（比列表快）：
```python
valid_users = {'user1', 'user2', 'user3'}
if input_username in valid_users:
    print("Access granted")
```

记住集合的特点：
- 元素必须是可哈希的（不可变类型）
- 无序（不能通过索引访问）
- 自动去重
- 支持高效的成员检测和集合运算

## 字符串

```
s = "abc"
sl = list(s)
sl[1] = 'f'
ns = ''.join(sl)
```

```
# 定义一个包含大写字母的字符串
original_str = "Hello World! PYTHON IS FUN."

# 使用 lower() 方法转换为小写
lowercase_str = original_str.lower()
```

```
s为字符串 s[::-1]会新生成一个列表，要原地反转需要s.reverse()
```

## list注意

list(cur)会生成一个新的list，保证旧的list变化的时候不会影响结果

```
cur = [1, 2, 3]
res = []

# 添加 cur 的浅拷贝（新列表）
res.append(list(cur))

# 修改原 cur，不会影响 res 中已添加的列表
cur.append(4)

print(cur)  # 输出：[1, 2, 3, 4]（原列表被修改）
print(res)  # 输出：[[1, 2, 3]]（res 中的列表仍是添加时的状态）
```




## 二进制求和

转换成10进制，相加，再转二进制

```
class Solution:
    def addBinary(self, a, b) -> str:
        return '{0:b}'.format(int(a, 2) + int(b, 2))
```



## 二分查找

[875. Koko Eating Bananas](https://leetcode.cn/problems/koko-eating-bananas/)

```
class Solution:
    def minEatingSpeed(self, piles: List[int], h: int) -> int:
        return bisect_left(range(max(piles)), -h, 1, key=lambda k: -sum((pile + k - 1) // k for pile in piles))

```



## Python `bisect` 模块详解

`bisect` 是 Python 标准库中的一个模块，提供了基于二分查找算法的函数，用于在**有序序列**中查找和插入元素。这个模块对于需要维护有序列表或进行高效查找的场景非常有用。

### 主要功能

`bisect` 模块主要提供两类操作：

1. **查找操作** - 确定元素在有序序列中的位置
2. **插入操作** - 在有序序列中插入元素并保持序列有序

### 核心函数

#### 查找函数

1. **bisect_left(a, x, lo=0, hi=len(a), *, key=None)**

   - 返回插入位置，使得插入后所有相等的元素位于插入值的**左侧**

   - 如果元素已存在，返回第一个相等元素的位置

   - 示例：

     python

     ```
     bisect.bisect_left([1, 3, 5, 7], 3)  # 返回1
     bisect.bisect_left([1, 3, 3, 5], 3)  # 返回1 (第一个3的位置)
     ```

2. **bisect_right(a, x, lo=0, hi=len(a), *, key=None)**
   或 **bisect(a, x, lo=0, hi=len(a), *, key=None)**

   - 返回插入位置，使得插入后所有相等的元素位于插入值的**右侧**

   - 如果元素已存在，返回最后一个相等元素之后的位置

   - 示例：

     python

     ```
     bisect.bisect_right([1, 3, 5, 7], 3)  # 返回2
     bisect.bisect_right([1, 3, 3, 5], 3)  # 返回3 (最后一个3之后的位置)
     ```

#### 插入函数

1. **insort_left(a, x, lo=0, hi=len(a), *, key=None)**

   - 使用`bisect_left`找到位置并实际插入元素

   - 示例：

     python

     ```
     lst = [1, 3, 5]
     bisect.insort_left(lst, 2)
     # lst变为[1, 2, 3, 5]
     ```

2. **insort_right(a, x, lo=0, hi=len(a), *, key=None)**
   或 **insort(a, x, lo=0, hi=len(a), *, key=None)**

   - 使用`bisect_right`找到位置并实际插入元素

   - 示例：

     python

     ```
     lst = [1, 3, 3, 5]
     bisect.insort_right(lst, 3)
     # lst变为[1, 3, 3, 3, 5]
     ```

### 参数详解

所有函数都支持以下参数：

- `a`: 有序列表/序列
- `x`: 要查找/插入的值
- `lo` (可选): 查找范围的起始索引 (默认为0)
- `hi` (可选): 查找范围的结束索引 (默认为列表长度)
- `key` (Python 3.10+): 键函数，用于从列表元素中提取比较键

### 关键特性

1. **要求输入序列必须是有序的** - 使用前必须确保列表已排序，否则结果不可预测
2. **时间复杂度为O(log n)** - 比线性查找O(n)高效得多
3. **不检查列表是否有序** - 由调用者保证输入的有序性
4. **支持自定义比较** (Python 3.10+) - 通过`key`参数

### 实际应用示例

#### 1. 维护有序列表

python

```
import bisect

scores = []
for score in [85, 72, 63, 95, 88]:
    bisect.insort(scores, score)
    
print(scores)  # 输出: [63, 72, 85, 88, 95]
```

#### 2. 成绩分段查询

python

```
breakpoints = [60, 70, 80, 90]
grades = ['F', 'D', 'C', 'B', 'A']

def grade(score):
    index = bisect.bisect_right(breakpoints, score)
    return grades[index]

print(grade(85))  # 输出 'B'
print(grade(92))  # 输出 'A'
```

#### 3. 处理自定义对象 (Python 3.10+)

```
from dataclasses import dataclass

@dataclass
class Student:
    name: str
    grade: int

students = [Student('Alice', 85), Student('Bob', 72), Student('Charlie', 90)]
students.sort(key=lambda s: s.grade)  # 按成绩排序

# 插入新学生并保持有序
new_student = Student('David', 88)
bisect.insort(students, new_student, key=lambda s: s.grade)

for s in students:
    print(f"{s.name}: {s.grade}")
```



## Python 结构体

在 Python 中，没有像 C/C++ 那样的显式结构体（`struct`）类型，但有多种方式可以实现类似结构体的功能。以下是几种常见的方法：

---

### 1. **使用 `class` 定义类**
通过定义一个类（可以简单只包含数据字段）来模拟结构体：
```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

# 使用
p = Person("Alice", 30)
print(p.name, p.age)  # 输出: Alice 30
```

---

### 2. **使用 `collections.namedtuple`**
`namedtuple` 是标准库中的工厂函数，可以快速创建一个轻量级的不可变结构体：
```python
from collections import namedtuple

# 定义结构体类型
Person = namedtuple("Person", ["name", "age"])

# 使用
p = Person("Bob", 25)
print(p.name, p.age)  # 输出: Bob 25
```
- **优点**：内存高效，支持元组操作（如拆包）。
- **缺点**：不可变（字段不能修改）。

---

### 3. **使用 `dataclasses`（Python 3.7+）**
`dataclass` 装饰器可以自动生成 `__init__` 等方法，适合可变结构体：
```python
from dataclasses import dataclass

@dataclass
class Person:
    name: str
    age: int

# 使用
p = Person("Charlie", 40)
p.age = 35  # 可修改
print(p)    # 输出: Person(name='Charlie', age=35)
```
- **优点**：代码简洁，支持默认值和类型注解。

---

### 4. **使用 `types.SimpleNamespace`**
适用于动态添加属性的简单对象：
```python
from types import SimpleNamespace

p = SimpleNamespace(name="David", age=20)
p.email = "david@example.com"  # 动态添加字段
print(p.name, p.age)  # 输出: David 20
```

---

### 5. **使用字典（最简方式）**
如果不需要类型安全或方法，直接使用字典：
```python
person = {"name": "Eve", "age": 22}
print(person["name"])  # 输出: Eve
```

---

### 选择建议：
- 需要**不可变数据** → `namedtuple`
- 需要**可变数据+类型注解** → `dataclass`
- 需要**动态字段** → `SimpleNamespace` 或字典
- 需要**自定义方法** → 普通 `class`

根据需求选择最适合的方式即可！



## MOD = 1_000_000_007

1. **语法支持**：Python 允许在整数和浮点数中使用 `_` 作为视觉分隔符，编译器会忽略这些下划线。
   - `1_000_000_007` 和 `1000000007` 是完全等价的。
   - 类似地，`0xFFFF_FFFF`、`1_234.567_89` 也是合法的。
2. **提高可读性**：
   - 大数（如 `1000000007`）容易数错位数，而 `1_000_000_007` 更清晰。
   - 在代码中，`1_000_000_007` 比 `1000000007` 更容易一眼看出是 **10 亿 + 7**（一个常用的模数，特别是在算法竞赛中）。
3. **不影响数值**：
   - Python 解释器在解析时会自动去除 `_`，因此 `1_000_000_007` 在内存中仍然是整数 `1000000007`。

### 示例
```python
MOD = 1_000_000_007
print(MOD)  # 输出 1000000007
print(MOD == 1000000007)  # 输出 True
```



## 无限大和无限小

```
-math.inf, math.inf
INT_MIN, INT_MAX = -2**31, 2**31 - 1
```

## 最大值

```
MAX = sys.maxsize
ans = [MAX for i in range(amount + 1)]
```



## 如果要用两个值排序怎么办？

如果排序键需要多个属性，可以返回一个元组：

```
# 先按 x.start 排序，如果相同再按 x.end 排序
lsave.sort(key=lambda x: (x.start, x.end))
```

Python 会按元组的字典序比较（先比较第一个元素，再比较第二个）。

```
import functools

def cmp(a, b):
    return a.start - b.start

arr = [...]
arr.sort(key = functools.cmp_to_key(cmp))

```

## list reverse

```
res.reverse()
```

## heapq 排序入堆

```
heapq.heappush(h, (-d, -p.x, -p.y))
```

### 612 · K Closest Points

```
import heapq
class Solution:
    """
    @param points: a list of points
    @param origin: a point
    @param k: An integer
    @return: the k closest points
    """
    def dist(self, a, b):
        return math.pow(a.x - b.x, 2) + math.pow(a.y - b.y, 2)
    def k_closest(self, points: List[Point], origin: Point, k: int) -> List[Point]:
        h = []
        for p in points:
            d = self.dist(p, origin)
            heapq.heappush(h, (-d, -p.x, -p.y))
            if len(h) > k:
                heapq.heappop(h)
        res =[]
        while len(h) > 0:
            _, x, y = heapq.heappop(h)
            res.append(Point(-x, -y))
        res.reverse()
        return res
```

## Str.find()

```
s = "hello world, hello python"

index = s.find("hello")
print(index)  # 输出：0

index = s.find("hello", 6)
print(index)  # 输出：13（第二个 "hello" 的起始位置）

```

## list.index()

```
lst = [10, 20, 30, 20, 40]

# 查找元素 20 的第一个位置
print(lst.index(20))  # 输出：1

# 从索引 2 开始查找 20
print(lst.index(20, 2))  # 输出：3（找到索引 3 的 20）
```



## 删除字符串字符

```
new_str = s[:i] + s[i+1:]  //拼接
```

```
# 转换为列表
lst = list(s)
# 删除索引 i 的元素
del lst[i]  # 或 lst.pop(i)
# 转回字符串
new_str = ''.join(lst)
```

## ord() and chr()

```
print(ord('A'))   # 输出：65（大写字母 A 的 ASCII 码）
print(ord('a'))   # 输出：97（小写字母 a 的 ASCII 码）
print(ord('0'))   # 输出：48（数字 0 的 ASCII 码）
print(ord(' '))   # 输出：32（空格的 ASCII 码）
print(ord('!'))   # 输出：33（感叹号的 ASCII 码）
```

```
c = 'B'
code = ord(c)
print(code)          # 输出：66
print(chr(code))     # 输出：'B'（将编码转回字符）
```

## 去重

```
nums: List[int]
n = set(nums)
```

## Stack

```
# 初始化栈（空列表）
stack = []

# 入栈
stack.append(1)
stack.append(2)
stack.append(3)
print(stack)  # 输出：[1, 2, 3]（栈顶是 3）

# 取栈顶
print(stack[-1])  # 输出：3

# 出栈
top = stack.pop()
print(top)       # 输出：3
print(stack)     # 输出：[1, 2]（栈顶变为 2）

# 判断空栈
print(not stack)  # 输出：False（栈非空）

# 栈大小
print(len(stack))  # 输出：2
```

```
from collections import deque

# 初始化栈
stack = deque()

# 入栈
stack.append(1)
stack.append(2)
stack.append(3)
print(list(stack))  # 输出：[1, 2, 3]

# 取栈顶
print(stack[-1])  # 输出：3

# 出栈
top = stack.pop()
print(top)        # 输出：3
print(list(stack))  # 输出：[1, 2]
```

## list中如何返回最大值的索引

```
lst = [3, 1, 4, 1, 5, 9, 2, 6]

# 步骤1：找到最大值
max_value = max(lst)  # 结果：9

# 步骤2：找到最大值的索引
max_index = lst.index(max_value)  # 结果：5（9 首次出现在索引 5）

print(max_index)  # 输出：5
```

## for s,f in enumerate(p):



## any

`any()` 是一个内置函数，用于**判断可迭代对象（如列表、元组、集合等）中是否至少有一个元素为 “真”（True）**。它的核心作用是快速检查 “是否存在满足条件的元素”。

```
# 示例：检查列表中是否有偶数
numbers = [1, 3, 5, 7, 8]
has_even = any(x % 2 == 0 for x in numbers)
print(has_even)  # 输出 True（8 是偶数）

# 示例：检查字符串列表中是否有非空字符串
strings = ["", "", "hello", ""]
has_non_empty = any(s for s in strings)  # 等价于 any(len(s) > 0 for s in strings)
print(has_non_empty)  # 输出 True（"hello" 是非空）
```

# 自定义比较函数（comparecompare function）

在 Python 中，对列表进行排序时，可以通过 `key` 参数指定排序依据，也可以通过 `functools.cmp_to_key` 将**自定义比较函数（comparecompare function）** 转换为排序键，从而实现自定义排序逻辑。

`compare` 函数的作用是**定义两个元素的比较规则**，返回值决定了元素的排序顺序：

- 返回负数：第一个元素排在第二个元素前面；
- 返回正数：两个元素位置不变；
- 返回正数：第二个元素排在第一个元素前面。

示例 1：用 compare 函数自定义排序（数字按绝对值大小排序）

```python
from functools import cmp_to_key

# 定义 compare 函数：按绝对值从小到大排序
def compare(a, b):
    if abs(a) < abs(b):
        return -1  # a 排在 b 前面
    elif abs(a) > abs(b):
        return 1   # b 排在 a 前面
    else:
        return 0   # 位置不变

# 待排序的列表
nums = [3, -1, 2, -4]

# 使用 cmp_to_key 转换 compare 函数为 key，传入 sort 方法
nums.sort(key=cmp_to_key(compare))

print(nums)  # 输出：[-1, 2, 3, -4]（绝对值 1 < 2 < 3 < 4）
```

示例 2：对字符串按长度排序（长的在后）

```python
from functools import cmp_to_key

# 定义 compare 函数：按字符串长度从小到大排序
def compare_str(a, b):
    if len(a) < len(b):
        return -1  # a 在前
    elif len(a) > len(b):
        return 1   # b 在前
    else:
        return 0   # 长度相同则位置不变

words = ["apple", "cat", "banana", "dog"]

# 应用排序
words.sort(key=cmp_to_key(compare_str))

print(words)  # 输出：['cat', 'dog', 'apple', 'banana']（长度 3 < 3 < 5 < 6）
```

1. **`cmp_to_key` 的作用**：Python 3 的 `sort` 方法默认只接受 `key` 参数，`cmp_to_key` 是 `functools` 模块提供的工具，用于将传统的 compare 函数转换为 `sort` 可识别的 key。
2. **与 `key` 参数的区别**：
    - `key=函数`：为每个元素生成一个 “排序键”（如 `key=len` 用长度作为键），排序依据键的大小；
    - `compare` 函数：直接定义两个元素的比较逻辑，更灵活（适合复杂的多条件排序）。
3. **适用场景**：当排序规则复杂（如多条件组合、自定义比较逻辑）时，用 compare 函数更直观。

示例 3：多条件排序（先按奇偶，再按大小）

```python
from functools import cmp_to_key

# 规则：奇数排在偶数前面；同是奇数/偶数则按从小到大排序
def compare_num(a, b):
    # 先判断奇偶
    if a % 2 == 1 and b % 2 == 0:
        return -1  # 奇数 a 在前，偶数 b 在后
    if a % 2 == 0 and b % 2 == 1:
        return 1   # 偶数 a 在后，奇数 b 在前
    # 同是奇数或偶数，按大小排序
    return a - b  # 正数表示 a > b，负数表示 a < b

nums = [3, 1, 4, 2, 5, 6]
nums.sort(key=cmp_to_key(compare_num))

print(nums)  # 输出：[1, 3, 5, 2, 4, 6]（奇数在前且递增，偶数在后且递增）
```

### 总结：

- `compare` 函数通过 `cmp_to_key` 转换后可用于 `sort` 方法，自定义排序逻辑；
- 返回值的正负决定了两个元素的相对位置；
- 适合处理复杂排序场景，比 `key` 参数更灵活但性能略低（一般可忽略）。



要理解这段代码的核心，需先搞懂 **`cmp_to_key` 的作用** + **自定义比较函数的规则** —— 这是 Python 中「自定义复杂排序逻辑」的经典方式，和之前的 `key` 函数排序是完全不同的思路（前者是「值比较」，后者是「两两比较」）。

### 一、核心背景：Python 排序的两种模式

Python 列表排序有两种核心方式，对应不同的场景：

| 排序方式               | 原理                                                         | 适用场景                                 |
| ---------------------- | ------------------------------------------------------------ | ---------------------------------------- |
| `key=函数`（之前讲的） | 为每个元素生成「排序键值」，按键值大小排序（单值 / 元组逐维比较） | 简单维度排序（如多字段升 / 降序）        |
| `cmp_to_key(比较函数)` | 自定义「两两元素的比较规则」，直接告诉程序 “a 应该在 b 前面 / 后面 / 相等” | 复杂逻辑排序（如奇偶分组、自定义优先级） |

### 二、`cmp_to_key` + 自定义比较函数的核心原理

#### 1. 自定义比较函数的规则（`compare_num`）

自定义比较函数 `compare(a, b)` 必须遵循固定返回值规则：

| 返回值        | 含义                                          |
| ------------- | --------------------------------------------- |
| 负数（如 -1） | `a` 应该排在 `b` 前面（`a < b` 按自定义规则） |
| 0             | `a` 和 `b` 相等，顺序任意                     |
| 正数（如 1）  | `a` 应该排在 `b` 后面（`a > b` 按自定义规则） |

你的 `compare_num` 函数拆解：

```python
def compare_num(a, b):
    # 规则1：奇数在前，偶数在后
    if a % 2 == 1 and b % 2 == 0:
        return -1  # a是奇数，b是偶数 → a排前面
    if a % 2 == 0 and b % 2 == 1:
        return 1   # a是偶数，b是奇数 → a排后面
    # 规则2：同奇偶时，按数值从小到大排
    return a - b  # a < b → 返回负数（a在前）；a > b → 返回正数（a在后）；相等返回0
```

#### 2. `cmp_to_key` 的作用

Python 3 中 `list.sort()` 已经移除了直接的 `cmp` 参数（Python 2 有），`functools.cmp_to_key` 是一个「适配器」：

- 接收一个「自定义比较函数」（如 `compare_num`）；
- 转换为 `sort` 能识别的 `key` 参数格式；
- 底层让排序算法（Timsort）按照自定义的「两两比较规则」排序，而非默认的键值比较。

#### 3. 排序的完整执行流程（以 `nums = [3,1,4,2,5,6]` 为例）

排序算法会遍历列表中的元素，**两两调用 `compare_num(a, b)`**，根据返回值调整顺序：

1. 比较 3（奇）和 1（奇）→ 返回 3-1=2（正数）→ 1 排 3 前面；
2. 比较 3（奇）和 4（偶）→ 返回 -1 → 3 排 4 前面；
3. 比较 4（偶）和 2（偶）→ 返回 4-2=2（正数）→ 2 排 4 前面；
4. 比较 5（奇）和 6（偶）→ 返回 -1 → 5 排 6 前面；
5. 最终所有奇数按升序排前，偶数按升序排后 → 结果：`[1, 3, 5, 2, 4, 6]`。

### 三、示例验证 & 结果分析

运行代码：

```python
from functools import cmp_to_key

def compare_num(a, b):
    if a % 2 == 1 and b % 2 == 0:
        return -1
    if a % 2 == 0 and b % 2 == 1:
        return 1
    return a - b

nums = [3, 1, 4, 2, 5, 6]
nums.sort(key=cmp_to_key(compare_num))
print(nums)  # 输出：[1, 3, 5, 2, 4, 6]
```

结果符合预期：

- 奇数组 `[1,3,5]`（升序）排在前；
- 偶数组 `[2,4,6]`（升序）排在后。

### 四、关键对比：`cmp_to_key` vs 普通 `key` 函数

如果用之前的 `key` 函数实现相同逻辑，代码会是：

```python
nums = [3, 1, 4, 2, 5, 6]
# 键值规则：奇数的键是 (0, a)，偶数的键是 (1, a) → 先按第一个值升序（0在前），再按a升序
nums.sort(key=lambda x: (0 if x%2==1 else 1, x))
print(nums)  # 同样输出 [1,3,5,2,4,6]
```

两种方式的对比：

| 方式            | 优点                         | 缺点                          | 适用场景                   |
| --------------- | ---------------------------- | ----------------------------- | -------------------------- |
| `cmp_to_key`    | 逻辑直观（直接定义两两比较） | 性能略低（Python 层两两调用） | 极复杂排序（如多条件嵌套） |
| 普通 `key` 函数 | 性能高（C 层键值比较）       | 需将规则转化为「键值维度」    | 可拆解为维度的排序         |

### 五、核心总结

1. **自定义比较函数规则**：返回负数（a 在前）、0（相等）、正数（a 在后）；
2. **`cmp_to_key` 作用**：将自定义比较函数适配为 `sort` 的 `key` 参数，让排序算法按自定义规则执行；
3. **这段代码的本质**：通过「两两比较」实现 “奇数在前、偶数在后，同奇偶按升序” 的复杂排序，是 Python 处理非标准排序规则的核心方法。

简单记：`key` 函数是「给每个元素贴标签，按标签排序」；`cmp_to_key` 是「直接告诉程序两个元素该怎么排」，后者更灵活但性能稍弱。













# python的排序中的lamda是什么语法和原理“envelopes.sort(key=lambda x: (x[0], -x[1]))”

要理解 `envelopes.sort(key=lambda x: (x[0], -x[1]))` 中 `lambda` 的语法和原理，我们可以拆成「lambda 本身的本质」「sort 的 key 参数规则」「这个排序逻辑的具体含义」三层来讲，最后结合示例落地。

### 一、lambda 是什么？（语法 + 原理）

`lambda` 是 Python 中定义「匿名函数」的语法糖，核心是**简化简单函数的定义**，无需用 `def` 显式命名。

#### 1. 基础语法



```python
lambda 参数列表: 表达式
```

- **参数列表**：和普通函数一样（可以是单个、多个、默认值等）；
- **表达式**：函数的返回值（只能是单个表达式，不能有循环、if 块等复杂逻辑）；
- **本质**：lambda 定义的匿名函数，和 `def` 定义的函数是同一类对象（都是 `function` 类型），只是没有显式名称。

#### 2. 对比普通函数

比如 `lambda x: (x[0], -x[1])` 等价于：

```python
def func(x):
    return (x[0], -x[1])
```

用 lambda 的核心场景：**临时使用、逻辑简单**（比如作为排序的 key、map/filter 的参数），无需单独定义命名函数。

#### 3. 关键特性

- 匿名性：定义后没有固定名称，通常直接作为参数传递（如传给 `sort` 的 `key`）；
- 轻量性：只能写单个表达式，返回值就是表达式的结果；
- 作用域：和普通函数一样，能访问外层作用域的变量（但不建议复杂使用）。

### 二、sort 的 key 参数：排序的核心规则

`list.sort(key=...)` 是 Python 列表的原地排序方法，`key` 参数的作用是：

> 为列表中的**每个元素**计算一个「排序键值」，排序时会根据这个「键值」而非元素本身比较大小。

#### 核心原理：

1. 遍历列表中的每个元素 `x`，用 `key` 指定的函数（这里是 lambda）计算 `x` 的「键值」；
2. 排序算法（Timsort）基于这些「键值」对原元素进行排序；
3. 最终列表的顺序由「键值的大小关系」决定，原元素本身不会被修改。

#### 补充：sort 的默认规则

- 比较多个维度时（比如键值是元组 `(a, b)`），Python 会「按元组的元素依次比较」：
    - 先比第一个元素 `a`，若相等再比第二个元素 `b`；
    - 若还是相等，继续比后续元素（如果有）。
- 数值比较遵循「升序」（小→大），字符串遵循 ASCII/Unicode 码序。

### 三、`lambda x: (x[0], -x[1])` 的排序逻辑

结合 `sort(key=...)` 的规则，我们拆解 `envelopes.sort(key=lambda x: (x[0], -x[1]))`：

#### 1. 前提假设

`envelopes` 是一个二维列表（比如表示信封的宽和高），每个元素 `x` 是一个二元组 / 列表，例如：

```python
envelopes = [[5, 4], [6, 4], [6, 7], [2, 3]]
```

#### 2. lambda 的计算逻辑

对每个元素 `x`（比如 `[5,4]`），lambda 计算的键值是：

- 第一个维度：`x[0]` → 取元素的第一个值（宽）；

- 第二个维度：`-x[1]` → 取元素的第二个值（高）的相反数；

    比如：

- `x=[5,4]` → 键值 `(5, -4)`；

- `x=[6,7]` → 键值 `(6, -7)`；

- `x=[6,4]` → 键值 `(6, -4)`。

#### 3. 排序的最终效果

排序时按「键值元组」依次比较：

1. **先按 `x[0]` 升序排列**（宽从小到大）；
2. **若 `x[0]` 相等，按 `x[1]` 降序排列**（因为键值是 `-x[1]`，升序排列 `-x[1]` 等价于降序排列 `x[1]`）。

#### 示例验证

```python
envelopes = [[5, 4], [6, 4], [6, 7], [2, 3]]
envelopes.sort(key=lambda x: (x[0], -x[1]))
print(envelopes)
# 输出：[[2, 3], [5, 4], [6, 7], [6, 4]]
```

解释排序过程：

- 第一步：按 `x[0]` 升序，先排 `2`（[2,3]），再 `5`（[5,4]），最后 `6`（[6,4]、[6,7]）；

- 第二步：`x[0]=6` 的两个元素，键值分别是 `(6,-7)` 和 `(6,-4)`；

    比较

    ```
    -7
    ```

    和

    ```
    -4
    ```

    ：

    ```
    -7 < -4
    ```

    ，所以

    ```
    [6,7]
    ```

    排在

    ```
    [6,4]
    ```

    前面（因为

    ```
    -7
    ```

    更小，对应原

    ```
    x[1]=7
    ```

    更大，实现降序）。

### 四、常见扩展与注意点

#### 1. 为什么要 `-x[1]`？

比如信封问题中，宽相同的信封，高降序排列后，能方便后续找「最长递增子序列」（高的在前，避免重复选宽相同的信封）。

#### 2. lambda 支持更复杂的多维度排序

比如想先按 `x[0]` 降序、再按 `x[1]` 升序：

```python
envelopes.sort(key=lambda x: (-x[0], x[1]))
```

#### 3. 替代 lambda：用 operator 模块（性能略优）

如果追求极致性能（比如超大数据量），可以用 `operator.itemgetter` 替代 lambda（底层是 C 实现，比 Python 级的 lambda 快）：

```python
from operator import itemgetter
# 等价于 lambda x: (x[0], -x[1])，但需要额外处理 -x[1]
envelopes.sort(key=lambda x: (itemgetter(0)(x), -itemgetter(1)(x)))
```

#### 4. 易错点：lambda 的参数和元素结构匹配

如果 `envelopes` 的元素不是二元组（比如是字典 `{"w":5, "h":4}`），lambda 要对应调整：

```python
envelopes.sort(key=lambda x: (x["w"], -x["h"]))
```

### 核心总结

1. **lambda 本质**：匿名函数，简化简单逻辑的函数定义，`lambda x: (x[0], -x[1])` 就是「接收 x，返回 (x [0], -x [1])」的函数；
2. **sort 的 key 原理**：为每个元素计算「排序键值」，排序时比较键值而非元素本身；
3. **这个排序逻辑**：先按元素第一个值升序，第一个值相等时按第二个值降序，是多维度排序的典型写法（比如信封、区间排序场景）。

如果没有lamda直接排序这个键值对 (5, -4) (6, -7) (6, -4) 就是先从小到大排序，再从大到小排序吗。如果envelopes.sort(key=lambda x: x[0]) 是如何排序的

要理清这两个问题，核心是抓住 **Python 排序的「键值比较规则」** —— 排序只看 `key` 函数返回的「排序键」，键的类型（单个值 / 元组）直接决定排序维度，我们逐一拆解：

### 一、先回答：无 lambda 时，元组 (5,-4) (6,-7) (6,-4) 本身的排序规则

首先明确：**lambda 只是「生成排序键」的工具，不是排序规则的核心**。即使没有 lambda，直接对这些元组排序，规则也是 Python 内置的「元组逐维度比较」：

#### 元组比较的核心规则

Python 比较两个元组时，会**从左到右依次比较对应位置的元素**：

- 若第 1 个元素不等：直接按第 1 个元素的大小定顺序；
- 若第 1 个元素相等：继续比较第 2 个元素，以此类推；
- 数值比较默认是「升序」（小 → 大）。

#### 对 (5,-4)、(6,-7)、(6,-4) 直接排序的结果

```python
tuples = [(5, -4), (6, -7), (6, -4)]
tuples.sort()  # 无 key，直接比较元组本身
print(tuples)  # 输出：[(5, -4), (6, -7), (6, -4)]
```

排序逻辑：

1. 先比第 1 维：5 <6 → (5,-4) 排最前；
2. 剩下两个元组第 1 维都是 6，比第 2 维：-7 <-4 → (6,-7) 排在 (6,-4) 前面。

#### 对应到信封排序的效果

原信封列表 `[[5,4], [6,7], [6,4]]`，通过 `lambda x: (x[0], -x[1])` 生成的键是 (5,-4)、(6,-7)、(6,-4)，排序后键的顺序是 [(5,-4), (6,-7), (6,-4)]，对应原信封的顺序就是：`[[5,4], [6,7], [6,4]]` → 即「先按 x [0] 升序，x [0] 相等时按 x [1] 降序」（因为 -x [1] 升序等价于 x [1] 降序）。

### 二、`envelopes.sort(key=lambda x: x[0])` 的排序逻辑

当 `key` 函数只返回 `x[0]`（即信封的第一个值，比如宽度），排序规则变为：

#### 核心规则

只按「每个元素的第一个值」升序排序，**完全忽略第二个值**；若第一个值相等，保持原列表中的相对顺序（Python 排序是「稳定排序」）。

#### 示例验证

假设原始信封列表：

```python
envelopes = [[6, 4], [5, 4], [6, 7], [2, 3]]
# 仅按 x[0] 排序
envelopes.sort(key=lambda x: x[0])
print(envelopes)  # 输出：[[2, 3], [5, 4], [6, 4], [6, 7]]
```

排序逻辑：

1. 提取每个元素的 x [0]：6、5、6、2 → 按升序排列为 2、5、6、6；
2. 按 x [0] 匹配原元素：
    - x [0]=2 → [2,3]（排第 1）；
    - x [0]=5 → [5,4]（排第 2）；
    - x [0]=6 的元素有 [6,4] 和 [6,7] → 保持它们在原列表中的相对顺序（[6,4] 在前，[6,7] 在后）。

#### 关键对比：和 `key=lambda x: (x[0], -x[1])` 的差异

| 排序写法                      | 排序维度           | x [0] 相等时的规则           | 示例结果（同原始列表）       |
| ----------------------------- | ------------------ | ---------------------------- | ---------------------------- |
| `key=lambda x: x[0]`          | 仅 x [0] 一维      | 保持原列表相对顺序           | [[2,3], [5,4], [6,4], [6,7]] |
| `key=lambda x: (x[0], -x[1])` | x [0] + x [1] 二维 | 按 x [1] 降序（-x [1] 升序） | [[2,3], [5,4], [6,7], [6,4]] |

### 三、核心总结

1. 元组本身的排序：无 lambda 时，(5,-4) (6,-7) (6,-4) 按「逐维度升序」排 → [(5,-4), (6,-7), (6,-4)]，对应信封就是「x [0] 升序，x [1] 降序」；
2. `key=lambda x: x[0]` 的排序：
    - 只按每个元素的第一个值升序；
    - 第一个值相等时，不处理第二个值，保持元素在原列表中的相对顺序（稳定排序）；
    - 对比二维排序，少了「第二个值降序」的约束，这也是信封问题中需要加 `-x[1]` 的原因（避免宽相同的信封按高升序排列，影响后续最长递增子序列的计算）。

简单记：`key` 返回什么，就按什么排 —— 返回单个值则一维排序，返回元组则逐维度升序排序；想要某一维降序，就对该维度取反（如 `-x[1]`）。





# **负数索引**

在Python中，负数索引表示**从右向左计数**：

python

```
s = "Python"
# 正索引：0  1  2  3  4  5
#         P  y  t  h  o  n
# 负索引：-6 -5 -4 -3 -2 -1

print(s[-1])   # "n"   最后一个字符
print(s[-2])   # "o"   倒数第二个字符
print(s[-3])   # "h"   倒数第三个字符
```



### **`res[-m:]` 具体含义**

python

```
res = "HelloWorld"
m = 3

# res[-3:] 表示：从倒数第3个字符开始，到字符串结束
print(res[-3:])  # "rld"
```

```
s = "PythonProgramming"

# 各种切片方式
print(s[:])           # "PythonProgramming" - 完整复制
print(s[3:])          # "honProgramming" - 从索引3开始
print(s[-5:])         # "mming" - 最后5个字符
print(s[:-3])         # "PythonProgram" - 除了最后3个字符
print(s[-8:-3])       # "ogram" - 从倒数第8到倒数第3（不含）
print(s[2:-4])        # "thonProgra" - 组合使用
```



# Python 装饰器（@）详解

你看到的 `@app.route()` 就是 Python 的**装饰器（Decorator）**，这是 Python 的一个非常强大和优雅的特性。让我详细解释它的原理和用法。

## **装饰器的本质**

**装饰器本质上是一个函数**，它接收一个函数作为参数，并返回一个新的函数。`@` 符号是 Python 的**语法糖**，让装饰器的使用更加简洁。

### **基本语法对比**

python

```
# 1. 原始装饰器写法（理解原理）
def my_decorator(func):
    def wrapper():
        print("Before function call")
        func()
        print("After function call")
    return wrapper

def say_hello():
    print("Hello!")

# 手动装饰
say_hello = my_decorator(say_hello)
say_hello()

# 2. 使用 @ 语法糖（简洁写法）
@my_decorator
def say_hello():
    print("Hello!")

# 直接调用
say_hello()
```
