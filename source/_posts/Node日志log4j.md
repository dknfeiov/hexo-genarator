---
title: Node log4j模块记录日志
date: 2018-12-21 18:07:23
tags: Node
---


#### 一.配置
    
> 常规配置, 封装logger方法，logger()调用后打印到控制台，并输出日志到文件

```javascript

    const log4js = require('log4js');

    log4js.configure({
        // 定义 appender
        appenders: {
            // 打印到控制台
            console: { type: 'console' },
            // 输出日志到文件
            record: { type: 'file', filename: process.cwd() + '/logs/out.log', maxLogSize: '2M' },
        },
        categories: {
            console: { appenders: ['console'], level: 'debug' },
            record: { appenders: ['record'], level: 'debug' },
            // default 必须有
            default: { appenders: ['console'], level: 'debug' }
        },
        // 将普通 console 输出替换成 log4j info 格式
        replaceConsole: true
    });

    const recordLog = log4js.getLogger('record');
    const consoleLog = log4js.getLogger('console');

    module.exports = function logger(funcName, msg) {
        recordLog[funcName](msg)
        consoleLog[funcName](msg)
    }

```



#### 二.使用示例

```javascript

    const logger = require('./utils/logger')

    // 全局异常记录
    process.prependListener('uncaughtException', (err) => {
        logger('error', `捕获到异常：${err.stack}\n`)
    });

    process.prependListener('unhandledRejection', (reason, p) => {
        logger('error', `未处理的 rejection：${err.stack}\n`)
    });

```

#### 三. 其他

    [参考1](https://angular.cn/guide/ajs-quick-reference) 

    [库github链接](https://github.com/log4js-node/log4js-node) 