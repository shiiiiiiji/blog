- 基本包装类型
    - 为了能（更方便）操作基本类型值，ECMAScript还提供了3个特殊的引用类型：Boolean、Number、String
    - 实际上，**每当读取一个基本类型值时，后台就会创建一个对应的基本包装类型的对象，从而能够调用一些方法来操作这些数据**，见下面代码演示：
    ```javascript
    // 对于这段代码
    var s1 = 'test';
    var s2 = s1.substring(2);

    // 其中第2句在执行过程中，对应可以细分为：
    var sTemp = new String('test'); // 创建String类型的一个实例
    var s2 = sTemp.substring(2);    // 在实例上调用指定的方法
    sTemp = null;   // 销毁这个实例
    ```
    - 引用类型与基本包装类型的主要区别就是：**对象的生存期**。
        - **使用new操作符创建**的引用类型的实例，在执行流离开当前作用域之前都一直保存在内存中。
        - **自动创建**的基本包装类型的对象，则只存在那行代码的执行瞬间，然后立即销毁。 => 不能在运行时为基本包装类型值添加属性和方法。
    - Object构造方法也会像**工厂方法**一样，根据传入值的类型返回相应基本包装类型的实例。例：`new Object('test')`，传入null和undefined会返回空对象
    - **使用new调用基本包装类型的构造函数**（返回相应的实例对象，理论上不支持这种方式，因为容易混淆处理的是基本类型值还是引用类型值，导致出现bug） 与 **直接调用同名的转型函数（返回基本类型值）** 是不一样的。
    - Boolean类型
        - 重写了valueOf()（返回true/false）
        - 重写了toString()和toLocaleString()（返回"true"/"false"）
    - Number类型
        - 重写了valueOf()、toString()和toLocaleString()
        - toFixed()
        - toExponential()
        - toPrecision()
    - String类型
        - 写了valueOf()、toString()和toLocaleString()
        - length属性
        - 字符方法
            - charAt()
            - charCodeAt()
            - 使用方括号加数值索引来访问字符串中的特定字符
        - 字符串方法
            - concat()：可接收多个参数，不影响原始字符串值。 => **拼接字符串**，在实际中更多使用**\+号**
            - 基于子字符串创建新字符串，不影响原始字符串值
                - slice()
                - substr()
                - substring()
            - 字符串位置方法
                - indexOf()
                - lastIndexOf()
            - trim()方法，不影响原始字符串值
                - trim()
                - trimLeft()
                - trimRight()
            - 字符串大小写转换方法
                - toLowerCase()、toLocalLowerCase()
                - toUpperCase()、toLocalUpperCase()
            - 字符串的模式匹配方法，等同于RegExp中的exec()方法
                - match()
                - search()
                - replace()
                - htmlEscape()
                - split()
            - localCompare()
            - 静态方法fromCharCode()
            - HTML方法（现在基本不用）
- 单体内置对象
    - 什么是内置对象？
        - 由ECMAScript实现提供的、不依赖于宿主环境的对象，这些对象在ECMAScript程序执行之前就已经存在了，例如Object、Array、String等
        - 不必实例化内置对象，因为它们已经实例化了
    - 两个单体内置对象：Global和Math
        - Global（全局）对象，理论上不属于任何其他对象的属性/方法最终都是Global的属性/方法，例如isNaN()、isFinite()、parseInt()、parseFloat()等
            - 还有：
                - URL编码方法
                    - encodeURI()、encodeURIComponent()
                    - decodeURI()、decodeURIComponent()
                - eval()：“代码注入”风险
            - Global对象的属性
                - 特殊的值：undefined、NaN、Infinity
                - 所有原声引用类型的构造函数
            - window：ECMAScript没有指出如何**直接访问**Global对象，但Window浏览器将这个全局对象作为window对象的**部分**实现。
        - Math对象
            - 属性：Math.E等常量
            - min()和max()方法
                - 接收任意多个数值参数
                - 如果是数组，可借助apply()方法，`Math.max.apply(Math, array)`，这样就可以找到数组的最大/小值
            - 舍入方法
                - ceil()：向上舍入为最接近的整数
                - floor()：向下舍入为最接近的整数
                - round()：四舍五入
            - random() => \[0, 1)
                - \[lowerValue, upperValue)
                - 第一个可能的值：lowerValue
                - 可能值的总数：upperValue - lowerValue + 1
                - 可能值是**连续**的
                - Math.floor(Math.random() * 可能值的总数 + 第一个可能的值
                - Math.floor(Math.random() * (upperValue - lowerValue + 1) + lowerValue)
            - 其他方法：Math.abs()等

- 引用类型小结
    - **对象**在JavaScript中被称为**引用类型的值**。
    - 引用类型与传统OOP中的类相似，但实现不同。（JavaScript能直接访问引用类型/类吗？是用对象来模拟的类？只是名称第一个大写？）
    - Object是一个基础类型，其他所有引用类型都从Object继承了基本的行为。（但使用Object.create(null)创建的对象未继承！是一个空白的对象。这里指的是对象，继承发生在类之间）
    - 基本类型值可以被作为对象来访问（Boolean、Number、String）
    - 每个包装类型都映射到同名的基本类型
    - 在读取模式下访问基本类型值，就会创建对应的基本包装类型的一个对象，从而方便了数据操作。
    - 操作基本类型值的语句一经执行完毕，就会立即销毁新创建的包装对象。
    - 在所有代码执行之前，作用域中就已经存在两个内置对象：Global和Math。（其他内置对象不存在？）