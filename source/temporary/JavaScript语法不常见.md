

1. function 定义简写
2. 没有arguments
3. 内部 this 指向方法被定义的地方
    a. 普通函数，指向window
    b. 构造函数，指向返回的对象
    c. 作为对象属性，指向对象本身
    

箭头函数不绑定自身的 this，而允许局部作用域的方案来沿袭处理之

更简短的函数并且不绑定this。

箭头函数不会创建自己的this,它只会从自己的作用域链的上一层继承this

箭头函数this为父作用域的this，不是调用时的this


Map 浏览器兼容性好

Map 对象保存键值对。任何值(对象或者原始值) 都可以作为一个键或一个值。




String.replace() 一次只替换一个 要加/g 才能替换所有  ？






const where = () => {
    let reg = /\s+at\s(\S+)\s\(/g
    let str = new Error().stack.toString()
    let res = reg.exec(str)
    return res && res[1]
}

var err = new Error("my error");  
Error.stackTraceLimit = Infinity;  
throw err;

这样可以看到从最顶层到被调用函数外部函数的整条调用链。




Map 对象保存键值对。任何值(对象或者原始值) 都可以作为一个键或一个值， 相比 Object 的优势如下：
    1. 一个Object的键只能是字符串或者 Symbols，但一个 Map 的键可以是任意值，包括函数、对象、基本类型。
    2. Map 中的键值是有序的，而添加到对象中的键则不是。因此，当对它进行遍历时，Map 对象是按插入的顺序返回键值。
    3. 你可以通过 size 属性直接获取一个 Map 的键值对个数，而 Object 的键值对个数只能手动计算。
    4. Map 可直接进行迭代，而 Object 的迭代需要先获取它的键数组，然后再进行迭代。
    5. Object 都有自己的原型，原型链上的键名有可能和你自己在对象上的设置的键名产生冲突。虽然 ES5 开始可以用 map = Object.create(null) 来创建一个没有原型的对象，但是这种用法不太常见。
    6. Map 在涉及频繁增删键值对的场景下会有些性能优势。


Set 对象允许你存储任何类型的唯一值，无论是原始值或者是对象引用
    new Set([1,2,4,1]) 数字数组快速去重


WeakMap对象也是键值对的集合。它的键必须是对象类型，值可以是任意类型。它的键被弱保持，也就是说，当其键所指对象没有其他地方引用的时候，它会被GC回收掉
WeakSet对象是一组对象的集合。WeakSet中的值必须是对象类型，不可以是别的类型，不重复且不可枚举，集合中的对象，如果不存在其他引用，那么该对象将可被垃圾回收
    WeakMap对象的一个用例是存储一个对象的私有数据或隐藏实施细节
    WeakSet的用例很有限，比如使用DOM元素作为键来追踪它们而不必担心内存泄漏。


Map的键和Set的值的等值判断
    判断使用与===相似的规则。
    -0和+0相等。
    NaN与自身相等（与===有所不同）。



toFixed函数不可靠 ?



JSON.stringify

    不可枚举的属性会被忽略
    所有以 symbol 为属性键的属性都会被完全忽略掉


Object.keys() 方法会返回一个由一个给定对象的自身可枚举属性组成的数组,数组中属性名的排列顺序和使用 for...in 循环遍历该对象时返回的顺序一致
    {不包含原型中的可枚举属性}


for...in
    for...in语句以任意顺序遍历一个对象的可枚举属性。对于每个不同的属性，语句都会被执行。
    循环将遍历对象本身的所有可枚举属性，以及对象从其构造函数原型中继承的属性(注意：包括原型中的可枚举属性)



Shadow DOM API的 ShadowRoot接口是一个DOM子树的根节点, 它与文档的主DOM树分开渲染。

ShadowRoot.mode 只读
ShadowRoot 的模式:是 "open" 或者 "closed".
将 ShadowRoot 的模式(mode) 设置为 "closed" 会让该 ShadowRoot 的内部实现无法被 JavaScript 访问及修改 — 也就是说将该实现不公开，例如，<video> 标签内部实现无法被 JavaScript 访问及修改。




getComputedStyle
window.ShadyCSS.getComputedStyleValue
getPropertyValue
getPropertyCSSValue
getAttribute

currentStyle
style