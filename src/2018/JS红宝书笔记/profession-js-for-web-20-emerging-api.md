## 新兴的 API

### requestAnimationFrame()

**动画：JavaScript 动画 vs CSS 变换及动画**

#### 早期动画循环

在 JavaScript 中创建动画的典型方式，就是使用 setInterval() 方法来控制所有动画。

```javascript
(function() {
	function updateAnimations() {
		doAnimation1();
		doAnimation2();
		// 其他动画
	}
	setInterval(updateAnimations, 100);
})();
```

为了创建一个小型动画库，updateAnimations() 方法就得不断循环地运行每个动画，并相应地改变不同元素的状态。

编写这种动画循环的关键是 **要知道延迟时间多长合适**。

- 一方面，循环间隔必须 **足够短**，这样才能让不同的动画效果显得更 **平滑流畅**；
- 另一方面，循环间隔还要 **足够长**，这样才能确保浏览器 **有能力** 渲染产生的变化。

大多数电脑显示器的刷新频率是 60 Hz，大概相当于每秒钟重绘 60 次，大多数浏览器都对重绘操作加以限制，不超过显示器的重绘频率，因为即使超过那个频率用户体验也不会有提升。

因此，最平滑动画的最佳循环间隔是 1000ms/60，约等于 17 ms。但无论是 setInterval() 还是 setTimeout() 都不十分精确，以毫秒表示的延迟时间并不代表到时候一定会执行动画代码，而仅代表到时候会把代码添加到任务队列中。如果 UI 线程繁忙，比如忙于处理用户操作，那么即使把代码加入队列也不会立即执行。

#### 循环间隔的问题

**知道什么时候绘制下一帧是保证动画平滑的关键**。开发人员没有办法确保浏览器按时绘制下一帧。浏览器使用的计时器的精度并非精确到毫秒级别（且有差异）。而且浏览器都开始限制后台标签页或不活动标签页的计时器。

#### requestAnimationFrame

Robert O'Callahan 提出：

- CSS 变换和动画的优势在于浏览器知道什么时候开始，因此会计算出正确的循环间隔，在恰当的时候刷新 UI
- 对于 JavaScript 动画，浏览器无从知晓什么时候开始。

=> mozRequestAnimationFrame() 方法告诉浏览器某些 JavaScript 代码将要执行动画，这样浏览器可以在运行某些代码后进行适当的优化

mozRequestAnimationFrame() 方法接收一个参数，即在重绘屏幕前调用的一个函数，这个函数负责改变下一次重绘时的 DOM 样式。（且只运行一次） => 解决了浏览器不知道 JavaScript 动画什么时候开始、不知道最佳循环间隔时间的问题

但还不知道代码到底什么时候执行？mozRequestAnimationFrame() 函数也会接收一个参数——时间码（从 1970 年 1 月 1 日起至今的毫秒数），表示下一次重绘的实际发生时间

- mozRequestAnimationFrame()
- webkitRequestAnimationFrame()
- msRequestAnimationFrame()

==> requestAnimationFrame()

### Page Visibility API

不知道用户是不是正在与页面交互。如果页面最小化了或者隐藏在了其他标签页后面，有些功能是可以停下的，比如轮询服务器或者某些动画效果。

Page Visibility API（页面可见性 API）可以知道页面是否对用户可见。

- document.hidden：页面是否隐藏（页面在后台标签页或者浏览器最小化）
- document.visibilityState：

  <!-- prettier-ignore -->
  
	- 页面在后台标签页中或者浏览器最小化 
	- 页面在前台标签页中 
	- 实际的页面已经隐藏，但用户可以看到页面的预览（类似于 window 7 中任务栏鼠标移入的预览）
	- 页面在屏幕外执行预渲染处理

- visibilitychange 事件：当文档从可见 -> 不可见 或 从不可见 -> 可见 时，触发该事件。

### Geolocation API

地理定位（geolocation），JavaScript 代码能够访问到用户的当前位置信息。

当然，在访问之前必须得到用户的明确许可（一般如果页面尝试访问地理位置信息，浏览器就会显示一个对话框，请求用户许可共享其位置信息），即同意在页面中共享其位置信息。

navigator.geolocation 对象：

- getCurrentPosition()，调用这个方法就会触发请求用户共享地理定位信息的对话框。接收 3 个参数：成功回调、失败回调（可选）、选项对象（可选）。其中，
  成功回调函数会接收到一个 Position 对象参数，该对象有两个属性：coords（包含与位置相关的信息） 和 timestamp。 失败回调函数也会接收到一个对象参数，包含两个属性：message（错误信息） 和 code（错误代码，1-用户拒绝共享、2-位置无效、3-超时）。选项对象，用于设定信息的类型，可以设定的选项有三个：enableHighAccuracy（布尔值，表示必须尽可能使用最准确的位置信息，默认值 false，设置为 true 需要时间更长，消耗更多电量）、timeout（以毫秒数表示的等待位置信息的最长时间）和 maximumAge（表示上一次取得的坐标信息的有效时间，以毫秒表示，如果时间到则重新取得新坐标信息，如果不需要频繁更新用户的位置信息，可以将 maximumAge 设置为 Infinity，从而始终都使用上一次的坐标位置）。
- watchPosition()：跟踪位置，与定时调用 getCurrentPosition() 效果相同（只有等待系统发出位置已改变，不会自己轮询位置）。返回一个数值标识符，用于跟踪监控的操作。基于这个返回值可以取消监控操作，只要将其传递给 clearWatch() 方法即可。

### File API

```html
<input type="file"/>
```

File API 在表单的文件输入字段的基础上，添加了一些直接访问文件信息的接口。HTML 5 在 DOM 中为文件输入元素添加了一个 files 集合，在通过文件输入字段选择了一或多个文件时，files 集合中将包含一组 File 对象，每个 File 对象对应着一个文件，每个 File 对象都有下列只读属性：name、size、type、lastModifiedDate

#### FileReader 类型

FileReader 类型实现的是一种异步文件读取机制，可以想象成 XMLHTTPRequest，区别在于它读取的是文件系统，而不是远程服务器。

#### 读取部分内容

File 对象 slice() 方法，接收两个参数：起始字节、要读取的字节数。返回一个 Blob 的实例（File 类型的父类型）

#### 对象 URL

对象 URL 也成为 Blob URL，指的是引用保存在 File 或 Blob 中数据的 URL。使用对象 URL 的好处是可以不必把文件内容读取到 JavaScript 中而直接使用文本内容。

window.URL.createObjectURL(<File 或 Blob 对象>)，返回一个字符串，指向一块内存的地址

window.URL.revokeObjectURL()

#### 读取拖放到文件

结合拖放 API 和 文件 API

#### 使用 XHR 上传文件

### Web 计时

Web Timing API，通过 JavaScript 就能使用浏览器内部的度量结果（之前是使用 Date 对象），通过直接读取这些信息可以做任何想做的分析。

window.performance 对象

### Web Workers

长时间运行的 JavaScript 会导致浏览器冻结用户界面，Web Workers 规范通过让 JavaScript 在后台运行解决这个问题。

浏览器实现 Web Workers 规范的方式有很多种，可以使用线程、后台进程或者运行在其他处理器核心上的进程（不需要太关心），可以放心地运行 JavaScript，而不必担心影响用户体验了。

#### 使用 Worker

```javascript
var worker = new Worker("stufftodo.js"); // 实例化 Worker 对象，传入要执行的 JavaScript 文件名就可以创建一个新的 Web Worker
```

运行代码会导致浏览器下载 js 文件，但只有 Worker 接收到消息才会实际执行文件中的代码。

要给 Worker 传递消息，可以使用 postMessage() 方法。

Worker 是通过 message 和 error 事件与页面通信的。

调用 terminate() 方法可以停止 Worker 的工作。

#### Worker 全局作用域

Web Worker 所执行的 JavaScript 代码与当前网页中的代码不共享作用域，且其中不能访问 DOM，也无法通过任何方式影响页面的外观。全局对象是 worker 对象本身。

### 小结

- requestAnimationFrame()：是一个着眼于优化 JavaScript 动画的 API，能够在动画运行期间发出信号。通过这种机制，浏览器就能够自动优化屏幕重绘操作
- Page Visibility API：让开发人员知道用户什么时候正在看着页面，而什么时候是隐藏的
- Geolocation API：在得到许可的情况下，可以确定用户所在的位置
- File API：可以读取文件内容，用于显示、处理和上传，与拖放结合，可以创造出拖放上传功能
- Web Timing：给出了页面加载和渲染过程的很多信息，对性能优化非常有价值
- Web Workers：可以运行异步 JavaScript 代码，避免阻塞用户界面。在执行负载计算和数据处理的时候，这个 API 非常有用。
