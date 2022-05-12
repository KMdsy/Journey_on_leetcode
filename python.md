---
title: Python语法查漏补缺
date: 2022-04-11 10:12:00
updated: 2022-05-12 23:38:00
tag:
- leetcode
- python
---

## List

- `res = list.pop()`，移除列表中指定索引的值，即`list.pop(index)`，默认移除最后一个值，函数返回被移除的值。



## Dictionary

- `res = dict.get(key)`: 返回指定键的值，若查询不到，返回`None`。
- `res = dict.pop(key)`：删除指定键，并返回该键对应的值。
- `res = list(dict.keys())`返回所有键组成的列表；`res = list(dict.values())`返回所有值组成的列表。
- 几种遍历方法：
    - `for k, v in dict.items(): print(k, v)`




## Set

- `set.add(x)`：将`x`添加到集合中
- `set.remove(x)`：将值`x`从集合中去除



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

