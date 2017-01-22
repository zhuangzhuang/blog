---
title: 好玩的函数
date: 2016-09-10 20:30:39
tags: [javascript, channel9]
---

# Funky-Functions[Channel9]

视频地址，[Funky Functions by Spencer Jobe](https://channel9.msdn.com/Blogs/semjs/semjs201505fun)

介绍如何在js中不使用`if`, `while`, `for`, `eval`, `try`, `catch`, `finally`, `switch`

技术，使用函数实现

如最简单的if

```typescript
var x = 0
if(x === 0) {
	alert("x is zero")
}
//可等效于
(x === 0) && (function(){
	alert("x is zero")
}());
```

封装一下如下

```typescript

var ELSE = 1;
var IF = function(cmp, trueFn){
	var emptyFn;
	cmp && trueFn();
	cmp && (emptyFn = function(){}());
	returm emptyFn || IF;
}

//Test
IF(x == 0, function(){
	alert("x is zero");
})(x == 1, function() {
	alert("x is one")
})(ELSE, function(){
	alert("x is somthing else");
});
```

Eval实现如下

```typescript
var EVAL = function(scriptValue) {
	var head = document.getElementsByTagName("head")[0];
	var script = document.createElement('script');
	script.text = scriptValue;
	head.appendChild(script);
	head.removeChild(script);
}
```



Try/Catch/Finally实现如下

```typescript
var TRY_STATUS = 0;
var TRY = function(tryFn, catchFn, finallyFn){
	TRY_STATUS = 0;
	var fn = "(function exec(){\n(" + 
		String(tryFn)+"())};\n TRY_STATUS=1;\n}());";
	EVAL(fn);
	IF(TRY_STATUS === 0, catchFn);
	IF(finallyFn, finallyFn);
}
```

视频中还有`FOR`, `WHILE`没有讲到。

附录：

[JAVASCRIPT语言精髓与编程实践](https://book.douban.com/subject/3012828/)：中涉及了怎么去IF, WHILE,让js更加函数式

[计算的本质](https://book.douban.com/subject/26148763/)  ：中第六章，完全使用函数做运算，包括数字，字符串， 全部可以用函数表示。

