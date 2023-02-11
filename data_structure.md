---
title: 数据结构
date: 2023-02-11 14:00:00
updated: 2022-02-11 22:00:00
tag:
- leetcode
- data structure
---

## 本章重点

数据结构是一类需要按照题目要求设计`class`并实现其中诸多基础方法的题目，大多对方法的复杂度有明确要求，这一类题目之前没有遇到过，但在剑指系列中属于入门难度，学习一个。

## 本章题目思路记忆要点

1. LRU（latest recently used）【题目146】：一种缓存机制，在缓存不够但需要加新内容的时候，最不常访问的项目被删除。**数据结构：哈希链表，其数据结构包含：hashmap(key: key, value: a link node) + 双向链表。**

    <img src="https://raw.githubusercontent.com/KMdsy/figurebed/master/img/image-20230211161354904.png" alt="image-20230211161354904" style="zoom: 33%;" />

2. LFU（latest frequently used）：一种缓存机制，在缓存不够但需要加新内容的时候，删除使用频次最低的项目，当有多个频次相同的项目时，删除最旧的。数据结构：两个hashmap：key2freq（O(1)得到key的freq）、key2value（get的时候提供`O(1)`复杂度），freq2keys：hashmap+双向链表（如上图）（遇到freq相同的情况下，删除最不常使用的，这里需要一个有序的链表，以常用程度来排列节点）

3. [两个栈实现队列](#剑指 Offer 09. 用两个栈实现队列)：定义出栈、入栈分别负责出入，当出栈无元素时，逆序pop入栈元素到出栈，实现先进先出

4. [包含min函数的栈](#剑指 Offer 30. 包含min函数的栈)：借助辅助栈，在元素入栈的时候，在辅助栈中存储当前最小值

    <img src="https://raw.githubusercontent.com/KMdsy/figurebed/master/img/image-20230211190537593.png" alt="image-20230211190537593" style="zoom: 33%;" />

    

## 题目

### 剑指 Offer 09. 用两个栈实现队列

> 用两个栈实现一个队列。队列的声明如下，请实现它的两个函数 `appendTail` 和 `deleteHead` ，分别完成在队列尾部插入整数和在队列头部删除整数的功能。(若队列中没有元素，`deleteHead` 操作返回 -1 ) 
>
> **示例 1：**
>
> ```
> 输入：
> ["CQueue","appendTail","deleteHead","deleteHead","deleteHead"]
> [[],[3],[],[],[]]
> 输出：[null,null,3,-1,-1]
> ```
>
> **示例 2：**
>
> ```
> 输入：
> ["CQueue","deleteHead","appendTail","appendTail","deleteHead","deleteHead"]
> [[],[],[5],[2],[],[]]
> 输出：[null,-1,null,null,5,2]
> ```
>
> **提示：**
>
> - `1 <= values <= 10000`
> - 最多会对` appendTail、deleteHead `进行` 10000` 次调用

```python
class CQueue:

    def __init__(self):
        '''
        定义两个stack，一个负责出，一个负责如入
        '''
        self.inn = []
        self.out = []

    def appendTail(self, value: int) -> None:
        self.inn.append(value)
    def deleteHead(self) -> int:
        if len(self.out) == 0:
            if len(self.inn) == 0:
                return -1
            else:
                # 此时in的尾部时最新进入的，被压入out头部使其最后出，实现先进先出
                while len(self.inn) != 0:
                    self.out.append(self.inn.pop())
        return self.out.pop()
# Your CQueue object will be instantiated and called as such:
# obj = CQueue()
# obj.appendTail(value)
# param_2 = obj.deleteHead()
```

### 剑指 Offer 30. 包含min函数的栈

> 定义栈的数据结构，请在该类型中实现一个能够得到栈的最小元素的 min 函数在该栈中，调用 min、push 及 pop 的时间复杂度都是 O(1)。
>
>  **示例:**
>
> ```
> MinStack minStack = new MinStack();
> minStack.push(-2);
> minStack.push(0);
> minStack.push(-3);
> minStack.min();   --> 返回 -3.
> minStack.pop();
> minStack.top();      --> 返回 0.
> minStack.min();   --> 返回 -2.
> ```
>
> **提示：**
>
> 1. 各函数的调用总次数不超过 20000 次

```python
class MinStack:

    def __init__(self):
        """
        initialize your data structure here.
        每次有元素入栈的时候，都存储当前最小的元素在辅助栈中
        """
        self.l = []
        self.minstack = [math.inf]

    def push(self, x: int) -> None:
        self.l.append(x)
        self.minstack.append(min([self.minstack[-1], x]))
        
    def pop(self) -> None:
        self.l.pop()
        self.minstack.pop()

    def top(self) -> int:
        return self.l[-1]

    def min(self) -> int:
        return self.minstack[-1]

# Your MinStack object will be instantiated and called as such:
# obj = MinStack()
# obj.push(x)
# obj.pop()
# param_3 = obj.top()
# param_4 = obj.min()
```



### 146. LRU 缓存

> 请你设计并实现一个满足 [LRU (最近最少使用) 缓存](https://baike.baidu.com/item/LRU) 约束的数据结构。
>
> 实现 `LRUCache` 类：
>
> - `LRUCache(int capacity)` 以 **正整数** 作为容量 `capacity` 初始化 LRU 缓存
> - `int get(int key)` 如果关键字 `key` 存在于缓存中，则返回关键字的值，否则返回 `-1` 。
> - `void put(int key, int value)` 如果关键字 `key` 已经存在，则变更其数据值 `value` ；如果不存在，则向缓存中插入该组 `key-value` 。如果插入操作导致关键字数量超过 `capacity` ，则应该 **逐出** 最久未使用的关键字。
>
> 函数 `get` 和 `put` 必须以 `O(1)` 的平均时间复杂度运行。
>
> **示例：**
>
> ```
> 输入
> ["LRUCache", "put", "put", "get", "put", "get", "put", "get", "get", "get"]
> [[2], [1, 1], [2, 2], [1], [3, 3], [2], [4, 4], [1], [3], [4]]
> 输出
> [null, null, null, 1, null, -1, null, -1, 3, 4]
> 
> 解释
> LRUCache lRUCache = new LRUCache(2);
> lRUCache.put(1, 1); // 缓存是 {1=1}
> lRUCache.put(2, 2); // 缓存是 {1=1, 2=2}
> lRUCache.get(1);    // 返回 1
> lRUCache.put(3, 3); // 该操作会使得关键字 2 作废，缓存是 {1=1, 3=3}
> lRUCache.get(2);    // 返回 -1 (未找到)
> lRUCache.put(4, 4); // 该操作会使得关键字 1 作废，缓存是 {4=4, 3=3}
> lRUCache.get(1);    // 返回 -1 (未找到)
> lRUCache.get(3);    // 返回 3
> lRUCache.get(4);    // 返回 4 
> ```
>
> **提示：**
>
> - `1 <= capacity <= 3000`
> - `0 <= key <= 10000`
> - `0 <= value <= 10^5`
> - 最多调用 `2 * 105` 次 `get` 和 `put`

```python
class Node:
    # 实现一个双向链表中的节点
    def __init__(self):
        self.key = None
        self.value = None
        self.prev = None
        self.next = None

class LRUCache:
    # 思路是哈希链表
    def __init__(self, capacity: int):
        self.map = {} # 哈希表, key: int, value: node
        self.head = Node()
        self.tail = Node()
        # 双向链表, 最常使用的放在尾部
        self.head.next = self.tail
        self.tail.prev = self.head
        self.size = 0
        self.cap = capacity

    def remove_head(self):
        key = self.head.next.key
        self.head.next = self.head.next.next
        self.head.next.prev = self.head
        self.map.pop(key)


    def move_to_tail(self, key):
        # 放在队尾
        self.map[key].prev.next = self.map[key].next
        self.map[key].next.prev = self.map[key].prev
        self.map[key].next = self.tail
        self.map[key].prev = self.tail.prev
        self.tail.prev.next = self.map[key]
        self.tail.prev = self.map[key]

    def append_to_tail(self, key):
        self.map[key].next = self.tail
        self.map[key].prev = self.tail.prev
        self.tail.prev.next = self.map[key]
        self.tail.prev = self.map[key]

    def get(self, key: int) -> int:
        if self.map.get(key) is None:
            return -1
        else:
            self.move_to_tail(key)
            return self.map[key].value

    def put(self, key: int, value: int) -> None:
        if self.map.get(key) is None:
            # 不存在
            self.map[key] = Node()
            self.map[key].key = key
            self.map[key].value = value
            if self.size < self.cap:
                self.append_to_tail(key)
                self.size += 1
            else:
                self.remove_head()
                self.append_to_tail(key)
        else:
            # 存在
            self.map[key].value = value
            self.move_to_tail(key)
            
# Your LRUCache object will be instantiated and called as such:
# obj = LRUCache(capacity)
# param_1 = obj.get(key)
# obj.put(key,value)
```

