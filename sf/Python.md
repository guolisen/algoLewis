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
UserString	包装 string 对象以便于 string 的子类化
————————————————
原文链接：https://blog.csdn.net/qq_39478403/article/details/105746952



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

