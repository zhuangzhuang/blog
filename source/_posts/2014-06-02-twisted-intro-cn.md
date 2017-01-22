---
title: 深入 twisted 学习
date: 2014-06-92 20:31:30
tags: [python, twisted]
---

三天内把twisted学了下，内容在[ twisted-intro-cn][1]，基本是将twisted的核心部分升入讲解了下。别的关于功能库的作者没有详细讲解。
twisted的核心部分是deferred，主要内容如下：

1. 讲解了单线程，异步和多线程怎样解决IO问题的缺点和优点。
2. 讲解了阻塞客服端，和非阻塞客服端的实现，引入了reactor
3. reactor的基本操作
4. 使用twisted改写异步客服端，手动控制异步Reader（说明如果在Reader中同步调用的话，reactor没有优势）
5. 抽象出**Transports**,**Protocols**,**Protocol Factories**并手动管理异步操作结果。
6. 引入callback, errback机制
7. 引入**Deferred**，异步操作和同步操作对比，deferred调用栈.
8. 修改客服端
9. 深入Deferred讨论了同步调用栈， 异步调用栈。划分Deffered的层次调用，层次间的交错调用。
10. 修改客服端
11. 修改服务器
12. 实现服务器和客户端的双向通信
13. 在Deferred中加入了Deferred实现异步中异步
14. 引入了proxy服务器， 在proxy中决定是否引入Deferred。
15. 测试
16. 编写服务
17. 引入了**yield**,**inlineCallbacks**,原先需要以异步的异步编写变成了写个单独的函数即可。
18. 引入了**DeferredList**，这样就可以控制一组Deferred了。
19. 加入了cancel Deferred. 可以异步取消掉一个操作
20. reactor中的大循环然后转化为每个Protocols小循环。从而引入了Erlang.
21. 从函数语言Haskell的惰性来简化了异步操作。

[1]: https://github.com/luocheng/twisted-intro-cn