## 面向对象（Object-Oriented）的程序设计

### OO
- 传统OO通过**类**可以创建任意多个具有相同属性和方法的**对象**
- 但JavaScript中其实没有类的概念，因此其中的对象也与传统OO中的对象有所不同
- ECMA-262将对象定义为“无序属性的集合”，散列表：一组名值对，其中值可以是数据或函数
- 每个对象**都是基于一个引用类型**创建的（可以是原生类型，也可以是自定义类型） => “类型”和“类”有什么区别？

### 对象属性的特性（attribute）
描述了属性（property）的各种特征。
- 属性类型：包括数据属性和访问器属性
    - 数据属性：包含一个数据值的位置，可以读取/写入值。数据属性有4个描述其行为的特性：
        - [[configurable]]：表示能否通过delete删除属性从而重新定义属性，能否修改属性的特性，或者能否把属性修改为访问器属性
        - [[Enumerable]]：表示能否通过for-in循环返回属性
        - [[Writable]]：表示能否修改属性的值
        - [[Value]]：包含这个属性的数据值。读取属性值时，从这个位置读；写入属性值时，把新值保存在这个位置
    - 访问器属性：不包含数据值。包含一对getter和setter函数（非必需）
        - getter：读取访问器属性时，会调用getter函数，其负责返回有效的值
        - setter：写入访问起属性时，会调用setter函数并传入新值，这个函数决定如何处理数据
        - 访问器属性有如下4个特性：
            - [[configurable]]：表示能否通过delete删除属性从而重新定义属性，能够修改属性的特性，能够把属性修改为数据属性
            - [[Enumerable]]：表示能够通过for-in循环返回属性
            - [[Get]]：在读取属性值时调用的函数
            - [[Set]]：在写入属性值时调用的函数
        - 不一定非要同时指定getter和setter，未指定默认为undefined，未指定即为不可读/不可写
        - 访问器属性**不能直接定义**
- 属性**特性的默认值**，不同属性定义方式不同
    - 直接定义
        - 数据属性：true/true/true/undefined
        - 访问器属性：<无法直接定义>
    - 属性定义（Object.defineProperty()）
        - 数据属性：false(手动设置为false后不可还原，默认时还可以配置)/false/false/undefined
        - 访问器属性：false/false/undefined/undefined
- 定义属性的特性（可添加并编辑新的属性）
    - Object.defineProperty()
    - Object.defineProperties()
- 读取属性的特性
    - Object.getOwnPropertyDescriptor()

### 创建对象（类实例化？）
1、使用Object构造函数，创建一个Object实例，然后为它添加属性和方法
```javascript
var person1 = new Object();
person1.name = 'zs';
person1.age = 18;
person1.job = 'programmer';
person1.sayName = function(){
    alert(this.name);
}
var person2 = new Object();
person2.name = 'ls';
person2.age = 28;
person2.job = 'teacher';
person2.sayName = function(){
    alert(this.name);
}
```

2、使用对象字面量（这种方式没有调用Object构造函数）
```javascript
var person1 = {
    name: 'zs',
    age: 18,
    job: 'programmer',
    sayName: function(){
        alert(this.name);
    }
}
var person2 = {
    name: 'ls',
    age: 28,
    job: 'teacher',
    sayName: function(){
        alert(this.name);
    }
}
```

```
使用前2种方法有个明显的缺点：**使用同一个接口创建很多对象，会产生大量的重复代码**（代码量，不是内存）
```

3、工厂模式
- 工厂模式是一种设计模式，抽象了创建具体对象的过程。但JavaScript中无法创建类 => 用函数来封装**以特定接口创建对象的细节**
```javascript
function createPerson(name, age, job){
    var o = new Object();
    o.name = name;
    o.age = age;
    o.job = job;
    o.sayName = function(){
        alert(this.name);
    }
    return o;
}
var person1 = createPerson('zs', 18, 'programmer');
var person2 = createPerson('ls', 28, 'teacter');
```
```
工厂模式解决了创建多个相似对象的问题，但是没有解决对象识别（一个对象的类型）的问题。
```

4、构造函数
- ECMAScript中的构造函数可以创建特定类型的对象。
- 原生构造函数和自定义的构造函数。原生构造函数有Object、Array等，在运行时会自动出现在执行环境中；创建**自定义的构造函数**，从而定义**自定义对象类型**的属性与方法。
```javascript
function Person(name, age, job){
    this.name = name;
    this.age = age;
    this.job = job;
    this.sayName = function(){
        alert(this.name);
    }
}
var person1 = new Person('zs', 18, 'programmer');
var person2 = new Person('ls', 28, 'teacter');
```
- 与工厂模式相比，构造函数：
    - 没有显式地创建对象
    - 直接将属性和方法赋给了this对象
    - 没有return语句（其实可以有，只不过用new调用时，得到的不是return返回值。**如果return一个object，那么new调用返回的结果将是这个object，否则是新创建的对象**）
    - 惯例：构造函数一般以**大写字母**开头

- 使用new操作符（调用构造函数）创建对象，实际上会经历以下4个步骤：
    - 创建一个新对象
    - 将**构造函数的作用域**赋给新对象（因此this就指向了这个新对象。新对象即为构造函数的作用域，那么this值便指向新对象。并不是把this赋给了新对象，而是因为this本身即指向函数据以执行的作用域，那么当new调用时，作用域就是创建的新对象）
    - 执行构造函数中的代码（为这个新对象添加属性）
    - 返回新对象

- 使用new操作符调用构造函数方式创建的所有实例对象，都有一个**constructor**属性，该属性指向构造函数。=>只要是对象，就会有`constructor`属性，指向其构造函数。**但是本质上对象的constructor属性不是本身自有的，而是其原型对象的constructor属性**
- 对象的constructor属性最初是用来**标识对象类型**的。另外，instanceof操作符检测对象类型更为可靠（为什么？）

通过后面重写构造函数原型，发现创建的对象实例的constructor指向的不是构造函数，而是Object，可以得知：实例与构造函数的联系其实是通过构造函数的原型，<实例对象>.constructor === <构造函数>.prototype.constructor。实例如下代码：
```javascript
function Person(){}
Person.prototype = {
    name: 'zs',
    age: 18,
    job: 'programmer',
    sayName: function(){
        alert(this.name);
    }
}
var p = new Person();
console.log(p.constructor === Person); /* false */
console.log(p.constructor === Object); /* true */
console.log(p.constructor === Person.prototype.constructor); /* true */
```

这就是`constructor`标识对象类型不靠谱的原因，因为原型对象很容易被重写，因此其constructor属性会被修改。但是有一点不会变的是“即使原型对象重写了，但是依然还是构造函数的原型（否则就不可能叫做原型对象了）”，所以`instanceof`判断的是**原型链上的原型是含有某构造函数的原型对象**来“标识对象类型”。

另一方面，也可以看出对象和构造函数本身没有任何联系，只是在new创建对象后的时候，对象和构造函数都有一个属性指向同一个对象（原型对象）。

- **创建自定义构造函数**意味着将来可以**将它的实例标识为一种特定的类型**。
- 构造函数 vs 函数
    - 构造函数与其他函数的唯一区别：在于调用它们的方式不同，任何函数，
        - 只要通过new操作符调用，那它就可以作为构造函数
        - 如果不通过new操作符调用，那就是普通函数
    - 另：
        - 构造函数主要作用是创建对象
        - 构造函数名一般习惯以大写字母开头
        - 构造函数在new调用和以普通函数调用执行过程不同

- 构造函数的问题
    - 每个方法（函数）都要在每个实例上重新创建一遍。
    相当于：
    ```javascript
    function Person(name, age, job){
        this.name = name;
        this.age = age;
        this.job = job;
        this.sayName = new Function('alert(this.name)'); /* 函数也是对象，这样每个实例对象都会有一个函数对象 */
    }
    ```
    这样每个对象实例上的方法并不是同一个（导致不同的作用域链和标识符解析），但是方法是完成相同的任务。（没必要！）
    - 把函数定义转移到构造函数外部
    改良版的构造函数：
    ```javascript
    function Person(name, age, job){
        this.name = name;
        this.age = age;
        this.job = job;
        this.sayName = sayName; /* sayName包含的一个指向函数的指针，这样对象实例就共享了在全局作用域中定义的同一个sayName函数 */
    }
    function sayName(){
        alert(this.name);
    }
    ```
    1）定义在外部的函数主观上/理论上只供实例对象调用。但实际上，外部作用域内均可
    2）把函数以全局函数的方式共享，就与构造函数/自定义类型形式上分离，无封装性
    3）函数定义在外部，容易引起混淆和污染

5、原型模式
- 理解原型对象
    - 只要创建一个函数，就会根据一组特定的规则为该函数创建一个**prototype属性**，这个属性指向函数的**原型对象**
    - 在默认情况下，所有原型对象都会自动获得一个**constructor属性**，这个属性是一个指向prototype属性所在函数的指针。创建自定义构造函数之后，其原型对象默认只会取得constructor属性，至于其他方法，则都是从**Object继承**而来
    - 当调用构造函数创建一个新实例后，该实例内部将包含一个指针[[Prototype]]（内部属性），指向构造函数的原型对象。一般来说，这个属性对脚本是不可见的，不过在Chrome等浏览器中，可以通过**\_\_proto\_\_**属性访问。实例与原型间的松散连接关系。
        - 通过这个内部属性，我们可以通过**查找对象属性**来访问实例上没有，而原型对象上有的属性和方法。这样，这个对象就可以用于包含可以由特定类型的所有实例共享的属性和方法。
        - 虽然在所有实现中都无法访问到[[Prototype]]，但可以通过`isPrototypeOf()`方法来确定对象间是否存在这种关系
        - 在ECMAScript5增加了`Object.getPrototypeOf()`方法，返回[[Prototype]]的值。即使用这个方法，可以取得一个对象的原型。
    - 虽然可以通过对象实例访问保存在原型中的值，但却不能通过对象实例**重写**原型中的值。
    - 使用`hasOwnProperty()`方法（从Object继承）可以检测一个属性是存在于实例中，还是存在于原型中。
    - 在读取对象的属性特性时使用的`Object.getOwnPropertyDescriptor()`方法只作用于**对象本身**（不包括原型）的属性
    - 个人总结：
        - 只有函数对象才有prototype属性
        - 普通对象都会有[[Prototype]]属性和constructor属性，其中：
            - [[Prototype]]指向构造函数的原型对象
            - **constructor指向原型对象的constructor属性**
- `in`操作符
    - 单独使用：in操作符会在**通过对象能够访问**给定属性时返回true，无论该属性存在于实例中，还是在原型中。=>同时使用hasOwnProperty()方法和in操作符，就可以确定该属性到底是存在于对象中还是存在于原型中。
    - 在for-in循环中使用：返回的是所有能**通过对象访问**、**可枚举的（enumerated）**属性，其中既包括存在于实例中的属性，也包括存在于原型中的属性。
    - 要取得对象上所有**可枚举**的**实例属性**，可以使用ECMAScript5的`Object.keys()`方法。接收一个对象作为参数，返回所有可枚举属性的字符串数组。
    - 如果想得到所有的实例属性，可以使用`Object.getOwnPropertyNames()`方法

- 原型模式
    - 使用原型对象的好处是可以让所有对象实例共享它的属性和方法。这样就不必在构造函数种定义对象实例的信息，而是可以将这些信息直接添加到原型对象中。
    ```javascript
    function Person(){

    }
    Person.prototype.name = 'zs';
    Person.prototype.age = 18;
    Person.prototype.job = 'programmer';
    Person.prototype.sayName = function(){
        alert(this.name);
    }
    var person1 = new Person();
    var person2 = new Person();
    console.log(person1.sayName === person2.sayName); // true，新对象的属性和方法是由所有实例所共享的
    ```
    - 更简单的原型语法
    ```javascript
    function Person(){

    }
    Person.prototype = {
        name: 'zs',
        age: 18,
        job: 'programmer',
        sayName: function(){
            alert(this.name);
        }
    }
    ```
    虽然简化后的代码实现的结果一样，但有一个例外：**Person.prototype的constructor属性不再指向Person了**，因为上述代码本质上相当于**完全重写了**默认的Person.prototype对象，因此constructor属性也就变成了新对象的constructor属性（字面量创建对象自带constructor属性，指向Object构造函数），不再指向Person函数了。
    此时，虽然通过instanceof操作符还能返回正确的结果（原型链并没有断），但通过constructor已经无法确定对象的类型了(此时实例对象的constructor === Object)。
    - 可以设回constructor值
    ```javascript
    function Person(){

    }
    Person.prototype = {
        constructor: Person, /* 但这种方式重设的constructor属性会导致它的[[Enumerable]]特性被设置为true，但默认情况下，constructor属性是不可枚举的 */
        name: 'zs',
        age: 18,
        job: 'programmer',
        sayName: function(){
            alert(this.name);
        }
    }
    ```
    不过可以使用`Object.defineProperty()`重设constructor属性
    ```javascript
    function Person(){

    }
    Person.prototype = {
        name: 'zs',
        age: 18,
        job: 'programmer',
        sayName: function(){
            alert(this.name);
        }
    }
    Object.defineProperty(Person.property, 'constructor', {
        enumerable: false,
        value: Person
    });
    ```
    借助`Object.assign()`方法实现：
    ```javascript
    function Person(){

    }
    Person.prototype = Object.assign(Person.prototype, {
        name: 'zs',
        age: 18,
        job: 'programmer',
        sayName: function(){
            alert(this.name);
        }
    });
    ```
    - 原型的动态性
        - 在原型中查找值的过程是**一次搜索**（指针，而非副本，实例与原型之间的松散连接关系），因此我们对原型对象所做的修改都能够立即从实例中反映出来。
        - 注意：
            - 如果是重写了构造函数的原型对象，并不会反映到实例中。因为实例的[[Prototype]]属性在对象创建时就指向了最初的原型对象
            - 而把原型修改为另外一个对象就等于切断了构造函数与最初原型之间的联系
            - 而实例中的指针仅指向原型，而不指向构造函数
            - 但重写后再通过构造函数创建对象，此时对象的原型属性即指向最新的原型对象
    - 原生对象的原型
        - 原型模式的重要性不仅体现在创建自定义类型方面，就连所有原生的引用类型，都是采用这种模式创建的
        - 但**不推荐**在业务中修改原生对象的原型！
    - 原型模式的问题
        - 省略了为构造函数传递初始化参数这一环节，所以所有实例在默认情况下都将取得相同的属性值 -> 不方便
        - 原型模式最大的问题是由其共享的本性导致的：
            - 对于函数（方法）属性非常合适 => 接除方法与对象的耦合
            - 对于保存基本值的属性，也说得过去（虽然都会取到一个默认的具体值），因为可以在对象实例上添加一个同名属性来屏蔽原型中的相应属性
            - 对于保存引用类型值的属性，在实例上对其的修改（重新定义除外）可能会同步到原型对象上，从而影响所有的实例对象
        - 一般不会单独使用原型模式

6、组合使用构造函数模式和原型模式
- 创建自定义类型的最常用方式，也是用来定义引用类型的一种默认模式
- 其中，
    - 构造函数模式用于定义实例属性
    - 原型模式用于定义方法和共享的属性
- 每个属性都会有自己的一份实例属性的副本，但同时又共享着对方法的引用，最大限度地节省了内存
- 还支持向构造函数传递初始化参数
```javascript
function Peerson(name, age, job){
    this.name = name;
    this.age = age;
    this.job = job;
    this.friends = ['friend A', 'friend B'];
}
Person.prototype = {
    constructor: Person,
    sayName = function(){
        alert(this.name);
    }
}
var person1 = new Person('zs', 18, 'programmer');
var person2 = new Person('ls', 28, 'teacher');
``` 

7、动态原型模式
传统OO，形式上构造函数和原型是封装在一起的，因此我们就**需要在构造函数中初始化原型**
```javascript
function Person(name, age, job){
    this.name = name;
    this.age = age;
    this.job = job;
    Person.prototype.sayName = function(){
        alert(this.name);
    }
}
```
但是这样每次创建对象时都会初始化原型，因此需要在原型未初始化时初始化即可（第一次调用构造函数时）
```javascript
function Person(name, age, job){
    this.name = name;
    this.age = age;
    this.job = job;
    if(typeof this.sayName != 'function'){ /* if检查的可以是初始化之后应该存在的任何属性/方法，且只需检查一个即可 */
        Person.prototype.sayName = function(){
            alert(this.name);
        }
    }
    
}
```
注意：不可重写原型对象（例：对象字面量） => 因为会覆盖初始的原型对象。造成第一次创建的对象的原型引用的是初始原型对象，而以后创建的对象的原型才是重写的原型对象

8、寄生构造函数模式
- 基本思想：创建一个函数，该函数的作用仅仅是封装创建对象的代码，然后再返回新创建的对象。
- 注意，使用new调用构造函数，函数在不返回值（即无return语句）时返回新对象实例，而如果有return语句时（且返回的是一个对象），会重写调用构造函数时返回的值。
- 因此，结合工厂模式和构造函数模式：
```javascript
function Person(name, age, job){
    var o = new Object();
    o.name = name;
    o.age = age;
    o.job = job;
    o.sayName = function(){
        alert(this.name);
    }
    return o;
}
var person1 = new Person('zs', 18, 'programmer');
var person2 = new Person('ls', 28, 'teacher');
```
- 这种模式可以在特殊的情况下用来**为对象创建构造函数**，例想创建一个具有额外方法的特殊数组：
```javascript
function SpecialArray(){
    var values = new Array(); /* 为values对象创建构造函数SpecialArray */
    values.push.apply(values, arguments); /* 接收参数 */
    values.toPipedString = function(){
        return this.join('|');
    }
    return values;
}
var colors = new SpecialArray('red', 'blue', 'green');
console.log(colors.toPipedString()); /* 'red|blue|green' */
```
- 注意：
    - 返回的对象与构造函数/构造函数的原型对象没有关系
    - 构造函数返回的对象与在构造函数外部创建的对象没有什么不同
    - 不能使用instanceof操作符来确定对象类型

**这里用不用new有什么区别吗？**（好像并没有）

9、稳妥构造函数模式
- 稳妥对象：没有公共属性，而且其方法也不引用this的对象
- 适用于一些安全的环境中，这些环境一般会禁止使用this和new，或者在防止数据被其他应用程序（如Mashup程序）改动时使用
- 稳妥构造函数模式遵循与寄生构造函数类似的模式，但：
    - 新创建的对象实例方法不引用this
    - 不适用new操作符调用构造函数
```javascript
function Person(name){
    var o = new Object();

    /* 可以在这里定义私有变量和函数 */

    o.sayName = function(){
        alert(name);
    }

    return o;
}
var person1 = Person('zs');
person1.sayName();
```
- 注意：
    - 以这种模式创建的对象中，除了使用sayName()方法之外，没有其他方法可以访问name的值
    - 这样创建的对象是一个稳妥对象，除了调用sayName()方法外，没有别的方式访问其数据成员（传入到构造函数的原始数据）
    - 创建的对象与构造函数间也没有任何关系

10、Object.create()
    - https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/create

### 继承（类之间，表现在子类的实例上）
