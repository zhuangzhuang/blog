---
title: 为Django加入调试功能
date: 2016-06-07 20:32:01
tags: [python, django]
---

默认的django的错误报告很不友好,就像这样
![Origin Django Error](/images/python/origin_django_error.png)
总的来说只能看看哪里错了啊, 不是特别友好.

相比下来Flask带的调试功能就强多了,如图
![Flask Error](/images/python/flask_error.png)
而且有交互功能.

查找得出Flask使用的是`werkzeug`中的一个功能.`werkzeug`是个wsgi工具库.而wsgi有是个简单的协议可以轻轻松松包装加入hook类似功能.

修改django工程.
在`settings.py`文件结尾加入如下代码

```python
if DEBUG:
    WSGI_APPLICATION = 'yourproject.wsgi_debug.application'
    DEBUG_PROPAGATE_EXCEPTIONS = True
```
    
在project的`wsgi.py`目录下加入文件'wsgi_debug.py'.
文件中加入代码.

```python
if DEBUG:
from .wsgi import application
from werkzeug.debug import DebuggedApplication

application = DebuggedApplication(application, True)
```

测试结果如下.
![Flask Error](/images/python/new_django_error.png)

现在django也有了在线调试功能了.
