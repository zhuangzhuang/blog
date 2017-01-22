---
title: 解决pycharm不能使用jupyter问题
date: 2017-01-22 20:32:24
tags: [python, pycharm, jupyter]
---

打算在`pycharm`中使用`jupyter`, 但是发现新版本的jupyter需要设置下才可以使用，网上刚好也没有解决方法
也有人提问了[Jupyter Notebook in Pycharm](https://www.heapoverflow.me/question-jupyter-notebook-in-pycharm-41736309)

原始问题如下:

![pycharm_jupyter_error](/images/python/jupyter_error.png)


可以看到有2个问题

1. 需要使用浏览器登陆，使用token认证一次
2. post 中加入`_xsrf`解决csrf问题 


解决如下,命令行中输入`jupyter notebook --generate-config`, 生成配置文件`jupyter_notebook_config.py`
加入下面配置即可

1. `c.NotebookApp.disable_check_xsrf = True`
2. `c.NotebookApp.token = ''`