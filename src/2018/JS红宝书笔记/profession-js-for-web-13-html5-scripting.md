## HTML 5 脚本编程

### 跨文档消息传递
cross-document messaging

XDM

postMessage() 方法与 window 上的 message 事件

不降低同源策略安全性的前提下，在来自不同域的文档间传递消息

### 原生拖放
- dragstart
- drag
- dragend

实现一个拖拽 demo

### 媒体元素
- audio
- video

### 历史状态管理
hashChange 事件，可以知道 URL 的参数什么时候发生了变化。

通过状态管理 API，能够在不加载页面的情况下改变浏览器的URL。=> history.pushState()，接收三个参数：状态对象、新状态的标题和可选的相对 URL。
```javascript
history.pushState({name: 'Nicholas'}, 'Nicholas page', 'nicholas.html');
```

执行 pushState() 方法后，新的状态信息就会被加入历史状态栈，而浏览器地址栏也会变成新的**相对 URL**。

但是，浏览器并不会真的向浏览器发送请求，即使状态改变之后查询 location.href 也会返回与地址栏中相同的地址。

且，pushState() 会创建新的历史状态，按下“后退”，会触发 window 对象的 popState 事件（其中的 state 属性，包含这当初以第一参数传递给 pushState 的状态对象）

得到这个状态对象后，必须把页面重置为状态对象中的数据表示的状态（浏览器不会自动做这些）。浏览器加载的第一个页面没有状态，因此单击“后退”按钮返回浏览器加载的第一个页面时，event.state 值为 null

要更新当前状态，可以调用 replaceState()，调用这个方法不会在历史状态栈中创建新状态，只会重写当前状态。

历史状态管理可以让我们不必卸载当前页面即可修改浏览器的历史状态栈。有了这种机制，用户就可以通过“后退”和“前进”按钮在页面状态间切换，而这些状态完全由 JavaScript 进行控制。