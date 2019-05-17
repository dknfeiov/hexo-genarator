
### 1. 诞生的背景

IndexedDB 是一种在用户浏览器中持久存储数据的方法。它允许您不考虑网络可用性，创建具有丰富查询能力的可离线 Web 应用程序。IndexedDB 对于存储大量数据的应用程序（例如借阅库中的 DVD 目录）和不需要持久 Internet 连接的应用程序（例如邮件客户端、待办事项列表或记事本）很有用

随着浏览器的处理能力不断增强，越来越多的网站开始考虑，将大量数据储存在客户端，这样可以减少用户等待从服务器获取数据的时间。

现有的浏览器端数据储存方案，都不适合储存大量数据：cookie不超过4KB，且每次请求都会发送回服务器端；Window.name属性缺乏安全性，且没有统一的标准；localStorage在2.5MB到10MB之间（各家浏览器不同）。所以，需要一种新的解决方案，这就是IndexedDB诞生的背景

通俗地说，IndexedDB就是浏览器端数据库，可以被网页脚本程序创建和操作。它允许储存大量数据，提供查找接口，还能建立索引。这些都是localStorage所不具备的。就数据库类型而言，IndexedDB不属于关系型数据库（不支持SQL查询语句），更接近NoSQL数据库。



### 术语定义



### 2. 特性

IndexedDB具有以下特点。

（1）键值对储存。 IndexedDB内部采用对象仓库（object store）存放数据。所有类型的数据都可以直接存入，包括JavaScript对象。在对象仓库中，数据以“键值对”的形式保存，每一个数据都有对应的键名，键名是独一无二的，不能有重复，否则会抛出一个错误。

（2）异步。 IndexedDB操作时不会锁死浏览器，用户依然可以进行其他操作，这与localStorage形成对比，后者的操作是同步的。异步设计是为了防止大量数据的读写，拖慢网页的表现。

（3）支持事务。 IndexedDB支持事务（transaction），这意味着一系列操作步骤之中，只要有一步失败，整个事务就都取消，数据库回到事务发生之前的状态，不存在只改写一部分数据的情况。

（4）同域限制 IndexedDB也受到同域限制，每一个数据库对应创建该数据库的域名。来自不同域名的网页，只能访问自身域名下的数据库，而不能访问其他域名下的数据库。

（5）储存空间大 IndexedDB的储存空间比localStorage大得多，一般来说不少于250MB。IE的储存上限是250MB，Chrome和Opera是剩余空间的某个百分比，Firefox则没有上限。

（6）支持二进制储存。 IndexedDB不仅可以储存字符串，还可以储存二进制数据。


1. 兼容性 Chrome 27+、Firefox 21+、Opera 15+和IE 10+支持这个API，但是Safari完全不支持。


###  open 数据库，获取IndexDB实例

1. 新建（打开）数据库，存在则打开，否则新建，第一个参数是数据库名称，格式为字符串，不可省略；第二个参数是数据库版本，是一个大于0的正整数（0将报错），默认值为 1

    var openRequest = indexedDB.open("test",1);

    打开数据库的结果是，有可能触发4种事件。

    - success：打开成功。

    - error：打开失败。

    - upgradeneeded：第一次打开该数据库，或者数据库版本发生变化。

    - blocked：上一次的数据库连接还未关闭。

    第一次打开数据库时，会先触发upgradeneeded事件，然后触发success事件。

```javascript 

    // 打开数据库，为事件添加回调函数
    var openRequest = indexedDB.open("test",1);
    var db;

    openRequest.onupgradeneeded = function(e) {
        console.log("Upgrading...");
    }

    openRequest.onsuccess = function(e) {
        console.log("Success!");
        db = e.target.result;
    }

    openRequest.onerror = function(e) {
        console.log("Error");
        console.dir(e);
    }

```

> 注意： open方法返回的是一个对象（IDBOpenDBRequest），回调函数定义在这个对象上面。其次，回调函数接受一个事件对象event作为参数，它的target.result属性就指向打开的IndexedDB数据库。


### IndexDB实例方法

> indexDB将数据存储在 对象仓库（object store）中， 类似于传统数据数据库中的 Table ，（键名）keyPath 是每条记录的唯一标示

1. 判断表是否存在，如果不存在，创建表

```javascript 

    if(!db.objectStoreNames.contains("firstOS")) {
        // 创建Table， 指定键名(将传入数据的id属性作为键名，传入数据的id不能重复)
        db.createObjectStore("firstOS",{ keyPath: "id" });
    }

    if(!db.objectStoreNames.contains("secondOS")) {
        // 创建Table，使用自动递增的整数作为键名（第一个数据为1，第二个数据为2，以此类推），autoIncrement 默认为false
        db.createObjectStore("secondOS",{ autoIncrement: true });
    }

```

2. 事务、对记录进行增删改查操作

transaction 方法用于创建一个数据库事务。向数据库添加数据之前，必须先创建数据库事务。
transaction 方法接受两个参数：第一个参数是一个数组，里面是所涉及的对象仓库，通常是只有一个；第二个参数是一个表示操作类型的字符串。目前，操作类型只有两种：readonly（只读）和readwrite（读写）。添加数据使用readwrite，读取数据使用readonly。

transaction 方法返回一个事务对象，该对象的objectStore方法用于获取指定的对象仓库。

transaction 方法有三个事件，可以用来定义回调函数。

- abort：事务中断。
- complete：事务完成。
- error：事务出错。


```javascript 

    var transaction  = db.transaction(["firstOS"],"readwrite");
    transaction.oncomplete = function(event) {
        console.log('事务已经建立')
    };
    var store = transaction.objectStore("firstOS");

    var request = store.add(o,1);

```



https://developer.mozilla.org/zh-CN/docs/Web/API/IDBObjectStore 




count()



问题

1. indexDB 将数据存储在硬盘，网页关闭后，数据是否随之销毁？ 
> IndexedDB 是持久的，已经写入硬盘 

2. open 数据库名相同、版本增加，如何处理？
> 触发

3. blocked 事件，event 值, 如何处理

4. 通过事务对象获取 对象仓库，需要在oncomplete 事件触发之后么？

5. 如何批量获取数据 store.getAll()   ，数据量过大时会不会导致内存溢出或者操作中断的情况
> attention: Chrome 48 supported

6. 是否提供批量插入数据的功能，单挑插入性能方面存在不足, 耗时过久事务是否会被浏览器中断
> indexDB 暂未提供批量插入数据的能力，只能单条插入，数据过大时，最好闲时分批处理，避免阻碍渲染，

7. 数据库与后台同步时，如果不使用增量更新，需要将数据库清空再重新导入数据，如何高效的destroy  
> store.clear()

8. openKeyCursor 支持范围么？
> supported [IDBKeyRange](https://developer.mozilla.org/en-US/docs/Web/API/IDBKeyRange)  
 
9. Chrome 支持 persistent 持久存储 indexDB么？， 默认的存储方式是什么  [storage](https://developer.mozilla.org/zh-CN/docs/Web/API/IndexedDB_API/Browser_storage_limits_and_eviction_criteria)

默认是临时存储，持久存储需要用户授权


10. 一个单独的数据库项目的大小没有限制。然而可能会限制每个 IndexedDB 数据库的大小

    - Firefox: 对 IndexedDB 数据库的大小没有限制。在用户界面上只会针对存储超过 50 MB 大小的 BLOB（二进制大对象）请求权限



#### 错误处理

1. The database is not running a version change transaction

    IDBDatabase 接口的 createObjectStore() 方法只能在 versionchange 事务中被调用,  也就是只能在 onupgradeneeded 回调中使用