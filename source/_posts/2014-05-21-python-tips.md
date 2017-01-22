---
title: python 代码段
date: 2014-05-21 20:31:18
tags: [python]
---

内容来自于[Armin Ronacher][1]的blog，[Python Idioms][2] 和 [Things you didn't know about Python][3] 
# cache_property

扩展了python内部的property, 用于缓存结果，代码如下

``` python
class cached_property(object):
    def __init__(self, func):
        self.func = func
        self.__name__ = func.__name__
        self.__doc__ = func.__doc__
        self.__module__ = func.__module__

    def __get__(self, obj, type=None):
        if obj is None:
            return self
        value = obj.__dict__.get(self.__name__, _missing)
        if value is _missing:
            value = self.func(obj)
            obj.__dict__[self.__name__] = value
        return value
```

[1]: http://lucumr.pocoo.org/talks/
[2]: http://dev.pocoo.org/~mitsuhiko/idioms.pdf
[3]: http://pocoo.org/~mitsuhiko/didntknow.pdf