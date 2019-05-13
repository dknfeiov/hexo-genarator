
1. JavaScript 6 种基础数据类型， 所有基本类型的值都是不可改变的。

    string，number，boolean，null，undefined，symbol

1. JavaScript 语言规定了 7 种语言类型。

    Undefined 
    Null
    Symbol
    String
    Number
    Boolean
    Object


2. JavaScript 是一种弱类型或者说动态语言。这意味着你不用提前声明变量的类型，在程序运行过程中，类型会被自动确定。这也意味着你可以使用同一个变量保存不同类型的数据


4. 
    number : 根据 ECMAScript 标准，JavaScript 中只有一种数字类型：基于 IEEE 754 标准的双精度 64 位二进制格式的值（-(2^63 -1) 到 2^63 -1）,它并没有为整数给出一种特定的类型。除了能够表示浮点数外，还有一些带符号的值：+Infinity，-Infinity 和 NaN (非数值，Not-a-Number)

    同样根据浮点数的定义，非整数的 Number 类型无法用 ==（=== 也不行） 来比较

    ```javascript 

        console.log( 0.1 + 0.2 == 0.3);     // false

        console.log(Math.abs(0.1 + 0.2 - 0.3) <= Number.EPSILON);  // true

    ```


5. object

    Object存储和查找一个属性的方式：

    a. 属性不超过128个，使用Search Cache，
    b. 当属性是较为连续的数字时，使用数组，此种方式最快。
    c. 其它情况使用哈希表，并且数字和字符串的哈希不一样。

    可以把Object当成哈希map使用，但是在效率上可能会比不上ES6的Map，并且还有Object原型查找的问题。