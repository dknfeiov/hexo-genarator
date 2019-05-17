

## 使用 prerender（预呈现）加快网页访问速度


### 一、前言
如何加速网页访问速度？除了常见的gzip压缩、文件bundle、各级缓存等等，还有没有其他的方式呢？
今天了解了一下 [Resource Hints](https://www.w3.org/TR/resource-hints/#dfn-prerender)，一种浏览器预操作机制，草案定义了 dns-prefetch, preconnect, prefetch, and prerender等多种预操作，在一个页面访问之前，预先做一些工作, 如DNS预解析、HTTP预连接、资源预加载、页面预呈现等，当页面真正加载时，可以更快的呈现出来。


### 二、核心

#### 1. prerender 基本介绍
上述多种预操作之中，prerender（预呈现）是其中提升访问速度最明显的，假定我们已经知道用户下一个将要访问的页面地址 http://www.xxx.com.cn，我们通过当前页通过以下方式提前加载页面
```html
    <link rel="prerender" href="(http://www.xxx.com.cn)">
```
浏览器加载并渲染页面，设置页面状态为预呈现（此时页面不可见），当用户真正访问时，浏览器变更页面的状态为可见，将其迅速呈现出来，页面秒速响应。

> 注意： 渲染一个页面对浏览器而言是一个昂贵的操作，因为并不是每一次都会去做预处理，包括但不限于以下情况，预处理将被中止。

* 当资源有限时，防止启动预渲染。
* 由于高成本或资源需求而放弃预渲染 - 例如高CPU或内存使用，昂贵的数据访问等等。
* 由于所获取内容的类型或属性而放弃预渲染：
* 如果目标表现出非幂等行为：共享本地存储的突变，带有除GET，HEAD或OPTION之外的动词的`XMLHttpRequest`，依此类推。
* 如果目标触发需要用户输入的条件：确认对话框，身份验证提示，警报等。


#### 2. prerender 兼容性
因为还处于工作草案状态，各浏览器对草案支持不一，prerender 目前只有Chrome支持的比较好，具体见下图:
![](https://dknfeiov.github.io/images/files/prerender/prerender-broswer.PNG) 



#### 3. prerender 实例
因为 prerender 涉及到 [页面可见状态](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/visibilityState) 的变更, 当页面处于预加载状态时，document.visibilityState（全局只读属性） 取值是 'prerender', 当页面真正呈现时，值变更为 'visible', 当预加载的时候，我们希望加载静态资源，但不希望在此时去执行一些业务代码、
因为浏览器在预加载状态会对一些操作进行限制，比如不允许变更存储状态（localStorage）等，因此，最好的方式是将业务代码延迟到页面呈现时再执行。


以我现在正在做的一个Vue项目为例：

>首先，新建一个 test.html 页面，一个单独的测试网页，用于发起prerender
```html 
    <!-- src 需要是完整的URL地址（包括http://） -->
    <link rel="prerender" href="http://10.18.200.239:7100">
```

以下是原始业务代码入口，现在我们要对它进行修改
```javascript
    // main.js Vue 项目js入口
    new Vue({
        router,
        store,
        render: h => h(App)
    }).$mount('#app');
```

监听 visibilitychange 事件，当页面可见状态由 prerender 变更为 visible 时，再对进行业务代码初始化。
```javascript
    // main.js Vue 项目js入口

    // 业务代码初始化
    function init() {
        new Vue({
            router,
            store,
            render: h => h(App)
        }).$mount('#app');
    }

    function visibleChange() {
        // 页面状态从 prerender 变更为 visible ，再进行业务代码初始化
        if (document.visibilityState === 'visible') {
            init(); 
            document.removeEventListener('visibilitychange', visibleChange);
        }
    }

    // 监听页面状态变更
    if (document.visibilityState === 'prerender') {
        document.addEventListener('visibilitychange', visibleChange);
    } else {
        // 不经过prerender ,普通方式加载
        init();
    }
```


因为启动的是本地nginx服务，服务器文件就在本机，响应速度很快，不管是否进行预处理，页面响应速度变化不明显，所以我将Chrome网络限制为 Fast 3G，以下是对照实验：

> 方案1：清除浏览器缓存，打开浏览器Tab页, 调整网速， 直接在浏览器访问本地项目 http://10.18.200.239:7100 , 页面加载耗时约10s

![](https://dknfeiov.github.io/images/files/prerender/data-insight-no-cache.PNG) 


> 方案2：
清除浏览器缓存，请求测试页 http://localhost:8080/test.html , 发起prerender请求, 打开新Tab页, 调整网速，输入项目地址 http://10.18.200.239:7100 ，页面加载耗时约2s, 从下图中可以看到，js、css等资源都是从缓存中加载（from disk cache）, 速度从10s 到 2s , 也就是说，我们给用户初次访问速度带来 400% 提升，但注意，实际情况可能达不到，还是看具体情况而定。

![](https://dknfeiov.github.io/images/files/prerender/data-insight-prerender.PNG) 



#### 4. prerender 新变化

如果想要知道 prerender 请求是否成功，可以在 chrome://net-internals/#prerender 查看
![](https://dknfeiov.github.io/images/files/prerender/prerender-internal.PNG) 


上图可以看到 Prerender History 表格，记录了发起的prerender请求列表

1. *Origin:*  Link Rel Prerender (same domain) 标识这个请求是我们发起的，而 Omnibox 标识是由Chrome浏览器自己发起的，Chrome浏览器为了提升页面访问速度，当用户在地址栏输入地址时，有时也会发起prerender

2. *URL:* 目标url

3. *Final Status：*  最终状态，常用值有 Timed Out超时、 Duplicate 重复、 Cancelled 取消、 [NoStatePrefetch Finished](https://developers.google.cn/web/updates/2018/07/nostate-prefetch)（成功）

自Chrome 63开始，prerender底层的机制发生了变化，表现上也出现了一些差异，但整体来说并不影响使用，底层使用 [NoStatePrefetch](https://developers.google.cn/web/updates/2018/07/nostate-prefetch) 取代原有的prerender底层机制, 

> 上面的示例同时在 chrome41 和 chrome47 上进行了测试，都给访问速度带来了额外的提升，以上的写法也是完全兼容的，具体详情见下方参考
 




#### 参考
[Introducing NoState Prefetch](https://developers.google.cn/web/updates/2018/07/nostate-prefetch)
[Resource Hints](https://www.w3.org/TR/resource-hints/#prerender)
[详解HTML5中rel属性的prefetch预加载功能使用](https://www.cnblogs.com/suyuwen1/p/5506397.html)
[Document​.visibility​State](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/visibilityState)