---
title: Angular-React比较
date: 2018-05-26 11:28:58
tags: Angular
---



### 概述
    
    我对前端框架的看法是，同一个问题的多种解决方案，每个方案都有各有适应的场景和人群，但既然解决的是同一个问题，殊途同归，本文就是根据自己的使用经验，列举出Angular、React二者在不同中的相通之处

> *示例代码皆为伪代码，仅供理解*

### 脉络

> 前端现代框架的一些基本逻辑:
1. 组件化, 从原来代码级的复用提升到组件层，组件抽象出可复用的功能与UI，就像一个个零件

2. 数据驱动，隐藏DOM操作的细节，页面工程化，整个页面就是一个大的机器，组件就是组成机器一个个的齿轮，受内外部数据驱动，影响周围的齿轮随之发生变化

3. 工程化，集成脚手架等插件，使用路由统一入口



### 细节


##### 一. Angular

> **关键词：**　模块化、组件化、依赖注入、脏检查

1. Angular应用是模块化的，一个NgModule（模块）就是一个容器，存放一些内聚代码块，如组件、指令、服务、管道等，提供导入、导出功能，每个Angular应用都会有一个根模块，引导根模块启动项目

2. 组件可以通过路由器加载，也可以通过模板创建，组件是指令的一种，是带模版的指令，除了组件外，指令还有结构型指令和功能型指令二种，指令拥有生命周期钩子函数。在生命周期不同阶段被调用

3. 依赖注入机制，注入器是树形结构，注入的服务是单例，子模块中定义的provider将被注册到根注入器，除非子模块为懒加载，懒加载模块单独生成一个注入器，除此之外，组件也拥有组件级注入器

4. Angular脏检查由ngZone触发，zone重写了一系列异步方法，如事件、定时器、封装了ajax请求，添加了回调钩子函数，在回调中进行数据变更检查，为组件设置onPush策略，组件将只在Input数据发生变化时触发检查，组件可注入changeDetection手动设置脏检查机制

5. 其他，路由、HttpCLient + 拦截器机制， 视图封装模式（Native、Emulated、None），动态组件、dom操作（Renderer）、Rxjs管理异步事件流



##### 二. React
>　**关键词：** 组件化、函数式编程、虚拟dom

1. React 维护一颗组件树， 数据通过props从上往下单向流动，组件内部通过state保存状态，通过setState改变state的值，默认当props或state发生变化，组件重新渲染，PureReactComponent组件默认判断新旧props,state（浅引用判断）值，如果没有变化，不重新渲染，但过多diff操作同样消耗性能， 普通Component 可通过使用 shouldComponentUpdate生命周期钩子，返回true|false 来决定是否重新渲染组件，结合 immutable 对象可以更好的对性能进行优化

2. React 组件有3种创建方式（函数式、ES6、ES5），函数式无状态组件拥有更好的性能,每次重新渲染相当于重新执行一次函数,但如果需要用到state 、组件生命周期，则需要使用ES6方式, 通过ES5语法创建现已不推荐使用, 

3. jsx-函数式编程，可以用js来构建视图，可以使用临时变量、自带的流程控制、js当前作用域等，相比较于Angular的模板（Template）更为灵活

4. react利用key来识别组件，它是一种身份标识标识，当key发生变化，销毁原有组件，创建新组件，key没有变化，数据变化，重新渲染原组件




### 二者比较

1. 数据流向： Angular 数据从上往下，单向流动，通过Output自定义事件与父组件通信 ， React 通过props从上往下，数据单向流动，通过state保存当前组件内部的状态，通过setState改变组件内部状态，与父组件通信通常通过状态提升来完成，常结合redux/mobx使用

2. 不可变性：因为数据单向流动，所有子组件从父级接收的数据是不可变的（@Input 、props），Angular如果要对@Input传递的数据进行处理，可以用set去实现

3. Angular使用模板语法，通过结构型指令如*ngIf、*ngFor 实现条件渲染、列表展示, React通过jsx语法实现同样的功能，如下
```javascript
    var && <div>、datas.map(i,v)
```

4. Angular 表单提供二种方式，模板驱动与响应式表单，前者通过模板语法与指令实现，后者由模型驱动，预先定义字段、校验项、初始值，提供校验方法，React 表单一般通过受控组件的方式实现

5. React 组件的props.children 类似于 Angular 的内容映射(通过ng-content指令实现)

6. 避免重复渲染，性能优化，React 通常使用 shouldComponentUpdate + Immutable 对象减少重复渲染， Angular通常设置 changedetectionstrategy.onpush + Immutable 对象

7. 集成第三方库时，常需要获取Dom对象，React使用 refs传递，Angular 通过ViewChild实现对dom节点的引用

```javascript
React : 
    <div ref={domRef}> // 通过this.domRef获取dom节点

Angular : 
    .ts:
        ViewChild(selector) domRef
    .html:
        <div seletor>
```

8. Angular 相对React ，概念更多，给出了一个更复杂的解决方案，但同时，Angular提供了React没有的模块化、提供成体系的前端技术解决方案、引入typescript进行类型定义，对于大型协作项目的团队式开发和长期维护，是一个不错的选择，但正因为想要做的事情太多，不得不引入大量抽象概念，前期需要花费更多的时间

9. React 虽只提供了简单的API，但结合JSX语法，非常灵活且强大，但是灵活意味着容易犯错，相比较Angular，更容易把代码写的难以维护，对开发者个人的能力要求更高

