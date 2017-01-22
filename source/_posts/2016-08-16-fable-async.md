---
title: Fable 排序
date: 2017-01-22 20:30:25
tags: [javascript , async, fable]
---



# Fable来做异步排序

题目来自于[我用js写了一个冒泡排序法，怎么用html和css把排序过程展现出来？](https://www.zhihu.com/question/41642706)

简单的说明就是在js中怎么做sleep来做到异步效果。

原来的解决方法是用[windjs](https://www.zhihu.com/question/41642706/answer/92225439) 不够后来老赵也不维护了，

es7中有async, await方案，不过暂时需要babel去编译， 原生支持async/await的有微软的Edge，和对于的js引擎。

下面来将[Fable](http://fsprojects.github.io/Fable/)这个应该是F#的js版本，F#有很强的一个技术叫[Computation Expressions](https://msdn.microsoft.com/visualfsharpdocs/conceptual/computation-expressions-%5bfsharp%5d)定义好预设的几个函数后，就可以组合使用。将原来的流程全部碎片成函数，但是编写的时候还是按照顺序编写，自己在碎片函数中控制流向。

F#同步排序如下

```f#
let swap (arr: int array) i j = 
    let tmp = arr.[i]
    arr.[i] <- arr.[j]
    arr.[j] <- tmp

let bubbleSort (arr: int array) = 
    for x in [0..arr.Length-1] do
        for y in [0 .. arr.Length-x-1] do
            if arr.[y] > arr.[y+1] then
                swap arr y (y+1)
```

浏览器启动后都是一瞬间结束，如果要暂停一会儿查看内部的信息会很困难。

基于F#的async可以轻松达到效果

```f#
let asyncBubbleSort (arr: int array) = async {  #1
    for x in [0..arr.Length-1] do
        for y in [0 .. arr.Length-x-1] do
            if arr.[y] > arr.[y+1] then
                do! Async.Sleep(500)           #2
                printfn "swaping: %d <-> %d" arr.[y] arr.[y+1]
                swap arr y (y+1)
    printfn "down!"
}

let arr = [|0;11;2;4;7;3|]
asyncBubbleSort arr
|> Async.StartImmediate  #3
```

再3处加入一点点代码就可以达到效果。



参考

http://tomasp.net/academic/papers/async/

http://tomasp.net/academic/papers/computation-zoo/
