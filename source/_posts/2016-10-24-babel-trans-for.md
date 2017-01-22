---
title: 优化javascript中的forEach
date: 2016-10-24 20:30:53
tags: [js, babel]
---

# 翻译js中的`forEach`为`for x in o`

想法来自weibo: http://weibo.com/1642632462/Eedu9mp6x

代码如下:

{% codeblock lang:typescript %}
const ArrayFucntionVisitor: TR.Visitor = {
    ArrowFunctionExpression(path){
        var p0 = path.node.params[0];
        var oldName = p0.name;
        path.scope.rename(oldName, this.itemName);
    },
    FunctionExpression(path){
        var p0 = path.node.params[0];
        var oldName = p0.name;
        path.scope.rename(oldName, this.itemName);
    }
}

const MyVisitor: TR.Visitor = {
    CallExpression(path){
        if(T.isMemberExpression(path.node.callee)){
            let member = path.node.callee;
            if(T.isIdentifier(member.property, {name: 'forEach'})){
                var arg0 = path.node.arguments[0];
                var identifer = path.scope.generateUidIdentifier('item');
                if(T.isFunctionExpression(arg0) || T.isArrowFunctionExpression(arg0)){                                                           
                    path.traverse(ArrayFucntionVisitor, {itemName: identifer.name})

                    var newFor = T.forInStatement(T.variableDeclaration("let", [T.variableDeclarator(identifer)]),
                                    path.node.callee.object, arg0.body);
                    path.insertAfter(newFor);
                    path.remove();
                }
            }
        }        
    }
}

{% endcodeblock  %}

ps: 仅仅考虑简单情况


结果如下:
![res](/images/babel_for_opt.png)