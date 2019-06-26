积累一定基础，更好的学习方式是去学习优秀的代码



2. Vue core 源码解析

```bash

│  config.js    # Vue 全局 config (silent、devtools等)
│  index.js     
│
├─components
│      index.js
│      keep-alive.js
│
├─global-api
│      assets.js    # Vue component()、directive()、filter() 方法注册    func() => vueInstance.options.components
│      extend.js    # Vue extend() Vue Class 继承
│      index.js     # initGlobalAPI  => Global init entry
│      mixin.js     # Vue mixin() 
│      use.js       # vue ues（）
│
├─instance
│  │  events.js     # events mixin , vm._events 记录事件， eventsMixin() 为Vue.prototype 添加事件方法（$on、$emit等）, initEvents() updateComponentListeners()
│  │  index.js      # define Vue function , Instance mixin entry
│  │  init.js       # init mixin , 为Vue.prototype 添加 _init ，vue instance 初始化方法
│  │  inject.js
│  │  lifecycle.js
│  │  proxy.js      # 通过 Proxy 对属性的设置和获取做一些校验
│  │  render.js     
│  │  state.js      # state mixin ,  为Vue.prototype 添加（$data、$props、$set、$delete、$watch）  initState(vm) 初始化 props、methods、data、computed、watch
│  │
│  └─render-helpers
│          bind-dynamic-keys.js
│          bind-object-listeners.js
│          bind-object-props.js
│          check-keycodes.js
│          index.js
│          render-list.js
│          render-slot.js
│          render-static.js
│          resolve-filter.js
│          resolve-scoped-slots.js
│          resolve-slots.js
│
├─observer
│      array.js         # Array 方法hack, 数组调用push、splice等方法时，调用 dep.notity()
│      dep.js           # define Dep(id,subs) static target Watcher ， addSub() removeSub() 添加、移除监听  depend() Target.addDep()  notity() watchers(subs) 执行 update()
│      index.js         # define Observer(value,dep,vmCount) 使一个对象可被检测 => vlaue.__ob__ = observer , 暴露方法 => defineReactive() 使一个属性可被检测 observe() 使一个对象可被检测
│      scheduler.js     # watcher队列 activatedComponent队列  flushSchedulerQueue()清空watcher调用队列 => watcher run() , watcher._vm.updated()
│      traverse.js      # traverse 深度遍历对象
│      watcher.js       # define Watcher(vm,expr,cb)    expr.call(vm) 获取数据  get()遍历数据收集依赖  add clean dep   依赖变更update() => cb()  teardown() 从vm和deps中移除watcher depend 触发所有deps 中的 depend方法  
│
├─util
│      debug.js
│      env.js
│      error.js
│      index.js
│      lang.js
│      next-tick.js
│      options.js
│      perf.js      
│      props.js     # vm prop  取默认值、check校验等
│
└─vdom
    │  create-component.js
    │  create-element.js
    │  create-functional-component.js
    │  patch.js
    │  vnode.js
    │
    ├─helpers
    │      extract-props.js
    │      get-first-component-child.js
    │      index.js
    │      is-async-placeholder.js
    │      merge-hook.js
    │      normalize-children.js
    │      normalize-scoped-slots.js
    │      resolve-async-component.js
    │      update-listeners.js
    │
    └─modules
            directives.js
            index.js
            ref.js

```


