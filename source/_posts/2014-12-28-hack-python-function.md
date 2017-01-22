---
title: Hack一下Python的默认参数的坑
date: 2014-12-28 20:31:46
tags: [python]
---

面试题, "为何Python中的函数不能使用可变参数".

举例:
```python
def foo(a=[]):
    a.append(1)
    print a

foo()
foo()
```

结果将是
`[1]`
`[1, 1]`

一般的解决方法如下(代码来自于[Python的魔法二:开发的'坑'][1])::

```python
def foo(a=None):
	if a is None:
		a = []
    a.append(1)
    print a
```

由于默认参数存在`fun.func_defaults`中,配合装饰器可以简单作下hack

```python
import copy

def hack_default(func):
    def wrap(*args, **kwargs):
        old_func_defaults = copy.deepcopy(func.func_defaults)
        func(*args, **kwargs)
        func.func_defaults = old_func_defaults
    return wrap

@hack_default
def foo(a=[]):
    a.append(1)
    print a

foo()
foo()
```

这下就不要担心参数被修改的问题了, 因为每次修改都被拷贝了一份

[1]: http://www.dongwm.com/archives/pythonmo-fa-er/
