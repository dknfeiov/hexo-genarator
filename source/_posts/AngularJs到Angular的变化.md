---
title: AngularJs到Angular的变化
date: 2017-10-28 11:28:58
tags: Angular, AngularJs
---

>以下只是个人使用过程中的的一些感受,具体二者之间的差别参考 [官方文档](https://angular.cn/guide/ajs-quick-reference) 

1. Typescript es6超集 , 支持 import export , 不需额外引入模块加载器

2. 将组件和指令二者剥离开来，前者组成页面，后者分为结构性指令和功能性指令，不再支持replace等

3. 移除controller, $scope等

4. provider，service,factory 统一为 service, 减少理解上的困惑

5. angularJs 只有一个注入器，所有服务都是单例，Angular的注入器则是一个树状结构，可以为一个注入对象实现不同的实例（在不同的注入器上）

6. controller <=> component 一定意义上等价，都封装了model实体模型，指定html模板,业务逻辑，也都挂靠在模块上

7. 在setTimeout等异步执行环境中不再需要手动调用$scope.$apply() , angular的代码都运行在 zone中，通过为异步方法打补丁的方式，在方法执行完后自动的执行数据检查，如果使用第三方类库，存在异步操作，
想要纳入Angular管理，可以通过以下方式实现

```javascript
    this.ngZone.run(() => {
      this.service.uploadEvent.emit(data);
    });
```



