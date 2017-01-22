---
title: 为Web.py加入调试功能
date: 2016-08-15 20:32:15
tags: [python, debug, web.py]
---

群里人提问webpy怎么在线调试。
web.py在国内的一些云如sina云，基本属于入门配置。
但是在线默认可能会被关闭调试功能。

默认webpy的500错误显示和django差不多，如下：

![Origin Webpy Error](/images/python/origin_webpy_error.png)


和django一样， 通过`werkzug`就可以达到在线调试效果， 如下：

![New Webpy Error](/images/python/new_webpy_error.png)

需要修改的如下：
web.py中和django不同的是没有异常开关， 需要重写方法

```python
class Application(web.application):
    def handle_with_processors(self):
        def process(processors):
            try:
                if processors:
                    p, processors = processors[0], processors[1:]
                    return p(lambda: process(processors))
                else:
                    return self.handle()
            except web.HTTPError:
                raise
            except (KeyboardInterrupt, SystemExit):
                raise
            #except:           删除这部分代码，将异常抛出去
            #    print >> web.debug, traceback.format_exc()
            #    raise self.internalerror()
        return process(self.processors)
app = Application(urls, globals())
app = DebuggedApplication(app.wsgifunc(), evalex=True)
```