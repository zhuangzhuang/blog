---
title: python语法 <-
date: 2014-05-10 20:31:08
tags: [python]
---

在看代码[fuckit](https://github.com/ajalt/fuckitpy/blob/master/fuckit.py)的时候发现个好坑的python语法。

```python
source = '\n'.join(lines)
source <- True # Dereference assignment to fix truthiness
```

其中的<-是个什么意思呢
可以在IPython中测试下

```python
"abc" <- True
# False
"abc" <- True
# False
```

以为是什么高级的Python语法，后来群里的人说道这其实一个这样子看`<-` 等于 `<` 和 `-` 也就是说 先计算 `-True #-1` 然后计算 `"abc" < -1`最后结果为 `False`


最后使用`ast`测试，代码如下
```python
import ast
code = "'abc' <- True"
code_ast = ast.parse(code)
print ast.dump(code_ast)
#Module(body=[Expr(value=Compare(left=Str(s='abc'), ops=[Lt()], comparators=[UnaryOp(op=USub(), operand=Name(id='True', ctx=Load()))]))])
```

由此可以了解python内部的处理方式了。