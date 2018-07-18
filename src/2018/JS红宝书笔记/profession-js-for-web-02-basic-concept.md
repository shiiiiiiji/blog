## 基本概念（ECMAScript 3）
**区分大小写。**

标识符：指变量、函数、属性的名字，或者函数的参数。命名要求：
- 第一个字符必须是一个字母、下划线（_）或一个美元符号（$）
- 其他字符可以是字母、下划线、美元符号或数字
（Question：为什么数字不可以？）

注释：C风格

关键字和保留字
- 关键字：用于表示控制语句的开始或结束，或者用于执行特定操作等。
- 保留字：暂时无特定用途，但将来可能用作关键字。
关键字和保留字均不能用作标识符。

ECMAScript的变量是**松散**类型的（可以用来保存任何类型的数据），每一个变量仅仅是一个**用于保存值的占位符**而已。

变量的定义与初始化
- 声明、定义、初始化、赋值https://www.zhihu.com/question/27639400
- 声明后未经初始化的变量会保存一个特殊的值`undefined`
- 使用`var`操作符定义的变量将成为定义该变量的作用域中的局部变量
- 省略`var`操作符定义并初始化的变量为全局变量（不推荐这种方式）

ECMAScript 数据类型
- 5种简单数据类型（基本数据类型）
    - Undefined
    - Null
    - Boolean
    - Number
    - String
- 1种复杂数据类型
    - Object

ECMAScript 不支持任何创建自定义类型的机制，所有值最终都将是上述6种数据类型之一。（ES6新定义了Symbol类型、Set类型、Map类型）

`typeof`**操作符**(不是函数！！！)
| 未定义变量/未初始化变量/Undefined类型值（undefined） | "undefined" |
| Boolean类型值 | "boolean" |
| Number类型值 | "number" |
| String类型值 | "string" |
| Null类型值（null）/Object类型值（除Function） | "object" |
| Function | "function" |

### Undefined 类型
- 只有一个值：undefined
- 未定义变量 vs 定义未初始化变量
未定义变量：
- 只可进行 typeof / delete 操作（delete无意义，严格模式报错）
- 技术角度有本质区别，但逻辑角度有合理性
定义未初始化变量
- 使用var声明变量但未对其加以初始化时，变量值即为undefined
- 没有必要显式初始化变量值为undefined

### Null 类型
- 只有一个值：null
- 逻辑上表示一个**空对象指针**
- undefined == null // true
- 只要意在保存对象的变量还没有真正保存对象，就有必要显式初始化变量值为 null

### Boolean 类型
- 只有两个字面值：true 和 false
- 所有其他类型的值都可以通过 Boolean() 方法转换成 Boolean 类型的值
| Boolean 类型 | true | false |
| Undefined 类型 | - | false |
| Null 类型 | - | false |
| Number 类型 | 非零数字值（包括无穷大） | 0 和 NaN |
| String 类型 | 非空字符串 | ""（空字符串） |
| Object 类型 | 任何对象 | - |

### Number 类型
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

### String 类型
- ECMAScript 中字符串不可变，一旦创建，值就不能改变。要改变某个变量保存的字符串，首先要销毁原来的字符串，然后再用另一个新值的字符串填充该变量。
- 转换为字符串
    - toString()，**除 null 和 undefined 外，所有值都有 toString() 方法**，返回相应值的字符串表现
    - 转型函数 String()
        - null -> "null"
        - undefined -> "undefined"
        - toString()
    - +"" // 加字符串

### Object 类型
- Object 类型的每个实例（“对象”）都具有下列属性和方法（**均存在于Object.prototype**）
    - constructor：保存着用于创建当前对象的**函数**，即构造函数。
    - hasOwnProperty(<propertyName>)：用来检查给定的属性在当前对象实例中（而不是在对象实例的原型中）是否存在
    - isPrototypeOf(<object>)：用于检查传入的对象是否是当前对象的原型
    - propertyIsEnumberable(<propertyName>)：用于检查给定的属性是否能够使用 for-in 语句来枚举
    - toLocaleString()：返回对象的字符串表示
    - toString()：返回对象的字符串表示
    - valueOf()：返回对象的字符串、数值或布尔值表示

### 操作符
- 一元操作符
    - 递增/减操作符
        - 会修改自身的值（无论前置还是后置），且值为 Number 类型
        - 适用于整数、浮点数、布尔值、字符串、对象
            - Boolean 类型：true -> 1, false -> 0，然后再进行自增/减操作
            - String 类型：
                - 只包含有效数值字符：-> 先转换成数字值，然后再进行自增/减操作
                - 不包含有效数值字符：-> NaN
            - Object 类型：
                - 先调用对象的 valueOf() 方法，以取得一个可供操作的值，然后应用上述规则；
                - 如果上一步结果是 NaN， 再调用对象的 toString() 方法， 再应用上述规则。
        - 不适用包括 null, undefined, NaN, Infinity，实测操作 null 会报错， undefined 和 NaN 结果为 NaN， Infinity 结果为 Infinity
    - 一元加/减操作符
        - 一元加
            - 数值类型值：无影响
            - 非数字类型值：== Number()
        - 一元减
            - 主要用于表示负数
            - 其他规则同上
    - 位操作符

- 布尔操作符（可适用于任何类型值）
    - 逻辑非`!`：可用于将一个值转换为与其对应的布尔值，实际规则 == Boolean()
    - 逻辑与`&&`：**返回结果不一定是布尔值！！！**
        - 短路运算符
        - 第一个操作数布尔值为 true，返回第二个操作数
        - 第一个操作数布尔值为 false，返回第一个操作数
    - 逻辑或`||`：与逻辑或规则相反

- 乘性操作符
    - 乘法、除法和求模
    - 非数值类型参与运算会自动进行类型转换（Number()）

- 加性操作符
    - 加法
        - 可用于字符串拼接和转换（'' + x）
            - x 为对象、数值或布尔值，则 -> x.toString()
            - x 为 undefined 或 null， 则 -> String(x)
    - 减法
        - 可用于数值转换（num1 - x，只要有一非数值类型）
            - x 为字符串、布尔值、null 或 undefined，则 -> Number(x)
            - x 为对象，则 -> x.valueOf() 取得表示该对象的数值。如果为 NaN，则结果为 NaN。**只有在没有 valueof() 方法时，则 -> toString() 方法得到的字符串转换为数值。

- 关系操作符
    - 按照以下规则：如果两个操作数，
        - 都是数值，则执行数值比较
        - 都是字符串，则比较字符串对应的字符编码值
        - 有一数值，则将另一操作数转换为数值，再执行数值比较
        - 有一对象，则调用其 valueOf() 方法，如果没有，则调用 toString() 方法
        - 有一布尔值，则将其转换为数值
    - 注意：任何操作数与 NaN 进行关系比较，结果都是 false。

- 相等操作符
    - ==，按照以下规则：如果两个操作数，
        - 有一布尔值，则 -> 数值
        - 一为字符串，一为数值，则字符串 -> 数值
        - 一为对象，另一不是对象，则 对象.valueOf()，用得到的基本类型值按照前面的逻辑进行比较
        - 都是对象，比较是不是同一个（是否都指向同一个对象）
        - 特例
            - null == undefined // true
            - null 和 undefined 不能转换成其他任何值
            - 有一为 NaN，则 x == NaN 返回 false，x != NaN 返回true，NaN == NaN 返回 false， NaN != NaN 返回 true。
    - ===

- 条件操作符：?:
- 赋值操作符
- 逗号操作符
    - 用于声明多个变量
    - 用于赋值，**返回表达式最后一项**

### 语句
- if
- do-while
- while
- for
- for-in
    - 枚举对象属性
    - ECMAScript 对象属性没有顺序，因此 for-in 循环输出的属性名的顺序是不可预测的
- label
    - 可由 break 或 continue 引用，一般都要与循环语句配合使用。（多在返回多层循环时使用）
- break 和 continue
- with
    - 将代码的作用域设置到一个特定的对象中
- switch
    - 比较值时使用的是全等操作符，不会发生类型转换
    - switch 语句中可以使用任何数据类型
    - case 的值不一定是常量，也可以是变量，甚至是表达式

### 函数
- 执行完 return 语句之后停止并立即退出，之后的任何代码都不会执行
- return 语句可以不带任何返回值，默认 undefined
- ECMAScript 函数中的参数在内部是用一个数组来表示的。在函数体内可以通过`arguments对象`（并不是Array实例）来访问这个参数数组
- arguments 的值永远与对应`命名参数`的值保持同步（但并不是相同的内存空间）
- arguments 对象的长度（length）是由传入参数的个数决定的，不是由定义函数时的命名参数的个数决定的
- ECMAScript 函数没有签名（因为其参数是由包含零或多个值的数组来表示的），所以无法实现真正意义上的**重载**。不过通过检查传入参数的类型和数量并作出不同的反应，可以模仿方法的重载
- JavaScript 核心语言特性在 ECMA-262 中是以名为 ECMAScript 的伪语言的形式来定义的。包含了所有基本的语法、操作符、数据类型以及完成基本的计算任务所必需的对象，但没有对取得输入和产生输出的机制作出规定。