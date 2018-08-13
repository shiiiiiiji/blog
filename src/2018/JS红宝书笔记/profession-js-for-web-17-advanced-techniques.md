## 高级技巧
### 高级函数
#### 安全的类型检测
类型检测经常会出现 **错误否定、错误肯定**。

原生对象检测解决办法：在任何值上调用 Object 原生的 toString() 方法，都会返回一个 `[object NativeConstructorName]` 格式的字符串。不能检测非原生构造函数的构造函数名。

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/toString

注意，这里要求 `Object.prototype.toString` 方法未被修改。

#### 作用域安全的构造函数
this 对象晚绑定（运行时绑定）。避免在全局对象上意外设置属性。

作用域安全的构造函数在进行任何更改前，首先确认 this 对象是正确类型的实例，如果不是，那么会创建新的实例并返回。

```javascript
function Person(name, age, job){
    if(this instanceof Person){
        this.name = name;
        this.age = age;
        this.job = job;
    } else {
        return new Person(name, age, job);
    }
}

var person1 = Person('Nicholas', 29, 'Software Engineer');
alert(window.name); // ''，如果没有设置，name 属性会设置在 window 全局对象上
alert(person1.name);    // 'Nicholas'

var person2 = new Person('Shelby', 34, 'Ergonomist');
alert(person2.name);    // 'Shelby'
```

因为锁定了可以调用构造函数的环境，因此使用 构造函数窃取模式的继承且不使用原型链，那么这个继承会被破坏。因为在子类型构造函数中调用父类型构造函数时，并没有将父类型的属性添加到 this （也就是子类型实例）上，而是会新创建一个父类型的实例。但结合原型链或者寄生组合则可以解决这个问题。

#### 惰性载入函数
惰性载入表示函数执行的分支仅会发生一次（例如浏览器能力检测，每次都判断会浪费性能），避免执行不必要的代码。

有两种实现方式：
- 在函数被调用时再处理函数：在第一次调用过程中，该函数就会被覆盖为另外一个按合适方式执行的函数，这样任何对原函数的调用都不用再经过执行的分支了。

```javascript
function Fn(){
    if(xxx){
        Fn = function(){};
    }else{
        Fn = function(){};
    }
}
```

- 在声明函数时就指定适当的函数。这样，第一次调用代码时就不会损失性能了。而在代码首次加载时会损失一点性能。
```javascript
var Fn = (function(){
    if(xxx){
        return function(){};
    }else{
        return function(){};
    }
})();
```
创建一个匿名、自执行的函数，用以确定应该使用哪一个函数实现。

#### 函数绑定
函数绑定要创建一个函数，可以在特定的 this 环境中以指定参数调用另一个函数。常常和回调函数与时间处理程序一起使用，**以便在将函数作为变量传递时的同时保留代码执行环境**。

```javascript
var handler = {
    message: "Event handled",
    handleChick: function(event){
        alert(this.message);
    }
}

var btn = document.getElementById('my-btn');
EventUtil.addhandler(btn, 'click', handler.handleChick);    // 点击时显示 undefined（没有保存handler.handleChick()的环境）

// 使用闭包来修正这个问题
EventUtil.addhandler(btn, 'click', function(event){
    handler.handleChick(event);
});
```

这个解决方案在 onclick 时间处理程序内使用了一个闭包直接调用 handler.handleClick()。但是，创建多个闭包可能会令代码变的难以理解和调试，因此很多 JavaScript 库实现了一个可以将函数绑定到制定环境的函数，这个函数一般都叫 bind()。

一个简单的 bind() 函数接受一个函数和一个环境，并返回一个在给定环境中调用给定函数的函数，并且将所有参数原封不动传递过去，语法如下：

闭包是创建一个函数（本身），并且保存了上级作用域。而 bind() 本质上就是里用了闭包的特点，将函数的执行环境传递过来并保存下来，保存了函数的执行环境（这里就是一个完整的闭包，而不是仅保存函数的执行环境）。

```javascript
function bind(fn, context){
    return function(){ // 创建了一个闭包。ß这个会在外部被调用，可传入参数并传递给内部 arguments 对象接收
        return fn.apply(context, arguments) // 使用 apply() 调用传入的函数，并传递 context 对象和参数。注意 arguments 对象是内部函数的，并非 bind() 的。
    }
}

// fn.apply(context, arguments) === context.fn(arguments); ???

EventUtil.addhandler(btn, 'click', bind(handler.handleChick, handle));
```

当调用返回的函数时，会在给定环境中执行被传入的函数，并给出所有参数。=> bind() 函数创建了一个保持了执行环境的函数。handler.handleChick() 方法和平时一样会获得 event 对象，因为所有的参数都通过被绑定的函数直接传给了它。

=> ECMAScript 5 为所有函数定义了一个原生的 bind() 方法：

```javascript
EventUtil.addhandler(btn, 'click', handler.handleChick.bind(handle));
```

只要是将某个函数指针以值的形式进行传递，同时该函数必须在特定环境中执行，被绑定函数的效用就凸显出来了。主要用于事件处理程序以及 setTimeout 和 setInterval。然而，被绑定函数与普通函数相比有更多的开销，需要更多内存，同时也因为多重函数调用稍微慢一点，所以最好只在必要时使用。

#### 函数柯里化
函数绑定紧密相关的主题是函数柯里化（function currying），用于创建 **已经设置好了一个或多个参数的函数**。

函数柯里化的基本方法和函数绑定是一样的：**使用一个闭包返回一个函数**。两者的区别在于，当函数被调用时，返回的函数还需要设置一些传入的参数。

```javascript
function add(num1, num2){
    return num1 + num2;
}

function curriedAdd(num2){
    return add(5, num2);
}

alert(add(2, 3));   // 5
alert(curriedAdd(3));   // 8
// 从技术上说 curriedAdd() 并非柯里化的函数，但很好第展示了其概念。
```

柯里化函数通常由以下步骤动态创建：**调用另一个函数并为它传入要柯里化的函数和必要参数**，通用方式如下：

```javascript
function curry(fn){
    var args = Array.prototype.slice.call(arguments, 1);
    return function(){
        var innerArgs = Array.prototype.slice.call(arguments);
        var finalArgs = args.concat(innerArgs);
        return fn.apply(null, finalArgs);
    }
}
```

curry() 函数的主要工作就是将被返回函数的参数进行排序。

curry() 的第一个参数是要进行柯里化的函数，其他参数是要传入的值。

为了获得第一个参数之后的所有参数，在 arguments 对象上调用了 slice() 方法，并传入参数 1 表示被返回的数组包含从第二个参数开始的所有参数。=> args 包含了来自外部函数的参数。

在内部函数中，创建了 innerArgs 数组（使用 slice() ）用来存放所有传入的参数。

有了存放来自外部函数和内部函数的参数数组后，就可以使用 concat() 方法将它们组合成 finalArgs，然后使用 apply() 将结果传递给该函数。

注意，这个函数并没有考虑到执行环境，所以调用 apply() 时第一个参数是 null。（？绑定 null 是什么效果，https://www.cnblogs.com/snandy/archive/2012/03/01/2373243.html）

函数柯里化还常常作为函数绑定的一部分包含在其中，构造出更为复杂的 bind() 函数：
```javascript
function bind(fn, context){
    var args = Array.prototype.slice.call(arguments, 2);
    return function(){
        var innerArgs = Array.prototype.slice.call(arguments);
        var finalArgs = args.concat(innerArgs);
        return fn.apply(context, finalArgs);
    }
}
```

=> 函数绑定的模拟实现借助了函数柯里化，原生的 bind() 方法不知实现是否如此。
=> call/apply/bind 可以绑定 this，this就是执行环境？？？

JavaScript 中的柯里化函数和绑定函数提供了强大的动态函数创建功能。使用 bind() 还是 curry() 要根据是否需要 object 对象响应来决定。都能用来创建复杂的算法和功能，但都不应滥用，因为每个函数都会带来额外的开销。


### 防篡改对象
tamper-proof object

手工设置每个属性的`[[Configurable]]`、`[[Writable]]`、`[[Enumerable]]`、`[[Value]]`、`[[Get]]`、`[[Set]]`特性，以改变属性的行为。类似的，ECMAScript 5 也增加了几个方法，通过它们可以指定对象的行为。

注意：一旦把对象定义为防篡改，就无法撤销了。

#### 不可扩展对象
默认情况下，所有对象都是可以扩展的，即任何时候都可以向对象中添加属性和方法。

```javascript
Object.preventExtensions(obj); // 不能给 obj 对象添加新属性和方法（非严格模式静默失败，严格模式会报错），已有成员不受影响（可修改和删除）
Object.isExtensible(obj);
```

#### 密封的对象
ECMAScript 5 为对象定义的第二个保护级别是 **密封对象**（sealed object），密封对象不可扩展，而且已有成员的 `[[Configurable]]` 特性将被设置为 false => **不能删除**属性和方法，因为不能使用 Object.defineProperty() 把数据属性修改为访问器属性，或者相反。属性 **值是可以修改** 的。

```javascript
Object.seal(obj);
Object.isSealed(obj);   // 因为被密封的对象不可扩展，所以用 Object.isExtensible(obj); 检测密封的对象也会返回 false
```

#### 冻结的对象
最严格的防篡改级别是 **冻结对象**（frozen object），冻结的对象既不可扩展，又是密封的，而且对象数据属性的`[[Writable]]`特性会被设置为 false。如果定义`[[Set]]`函数，访问器属性仍然是可写的。

```javascript
Object.freeze(obj);
Object.isFrozen(obj);
```

### 高级定时器
使用 setTimeout() 和 setInterval() 创建的定时器，容易误解为是线程，其实 JavaScript 是运行于单线程的环境中的，而定时器仅仅只是计划代码在未来的某个时间执行。**执行时间是不能保证的**，因为在页面的生命周期中，不同时间可能有其他代码在控制 JavaScript 进程。

在页面下载完成后的代码运行、事件处理程序、Ajax 回调函数都必须使用同样的线程来执行，实际上，浏览器负责进行排序，指派某段代码在某个时间点运行的 **优先级**。

可以把 JavaScript 想象成在时间线上运行的。当页面载入时，首先执行是任何包含在 script 元素中的代码，通常是页面生命周期后面要用到的一些简单的函数和变量的声明，有时候也包含一些初始数据的处理。在这之后，JavaScript 进程将等待更多代码执行。当进程空闲时，下一个代码会被触发并立刻执行。

除了主 JavaScript 执行进程外，还有 **一个需要在进程下一次空闲时执行的代码队列**。随着页面在其生命周期中的推移，代码会按照执行顺序添加入队列。（有哪些方式？）

例如，当某个按钮被按下时，它的事件处理程序代码就会被添加到队列中，并在下一个可能的事件里执行。当接收到某个 Ajax 响应时，回调函数的代码会被添加到队列。

**在 JavaScript 中没有任何代码是立刻执行的，但一旦进程空闲则尽快执行。**

定时器对队列的工作方式是，**当特定时间过去后将代码插入**。

注意，给队列添加代码并不意味着对它立刻执行，而只能表示它会尽快执行。（队列谁来管理？不占线程吗？）

关于定时器要记住的最重要的事情是，**指定的时间间隔表示何时将定时器的代码添加到队列，而不是何时实际执行代码**。队列中所有的代码都要等到 JavaScript 进程空闲之后才能执行，而不管它们是如何添加到队列中的。

执行完一套代码后，JavaScript 进程返回一段很短的时间，这样页面上的其他处理就可以进行了。由于 JavaScript 进程会阻塞其他页面处理，所以必须有这些小间隔来防止用户界面被锁定（代码长时间运行中还有可能出现）。这样设置一个定时器，可以确保在定时器代码执行前至少有一个时间间隔。

#### 重复的定时器
使用 setInterval() 创建的定时器确保了定时器代码规则地插入队列中。这个方式的问题在于，定时器代码可能在代码再次被添加到队列之前还没有完成执行，结果导致定时器代码连续运行好几次，而之间没有任何停顿。幸好，JavaScript 引擎够聪明，能避免这个问题，当使用 setInterval() 时，仅当没有该定时器的任何其他代码实例时，才将定时器代码添加到队列中。这确保了定时器代码加入到队列中的最小时间间隔为指定间隔。

这种重复定时器的规则有两个问题：
- 某些间隔会被跳过
- 多个定时器的代码执行之间的间隔可能会比预期的小

为了避免 setInterval() 的重复定时器的缺点，可以使用 `链式 setTimeout() 调用的模式`：
```javascript
setTimeout(function(){
    // 处理中
    setTimeout(arguments.callee, interval); // arguments.callee 获取当前执行函数的引用
}, interval);
```

这样做的好处是在前一个定时器代码执行完之前，不会向队列插入新的定时器代码，确保不会有任何缺失的间隔。而且，他可以保证在下一次定时器代码执行之前，至少要等待指定的间隔，避免了连续的运行。

每个浏览器窗口、标签页或者 frame 都有其各自的代码执行队列。进行跨 frame 或者跨窗口的定时调用，当代码同时执行的时候可能会导致竞争条件。无论何时需要使用这种通信类型，最好是在接收 frame 或者窗口中创建一个定时器来执行代码。

#### Yielding Processes
运行在浏览器中的 JavaScript 都被分配了一个确定数量的资源。不同于桌面应用往往能够随意控制它们要的内存大小和处理器时间，JavaScript 被严格限制了，以防止恶意的 Web 程序员把用户的计算机搞挂了。

其中一个限制是长时间运行脚本的制约，如果代码运行超过特定的时间或者特定语句数量就不让它继续执行。否则会弹出一个浏览器错误的对话框，告诉用户某个脚本会用过长的时间执行，询问是允许其继续执行还是停止它。因为就需要确保用户用户看不到这个对话框。定时器是绕开此限制的方法之一。

脚本长时间运行的问题通常是由两个原因之一造成的：
- 过长的、过深嵌套的函数调用
- 进行大量处理的循环

后者通常遵循的模式如下：
```javascript
for(var i = 0;i  < len; i++){
    process();
}
```

此模式的问题在于要处理的项目的数量在运行前是不可知的。

展开循环之前，需要明确：
- 改处理是否必须同步完成？
- 数据是否必须按顺序完成？

如果都是“否”，就可以使用 **定时器分割这个循环**，这是一种叫做 **数组分块（array chunking）**的技术：基本的思路是为要处理的项目创建一个队列，然后使用定时器取出下一个要处理的项目进行处理，接着再设置另一个定时器。基本模式如下：

```javascript
setTimeout(function(){
    // 取出下一个条目并处理
    var item = array.shift();
    process(item);

    // 若还有条目，再设置另一个定时器
    if(array.length > 0){
        setTimeout(arguments.callee, 100);
    }
}, 100);
```

在数组分块模式中，array 变量本质上就是一个“待办事宜”列表。

```javascript
function chunk(array, process, context){
    setTimeout(function(){
        var item = array.shift();
        process.call(context, item);

        if(array.length > 0){
            setTimeout(arguments.callee, 100);
        }
    }, 100);
}
```

需要注意的是，传递给 chunk() 的数组是用作一个队列的，因此当处理数据的同时，数组中的条目也在改变，如果向保持原数组不变，则应该将该数组的克隆传递给 chunk()：

```javascript
chunk(data.concat(), fn, this);
```

数组分块的重要性在于它可以将多个项目的处理在执行队列上分开，在每个项目处理之后，给予其它的浏览器处理机会运行，这样就可以避免长时间运行脚本的错误。

一旦某个函数需要花 50 ms 以上的时间完成，那么最好看看能否将任务分割为一系列可以使用定时器的小任务。

#### 函数节流
浏览器中某些计算和处理要比其他的昂贵很多，连续进行可能会导致浏览器挂起甚至崩溃（例如 DOM 操作） => 为了绕开这个问题，可以使用定时器对其（指函数、计算处理过程、事件处理程序等）进行 **节流**。

函数节流背后的基本思想是指，某些代码不可以在没有间断的情况下连续重复执行。

所以，第一次调用函数，创建一个定时器，在指定的时间间隔之后运行代码。在第二次调用该函数时，它会清除前一次的定时器并设置另一个。如果前一个定时器已经执行过了，这个操作就没有任何意义。然而，如果前一个定时器尚未执行，其实就是将其替换为一个新的定时器。目的是 **只有在执行函数的请求停止了一段时间之后才执行**，该模式基本形式如下：

```javascript
var processor = {
    timeoutId: null,

    // 实际进行处理的方法
    performProcessing: function(){
        // 实际执行的代码
    },

    // 初始处理调用的方法
    process: function(){
        clearTimeout(this.timeoutId);

        var that = this;
        this.timeoutId = setTimeout(function(){
            that.performProcessing();
        }, 100);    // 如果 100 ms 之内调用了 process() 共 20 次，performProcessing() 仍只会被调用一次
    }
}

processor.process();
```

简化版本：
```javascript
function throttle(method, context){
    clearTimeout(method.tId);
    method.tId = setTimeout(function(){
        method.call(context);
    }, 100);
```

对于事件而言，有些连续触发的事件（如scroll、resize等），最好控制处理的频率，以确保浏览器不会在极短的时间内进行过多的计算（控制事件处理程序的执行 => 因此造成页面卡死或缓慢的原因在于事件处理程序的执行，而不是事件本身的触发）。

**只要代码是周期执行的**，都应该使用节流，但是你不能控制请求执行的速率（拿事件来说，即事件触发的频率）。

### 自定义事件


### 拖放


### 小结
- 函数：JavaScript 中函数非常强大，是第一类对象：
    - 使用闭包和函数环境切换，有很多使用函数的强大方法
    - 创建作用域安全的构造函数，确保在缺少 new 操作符时调用构造函数不会改变错误的环境对象
    - 可以使用惰性载入函数，将任何代码分支推迟到第一次调用函数的时候
    - 函数绑定可以让你在创建始终在指定环境中运行的函数（指定环境）
    - 函数柯里化可以让你创建已经填了某些参数的函数（指定参数）
    - 函数绑定和函数柯里化结合可以在任意环境中以任意参数执行任意函数
- ECMAScript 5 允许通过以下几种方式来创建防篡改对象
    - 不可扩展的对象，不允许给对象添加新的属性或方法
    - 密封的对象，也是不可扩展的对象，不允许删除已有的属性和方法
    - 冻结的对象，也是密封的对象，不允许重写对象的成员
- 定时器，setTimeout() 和 setInterval() 创建
    - 定时器代码是放在一个等待区域，直到时间间隔到了之后，此时将代码添加到 JavaScript 的处理队列中，等待下一次 JavaScript 进程空闲时被执行
    - 每次一段代码执行结束之后，都会有一小段空闲时间进行其他浏览器处理
    - 这种行为意味着，可以使用定时器将长时间运行的脚本切分为一小块一小块可以在以后运行的代码段。有助于 Web 应用对用户交互有更积极的响应
- JavaScript 中经常以事件的形式应用观察者模式。
    - 虽然事件常常和 DOM 一起使用，但是也可以自定义事件，有助于将不同部分的代码相互之间解耦。
- 拖放