---
title: Python语法查漏补缺
date: 2022-04-11 10:12:00
updated: 2022-12-05 22:45:00
tag:
- leetcode
- python
---

## 内置函数

- 位运算

    - 异或：`x ^ y`
    - 取反：`x ~ y`
    - 与、或：`x & y`, `x | y`
    - 左右移动`n`位：`x >> n`，`x << n`

- 进制转换：

    ![image-20221205224445635](https://raw.githubusercontent.com/KMdsy/figurebed/master/img/image-20221205224445635.png)

    - 0b / 0B --> 二进制字符前缀

    - 0o / 0O --> 八进制字符前缀

    - 0x / 0X --> 十六进制字符前缀

## List

- `res = list.pop()`，移除列表中指定索引的值，即`list.pop(index)`，默认移除最后一个值，函数返回被移除的值。
- `list.insert(idx, value)`，向列表中的`idx`位置插入值`value`，其后的值自动向后移动。
- ⚠️ 警惕列表的创建方式：使用`[0] * m`创建的列表，实质上是分配了`m`个存储了常数的内存单元，此时这`m`个位置可以单独索引。但使用二级乘法`[[0] * m] * n`则是将刚才的`m`个内存复制了`n`遍，修改`mat[i][j]`会导致所有`mat[i-k][j]`都发生变化。
- 



## Dictionary

- `res = dict.get(key)`: 返回指定键的值，若查询不到，返回`None`。
- `res = dict.pop(key)`：删除指定键，并返回该键对应的值。
- `res = list(dict.keys())`返回所有键组成的列表；`res = list(dict.values())`返回所有值组成的列表。
- 几种遍历方法：
    - `for k, v in dict.items(): print(k, v)`




## Set

- `set.add(x)`：将`x`添加到集合中
- `set.remove(x)`：将值`x`从集合中去除



## Collections

`collections`拓展了python内置数据类型，提供更方便的方法与高性能存储类型。比如基础的字典是不支持顺序的，collections模块的OrderedDict类构建的字典可以支持顺序。

⚠️：在leetcode中，无需`import`，即可使用该标准库，例如`queue = collection.deque([p])`

### 概览

该库包括9种数据类型

```python
import collections
print(collections.__all__)
['deque', 'defaultdict', 'namedtuple', 'UserDict', 'UserList', 
'UserString', 'Counter', 'OrderedDict', 'ChainMap']
```

| 名称         | 用法                                                         |
| ------------ | :----------------------------------------------------------- |
| namedtuple() | 创建命名元组子类的工厂函数，生成可以使用名字来访问元素内容的tuple子类 |
| deque        | 类似列表(list)的容器，实现了在两端快速添加(append)和弹出(pop) |
| ChainMap     | 类似字典(dict)的容器类，将多个映射集合到一个视图里面         |
| Counter      | 字典的子类，提供了可哈希对象的计数功能                       |
| OrderedDict  | 字典的子类，保存了他们被添加的顺序，有序字典                 |
| defaultdict  | 字典的子类，提供了一个工厂函数，为字典查询提供一个默认值     |
| UserDict     | 封装了字典对象，简化了字典子类化                             |
| UserList     | 封装了列表对象，简化了列表子类化                             |
| UserString   | 封装了字符串对象，简化了字符串子类化（中文版翻译有误）       |

### 常用类

#### deque

常用于**栈**（stack）和**队列**（queue），在两端追加元素时的复杂度都近似为$O(1)$，以下梳理基础方法。

- `q = collections.deque(['a', 'b', 'c'])`：初始化队列，可加`maxlen`参数用于固定序列长度
- `q.append('d'),q.appendleft('d')`：分别从右、左边添加一个元素
- `q.extend(['d', 'e']), q.extendleft(['d', 'e'])`：分别从右、左边拓展列表
- `q.pop(), q.popleft()`：分别从右、左边弹出一个元素
- `q.clear()`：清除队列
- `q.remove(x)`：移除第一个值为`x`的元素
- `q.index(x)`：查找第一个`x`出现的位置，没有则返回`ValueError`
- `q.insert(idx, x)`：在`idx`位置插入`x`
- `q.reverse()`：元素逆序
- `q.rotate(x)`：向右循环移动`x`个位置，`x < 0`则为向左移动

## Type Hints

**[python3.5特性]** 基础的注解方法：`def greeting(name: str) -> str:`，用<u>冒号</u>与<u>箭头</u>指定了方法的输入类型与输出类型。

- 🔆：运行时不强制执行函数和变量类型注解，但这些注解可用于类型检查器、IDE、静态检查器等第三方工具，当类型不匹配时将raise warning。
- 除了基础的数据类型外，支持注解的类型包括`typing`中的其他类
    - `typing.Any`：所有类型的数据都可以视为`Any`
    - `typing.Tuple`：
        - `Tuple[int, float, str]` ：是由整数、浮点数、字符串组成的三项元组
        - `Tuple[int, ...]`：用省略号字面量指定同质变长元组
        -  `Tuple[()]`：空元组
    - `typing.Union`：`Union[int, float, str]`表示可以是`int`, `float`, `str`中的任意一种
    - `typing.Optional`：`Optional[int]`输入值可以是`int`或是`None`，等价于`Union[X, None]`
        - ⚠️ 含默认值的可选参数不需要在类型注解上添加 `Optional` 限定符，因为它仅是可选的，如`def foo(arg: int = 0) -> None`
        - ⚠️ 显式应用 `None` 值时，不管该参数是否可选， `Optional` 都适用，如`def foo(arg: Optional[int] = None) -> None`
    - `typing.Callable`：`Callable[[int], str]` 是把`int`转为 `str `的函数
    - `typing.Concatenate`：不常用，详见 [link](https://docs.python.org/zh-cn/3/library/typing.html#typing.Concatenate)

​				

