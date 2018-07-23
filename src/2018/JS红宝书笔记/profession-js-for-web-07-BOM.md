## BOM
The Browser Object Model，浏览器对象模型

### window 对象
BOM 核心对象是 window ，表示浏览器的一个实例。
- 既是通过 JavaScript 访问浏览器窗口的一个接口
- 又是 ECMAScript 规定的 Global 对象

#### 全局作用域
所有在全局作用域中声明的变量、函数都会变成 window 对象的属性和方法。

定义全局变量 vs 在 window 对象上定义属性
- 全局变量不能通过 delete 操作符删除。而直接定义在 window 对象上定义的属性可以。（因为前者定义属性的`[[Configurable]]`特性值为false）
- 尝试访问未声明的变量会抛出错误，但是通过查询 window 对象，可以知道某个可能未声明的变量是否存在

#### 窗口关系及框架
如果页面中包含框架，则每个框架都有自己的 window 对象。并且保存在 frames 集合中。
- top 对象：始终指向最高（最外）层框架，也就是浏览器窗口
- parent 对象：始终指向当前框架的直接上层框架
- self 对象：始终指向 window

需要注意的是，在使用框架的情况下，浏览器中会存在多个Global对象，在每个框架中定义的全局变量会自动成为该框架中 window 对象的属性。由于每个 window 对象都包含原生类型的构造函数，因此每个框架都有一套自己的构造函数，这些构造函数一一对应，但并不相等。=>这个问题会影响到对跨框架传递的对象使用 instanceof 操作符。

#### 窗口位置
确定和修改 window 对象位置的属性和方法。（跨浏览器需要做好兼容性）

- window.screenLeft / window.screenX
- window.screenTop  / window.screenY
- window.moveTo()
- window.moveBy()

```javascript
var leftPos = (typeof window.screenLeft == 'number') ? window.screenLeft : window.screenX;
var topPos  = (typeof window.screenTop == 'number')  ? window.screenTop  : window.screenY;
```

#### 窗口大小
跨浏览器确定窗口大小同样不简单。

- window.innerWidth
- window.innerHeight
- window.outerWidth
- window.outerHeight

- 浏览器窗口本身尺寸
- 页面视图容器尺寸
- 视口（viewport）大小

- (window.)document.documentElement.clientWidth
- (window.)document.documentElement.clientHeight
- (window.)document.body.clientWidth
- (window.)document.body.clientHeight

对于移动设备，window.innerWidth 和 window.innerHeight 保存着可见视口，也就是屏幕上可见页面区域的大小。（移动 IE 浏览器不支持，但可以通过 document.documentElement.clientWidth 和 document.documentElement.clientHeight 提供相同的信息。）随着页面的缩放，这些值也会相应变化。

在其他移动浏览器中，document.documentElement 度量的是布局视口，即渲染后页面的实际大小（与可见视口不同，可见视口只是整个页面中的一小部分）。移动 IE 浏览器把布局视口的信息保存在 document.body.clientWidth 和 document.body.clientHeight 中。这些值不会随着页面缩放变化。

https://quirksmode.org/mobile/viewports2.html

- window.resizeTo()
- window.resizeBy()

#### 导航和打开窗口
指向新窗口的引用 = window.open(url, name, features, replace)

#### 间歇调用和超时调用
JavaScript 是**单线程语言**，但它允许通过设置超时值和间歇时间值来调度代码在特定的时刻执行。

##### 超时调用
window.setTimeout()，接受两个参数：
- 要执行的代码（可以是包含js代码的字符串，但会有性能损失）
- 以毫秒表示的时间（即在执行代码前需要等待多少毫秒），**但经过该时间后指定代码不一定执行**

原因如下：
- JavaScript 是一个单线程序的解释器，因此一定时间内只能执行一段代码。
- 为了控制要执行的代码，就有一个 JavaScript 任务队列。
- 这些任务会按照将它们添加到队列的顺序执行。setTimeout() 的第二个参数告诉 JavaScript 再过多长时间把当前任务添加到队列中。
- 如果队列是空的，那么添加的代码会立即执行；如果队列不是空的，那么它就要等前面的代码执行完了以后再执行。

调用 setTimeout() 之后，会返回一个数值 ID，表示超时调用。这个超时调用 ID 是计划执行代码的唯一标识符，可以用于**取消超时调用**。要取消尚未执行的超时调用计划，可以调用`clearTimeout()`方法并将相应的超时调用 ID 作为参数传递给它。

超时调用的代码都是在全局作用域下执行的，因此函数中 this 的值在非严格模式下指向 window 对象，在严格模式下是 undefined。

##### 间歇调用
间歇调用与超时调用类似，按照指定的时间间隔重复执行代码，直至间歇调用被取消或者页面被卸载。

window.setInterval()，接受参数与 setTimeout() 相同。同样，调用 setInterval() 方法会返回一个间歇调用 ID ，可用于在将来某个时刻取消间歇调用，可以调用`clearInterval()`方法并将相应的间歇调用 ID 作为参数传递给它。

**取消间歇调用的重要性要>>>高于取消超时调用**，因为在不加干涉的情况下，间歇调用将会一直执行到页面卸载。

**使用超时调用来模拟间歇调用是一种最佳模式**。（在开发环境下，很少使用真正的间歇调用，原因是后一个间歇调用可能会在前一个间歇调用结束之前启动）

#### 系统对话框
- window.alert()
- window.confirm()
- window.prompt()

通过这几个方法打开的对话框都是同步和模态的，即显示这些对话框时代码会停止执行，而关掉这些对话框代码又会恢复执行。

两个异步显示的对话框：
- window.print()：显示“打印”对话框
- window.find()：显示“查找”对话框

[什么是模态框](http://metaphor.space/2016/03/19/model-dialog/)

### location 对象
提供了与当前窗口中加载的文档有关的信息，还提供了一些导航功能。事实上，loaction 对象是很特别的一个对象，既是 window 对象的属性，也是 document 对象的属性=> window.location 和 document.location 引用的是同一个对象。

```javascript
window.location === document.location; // true
```

location 对象的用处不只表现在它保存着当前文档的信息，还表现在它将 URL 解析为独立的的片段。

#### 查询字符串参数
```javascript
function getQueryStringArgs(){
    // 取得查询字符串并去掉开头的问号
    var qs = (location.search.length > 0 ? location.search.substring(1) : ''),
        args = {},  // 保存数据的对象
        items = qs.length ? qs.split('&') : [], // 取得每一项
        item = null,
        name = null,
        value = null,
        // 在 for 循环中使用
        i = 0, 
        len = items.length;
    
    for(i = 0; i < len; i++){
        item = items[i].split('=');
        name = decodeURIComponent(item[0]);
        value = decodeURIComponent(item[1]);
        if(name.length){
            args[name] = value;
        }
    }

    return args;
}
```

#### 位置操作
- location.assign(<url>)
立即打开新URL，并在浏览器的历史记录中生成一条记录。location.href 或 window.location 设置为一个URL值，也会以该值调用 assign() 方法。

修改 location 对象的其他属性也可以改变当前加载的页面。每次修改 location 的属性（hash除外），页面都会以新 URL 重新加载。（都会在浏览器的历史记录中生成一条新纪录，用户单击“后退”按钮都会导航到前一个页面）

- location.replace(<url>)
不会在历史记录中生成新记录。

- location.reload()
重新加载当前显示的页面。如果调用 reload() 时不传递任何参数，页面就会以最有效的方式重新加载，也就是说，如果页面自上次请求以来并没有改变过，页面就会从浏览器缓存中重新加载。如果要强制从服务器重新加载，需要传递true，`location.reload(true)`。

位于 reload() 之后的代码**可能会也可能不会执行**，取决于网络延迟或系统资源等因素，所以最好将**reload()**放在代码的最后一行。

```javascript
location.reload();      // 重新加载（有可能从缓存中加载）
location.reload(true);  // 重新加载（从服务器重新加载）
```

### navigator 对象
识别客户端浏览器的事实标准。navigator.userAgents

### screen 对象
保存着与客户端显示器有关的信息，一般只用于站点分析

### history 对象
保存着用户上网的历史记录。

每个浏览器窗口、每个标签页乃至每个框架，都有自己的 hostory 对象与特定的 window 对象关联。

处于安全考虑，开发人员无法直接获取用户浏览的 URL ，但借由用户访问过的页面列表，同样可以在不知道实际 URL 的情况下实现后退和前进。

- history.go()
- history.back()
- history.forward()
- history.length
