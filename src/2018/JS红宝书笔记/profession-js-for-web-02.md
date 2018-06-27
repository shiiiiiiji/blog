## 基本概念（ECMAScript 3）
- 区分大小写
- ECMAScript的变量是**松散**类型的（可以用来保存任何类型的数据），每一个变量仅仅是一个**用于保存值的占位符**而已。
- 变量的定义与初始化
    - 声明、定义、初始化、赋值https://www.zhihu.com/question/27639400
    - 声明后未经初始化的变量会保存一个特殊的值`undefined`
    - 使用`var`操作符定义的变量将成为定义该变量的作用域中的局部变量
    - 省略`var`操作符定义并初始化的变量为全局变量（不推荐这种方式）

- ECMAScript 数据类型
    - 5种简单数据类型（基本数据类型）
        - Undefined
        - Null
        - Boolean
        - Number
        - String
    - 1种复杂数据类型
        - Object

- ECMAScript 不支持任何创建自定义类型的机制，所有值最终都将是上述6种数据类型之一。

- `typeof`操作符(不是函数！！！)
| 未定义变量/未初始化变量/Undefined类型值（undefined） | "undefined" |
| Boolean类型值 | "boolean" |
| Number类型值 | "number" |
| String类型值 | "string" |
| Null类型值（null）/Object类型值（除Function） | "object" |
| Function | "function" |

- Undefined 类型
    - 只有一个值：undefined
    - vs 定义未初始化变量
        - 使用var声明变量但未对其加以初始化时，变量值即为undefined
        - 没有必要显式初始化变量值为undefined
    - vs 未定义变量
        - 只可进行 typeof / delete 操作（delete无意义，严格模式报错）
        - 技术角度有本质区别，但逻辑角度有合理性

- Null 类型
    - 只有一个值：null
    - 逻辑上表示一个**空对象指针**
    - undefined == null // true
    - 只要意在保存对象的变量还没有真正保存对象，就有必要显式初始化变量值为 null

- Boolean 类型
    - 只有两个字面值：true 和 false
    - 所有其他类型的值都可以通过 Boolean() 方法转换成 Boolean 类型的值
| Boolean 类型 | true | false |
| Undefined 类型 | - | false |
| Null 类型 | - | false |
| Number 类型 | 非零数字值（包括无穷大） | 0 和 NaN |
| String 类型 | 非空字符串 | ""（空字符串） |
| Object 类型 | 任何对象 | - |

- Number 类型
    - IEEE 754 格式：整数和浮点数
    - 浮点数值在进行算数计算时，无法保证其精确度（IEEE 754 数值格式通病）
    - 数值范围
        - Number.MIN_VALUE
        - Number.MAX_VALUE
        - 超出范围的数值将会自动转换成特殊的**Infinity**值（**不能参与计算，值为NaN**）
            - Infinity（Number.NEGATIVE_INFINITY，正无穷）、-Infinity（Number.POSITIVE_INFINITY，负无穷）
            - isFinite() 方法
    - NaN：非数值（Not a Number）是一个特殊的数值
        - 用于本来返回数值的操作数未返回数值的情况（不会抛出错误导致程序停止）
        - NaN 参与任何计算，都会返回 NaN
        - NaN 与任何值都不相等（包括 NaN 本身）
        - isNaN() 方法在接收到一个参数后会尝试将其转换为数值
    - 数值转换
        - Number() - 用于任何数据类型
            - 如果是 Undefined 类型， undefined -> 0
            - 如果是 Null 类型， null -> 0
            - 如果是 Boolean 类型， true -> 1， false -> 0
            - 如果是 String 类型：
                - 只包含有效数值（包括Infinity和NaN） -> 十进制（八进制前面的0会被忽略，十六进制会转换为十进制）
                - 空字符串 -> 0
                - 包含其他字符 -> NaN
            - 如果是 Object 类型：
                - 首先调用对象的 valueOf() 方法，再按照上述规则
                - 如果转换结果是 NaN， 则调用对象的 toString() 方法，再按照上述规则
            
        - parseInt() - 用于字符串类型
            - 忽略字符串前面的空格，直至找到**第一个非空格字符**：
                - 如果第一个非空格字符串 -> NaN
                - 一直解析到所有后续字符或者遇到一个非数字字符（包括负号和小数点），然后按照各种整数格式（十进制、八进制、十六进制） -> 十进制
                - 可以指定基数作为第二个参数（此时进制前置符号可忽略）

        - parseFloat() - 用于字符串类型
            - 只解析十进制，无第二个基数参数
            - 结果如果为整数，则会返回整数
            - 第二个小数点无效

- String 类型
    - ECMAScript 中字符串不可变，一旦创建，值就不能改变。要改变某个变量保存的字符串，首先要销毁原来的字符串，然后再用另一个新值的字符串填充该变量。
    - 转换为字符串
        - toString()，**除 null 和 undefined 外，所有值都有 toString() 方法**，返回相应值的字符串表现
        - 转型函数 String()
            - null -> "null"
            - undefined -> "undefined"
            - toString()
        - +"" // 加字符串

- Object 类型
    - Object 类型的每个实例（“对象”）都具有下列属性和方法
        - constructor：保存着用于创建当前对象的**函数**，即构造函数。
        - hasOwnProperty(<propertyName>)：用来检查给定的属性在当前对象实例中（而不是在对象实例的原型中）是否存在
        - isPrototypeOf(<object>)：用于检查传入的对象是否是当前对象的原型
        - propertyIsEnumberable(<propertyName>)：用于检查给定的属性是否能够使用 for-in 语句来枚举
        - toLocalString()：返回对象的字符串表示
        - toString()：返回对象的字符串表示
        - valueOf()：返回对象的字符串、数值或布尔值表示
    