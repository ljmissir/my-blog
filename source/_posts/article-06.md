---
title: Js事件循环机制
date: 2019-04-17 19:14:19
tags: js
cover: https://user-gold-cdn.xitu.io/2019/4/16/16a253e404a656b3?imageView2/1/w/1304/h/734/q/85/format/webp/interlace/1
---
> 概述

#### 事件循环机制
　　js的事件循环机制，在我理解起来就是执行上下文，对函数的出栈和入栈的一个过程。js的一大特点就是单线程，试想一下，如果js是多线程语言，同时存在两个线程，一个线程是在某个Dom上添加一个节点，而另一个线程却是删除节点，那么这样浏览器就会产生错乱，而我们关心的js事件循环机制就是由js单线程的特质决定的。
　　先看下大致流程
<img src="/img/img-05.png"/>
　　在上图过程中：
1、js的引擎 逐句的执行js代码，形成执行栈
2、当执行栈遇到异步代码时候，会指给对应的异步进程进行处理（WEB API）
3、等待异步任务有了运行结果，js的运行环境就会在对应的任务队列中push事件任务，等待着被js引擎执行，而当异步没有产生回调，那他就不会push到任何任务队列中去。
4、执行栈执行任务完毕，便会查询任务队列，如果不为空，则读取一个任务推入主线程处理
5、重复第4个步骤，直到任务队列为空，这样的过程就是事件循环。
　　涉及到的异步进程：
- Dom事件，是由浏览器的Dom模块处理，达到触发条件时，在任务队列中添加相应的回调函数
- 定时器setTimeout等，是由浏览器的Timer模块处理，达到设置的时间点时，在任务队列中添加回调
- ajax请求，是由浏览器的Network模块处理，等待请求返回，在任务队列中添加回调
#### 栈
> 栈（stack）又名堆栈，它是一种运算受限的线性表。其限制是仅允许在表的一端进行插入和删除运算

<img src="/img/img-06.png"/>
知道了栈是一种后进先出的数据结构后，我们运行一下代码，并模拟它的入栈和出栈过程
```javaScript
    function fun2() {
        console.log('fun2')
    }
    function fun1() {
        fun2();
    }
    setTimeout(() => {
        console.log('setTimeout')
    })
    fun1();
```
以上出入栈如下图所示
<img src="/img/img-07.png"/>
#### 任务队列
　　js中存在着多个任务队列，并且不同的任务队列之间优先级不同，优先级高的先被获取。同一队列中按照队列顺序被取用 任务队列分为两种类型
- macrotask queue（宏任务队列）：script（整体代码），setTimeout， setInterval，setImmediate，I/O，UI rendering
- microtask queue（微任务队列）：process.nextTick， Promises（这里指浏览器实现的原生 Promise）， MutationObserver

两者的区别：
- macrotask queue：存在多个，存在优先级
- microtask queue：仅一个，按照队列顺序执行

　　事实上，事件循环的顺序，决定了JavaScript代码的执行顺序。执行时从script(整体代码)开始第一次循环，之后全局上下文进入函数调用栈，直到执行栈清空，然后开始执行所有的microtask,当所有可执行的micro-task执行完毕之后。循环再次从macro-task开始，找到其中一个任务队列执行完毕，然后再执行所有的micro-task，这样一直循环下去。事件循环每次只会入栈一个 macrotask ，主线程执行完该任务后又会先检查 microtasks 队列并完成里面的所有任务后再执行 macrotask。


　　我们举个例子验证下
```javaScript
    console.log('-----start-----')
    setTimeout(()=>{
        console.log('setTimeout1-macroTask')
    },1000)
    Promise.resolve().then(() => {
        console.log('Promise1-microTask')
    });
    Promise.resolve().then(() => {
        console.log('Promise2-microTask')
    });
    setTimeout(()=>{
        console.log('setTimeout2-macroTask')
    },100)
    console.log('-----end-----')
```

　　具体过程，我们稍微概括下：
1、遇到console.log,立即执行，输出“-----start-----”
2、setTimeout,交给异步模块处理，执行完后，回调放入macrotask queue中
3、遇到Promise，执行then部分，回调放入microtask queue
4、遇到Promise，执行then部分，回调放入microtask queue，此时的microtask queue队列中就有两个事件了
5、还是遇到setTimeout,同样交给异步处理模块，执行完后，回调同样是一个macrotask任务，但因为setTimeout2的时间要比setTimeout1的短，因此先输出setTimeout2
6、再次遇到console.log,依旧立即执行，输出“-----end-----”
7、执行结束