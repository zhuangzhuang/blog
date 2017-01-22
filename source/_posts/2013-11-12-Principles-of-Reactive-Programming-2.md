---
title: Principles of Reactive Programming
date: 2013-11-12 20:33:06
tags: [scala, coursera, rx]
---

# 第二节课笔记

### Function and State
使用Bank说明可变的Object,使用2个例子说明不变函数, 可变函数

### Identity and Change
说明两个变量可以相同

{% codeblock lang:scala %}
val x = new Obj
val y = new Obj
f(x, y)

val x = new Obj
val y = new Obj
f(x, x)
{% endcodeblock %}

如果 `f(x, y)` 和 `f(x, x)` 相同可以说明x,y 相同

语言中不使用这样的实现
`val x = new Obj --> val x = new Obj`
`val y = x       --> val y = new Obj`

### Loops
`while`是scala语言内置标识符, 但是可以使用函数模拟

{% codeblock lang:scala %}
def WHILE(condition: => Boolean)(command: => Unit): Unit =
	if (condition) {
		command
		WHILE(condition)(command)
	}
	else ()
{% endcodeblock %}	

`WHILE` 是尾递归函数

{% codeblock lang:scala %}
REPEAT{
	command
}(condition)

def REPEAT(command: => Unit)(condition: => Boolean): Unit = {
	command
	if (condition)()		
	else
		REPEAT(command)(condition)
}
{% endcodeblock %}	

{% codeblock lang:scala %}
REPEAT{
	command
}UNTIL (condition)

//
{% endcodeblock %}

`For-Loops` 使用`foreach`转化
而`for yield` 使用`map,flatMap`转化

{% codeblock lang:scala %}
def foreach(f: T=> Unit): Unit =
	// ...

for(i <- 1 until 3; j <- "abc") println(i + " " + j)
//可转化为
(1 until 3) foreach ( i => "abc" foreach (j => println(i + " " + j)))

{% endcodeblock %}	

### Completed Extended Example: Discrete Event Simulation
模拟数字电路
Inverse
And
Or
使用wire连接, 有延时
半加器

`S = a | b & ^(a&b)`
`C = a & b`

{% codeblock lang:scala %}

val a, b, c = new Wire

//每一个函数都有个side Effect(产生一个门)
def inverter(input: Wire, output: Wire): Unit
def andGate(a1: Wire, a2: Wire, output: Wire): Unit
def orGate(o1: Wire, o2: Wire, output: Wire): Unit

def halfAdder(a: Wire, b: Wire, s: Wire, c: Wire): Unit = {
	val d = new Wire
	val e = new Wire
	orGate(a, b, d)
	andGate(a, b, c)
	inverter(c, e)
	andGate(d, e, s)
}
{% endcodeblock %}

使用半加器做成一个全加器
{% codeblock lang:scala %}
def fullAdder(a: Wire, b: Wire, cin: Wire, sum: Wire, count: Wire): Unit = {
	val s = new Wire
	val c1 = new Wire
	val c2 = new Wire
	halfAdder(b, cin, s, c1)
	halfAdder(a, s, sum, c2)
	orGate(c1, c2, cout)
}
{% endcodeblock %}

### Discrete Event Simulation: API and Usage 

{% codeblock lang:scala %}
type Action = () => Unit

//定义连线(Wire)
API: getSignal setSignal addAction

class Wire {
	private var sigVal = false
	private var actions: List[Action] = List()

	def getSignal: Boolean = sigVal

	def setSignal(s: Boolean): Unit = 
		if (s != sigVal) {
			sigVal = s
			actions foreach (_())
		}

	def addAction(a: Action): Unit = {
		actions = a :: actions
		a()
	}
}

//反相器
def inverter(input: Wire, output: Wire): Unit = {
	def invertAction(): Unit = {
		val inputSig = input.getSignal
		afterDelay(InverterDelay){
			output setSignal !inputSig
		}
	}
	input addAction invertAction
}

// AndGate
def andGate(in1: Wire, in2: Wire, output: Wire): Unit = {
	def addAction(): Unit = {
		val in1Sig = in1.getSignal
		val in2Sig = in2.getSignal
		afterDelay(AndGateDelay) {
			output setSignal (in1Sig & in2Sig)
		}
	}
	in1 addAction andAction
	in2 addAction andAction
}

// OrGate
// 实现省略
{% endcodeblock %}

关于

{% codeblock lang:scala %}
def addAction(): Unit = {
		val in1Sig = in1.getSignal
		val in2Sig = in2.getSignal
		afterDelay(AndGateDelay) {
			output setSignal (in1Sig & in2Sig)
		}
}
{% endcodeblock %}

和

{% codeblock lang:scala %}
def addAction(): Unit = {		
		afterDelay(AndGateDelay) {
			output setSignal (in1.getSignal & in2.getSignal)
		}
}
{% endcodeblock %}

的差别.
第二个不符合

### Discrete Event Simluation: Implementation and Test 

{% codeblock lang:scala %}
def afterDelay(delay: Int)(block: => Unit): Unit = {
	val item = Event(currentTime + delay, ()=> block)
	agenda = insert(agenda, item)
}

// 事件注意排序(可以使用堆实现)
private def insert(ag: List[Event], item: Event): List[Event] = ag match {
	case first :: rest if first.time <= item.time =>
		first :: insert(rest, item)
	case _ =>
		item :: ag
}
{% endcodeblock %}

实际运行Loop
{% codeblock lang:scala %}

private def loop(): Unit = agenda match {
	case first :: rest =>
		agenda = rest
		current = first.time
		first.action
		loop()
	case Nil =>
}
{% endcodeblock %}

还可以加入Probes(探头)

{% codeblock lang:scala %}
def probe(name: String, wire: Wire): Unit = {
	def probeAction(): Unit = {
	println(s"$name $currentTime value = ${wire.getSignal}")
	}
	wire addAction probeAction
}
{% endcodeblock %}

关于 `orGate` 的实现, `orGate` 可以使用andGate和inverter配合实现因为 `a | b = ^(^a & ^b`)

关于离散信号模拟的资料还有

1. [stacklesspython 实现](http://www.grant-olson.net/files/why_stackless.html#pushing-data)
2. [sicp 介绍](http://mitpress.mit.edu/sicp/full-text/book/book-Z-H-22.html#%_sec_3.3.4)
