---
title: Python语法查漏补缺
date: 2022-04-11 10:12:00
updated: 2022-02-27 10:45:00
tag:
- leetcode
- python
---

## Tips

1. 在类OJ的运算平台中，注意输入输出的格式，输入为`s = input()`，对于字符串迭代，记得将字符串展开为list，`s_list = list(s)`
2. 

## 1. 内置函数

- 位运算

    - 异或：`x ^ y`
    - 取反：`x ~ y`
    - 与、或：`x & y`, `x | y`
    - 左右移动`n`位：`x >> n`，`x << n`

- 进制转换：十进制数`x`转`n`进制 `int(x, n)`

    ![image-20221205224445635](https://raw.githubusercontent.com/KMdsy/figurebed/master/img/image-20221205224445635.png)

    - 0b / 0B --> 二进制字符前缀

    - 0o / 0O --> 八进制字符前缀

    - 0x / 0X --> 十六进制字符前缀

- `nonlocal `keyword：用于在函数内部引用外部定义的变量，此时在函数内部可以对外部变量做修改

    ```python
    def myfunc1():
      x = "John"
      def myfunc2():
        nonlocal x
        x = "hello"
      myfunc2() 
      return x
    
    print(myfunc1()) # >> 'hello'
    ```

    

## 2. List

- `res = list.pop()`，移除列表中指定索引的值，即`list.pop(index)`，默认移除最后一个值，函数返回被移除的值。
- `list.insert(idx, value)`，向列表中的`idx`位置插入值`value`，其后的值自动向后移动。
- ⚠️ 警惕列表的创建方式：使用`[0] * m`创建的列表，实质上是分配了`m`个存储了常数的内存单元，此时这`m`个位置可以单独索引。但使用二级乘法`[[0] * m] * n`则是将刚才的`m`个内存复制了`n`遍，修改`mat[i][j]`会导致所有`mat[i-k][j]`都发生变化。
- `list.sort(key=lambda x:x[-1], reverse=True)`：对list item不为基础数据类型的，用list item中的[-1]项对list做排序，变体`list.sort(key=lambda x:len(x))`：对list item为string的列表，按照string长度排序。



## 3. Dictionary

- `res = dict.get(key)`: 返回指定键的值，若查询不到，返回`None`。
- `res = dict.pop(key)`：删除指定键，并返回该键对应的值。
- `res = list(dict.keys())`返回所有键组成的列表；`res = list(dict.values())`返回所有值组成的列表。
- 几种遍历方法：
    - `for k, v in dict.items(): print(k, v)`



## 4. Set

- `set.add(x)`：将`x`添加到集合中
- `set.remove(x)`：将值`x`从集合中去除



## 5. Collections

`collections`拓展了python内置数据类型，提供更方便的方法与高性能存储类型。比如基础的字典是不支持顺序的，collections模块的OrderedDict类构建的字典可以支持顺序。

⚠️：在leetcode中，无需`import`，即可使用该标准库，例如`queue = collection.deque([p])`

### 5.1 概览

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

### 5.2 常用类

#### 5.2.1 deque

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

#### 5.2.2 Counter

一个计数器，通过对**对象（iterable / mapping / keyword args）**哈希化，来计数迭代器中的内容

```python
c = Counter()                           # a new, empty counter
c = Counter('gallahad')                 # iterable -> Counter({'a': 3, 'l': 2, 'g': 1, 'h': 1, 'd': 1})
c = Counter({'red': 4, 'blue': 2})      # mapping -> Counter({'red': 4, 'blue': 2})
c = Counter(cats=4, dogs=8)            # keyword args -> Counter({'dogs': 8, 'cats': 4})
# call

```

常用方法

- `c.elements`: 返回一个无序迭代器，其中每个元素`k`被重复`v`次
- `c.most_common(n)`: 返回出现次数最多的`n`个`kv`对，如：，mapping例子中，`c.most_common(1) -> [('red', 4)]`
- `c.update(iterable-or-mapping)`: 用新的内容更新counter，**注意，新的计数将会加在旧的计数器上**

一些运算符案例

![image-20230213112119814](https://raw.githubusercontent.com/KMdsy/figurebed/master/img/image-20230213112119814.png)

#### 5.2.3 OrderedDict

按照插入顺序存储的有序字典，擅长重新排序操作，也可以根据`key`或者`value`排序。在[leetcode LRU](https://leetcode.cn/problems/lru-cache/)部分有用到。

- `od.popitem(last=True)`：按照LIFO的顺序弹出最后一个元素（最新加入的元素）；当`last=False` ，按照FIFO顺序弹出第一个加入的元素。
- `od.move_to_end(key, last=True)`：移动给定 `key`到最后一个位置；当`last=False`，移动到第一个位置。如果`key`不存在，return KeyError。
- `od.reversed()`：返回一个逆序的迭代器
- `od.items()`：正序的迭代器，与普通字典相同

#### 5.2.4 defaultdict

具有复杂默认类型的字典。

普通的字典在索引不存在的key时，会抛出KeyError，其解决方法是`dict.get(no_exist_key, default_value)`，defaultdict是一个支持复杂默认类型的字典。用法：`dic = collection.defaultdict(predefined_class_or_function)`

```python
# define a class
class myClass:
  ...
# 定义类作为默认值  
dic = collection.defaultdict(myClass)
dic[key] # 调用一次myClass构造函数，多次调用同一个不存在的key，只构造一个函数

# 定义函数作为默认值
def myFunc():
  ...
dic2 = collection_defaultdict(myFunc)
dic2[key] # 调用一次myFunc函数，多次调用同一个不存在的key，只构造一个函数
```





## 6. Type Hints

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

