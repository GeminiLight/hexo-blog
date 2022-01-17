---
title: 【Python】with语句原理
author: Gemini向光性
date: 2020-8-14
categories: 
  - PL - 编程语言
tags: 
  - Python
---

在我们使用Python的时候，常使用到如下的代码块:

```Python
# 文件读取
with open(file, 'r') as f:
    # CODE BLOCK #

# 梯度计算
with tf.GradientTape() as g:
    # CODE BLOCK #
```

在很多场景中，使用with语句来可以让我们可以更好地来管理资源和简化代码，它可以看做是对try/finally模式的简化。它原理上是利用了上下文管理器，下文简要介绍将对其执行原理和自定义的方法。<!-- more -->

## 上下文管理器概念

### 上下文管理协议（Context Management Protocol）

包含方法 `__enter__()` 和 `__exit__()` ，支持该协议的对象要实现这两个方法。

### 上下文管理器（Context Manager）

支持上下文管理协议的对象，这种对象必须实现 `__enter__()` 和 `__exit__()` 方法。
上下文管理器定义执行with语句时要建立的运行时上下文，负责执行with语句块上下文中的进入与退出操作。
通常使用with语句调用上下文管理器，也可以通过直接调用其方法来使用。

- **`__enter__()`**
  - with语句执行时，先获取上下文管理器对象，随后调用其 `__enter__()`
  - 若有 `as var` 语句，则将返回值赋给变量var
  - 可以返回上下文管理器对象本身，也可以是其他相关对象
- **`__exit__()`**
  - 带有三个参数 `exc_type, exc_val, exc_tb`
  - 若上下文管理器对象执行无异常，则三个参数均为 `None`
  - 若发生异常，则三个参数分别为 异常类型，异常值和tracback信息

## with语句执行过程

```python
#   EXP: 表达式
#   VAR: 变量名，[as VAR][可选]
# BlOCK: 代码块

with EXP as VAR:
    BLOCK
```

<img src="/resource/images/pl-python-with.png" width="100%" />

1. 执行代码时，先执行 `EXPR` 语句，生成上下文管理器对象 context_manager；
2. 获取上下文管理器的 `__exit()__` 方法，并保存起来用于之后的调用；
3. 调用上下文管理器的 `__enter__()` 方法，且可将返回值赋给as语句变量；
4. 执行BLOCK中的表达式；
5. 不管是否执行过程中是否发生了异常，执行上下文管理器的 `__exit__()` 方法， 执行“清理”工作，如释放资源等。
   1. 如果执行过程中没有出现异常，或者语句体中执行了语句 `break` / `continue` / `return` ，则以 `None` 作为参数调用 `__exit__(None, None, None)` ；
   2. 如果执行过程中出现异常，则使用sys.exc_info得到的异常信息为参数调用 `__exit__(exc_type, exc_value, exc_traceback)` ；
6. 出现异常时，如果 `__exit__(type, value, traceback)` 返回 `False` ，则会重新抛出异常，让with之外的语句逻辑来处理异常，这也是通用做法；如果返回True，则忽略异常，不再对异常进行处理。

## 自定义上下文管理器

- 它使代码更简练，可以简化try/finally模式
- 当代码异常产生时，`__exit__()` 会执行清理工作
- 可以对软件系统中的资源进行管理，比如数据库连接、共享资源的访问控制等<br />

```python
# coding = utf-8

# 上下文管理器类
class TestWith(object):
    def __init__(self):
        pass

    def __enter__(self):
        """进入with语句的时候被调用
           并将返回值赋给as语句的变量名
        """
        print('__enter__')
        return "var"

    def __exit__(self, exc_type, exc_val, exc_tb):
        """离开with的时候被with调用"""
        print('__exit__')
        return True


# with后面必须跟一个上下文管理器
# 如果使用了as，则是把上下文管理器的 __enter__() 方法的返回值赋值给 target
# target 可以是单个变量，或者由“()”括起来的元组（不能是仅仅由“,”分隔的变量列表，必须加“()”）
if __name__ = 'main':
    with TestWith() as var:
        print(var)


# 运行结果
'''
__enter__
var
__exit__
'''
```

本例仅对应代码正常执行的流程，其他特殊情况不再一一列举，有兴趣可单独实验。

## 参考

> [浅谈 Python 的 with 语句](https://developer.ibm.com/zh/articles/os-cn-pythonwith/) <br />
> [Python中with用法详解](https://www.cnblogs.com/pythonbao/p/11211347.html) <br />
> [Python中with使用](https://zhuanlan.zhihu.com/p/74552877) <br />