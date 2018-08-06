## 事件
JavaScript 与 HTML 之间的交互是通过**事件**实现的。事件就是文档或浏览器窗口中发生的一些特定的交互瞬间。可以使用**侦听器（或处理事件）**来预订事件，以便事件发生时执行相应的代码。——“观察者模式”

### 事件流
事件流：描述的是从页面中接收事件的顺序。
- 事件冒泡（event bubbling）：即事件开始时由最具体的元素（文档中嵌套层次最深的那个节点）接收，然后逐级向上传播到较为不具体的节点（文档）。
- 事件捕获（event capturing）：不太具体的节点应该更早接收到事件，而最具体的节点应该最后接收到事件。事件捕获的用意在于在事件到达预定目标之前捕获它。

DOM事件流（规范）：“DOM2级事件”规定的事件流包括三个阶段：事件捕获阶段、处于目标阶段和事件冒泡阶段。首先发生的是事件捕获，为截获事件提供了机会，然后是具体的目标接收到事件，最后一个阶段是冒泡阶段，可以在这个阶段对事件作出响应。

### 事件处理程序
事件就是用户或浏览器自身执行某种动作，而响应某个事件的函数就叫做**事件处理程序（或事件侦听器）**，为事件指定处理程序的方式有好几种：
- HTML 事件处理程序：某个元素支持的每种事件，都可以使用一个与相应事件处理程序同名的 HTML 特性来指定，特性值是能够执行的 JavaScript 代码。

可以包含要执行的具体动作，也可以调用在页面其他地方定义的脚本。事件处理程序中的代码在执行时，有权访问全局作用域中的任何代码。通过局部变量 event，可以直接访问事件对象，无需定义，也不用从函数的参数列表中读取。函数内部，this 值等于事件的目标元素

缺点：
    - 存在时差问题，HTML 元素一出现就可能会触发事件，但事件处理程序有可能尚不具备执行条件，需要做好容错处理（try...catch）
    - HTML 与 JavaScript 代码紧密耦合
    - http://www.jibbering.com/faq/names/event_handler.html

- DOM 0 级事件处理程序：通过 js 指定事件处理程序的传统方式，就是将一个函数赋值给一个函数处理程序**属性**，但前提是**必须取得操作对象的引用**

每个元素（包括 window 和 document）都有自己的事件处理程序属性，这些属性通常全部小写，将这些属性的值设置为一个函数，就可以指定事件处理程序。使用 DOM 0 级方法指定的事件处理程序被认为是元素的方法，因此，这时候的事件处理程序是在元素的作用域中运行，换句话说，程序中的 this 引用当前元素。

```javascript
var btn = document.getElementById("myBtn");
btn.onclick = function(){
    alert(this.id); //"myBtn"
};
```

**以这种方式添加的事件处理程序会在事件流的冒泡阶段被处理**。

删除通过 DOM 0 级方法指定的事件处理程序的方法，将事件处理程序属性的值设为 null 即可：
```javascript
btn.onclick = null;
```
（如果使用 HTML 指定事件处理程序，那么在 js 中元素事件处理程序属性的值就是一个包含着在同名 HTML 属性中指定的代码的函数。将属性值置为 null，也可以删除使用 HTML 指定的事件处理程序）

- DOM 2 级事件处理程序

定义了两个方法，用于处理指定和删除事件处理程序：`addEventListener()`和`removeEventListener()`，所有 DOM 节点中都包含这两个方法，并且都接受 3 个参数：要处理的事件名、作为事件处理程序的函数和一个布尔值。布尔值为true：表示在捕获阶段调用事件处理程序；布尔值为false：表示在冒泡阶段调用事件处理程序。

事件处理程序也在在其依附的元素的作用域中运行的。使用 DOM 2 级方法可以添加多个事件处理程序（按照添加顺序触发）。

通过 addEventListener() 添加的事件处理程序只能使用 removeEventListener() 来移除，移除时传入的参数与添加事件处理程序时使用的参数相同=>匿名函数无法移除

大多数情况下，都是将事件处理程序添加到事件流的冒泡阶段，这样可以最大限度地兼容各种浏览器（why???，现代浏览器应当都能处理了吧）。最好只在需要 在事件到达目标之前截获它的时候 将事件处理程序添加到捕获阶段，如果不是特别需要，不建议在事件捕获阶段注册事件处理程序。（why???）

- IE 事件处理程序与跨浏览器的事件处理程序（jQuery源码）
`attachEvent()`和`detachEvent()`

https://github.com/jquery/jquery/blob/899c56f6ada26821e8af12d9f35fa039100e838e/src/event.js#L196

### 事件对象
在触发 DOM 上的某个事件时，会产生一个事件对象 event，包含着所有与事件有关的信息。

DOM 中的事件对象。兼容 DOM 的浏览器会将一个 event 对象传入到事件处理程序中，无论指定事件处理程序时使用什么方法（DOM 0 级/DOM 2 级），都会传入 event 对象。在通过 HTML 特性指定事件处理程序时，变量 event 中保存着 event 对象。

```javascript
var btn = document.getElementById("myBtn");
    btn.onclick = function(event){
    alert(event.type); //"click"
};
btn.addEventListener("click", function(event){
    alert(event.type); //"click"
}, false);
```

```html
<input type="button" value="Click Me" onclick="alert(event.type)"/>
```

event 对象包含与创建它的特定事件有关的属性和方法，触发的事件类型不一样，可用的属性和方法也不一样。

- `bubbles`：Boolean，只读，表明事件是否冒泡
- `cancelable`：Boolean，只读，表明是否可以取消事件的默认行为
- `currentTarget`：Element，只读，其事件处理程序当前正在处理事件的那个元素。
- `defaultPrevented`：Boolean，只读，为 true 表示已经调用了 preventDefault()。（DOM 3 级事件中新增）
- `detail`：Integer，只读，与事件相关的细节信息
- `eventPhase`：Integer，只读，调用事件处理程序的阶段：1-捕获阶段，2-“处于目标”阶段，3-冒泡阶段
- `preventDefault()`：Function，只读，取消事件的默认行为，前提是**cancelable 属性为 true**，才可以使用这个方法
- `stopImmediatePropagation()`:Function，只读，取消事件的进一步捕获或冒泡，同时阻止其他任何事件处理程序被调用。（DOM 3 级事件中新增）
- `stopPropagation()`：Function，只读，取消事件的进一步捕获或冒泡，前提是**bubbles 属性为 true **，才可以使用这个方法
- `target`：Element，只读，事件的目标
- `trusted`：Boolean，只读，为 true 表示事件是浏览器生成的，为 false 表示事件是由开发人员通过 JavaScript 创建的。（DOM 3 级事件中新增）
- `type`：String，只读，被触发的事件的类型
- `view`：AbstractView，只读，与事件关联的抽象视图，等同于发生事件的 window 对象

在事件处理程序内部，对象 this 始终等于 currentTarget 的值，而 target 则只包含事件的实际目标。如果直接将事件处理程序指定给了目标元素，则 this、currentTarget、target包含相同的值。
```javascript
var btn = document.getElementById("myBtn");
btn.onclick = function(event){
    alert(event.currentTarget === this); //true
    alert(event.target === this); //true
};

document.body.onclick = function(event){
    alert(event.currentTarget === document.body); //true
    alert(this === document.body); //true
    alert(event.target === document.getElementById("myBtn")); //true
};
```

在需要通过一个函数处理多个事件时，可以使用 type 属性：
```javascript
var btn = document.getElementById("myBtn");
var handler = function(event){
    switch(event.type){
        case "click":
            alert("Clicked");
            break;
        case "mouseover":
            event.target.style.backgroundColor = "red";
            break;
        case "mouseout":
            event.target.style.backgroundColor = "";
            break;
    }
};
btn.onclick = handler;
btn.onmouseover = handler;
btn. onmouseout = handler;
```

要阻止特定事件的默认行为，可以使用 preventDefault() 方法。例如链接的默认行为就是在被单击时会导航到其 href 特性指定的 URL。如果你想组织链接导航这一默认行为，那么通过链接的 onclick 事件处理程序可以取消。只有 cancelable 属性设置为 true 的事件，才可以使用 preventDefault() 来取消其默认行为。
```javascript
var link = document.getElementById("myLink");
link.onclick = function(event){
    event.preventDefault();
};
```

stopPropagation() 方法用于立即阻止事件在 DOM 层次中的传播，即取消进一步的事件捕获或冒泡。

eventPhase 属性，可以用来确定事件当前正位于事件流的哪个阶段。
- 如果是在捕获阶段调用的事件处理程序，那么 eventPhase 属性值为 1
- 如果事件处理程序处于目标对象上，则 eventPhase 属性值为 2
- 如果是在冒泡阶段调用的事件处理程序，eventPhase 属性值为 3
需要注意的是，尽管“处于目标”发生在冒泡阶段，但 eventPhase 仍然等于 2

```javascript
var btn = document.getElementById("myBtn");
btn.onclick = function(event){
    alert(event.eventPhase); //2
};
document.body.addEventListener("click", function(event){
    alert(event.eventPhase); //1
}, true);
document.body.onclick = function(event){
    alert(event.eventPhase); //3
};
```

---
梳理下相关概念：
- 首先事件流描述的是从页面中接收事件的顺序。DOM事件流总是不变的，某个事件被触发后，一般都是会经历`捕获阶段`、`处于目标阶段`、`冒泡阶段`，以click事件为例，当触发时，会经历document - html - body - ... - 具体元素 - ... - body - html - document，可以理解为在事件流的“流动”过程中，每经过一个节点都可以在上面做事情（类似于钩子函数的概念），而这个就是事件处理函数，而在这个过程中，每个节点都可以注册一个函数（当事件流到达节点时，就会触发函数的执行）。
- 注册事件处理函数的节点称之为 currentTarget （事件处理程序中的 this 也指向这个节点），而在事件开始触发的那个节点是 target。以上述点击事件为例，在那一个点上最开始接触到实时点击的就是 target
- **事件流经过的地方如果注册了相应的事件处理程序，就会被触发**
---

**只有在事件处理程序执行期间，event 对象才会存在；一旦事件处理程序执行完成，event 对象就会被销毁**

IE 中的事件对象与跨浏览器的事件对象。（暂不深入研究）

### 事件类型
Web 浏览器中可能发生的事件有很多类型，不同类型的事件具有不同信息，DOM 3 级事件规定了以下几类事件：
- UI（User Interface，用户界面） 事件
- 焦点事件
- 鼠标事件
- 滚轮事件
- 文本事件
- 键盘事件
- 合成事件
- 变动（mutation）事件
- ……

如何封装一个自定义事件？？？自定义事件有事件流的概念吗？另外，其实在当前混合式开发过程中，容器也会提供一些事件？事件 vs 生命周期？

#### UI 事件
现有 UI 事件如下：
- DOMActivate 事件（已废弃）：表示元素已经被用户操作（键盘/鼠标）激活
- load 事件：
    - 当页面完全加载后在 window 上面触发
    - 当所有框架都加载完毕后在框架集上面触发
    - 当图像加载完毕后在 img 元素上面触发
    - 当嵌入的内容加载完毕后在 object 元素上面触发
- unload 事件：
    - 当页面完全卸载后在 window 上面触发（只要用户从一个页面切换到另一个页面，就会发生 unload 事件）。**利用这个事件最多的情况是清除引用，以避免内存泄露**
    - 当所有框架都卸载完毕后在框架集上面触发
    - 当图像卸载完毕后在 img 元素上面触发
    - 当嵌入的内容卸载完毕后在 object 元素上面触发
- abort 事件：在用户停止下载过程中，如果嵌入的内容没有加载完，则在 object 元素上面触发
- error 事件：
    - 当发生 JavaScript 错误时在 window 上面触发
    - 当无法加载图像时在 img 元素上面触发
    - 当无法加载嵌入内容时在 object 元素上面触发
    - 当有一或多个框架无法加载时在框架集上面触发
- select 事件：当用户选择文本框（input/textarea）中的一或多个字符时触发
- resize 事件：当窗口或框架的大小变化时在 window 或框架上面触发
- scroll 事件：当用户滚动带滚动条的元素中的内容时，在该元素上面触发。body 元素中包含所加载页面的滚动条

#### 焦点事件
- blur 事件：在元素失去焦点时触发，这个事件不会冒泡
- DOMFocusIn 事件
- DOMFocusOut 事件
- focus 事件
- focusin 事件
- focusout 事件

当焦点从一个元素（A）移动到另一个元素（B），会依次触发下列事件：
- focusout - A
- focusin - B
- blur - A
- DOMFocusOut - A
- focus - B
- DOMFocusIn - B

#### 鼠标和滚轮事件
- click 事件
- dbclick 事件
- mousedown 事件
- mouseenter 事件
- mouseleave 事件
- mousemove 事件
- mouseout 事件
- mouseover 事件
- mouseup 事件

mousedown、mouseup、click、dbclick触发顺序：
- mousedown 事件
- mouseup 事件
- click 事件
- mousedown 事件
- mouseup 事件
- click 事件
- dbclick 事件

- 客户区坐标位置：clientX 和 clientY
- 页面坐标位置：pageX 和 pageY
- 屏幕坐标位置：screenX 和 screenY
- 修改键：Shift、Ctrl、Alt、Meta（Windows/Cmd） => shiftKey、ctrlKey、altKey、metaKey
- 相关元素：relatedTarget
- 鼠标按钮：button

触摸设备

无障碍访问

#### 键盘与文本事件
- keydown 事件
- keypress 事件
- keyup 事件

- 键码：keyCode
- 字符编码：charCode
- textInput事件

#### 复合事件
#### 变动事件
#### HTML 5 事件
- contextmenu 事件
- beforeunload 事件：之所以有发生在 window 对象上的 beforeunload 事件，是为了让开发人员有可能在页面卸载前阻止这一操作。这个事件会在浏览器卸载页面之前触发，可以通过它来取消卸载并继续使用原有页面。但是，**不能彻底取消这个事件，因为那就相当于让用户无法离开当前页面了**。为此，这个事件的意图是将控制权交给用户（弹出对话框）。
```javascript
EventUtil.addHandler(window, "beforeunload", function(event){
    event = EventUtil.getEvent(event);
    var message = "I'm really going to miss you if you go.";
    event.returnValue = message;
    return message;
});
```

- DOMContentLoaded 事件
window 的 load 事件会在页面中的一切都加载完毕时触发，但这个过程可能会因为要加载的外部资源过多而破费周折。DOMContentLoaded 事件则在形成完整的 DOM 树之后就会触发，不理会图像、JavaScript 文件、CSS 文件或其他资源是否已经加载完毕。

要处理 DOMContentLoaded 事件，可以为 document 或 window 添加相应的事件处理程序（尽管这个事件会冒泡到 window ，但它的目标实际上是 document）
```javascript
EventUtil.addHandler(document, "DOMContentLoaded", function(event){
    alert("Content loaded");
});
```

通常 DOMContentLoaded 事件既可以添加事件处理程序，也可以执行其他 DOM 操作。这个事件始终都会在 load 事件之前触发。

对于不支持 DOMContentLoaded 事件的浏览器，建议在页面加载期间设置一个时间为 `0毫秒` 的超时调用：
```javascript
setTimeout(function(){
    // attach event handlers here
    // 在此添加事件处理程序
}, 0);
```
=> “在当前 JavaScript 处理完成后立即运行这个函数”，在页面下载和构建期间，只有一个 JavaScript 处理过程，因此超时调用会在该过程结束时立即触发。至于能否与 DOMContentLoaded 被触发的时间能否同步，主要还是取决于用户使用的浏览器和页面中的其他代码。为了确保有效，必须将其作为页面中的第一个超时调用，但也还是无法保证在所有环境中该超时调用一定会早于 load 事件被触发。

- readystatechange 事件（注意兼容性与差异）：readyState 属性
    - uninitialize（未初始化）：对象存在但尚未初始化
    - loading（正在加载）：对象正在加载数据
    - loaded（加载完毕）：对象加载数据完成
    - interactive（交互）：可以操作对象了，但还没有完全加载
    - compete（完成）：对象已经加载完毕

- pageshow 和 pagehide 事件
- hashchange 事件
HTML 5 新增了此事件，以便在 URL 的参数列表（及 URL 中“#”号后面的所有字符串）发生变化时通知开发人员。必须要把 hashchange 事件处理程序添加给 window 对象，然后 URL 参数列表只要变化就会调用。对应的事件对象额外包含两个属性：oldURL 和 newURL，分别保存着参数列表变化前后的完整 URL。
```javascript
EventUtil.addHandler(window, "hashchange", function(event){
    alert("Current hash: " + location.hash);
});

var isSupported = ("onhashchange" in window); // 可能在 IE 下还有 bug

var isSupported = ("onhashchange" in window) && (document.documentMode === undefined || document.documentMode > 7);
```

#### 设备事件
- orientationchange 事件
- MozOrientation 事件（Firefox）
- deviceorientation 事件
- devicemotion 事件

#### 触摸与手势事件
[Touch Events 规范 - W3C](https://dvcs.w3.org/hg/webevents/raw-file/tip/touchevents.html)

- 触摸事件
    - touchstart 事件：当手指触摸屏幕时触发，即使已经有一个手指放在了屏幕上也会触发
    - touchmove 事件：当手指在屏幕上滑动时连续地触发，在这个事件发生期间，调用 preventDefault() 可以组织滚动
    - touchend 事件： 当手指从屏幕上移开时触发
    - touchcancel 事件：当系统停止跟踪触摸时触发。（？关于此事件的确切触发时间，文档中没有明确说明）

上述事件都会冒泡，也都可以取消。虽然没在 DOM 规范中定义，但是是以兼容 DOM 的方式实现的 => 每个触摸事件都提供了在鼠标事件中常见属性：bubbles、cancelable、view、clientX、clientY、screenX、screenY、detail、altKey、shiftKey、ctrlKey 和 metaKey。

除了常见的 DOM 属性外，触摸事件还包含下列三个用于跟踪触摸的属性：
1. touches：表示当前跟踪的触摸操作的 Touch 对象的数组
2. targetTouches：特定于事件目标的 Touch 对象的数组
3. changeTouches：表示自上次触摸以来发生了什么改变的 Touch 对象的数组

每个 Touch 对象包含下列属性：clientX、clientY、identifier、pageX、pageY、screenX、screenY、target

```javascript
function handleTouchEvent(event){
    //only for one touch
    if (event.touches.length == 1){
        var output = document.getElementById("output");
        switch(event.type){
            case "touchstart":
                output.innerHTML = "Touch started (" + event.touches[0].clientX + "," + event.touches[0].clientY + ")";
                break;
            case "touchend":
                output.innerHTML += "<br>Touch ended (" + event.changedTouches[0].clientX + "," + event.changedTouches[0].clientY + ")";
                break;
            case "touchmove":
                event.preventDefault(); //prevent scrolling
                output.innerHTML += "<br>Touch moved (" + event.changedTouches[0].clientX + "," + event.changedTouches[0].clientY + ")";
                break;
        }
    }
}
EventUtil.addHandler(document, "touchstart", handleTouchEvent);
EventUtil.addHandler(document, "touchend", handleTouchEvent);
EventUtil.addHandler(document, "touchmove", handleTouchEvent);
```

- 手势事件
    - gesturestart
    - gesturechange
    - gestureend

### 内存和性能
起因：由于**事件处理程序**可以为现代 Web 应用程序提供交互能力，所以开发者会向页面中添加大量的处理程序。（注意：真正引起内存问题的是注册在具体节点的事件处理程序，而不是事件和事件流）

内因：在 JavaScript 中，添加到页面上的事件处理程序数量会直接影响页面的整体运行性能。
- 函数即对象，会占用内存。内存中对象越多，性能越差
- 必须事先指定所有事件处理程序而导致的**DOM访问次数**，会延迟整个页面的交互就绪时间

#### 事件委托
对“事件处理程序过多”问题的解决方案就是——**“事件委托”**。事件委托利用了**事件冒泡**，只指定一个事件处理程序，就可以管理某一类型的所有事件。

```html
<ul id="myLinks">
    <li id="goSomewhere">Go somewhere</li>
    <li id="doSomething">Do something</li>
    <li id="sayHi">Say hi</li>
</ul>
```

传统做法：
```javascript
var item1 = document.getElementById("goSomewhere");
var item2 = document.getElementById("doSomething");
var item3 = document.getElementById("sayHi");
EventUtil.addHandler(item1, "click", function(event){
    location.href = "http://www.wrox.com";
});
EventUtil.addHandler(item2, "click", function(event){
    document.title = "I changed the document's title";
});
EventUtil.addHandler(item3, "click", function(event){
    alert("hi");
});
```

使用事件委托，在 DOM 树尽量最高的层次上添加一个事件处理程序：
```javascript
var list = document.getElementById("myLinks");
EventUtil.addHandler(list, "click", function(event){
    event = EventUtil.getEvent(event);
    var target = EventUtil.getTarget(event);    // 事件目标是被单击的列表项
    switch(target.id){
        case "doSomething":
        document.title = "I changed the document's title";
        break;
    case "goSomewhere":
        location.href = "http://www.wrox.com";
        break;
    case "sayHi":
        alert("hi");
        break;
    }
});
```

#### 移除事件处理程序
每当将事件处理程序指定给元素时，运行中的浏览器代码与支持页面交互的 JavaScript 代码之间就会建立一个连接。=>这种链接越多，页面执行起来就越慢（事件委托，可以限制建立的连接数量）。另外，**在不需要的时候移除事件处理程序**，也是解决这个问题的一种方案。=>内存中留有那些过时不用的“空事件处理程序”，也是造成 Web 应用程序内存与性能问题的主要原因。

造成空事件处理程序的原因有以下情况：
- 从文档中移除带有事件处理程序的元素时。
removeChild()、replaceChild()、innerHTML => 原来添加到元素中的事件处理程序极有可能无法被当作垃圾回收。
```html
<div id="myDiv">
    <input type="button" value="Click Me" id="myBtn">
</div>
<script type="text/javascript">
var btn = document.getElementById("myBtn");
btn.onclick = function(){
    // ...
    document.getElementById("myDiv").innerHTML = "Processing..."; // 麻烦了！
    };
</script>
```

如果你知道某个元素即将被移除，最好手工移除事件处理程序：
```html
<div id="myDiv">
    <input type="button" value="Click Me" id="myBtn">
</div>
<script type="text/javascript">
    var btn = document.getElementById("myBtn");
    btn.onclick = function(){
        // ...
        btn.onclick = null; // 移除事件处理程序
        document.getElementById("myDiv").innerHTML = "Processing...";
    };
</script>
```

注意：**在事件处理程序中删除目标元素也能阻止事件冒泡，目标元素在文档中是事件冒泡的前提**=>如果事先知道将来有可能使用 innerHTML 替换掉页面中的某一部分，那么就可以不直接将事件处理程序添加到该部分的元素中，而通过将事件处理程序指定给较高层次的元素，同样能够处理该区域中的事件。

- 卸载页面的时候
在页面卸载之前没有清理事件处理程序，会滞留在内存中。

最好的做法是在页面卸载之前，先通过 onunload 事件处理程序移除所有事件处理程序。（怎么移除？？？一个个移除很麻烦，事件委托移除很简单）

这个过程可以理解为，通过 onload 事件处理程序添加的东西，最后都要通过 onunload 事件处理程序将它们移除。


### 模拟事件
=== 模拟（触发）事件

事件，就是网页中某个特别值得关注的瞬间。

事件经常由用户操作或通过其他浏览器功能来触发，也可以使用 JavaScript 在任意时刻来触发特定的事件。

- DOM 中的事件模拟：document.createEvent()
```javascript
var btn = document.getElementById("myBtn");
// 创建事件对象
var event = document.createEvent("MouseEvents");
// 初始化事件对象
event.initMouseEvent("click", true, true, document.defaultView, 0, 0, 0, 0, 0, false, false, false, false, 0, null);
// 触发事件
btn.dispatchEvent(event);
```