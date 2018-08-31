---
title: Generator与async、await[借鉴自MDN]
date: 2018-08-30 19:19:20
tags: javascript 、es6
---


### 一. ES6 Generator(生成器) 语法

    function* 用于定义一个生成器函数（generator function），它返回一个Generator对象, Generator对象被创建后，通过调用next()方法获取值
    
    yield 关键字用来暂停和恢复一个生成器函数 

1. yield关键字使生成器函数执行暂停，yield关键字后面的表达式的值返回给生成器的调用者。它可以被认为是一个基于生成器的版本的return关键字

2. yield关键字实际返回一个IteratorResult对象，它有两个属性，value和done。value属性是对yield表达式求值的结果，而done是false，表示生成器函数尚未完全完成。

3. 一旦遇到 yield 表达式，生成器的代码将被暂停运行，直到生成器的 next() 方法被调用。每次调用生成器的next()方法时，生成器都会恢复执行，直到达到以下某个值:

    - yield，导致生成器再次暂停并返回生成器的新值。 下一次调用next()时，在yield之后紧接着的语句继续执行。

    - throw用于从生成器中抛出异常。这让生成器完全停止执行，并在调用者中继续执行，正如通常情况下抛出异常一样。

    - 到达生成器函数的结尾；在这种情况下，生成器的执行结束，并且IteratorResult给调用者返回undefined并且done为true。
    
    - 到达return 语句。在这种情况下，生成器的执行结束，并将IteratorResult返回给调用者，其值是由return语句指定的，并且done 为true。



    ```javascript
        // 示例：
        function* countAppleSales () {
            var saleList = [3, 7, 5];
            for (var i = 0; i < saleList.length; i++) {
                yield saleList[i];
            }
        }
        var appleStore = countAppleSales(); // Generator { }
        console.log(appleStore.next()); // { value: 3, done: false }
        console.log(appleStore.next()); // { value: 7, done: false }
        console.log(appleStore.next()); // { value: 5, done: false }
        console.log(appleStore.next()); // { value: undefined, done: true }
    ```



    ##### 注意： 
        1. 不能在forEach等方法中直接使用yield，因为forEach等接受一个函数作为参数，yield外层执行环境不是生成器函数，所以会报语法错误，错误示例如下
    ```javascript
        function* nameGenerator() {
            ['Hellon','Nancy'].forEach(item => {
                yield item  // => Uncaught SyntaxError: Unexpected identifier
            })
        }
    ```
        2. 也可作为对象属性存在，通过如下方式定义
    ```javascript
        *countAppleSales() {}
    ```

3. [yield*](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/yield*) 表达式用于委托给另一个generator 或可迭代对象。



### 二. Async function： 
1. async function 声明将定义一个返回 AsyncFunction 对象的异步函数。异步函数是指通过事件循环异步执行的函数，它会通过一个隐式的 Promise 返回其结果。但是如果你的代码使用了异步函数，它的语法和结构会更像是标准的同步函数

    1). 当调用一个 async 函数时，会返回一个 Promise 对象。当这个 async 函数返回一个值时，Promise 的 resolve 方法会负责传递这个值；当 async 函数抛出异常时，Promise 的 reject 方法也会传递这个异常值。
    2). async 函数中可能会有 await 表达式，这会使 async 函数暂停执行，等待表达式中的 Promise 解析完成后继续执行 async 函数并返回解决结果

2. await  操作符用于等待一个Promise 对象。它只能在异步函数 async function 中使用
    1). await 表达式会暂停当前 async function 的执行，等待 Promise 处理完成。若 Promise 正常处理(fulfilled)，其回调的resolve函数参数作为 await 表达式的值，继续执行 async function。
    2). 若 Promise 处理异常(rejected)，await 表达式会把 Promise 的异常原因抛出。
    3). 另外，如果 await 操作符后的表达式的值不是一个 Promise，那么该值将被转换为一个已正常处理的 Promise。


### 三. Generator 结合 async/await

```javascript
    // 模拟发起请求，获取数据
    function getData() {
        return new Promise((resolve) => {
            data = `MOCK DATA ${new Date()}`
            setTimeout(_ => {
                resolve(data)
            }, 2000)
        })
    }

    // 迭代器，调用next()请求最新数据
    async function* timerGenerator () {
        while (true) {
            yield getData()
        }
    }

    // 业务代码，获取最新数据
    async function execute() {
        const timer = timerGenerator()
        console.log(await timer.next()) // {value: "MOCK DATA Thu Aug 30 2018 10:57:51 GMT+0800 (中国标准时间)", done: false}
        console.log(await timer.next()) // {value: "MOCK DATA Thu Aug 30 2018 10:57:53 GMT+0800 (中国标准时间)", done: false}
        console.log(await timer.next()) // {value: "MOCK DATA Thu Aug 30 2018 10:57:55 GMT+0800 (中国标准时间)", done: false}
        console.log(await timer.next()) // {value: "MOCK DATA Thu Aug 30 2018 10:57:57 GMT+0800 (中国标准时间)", done: false}
    }
    execute()
```

    
