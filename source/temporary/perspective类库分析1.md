


web worker 是运行在后台的 JavaScript，独立于其他脚本，不会影响页面的性能。不会打断主线程渲染引擎的执行
Web Worker的目就，就是为JavaScript创造多线程环境，允许主线程将一些任务分配给子线程。在主线程运行的同时，子线程在后台运行，两者互不干扰。等到子线程完成计算任务，再把结果返回给主线程


Web Worker有以下几个特点：

同域限制：子线程加载的脚本文件，必须与主线程的脚本文件在同一个域。
DOM限制：子线程无法读限网页的DOM对象，即document、window、parent这些对象，子线程都无法得到。（但是，navigator对象和location对象可以获得。）
脚本限制：子线程无法读取网页的全局变量和函数，也不能执行alert和confirm方法，不过可以执行setInterval和setTimeout，以及使用XMLHttpRequest对象发出AJAX请求。
文件限制：子线程无法读取本地文件，即子线程无法打开本机的文件系统（file://），它所加载的脚本，必须来自网络。


### 1. 概述

    Perspective 流数据可视化引擎，可以在浏览器中实现数据实时分析，由 C++ 编译成WebAssembly  和 asm.js
    Perspective-viewer 基于WebComponent的嵌入式可配置UI




###　2. 核心

    table() 表示单个数据集，是用于将静态和流数据输入到Perspective中的接口，在浏览器中， table() 驻留在Web Worker中以将其运行时与渲染器隔离
    view() 表示对table() 的一个特定连续查询， 用于从table() 中读取和计算分析数据， view() 在浏览器环境下也驻留在 Web Worker 中

    <perspective-viewer> 是一个UI组件，通过drag and drop 与 view() 交互，一个组件同时只能管理一个view() 

    一个 tabel（） 可以对应 多个 view() 






优势： 

    1. 支持多指标、多维度、多字段排序、多字段过滤
    2. 表现良好的UI呈现
    3. 支持自定义指标





1. 自定义指标暂不支持自定义变量输入 eg : 'sales' / 4, 只支持基于指标间的运算 'sales' / 'price'
2. 




维度、指标、排序、过滤


基础能力
1. 对维度、指标等进行拖拽分析
2. 支持grid、Chart多种渲染方式
3. 支持基础指标聚合（max、min、avg、sum等）
4. 支持基础排序功能（针对某个指标进行升序|降序展示）
5. 支持基础过滤功能（针对字段值进行过滤）

拓展功能

支持原始数据预览
支持多维度
支持多指标
支持自定义指标、支持表达式
支持多字段排序
支持多字段过滤
支持维度分区（数值分区、时段分区）


性能

数据量级支持
请求响应时间


其他

可扩展性、可维护性
浏览器兼容性


saiku 
优势： 基础能力、拓展能力基本全部支持，Chart种类支持丰富，预定义指标（数值指标）、层级维度（时间、区域等），支持表达式自定义指标，指标、维度等概念更直观，使用人员上手容易，中文支持一般
劣势： 可拓展性较差，前端基本无法拓展功能，不支持原始数据预览，需要数仓人员预定义指标、维度等，前期工作量大，部分操作如过滤、排序、钻取等繁琐且不直观

pivotTable
优势： 基础能力全支持，操作简单灵活，支持数据预览，支持多维度、自定义指标、排序、过滤、维度分区
劣势： 最大劣势是不支持多指标，提供的UI组件视觉效果较老旧，可拓展性一般，若使用提供的接口，自己写UI，拖拽、自定义指标、排序、过滤、维度分区等，工作量很大

perspective
优势： 基础能力、拓展能力基本全部支持，UI可视化效果好、性能较高，可直接嵌入使用，核心团队还在不断维护开发中
劣势： 核心模块由C++编译而来，二次开发难度高，部分小bug较多、自定义指标不支持表达式，只支持字段间的计算，不支持维度分区，中文支持需要自己提供











### MDN  WebAssembly 分析

> WebAssembly是一门低级的类汇编语言。它有一种紧凑的二进制格式，使其能够以接近原生性能的速度运行，并且为诸如C++和Rust等拥有低级的内存模型语言提供了一个编译目标以便它们能够在网络上运行
> WebAssembly是一种运行在现代网络浏览器中的新型代码，并且提供新的性能特性和效果, 它为诸如C、C++和Rust等低级源语言提供一个高效的编译目标。提供了一种在网络平台以接近本地速度的方式运行多种语言编写的代码的方式

> WebAssembly的目标

快速、高效、可移植——通过利用常见的硬件能力，WebAssembly代码在不同平台上能够以接近本地速度运行。
可读、可调试 —— WebAssembly是一门低阶语言，但是它有确实有一种人类可读的文本格式（其标准即将得到最终版本），这允许通过手工来写代码，看代码以及调试代码。
保持安全 —— WebAssembly被限制运行在一个安全的沙箱执行环境中。像其他网络代码一样，它遵循浏览器的同源策略和授权策略。
不破坏网络 —— WebAssembly的设计原则是与其他网络技术和谐共处并保持向后兼容。


> WebAssembly关键概念






> WebAssembly当前不能直接的存取DOM；它只能调用JavaScript，并且只能传入整形和浮点型的原始数据类型作为参数。这就是说，为了使用任何Web API，WebAssembly需要调用到JavaScript，然后由JavaScript调用Web API


> WebAssembly 对象是所有 WebAssembly 相关功能的命名空间

WebAssembly.instantiate()
用于编译和实例化 WebAssembly 代码的主 API，返回一个 Module 和它的第一个实例。
WebAssembly.instantiateStreaming()
直接从流式底层源编译和实例化WebAssembly模块，同时返回Module及其第一个实例。
WebAssembly.compile()
把 WebAssembly 二进制代码编译为一个 WebAssembly.Module ，不进行实例化。
WebAssembly.compileStreaming()
直接从流式底层源代码编译WebAssembly.Module ，将实例化作为一个单独的步骤。
WebAssembly.validate()
校验 WebAssembly 二进制代码的类型数组是否合法，合法则返回 true ，否则返回 false 。


```javascript

    var importObject = {
        imports: {
            imported_func: function(arg) {
                console.log(arg);
            }
        }
    };

    fetch('simple.wasm').then(response =>
        response.arrayBuffer()
    ).then(bytes =>
        WebAssembly.instantiate(bytes, importObject)
    ).then(result =>
        result.instance.exports.exported_func()
    );


```