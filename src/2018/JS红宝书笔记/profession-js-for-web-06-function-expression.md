## 函数表达式
函数声明的一个重要特征就是**函数声明提升**（function declaration hoisting），执行代码之前会先读取函数声明。

函数表达式常见的语法形式：创建一个（匿名）函数并将其赋给变量
```javascript
var fn = function(){
    // 函数体
}
```

匿名函数（拉姆达函数），匿名函数的name属性是空字符串。
```javascript
(function(){}).name === '';
var fn = function(){};
fn.name === 'fn';
```

函数可**作为值**赋给变量。

### 递归
递归函数是在一个函数通过名字调用自身的情况下构成的。

arguments.callee —— 指向正在执行的函数的指针（严格模式会报错）

命名函数表达式。
```javascript
var factorial = (function f(num){
    if(num < 1){
        return 1;
    }else{
        return num * f(num-1);
    }
});
```

### 闭包
> 闭包是指**有权访问另一个函数作用域中的变量的函数**。——《js高程》
> Closures are functions that have access to variables from another function’s scope.
> 闭包是函数和声明该函数的词法环境的组合。——《MDN》
> A closure is the combination of a function and the lexical environment within which that function was declared.

---
JS变量作用域：使用的是静态作用域。静态作用域在编译阶段就能确定变量的引用。和程序定义的位置有关，和代码执行的顺序无关。（词法作用域，也叫静态作用域，它的作用域是指在词法分析阶段就确定了，不会改变。动态作用域是在运行时根据程序的流程信息来动态确定的，而不是在写代码时进行静态确定的。词法作用域中使用的域，是变量在代码中**声明的位置**所决定的。）

JS使用词法环境来管理静态作用域。什么是词法环境？=>简单说，就是描述环境的一个**对象**。那如何描述每个环境呢？
- 环境记录：用来记录环境里面定义的形参、函数声明、变量等。
- 对外部词法环境的引用（outer）

词法环境 vs 执行环境（js高程）？应该是一个概念。执行环境定义了变量或函数有权访问的其他数据，决定了它们各自的行为。每个执行环境都有一个与之关联的变量对象。环境中定义的所有变量和函数都保存在这个对象中（虽然代码中无法访问，但在处理数据时解析器会使用到）

全局执行环境是最外围的一个执行环境。根据ECMAScript实现所在的宿主环境不同，表示执行环境的对象也不一样。=>在Web浏览器中，全局执行环境被**认为**是window对象，因此所有全局变量和函数都是作为window对象的属性和方法创建的。

**某个执行环境中的所有代码执行完毕后，该环节被销毁，保存在其中的所有变量和函数定义也随之销毁，全局执行环境直到应用程序退出——例如关闭网页/浏览器时才会被销毁**

每个函数都有自己的执行环境。当执行流进入一个函数时，函数的环境就会被推入一个环境栈中。而在函数执行之后，栈将其环境弹出，把控制权返回给之前的执行环境。ECMAScript程序中的执行流正由这个方便的机制控制着。

当代码在一个环境中执行时，会创建变量对象的一个**作用域链（scope chain）**。**作用域链的用途，是保证对执行环境有权访问的所有变量和函数的有序访问**。作用域链的前端，始终都是当前执行的代码所在环境的变量对象。如果这个环境是函数，则将其**活动对象**作为变量对象。活动对象在最开始时只包含一个变量，即arguments对象（这个对象在全局环境中是不存在的）。作用域链的下一个变量对象来自包含（外部）环境，层层延续到全局执行环境；全局执行环境的变量对象始终都是作用域链中的最后一个对象。

**标识符解析**是沿着作用域链一级一级地搜索标识符的过程。始终从作用域链的前端开始，然后逐级往后回溯，直到找到为止，如果找不到，通常会报错。

```javascript
var color = "blue";
function changeColor(){
    var anotherColor = "red";
    function swapColors(){
        var tempColor = anotherColor;
        anotherColor = color;
        color = tempColor;
        //color, anotherColor, and tempColor are all accessible here
    }
    //color and anotherColor are accessible here, but not tempColor
    swapColors();
}
//only color is accessible here
changeColor();
```
![](http://pazgkbbu5.bkt.clouddn.com/execution%20contexts.png)

**内部环境可以通过作用域链访问所有的外部环境，但外部环境不能访问内部环境中的任何变量和函数。**

这些环境之间的联系是线性的、有次序的。每个环境都可以向上搜索作用域链，以查询变量和函数名。但不能向下。

函数参数也被当做变量来对待，其访问规则与执行环境中的其他变量相同。

虽然执行环境的类型总共只有两种：（全局和局部（函数）），但是有一些情况可以延长作用域链：会把指定的对象添加到作用域链前端。
- try-catch语句的catch块：被抛出的错误对象的声明
- with：指定的对象

没有块级作用域，即花括号封闭起来的代码块没有自己的作用域，在ECMAScript中就是没有自己的执行环境。如果有块级作用域的话，块级代码执行完毕后，会销毁其变量对象。但在js中没有块级作用域，更没有变量对象（相当于还是在“外部”作用域中）。

> 执行环境 === 作用域 === 词法环境
> 作用域链就是将作用域链接起来
---

JavaScript中的函数会形成闭包。闭包是由函数以及创建该函数的词法环境组合而成。这个环境包含了这个闭包创建时所能访问的所有局部变量。

创建闭包的常用方式，就是在一个函数内部创建另一个函数：
```javascript
function createComparsionFunction(propertyName){
    return function(object1, object2){

        var value1 = object1[propertyName];
        var value2 = object2[propertyName];

        if(value1 < value2){
            return -1;
        }else if(value1 > value2){
            return 1;
        }else{
            return 0;
        }
    }
}
```
详细解析如下：即使内部函数（一个匿名函数）被返回了，而且在其他地方调用，但仍然可以访问变量propertyName。之所以还能访问这个变量，是因为**内部函数**的作用域链中包含 createComparsionFunction 函数的作用域。

这样作用域链难道会分叉吗？不是说是一个栈吗？环境有栈，作用域链为什么没有？=>作用域链是执行环境的，保存在[[Scope]]中。

如何创建作用域链以及作用域链有什么作用。=>彻底理解闭包。

当某一个函数被调用时，会创建一个执行环境及相应的作用域链。然后使用arguments和其他命名参数的值来初始化函数的活动对象（与执行环境对应）。在作用域链中，外部函数的活动对象始终处于第二位，层层回溯直至全局执行环境的变量对象。

在函数执行过程中，为读取和写入变量的值，就需要在作用域链中查找变量。

```javascript
function compare(v1, v2){
    if(v1 < v2) return -1;
    else if(v1 > v2) return 1;
    else return 0;
}
var res = compare(5, 10);
```

以上代码先定义了compare函数，然后又在全局作用域中调用了它。

当调用compare函数时，会创建一个包含arguments、v1和v2的活动对象。

全局执行环境的变量对象（包含res和compare）在compare执行环境的作用域链中则处于第二位。如下图所示：执行环境、作用域链、变量对象（活动对象）的关系。
![](http://pazgkbbu5.bkt.clouddn.com/scope-chain.png)

后台的每个执行环境都有一个变量对象。全局环境的变量对象始终存在，而像compare函数这样的局部环境的变量对象，则只有函数执行的过程中存在。

在创建compare函数时，会创建一个预先包含全局变量对象的作用域链，这个作用域链被保存在内部的**[[Scope]]属性**中。

当调用compare函数时，会为函数创建一个执行环境，然后通过复制函数的[[Scope]]属性中的对象构建起执行环境的作用域链。此后，又有一个活动对象（在此作为变量对象使用）被创建并被推入执行环境作用域链的前端。

对于这个例子中compare函数的执行环境而言，其作用域链中包含两个变量对象：本地活动对象和全局变量对象。显然，**作用域链本质上是一个指向变量对象的指针列表**，它只引用但不实际包含变量对象。

无论什么时候在函数中访问一个变量时，都会从作用域链中搜索具有相应名字的变量。一般来讲，当函数执行完毕后，局部活动对象就会被销毁，内存中仅保存全局作用域（全局执行环境的变量对象）。

但是，**闭包的情况有所不同**：

**在另一个函数内部定义的函数会将包含函数（即外部函数）的活动对象添加到它的作用域链中。**因此，在 createComparsionFunction 函数内部定义的匿名函数的作用域链中，实际上将会包含外部函数 createComparsionFunction 的活动对象。在匿名函数从 createComparsionFunction 中被返回后，它的作用域链被初始化为包含 createComparsionFunction 函数的活动对象和全局变量对象。

实际上，外部函数的活动对象只有在其执行时才会生成，所以闭包如果要包含外部函数的作用域（即其活动对象），外部函数必定执行过。但内部函数的作用域链是在定义时就已经确定了（静态作用域）。

这样，匿名函数就可以访问在 createComparsionFunction 中定义的所有变量。更为重要的是，createComparsionFunction 函数在执行完毕后，其活动对象也不会被销毁，因为**匿名函数的作用域链仍然在引用这个活动对象**。换句话说，当 createComparsionFunction 函数返回后，其执行环境的作用域链会被销毁，但它的活动对象仍然会留在内存中；直至匿名函数被销毁后， createComparsionFunction 函数的活动对象才会被销毁。

```javascript
// 创建函数
var compareNames = createComparisonFuncion('name');
// 调用函数
var result = compareNames({name: 'zs'},{name: 'ls'});
// 解除对匿名函数的引用（以便释放内存）
compareNames = null;
```
创建的比较函数被保存在变量compareNames中。而通过将compareNames设置为等于null解除该函数的引用，就等于通知垃圾回收例程将其清除。随着匿名函数的作用域链被销毁，其他作用域（除了全局作用域）也都可以安全的销毁了。

下图展示了调用 compareNames 函数的过程中产生的作用域链之间的关系：
![](http://pazgkbbu5.bkt.clouddn.com/closures-scope-chain.png)

由于闭包会携带包含它的函数的作用域，因此会比其他函数占用更多的内存。主要是由于闭包除了包含自身的作用域链，还会引用包含外部包含函数的作用域，返回后如果不手动解除对其的引用，就会一直占用内存。

#### 闭包与变量
上面提到了**标识符解析**是沿着作用域链一级一级地搜索标识符的过程。具体到闭包内，保存的是整个变量对象，而不是某个特殊的变量。

一个经典的案例：
```javascript
function createFunctions(){
    var result = new Array();
    for(var i=0;i<10;i++){
        result[i] = function(){
            return i;
        }
    }
    return result;  // 返回一个函数数组，每个函数的作用域链中都保存着createFunctions()函数的活动对象
}
```

通过创建另一个匿名函数并理解执行强制让闭包的行为符合预期：
```javascript
function createFunctions(){
    var result = new Array();
    for(var i=0;i<10;i++){
        result[i] = function(num){
            return function(){
                return num;
            }
        }(i);   // 创建一个自执行匿名函数并将其赋给数组
    }
    return result;
}
```
由于**函数参数是按值传递**的，所有就会将变量i的当前值赋值给参数num。

脑洞时刻：如果num是引用类型呢？
```javascript
function createFunctions(){
    var result = new Array();
    var obj = {};
    for(var i=0;i<10;i++){
        obj[i] = i;
        result[i] = function(num){
            return function(){
                return num;
            }
        }(obj);
    }
    return result;
}
```
结果并没有达到预期，当在意料之内，因为按值传递时，传的是引用类型值的地址，在闭包里引用的直接上层变量对象虽然不同，但是里面obj变量的值却引用的是同一个地址。

其实，这里说明的一个核心问题在于：闭包，保存的是整个变量对象（的引用），而不是某个特殊的、具体的变量。

#### 关于this对象

上下文是什么意思？上下文 vs 执行上下文？
[理解 JavaScript 作用域](https://mp.weixin.qq.com/s?__biz=MzAxODE2MjM1MA==&mid=2651554597&idx=2&sn=83fa2d288b149cd1a4199225f593debd&chksm=802554e4b752ddf241a6121827e2bc6601df1dc125594c6fc0aff636aedf734624147187b9a1&mpshare=1&scene=23&srcid=0720vKGeNjjdaHnDfAPKRf37#rd)

this对象是在 **运行时** **基于函数的执行环境**绑定的：
- 在全局函数中，this等于window。
- 而当函数被作为某个对象的方法调用时，this等于那个对象
- 匿名函数的执行环境具有全局性，因此其this对象通常指向window。
- call、apply、bind

每个函数在被调用时都会自动取得两个特殊的量：**this**和**arguments**，内部函数在搜索这两个变量时，只会搜索到其活动对象为止，因此永远不可能直接访问外部函数中的这两个变量。（因为活动对象中一定会有这两个变量的定义，即一定会找得到）

不过，把外部作用域中的this对象保存在一个闭包能够访问到的变量里，就可以让闭包访问该对象了。arguments同理，甚至于如果内部作用域和外部作用域变量同名时，都可以用这个方法。

```javascript
var name = "window";
var object = {
    name: "My Object",
    getName: function(){
        var that = this;
        return function(){
            return that.name;
        }();
    }
}
```

注意下面三段代码 => 语法的细微变化，都有可能意外改变this的值
```javascript
object.getName();   // 普通调用
(object.getName)(); // 因为object.getName和(object.getName)的定义是相同的，所有this的值得到了维持（why?）
(object.getName = object.getName)();    // 因为执行了一条赋值语句，然后再调用赋值后的结果。因为这个赋值表达式的值是函数本身，所以this的值不能得到维持（why?）
```

`()`这个运算符在js里到底作用是什么？有什么影响吗？

#### 内存泄露
因为闭包会引用包含函数的整个活动对象。不过可以手动对部分变量置为null的方式解除其中内存占用大的变量的内存占用，而取其中有用的部分存储在内存中。

### 模仿块级作用域/私有作用域
js不会告诉多次声明了同一个变量，这种情况下，后续的声明会忽略，只执行其中的变量初始化。

用作块级作用域（通常也称为私有作用域）的匿名函数的语法表示如下：
```javascript
(function(){
    // 这里是块级作用域
})();
```
以上代码**定义并立即调用了一个匿名函数**。将函数声明包含在一对圆括号中，表示它实际上是一个函数表达式，而紧跟其后的圆括号会立即调用这个函数。

这样定义的私有作用域，会在代码执行结束后被销毁。

通过匿名函数创建的闭包，可以减少闭包占用的内存问题，因为没有指向匿名函数的引用。只要函数执行完毕，就可以立即销毁其作用域链了。

### 私有变量
在很多编程语言中，可以使用public、private、protected等修饰符来设置成员和方法的访问权限，但在js中，严格来讲没有私有成员的概念，所有对象属性都是公开的。不过，也没有类的概念——构造函数。

不过类似的有私有变量的概念，任何在函数中定义的变量，都可以认为是私有变量，因为**不能在函数外部**直接访问这些变量。私有变量包括函数的参数、局部变量和在函数内部定义的其他函数。

不过，我们可以利用闭包创建用于访问私有变量的共有方法。有权访问私有变量和私有函数的共有方法称为**特权方法**。有如下两种方式创建特权方法：

1、在构造函数内部定义
```javascript
// 模拟private和public
function MyObject(){
    // 私有变量和私有函数
    var privateVariable = 10;
    function privateFunction(){
        return false;
    }

    // 特权方法
    this.publicMethod = function(){
        privateVariable++;
        return privateFunction();
    }
}
```
特权方法作为闭包有权访问在构造函数中定义的所有变量和函数。创建MyObject实例后，除了使用publicMethod()这一个途径外，没有任何办法可以直接访问privateVariable和privateFunction。

另外，需要注意的是：函数的内部变量只有当其被调用时才会存在，调用结束一般会立即销毁。但其实本质上每次调用相当于重新创建了一个内部对象，即使上一次函数环境和变量对象未被销毁。因此，上面的函数作为构造函数被调用时，每次调用就相当于创建了一个**新的**函数执行环境和变量对象，因为返回的闭包被新创建的对象实例所引用导致在调用结束后其变量对象无法被销毁（保存在了内存中，或者叫维护了一个私有作用域，只有这个闭包/公开方法可以访问）。=>私有变量在每一个实例中都不相同，因为构造函数的执行环境会重新创建，更何况是特权方法的执行环境。

使用私有和特权成员，可以隐藏那些不应该被直接修改的数据。

脑洞时间：访问器属性属于创建了一个私有属性吗？（why?）

但使用构造函数模式定义特权方法有一个缺点就是：每次重新调用构造函数都会重新创建构造函数内部定义的方法。

2、通过私有作用域定义
=>静态私有变量。

```javascript
(function(){
    // 私有变量和私有函数
    var privateVariable = 10;
    function privateFunction(){
        return false;
    }

    MyObject = function(){};    // 没加var，MyObject是全局变量

    // 公有/特权方法
    MyObject.prototype.publicMethod = function(){
        privateVariable++;
        console.log(privateVariable);
        return privateFunction();
    }
})();
```
这种模式创建了一个私有作用域，在其中，首先定义了私有变量和私有函数，然后定义了构造函数及其公有方法（且公有方法是在原型上定义的）。

需要注意的是，上面直接使用函数表达式定义构造函数，而不是函数声明（会定义在局部环境上），而且没有使用`var`，因此定义的构造函数是全局环境上的（**初始化未经声明的变量，总是会创建一个全局变量**）。

其实，上面代码等同于：
```javascript
var MyObject = function(){};    // 没加var，MyObject是全局变量
// 或者
function MyObject(){};
(function(){
    // 私有变量和私有函数
    var privateVariable = 10;
    function privateFunction(){
        return false;
    }

    // 公有/特权方法
    MyObject.prototype.publicMethod = function(){
        privateVariable++;
        console.log(privateVariable);
        return privateFunction();
    }
})();
```

这种模式特殊之处，在于**私有变量和函数是由实例共享的**。且因为特权方法是在原型中定义，因此所有实例都使用同一函数（而特权方法因为在私有作用域内定义的，形成闭包，能够保存着对包含作用域的引用）。

两种方式，完全可以结合使用，视具体需求而定。

另外，对于私有变量，因为多了中间一层作用域，导致查找变量时多查找了一个层次，会在一定程度上影响查找速度。=>闭包和私有变量的一个显明的不足之处。

### 模块模式
使用闭包可以用于为自定义类型创建私有变量和特权方法，但**模块模式**则是为**单例**创建私有变量和特权方法。所谓单例，指的是只有一个实例的对象，而在js中，一般是以对象字面量的方式来创建单例对象的。

？？？一个实例的对象？？？对象本身不就是一个实例吗？
```javascript
var singleton = {
    name: value,
    method: function(){}
}
```
模块模式通过为单例添加私有变量和特权方法能够使其得到增强。
```javascript
var singleton = function(){
    // 私有变量和私有函数
    var privateVariable = 10;
    function privateFunction(){
        return false;
    }

    // 特权/公有方法和属性
    return {
        publicProperty: true,
        publicMethod: function(){
            privateVariable++;
            return privateFunction();
        }
    }
}();    // 立即执行匿名函数，返回一个对象
```
从本质上讲，这个返回的对象字面量定义的是单例的公共接口。这种模式在需要对单例进行某种初始化，同时又需要维护其私有变量时是非常有用的。简言之，如果必须创建一个对象并以某些数据对其进行初始化，同时还要公开一些能够访问这些私有数据的方法，那么就可以使用模块模式。

这种模式创建的单例是Object类型。（因为字面量创建的对象返回）

### 增强的模块模式
适合那些单例必须是某种类型的实例，同时还必须添加某些属性和（或）方法来对其增强的情况。

```javascript
var singleton = function(){
    // 私有变量和私有函数
    var privateVariable = 10;
    function privateFunction(){
        return false;
    }
    // 创建对象
    var object = new CustomType();

    // 添加特权/公有属性和方法
    object.publicProperty = true;
    object.publicMethod = function(){
        privateVariable++;
        return privateFunction();
    }

    // 返回这个对象
    return object;
}();
```

### 小结
在js中，函数表达式非常有用，使用时无需对函数命名，从而实现动态编程。匿名函数，也成为拉姆达函数。

闭包的作用：
- 模拟块级作用域
- 在对象中创建私有变量：可以使用构造函数模式、原型模式来实现自定义类型的特权方法，也可以使用（增强的）模块模式来实现单例的特权方法。