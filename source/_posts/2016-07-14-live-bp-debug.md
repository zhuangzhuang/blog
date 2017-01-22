---
title: wsgi app动态调试
date: 2016-07-14 20:32:09
tags: [python, debug, wsgi]
---

默认flask的调试器是个好东西, 出现异常的时候就会动态打开个在线调试器.
但是如果不出现异常就比较惨了.
返回的结果不对? 一种就是加入`import pdb; pdb.set_trace()`然后单步调试.
第二种就是在感觉有异常的地方人工加入 `raise Exception("xx")`.
两种方法都有坏处.需要人工干涉代码.

于是就想到了python自带的调试器的`pdb`的`bp`功能.
查询后发现使用的是`sys.set_trace`功能.
简单的办法加入`line trace`. 在请求参数中指定模块和行数.使用wsgi中间件开始的时候监控程序中运行.到达指定模块*行的时候触发异常.

代码如下:

```python
if DEBUG:
from werkzeug.wrappers import Request
import sys
import os


class LiveBpMiddleware(object):
    def __init__(self, app, use_exception=Exception("Break Here")):
        self.app = app
        self.tracing = False
        self.org_trace = sys.gettrace()
        self.use_exception = use_exception

    def __call__(self, environ, start_response):
        request = Request(environ)
        livebp = request.args.get('livebp', u'')
        if livebp and livebp.count(u':') == 1:
            model, line = livebp.split(u':')
            filename = self.lookupmodule(model)
            if filename:
                self.trace_filename = filename
                self.trace_line = int(line)
                self.tracing = True
                sys.settrace(self.trace_fun)
        res = self.app(environ, start_response)
        if self.tracing:
            self.remove_trace()
        return res

    def remove_trace(self):
        sys.settrace(self.org_trace)
        self.tracing = False

    def trace_fun(self, frame, event, arg):
        if event == 'line':
            line_no = frame.f_lineno
            filename = frame.f_code.co_filename
            if line_no == self.trace_line and os.path.realpath(filename) == os.path.realpath(self.trace_filename):
                print line_no, filename
                sys.settrace(self.org_trace)
                self.tracing = False
                raise self.use_exception
        return self.trace_fun

    def lookupmodule(self, filename):
        'copy from pdb model'
        if os.path.isabs(filename) and  os.path.exists(filename):
            return filename
        f = os.path.join(sys.path[0], filename)
        if os.path.exists(f):
            return f
        root, ext = os.path.splitext(filename)
        if ext == '':
            filename = filename + '.py'
        if os.path.isabs(filename):
            return filename
        for dirname in sys.path:
            while os.path.islink(dirname):
                dirname = os.readlink(dirname)
            fullname = os.path.join(dirname, filename)
            if os.path.exists(fullname):
                return fullname
        return None
```

测试如下
![LiveBp Test](/images/python/livebp_test.png)

参考
[SysTracing](http://pymotw.com/2/sys/tracing.html)

