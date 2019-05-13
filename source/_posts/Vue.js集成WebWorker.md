---
title: Vue.js集成WebWorker
date: 2018-12-21 17:02:43
tags: Vue, WebWorker
---


### 一. WebWorker简单介绍

 - Web Worker为Web内容在后台线程中运行脚本提供了一种简单的方法。线程可以发送XMLHttpRequest请求，可以执行任务而不干扰用户界面。可以通过指定的方式与创建它的JavaScript代码进行交互


 - 在worker内，不能直接操作DOM节点，也不能使用window对象的默认方法和属性。但可以通过self全局属性去使用WebSocket、IndexedDB等功能


 - workers和主线程间的数据传递都通过postMessage()实现，使用onmessage事件处理函数来响应消息（消息被包含在Message事件的data属性中）。这个过程中数据并不是被共享而是被复制。


### 二. 配置


#### vue.config.js  （vue 配置文件）

```javascript

    // 配置worker-loader, 处理webworker资源
    chainWebpack: config => {
        config.module
            .rule('webworker')
            .test(/\.worker\.js$/)
            .use('worker-loader')
            .loader('worker-loader')
            .end()

    }
    parallel: false

```
>注意1： parallel需要设置为false，不然 build 的时候 loader 会报错，大概原因是多线程之间实例共享出现了问题，详情见 https://github.com/vuejs/vue-cli/issues/2785

>注意2： 通过配置文件的方式，生产环境存在找不到worker文件的问题


更简单的方式是直接通过 inline 方式，不需要配置webpack，也可以避免上面的问题

```javascript 

    import PreviewWorker from "worker-loader!../../workers/preview.worker.js";

```


#### preview.worker.js （worker）
```javascript
   // 事件处理
    self.addEventListener('message', function (e) {
        const { data } = e;
        console.log('接收到主线程信息', e)
        postMessage('worker反馈信息到主线程')
    }, false);


```

#### main.js (主线程)
```javascript

    import PreviewWorker from "../../workers/preview.worker.js";

    const worker = new PreviewWorker();
    worker.postMessage({ a: 1 });
    worker.addEventListener("message", function(event) {
        console.log("接收到worker信息", event);
    });

    // 错误处理
    worker.addEventListener("error", event => {
        console.log("worker 错误", {
            message: event.message,
            filename: event.stack,
            lineno: event.lineno,
            colno: event.colno
        });
    });


```


### 三.问题解决


1. 不断发送 websocket 请求, Frame内容是 webpackClose , 禁用热更新，问题仍存在, 详情见 https://github.com/webpack/webpack-dev-server/issues/1604

![](https://dknfeiov.github.io/images/files/worker/continue_hmr.PNG) 

> 解决方案:  禁止host检测

```javascript

    devServer: {
        disableHostCheck: true
    }

```



2. 加载worker文件，返回的是主页index.html, 定位问题原因是无法检测到worker文件修改，没有生成 .worker.js 文件

![](https://dknfeiov.github.io/images/files/worker/worker_file_error.PNG) 

> 解决方案： 直接通过 inline 方式加载

```javascript 

    import PreviewWorker from "worker-loader!../../workers/preview.worker.js";

```



### 四.其他相关

1. 记得及时销毁worker，避免内存泄漏

```javascript

    beforeDestroy() {
        // worker 回收
        if (this.worker) {
            this.worker.terminate();
            console.warn("WORKRT DESTROYED!");
        }
    }

```

2. [workerize-loader](https://github.com/developit/workerize-loader) 也是一个处理webwoker资源的加载器，提供另一种加载方式


3. worker 与 主线程之间传输大文件时，可以采用 Transferable Objects 提升传输性能，但存在一些限制, 详情见[Transferable Objects: Lightning Fast!](http://updates.html5rocks.com/2011/12/Transferable-Objects-Lightning-Fast)