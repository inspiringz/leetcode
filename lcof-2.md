# 剑指 Offer（第 2 版）

## 03. 数组中重复的数字

找出数组中重复的数字。

在一个长度为 n 的数组 nums 里的所有数字都在 0～n-1 的范围内。数组中某些数字是重复的，但不知道有几个数字重复了，也不知道每个数字重复了几次。请找出数组中任意一个重复的数字。

示例 1：

```
输入：
[2, 3, 1, 0, 2, 5, 3]
输出：2 或 3 
```

限制：2 <= n <= 100000

- Code 

```python
# sort
class Solution:
    def findRepeatNumber(self, nums: List[int]) -> int:
        nums.sort()
        for i in range(len(nums)):
            if nums[i] == nums[i + 1]:
                return nums[i]
# set
class Solution:
    def findRepeatNumber(self, nums: List[int]) -> int:
        tmp = set()
        for i in nums:
            if i in tmp:
                return i
            tmp.add(i)
```

### 04. 二维数组中的查找

在一个 n * m 的二维数组中，每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

示例:

现有矩阵 matrix 如下：

```
[
  [1,   4,  7, 11, 15],
  [2,   5,  8, 12, 19],
  [3,   6,  9, 16, 22],
  [10, 13, 14, 17, 24],
  [18, 21, 23, 26, 30]
]
```

给定 target = 5，返回 true。

给定 target = 20，返回 false。

限制：

0 <= n <= 1000, 0 <= m <= 1000

- Code:

```python
# loop if target in List
class Solution:
    def findNumberIn2DArray(self, matrix: List[List[int]], target: int) -> bool:
        for _l in matrix:
            if target in _l:
                return True
        return False
# search from Up right corner
class Solution:
    def findNumberIn2DArray(self, matrix: List[List[int]], target: int) -> bool:
        row, col = 0, len(matrix) and len(matrix[0])-1 # and 全真时解析所有运算数，返回最后一个变量
        while row < len(matrix) and col >= 0:
            if matrix[row][col] == target:
                return True
            if matrix[row][col] > target:
                col -= 1
            else:
                row += 1
        return False
```

## 06. 从尾到头打印链表

输入一个链表的头节点，从尾到头反过来返回每个节点的值（用数组返回）。

 
示例 1：

```
输入：head = [1,3,2]
输出：[2,3,1]
```

限制： 0 <= 链表长度 <= 10000

- Code

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def reversePrint(self, head: ListNode) -> List[int]:
        result = []
        while head:
            result.insert(0, head.val) # result.append(head.val)
            head = head.next
        return result # result[::-1]
```

## 09. 用两个栈来实现队列

用两个栈实现一个队列。队列的声明如下，请实现它的两个函数 appendTail 和 deleteHead ，分别完成在队列尾部插入整数和在队列头部删除整数的功能。(若队列中没有元素，deleteHead 操作返回 -1 )

```
示例 1：
输入：
["CQueue","appendTail","deleteHead","deleteHead"]
[[],[3],[],[]]
输出：[null,null,3,-1]

示例 2：

输入：
["CQueue","deleteHead","appendTail","appendTail","deleteHead","deleteHead"]
[[],[],[5],[2],[],[]]
输出：[null,-1,null,null,5,2]
```

提示：

1 <= values <= 10000，最多会对 appendTail、deleteHead 进行 10000 次调用。

- Code

栈 A 用来 appendTail，倒序 pop 给栈 B，栈 B 用于 deleteHead。

```py
class CQueue:

    def __init__(self):
        self.A, self.B = [], []
        

    def appendTail(self, value: int) -> None:
        self.A.append(value)
        return None

    def deleteHead(self) -> int:
        if self.B: return self.B.pop()
        if not self.A: return -1
        while self.A:
            self.B.append(self.A.pop())
        return self.B.pop()
            

# Your CQueue object will be instantiated and called as such:
# obj = CQueue()
# obj.appendTail(value)
# param_2 = obj.deleteHead()
```

## 10- I. 斐波那契数列

写一个函数，输入 n ，求斐波那契（Fibonacci）数列的第 n 项。斐波那契数列的定义如下：

F(0) = 0,   F(1) = 1
F(N) = F(N - 1) + F(N - 2), 其中 N > 1.

斐波那契数列由 0 和 1 开始，之后的斐波那契数就是由之前的两数相加而得出。答案需要取模 1e9+7（1000000007），如计算初始结果为：1000000008，请返回 1。

示例 1：

```
输入：n = 2
输出：1
```

示例 2：

```
输入：n = 5
输出：5
```

提示：0 <= n <= 100

- Code

> https://zhuanlan.zhihu.com/p/27643991

```py
from functools import lru_cache
class Solution:
    @lru_cache(maxsize=None, typed=False)
    def fib(self, n: int) -> int:
        if n < 2: return n
        return (self.fib(n-1) + self.fib(n-2)) % 1000000007
```



