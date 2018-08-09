## 错误处理与调试

### 浏览器报告的错误

### 错误处理
####  try-catch

```javascript
try{
    // 可能会导致错误的代码
} catch(error){
    // 在错误发生时怎么处理
}
```

如果在 try 块中的任何代码发生了错误，就会立即退出代码执行过程，然后接着执行 catch 块。此时 catch 块会接收到一个包含错误信息的对象。

##### finally 子句
无论如何都会执行（无论 try 或 catch 语句块中包含什么代码——甚至 return 语句）
```javascript
function testFinally(){
    try{
        return 2;   // 会被忽略
    } catch(error){
        return 1;
    } finally {
        return 0;   // 函数最终返回 0
    }
}
```

- 使用了 finally，catch 就可选
- 使用了 finally，try 和 catch 中的 return 语句就会被忽略

##### 错误类型
- Error：基类型，主要目的是供开发人员抛出自定义错误
- EvalError：
- RangeError
- ReferenceError
- SyntaxError
- TypeError
- URIError

#### 抛出错误
throw，用于随时抛出自定义错误。在遇到 throw 操作符时，代码会**立即停止执行**（其他语言也可能会这样吗？）。仅当有 try-catch 语句捕获到被抛出的值时，代码才会继续执行。

- 通过抛出错误类型的实例对象，模拟浏览器错误
- 继承 Error 创建自定义错误类型
```javascript
function CustomError(message){
    this.name = 'CustomError';
    this.message = message;
}

CustomError.prototype = new Error();

throw new CustomError('My message');
```

要针对函数为什么会执行失败**给出更多信息**（=>便于调试，查找错误来源），抛出自定义错误是一种很方便的方式。应该在出现某种特定的已知错误条件，导致函数无法正常执行时抛出错误。换句话说，浏览器会在某种特定的条件下执行函数时抛出错误。

开发过程中，重点关注函数和可能导致函数执行失败的因素，良好的错误处理机制应该可以确保代码中只发生你自己抛出的错误。

抛出错误（throw）与捕获错误（try-catch）：
- 捕获错误的目的在于避免浏览器以默许方式处理它们，只应该捕获那些你确切地知道该如何处理的错误
- 抛出错误的目的在于提供错误发生具体原因的消息

#### 错误（error）事件
任何没有通过 try-catch 处理的错误都会触发 window 对象的 error 事件。在任何 web 浏览器中，onerror 时间处理程序都不会创建 event 对象，但它可以接收三个参数：错误消息、错误所在的 URL 和行号。

要指定 onerror 时间处理程序，必须使用如下所示的 DOM 0 级技术：
```javascript
window.onerror = function(message, url, line){
    alert(message);
}
```

只要发生错误，无论是不是浏览器生成的，都会触发 error 事件，并执行这个事件处理程序。然后，浏览器默认的机制发挥作用，像往常一样显示出错误信息。可以在事件处理程序中返回 false，可以阻止浏览器报告错误的默认行为。
```javascript
window.onerror = function(message, url, line){
    alert(message);
    return false;   // 这样实际上就充当了整个文档中的 try-catch 语句，可以捕获所有无代码处理的运行时错误
}
```

图像页支持 onerror 事件，只要图像的 src 特性中的 URL 不能返回可以被识别的图像格式，就会触发 error 事件。

#### 处理错误的策略
记录和监控系统 => 分析错误模式，追查错误原因，同时帮助确定错误会影响到多少用户。

#### 常见的错误类型
错误处理的核心，是首先要知道代码里会发生什么错误。

由于 JavaScript 是松散类型的，而且也不会验证函数的参数，因此错误只会在代码运行期间出现。（why？）

一般来说，需要关注三种错误：
- 类型转换错误
- 数据类型错误
- 通信错误

以上错误会 在特定的模式下 或者 没有对值进行足够的检查的情况下发生。

类型转换错误：发生在使用某个操作符，或者使用其他可能会自动转换值的数据类型的语言结构时。

数据类型错误：松散类型 => 在使用变量和函数参数之前，不会对它们进行比较以确保它们的数据类型正确。

通信错误：随着 Ajax 编程的兴起 => Web APP 在其生命周期内动态加载信息或功能。与服务器之间的任何一次通信，都有可能会产生错误。

#### 区分致命错误和非致命错误
非致命错误
- 不影响用户的主要任务
- 只影响页面的一部分
- 可以恢复
- 重复相同操作可以消除错误

致命错误
- 应用程序根本无法继续运行
- 错误明显影响到了用户的主要操作
- 会导致其他连带错误

#### 把错误记录到服务器
```javascript
function logError(sev, msg){
    var img = new Image();
    img.src = "log.php?sev=" + encodeURIComponent(sev) + "&msg=" + encodeURIComponent(msg);
}
```

- 所有浏览器都支持 Image 对象，包括那些不支持 XMLHttpRequest 对象的浏览器
- 可以避免跨域限制
- 在记录错误的过程中出问题的概率比较低

只要是使用 try-catch 语句，就应该把相应错误记录到日志中。

### 调试技术
#### 将消息记录到控制台
- console.error(message)：将 错误消息   记录到控制台
- console.info (message)：将 信息性消息 记录到控制台
- console.log  (message)：将 一般消息   记录到控制台
- console.warn (message)：将 警告消息   记录到控制台

#### 将消息记录到当前页面
```javascript
function log(message) {
    var console = document.getElementById("debuginfo");
    if (console === null) {
        console = document.createElement("div");
        console.id = "debuginfo";
        console.style.background = "#dedede";
        console.style.border = "1px solid silver";
        console.style.padding = "5px";
        console.style.width = "400px";
        console.style.position = "absolute";
        console.style.right = "0px";
        console.style.top = "0px";
        document.body.appendChild(console);
    }
    console.innerHTML += "<p>" + message + "</p>";
}
```

#### 抛出错误
```javascript
function assert(condition, message){
    if (!condition){
        throw new Error(message);
    }
}

function divide(num1, num2){
    assert(typeof num1 == "number" && typeof num2 == "number",
           "divide(): Both arguments must be numbers.");
    return num1 / num2;
}
```

### 小结
避免浏览器响应 JavaScript 错误的方法：
- 在可能发生错误的地方使用 try-catch，有机会以适当的方式对错误给出响应，而不必沿用浏览器处理错误的机制
- 使用 window.onerror 时间处理程序，这种方式可以接受 try-catch 不能处理的所有错误

另外，对任何 Web 应用程序都应该分析可能的错误来源，并制定处理错误的方案：
- 首先，必须要明确致命错误和非致命错误
- 其次，再分析代码，以判断最可能发生的错误。主要原因有：
    - 类型转换
    - 未充分检测数据类型
    - 发送给服务器或从服务器接收到的数据有错误