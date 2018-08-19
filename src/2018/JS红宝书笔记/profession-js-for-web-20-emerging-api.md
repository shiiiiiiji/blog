## 新兴的 API
### requestAnimationFrame()
#### 早期动画循环

```javascript
(function(){
    function updateAnimations(){
        doAnimation1();
        doAnimation2();
        // etc.
        // 其他动画
    }
    setInterval(updateAnimations, 100);
})();
```

为了创建一个小型动画库，updateAnimations() 方法就得不断循环地运行每个动画，并相应地改变不同元素的状态。

编写这种动画循环的关键是要知道延迟时间多长合适。一方面，循环间隔必须足够短，这样才能让不同的动画效果显得更平滑流畅；另一方面，循环间隔还要足够长，这样才能确保浏览器有能力渲染产生的变化。大多数电脑显示器的刷新频率是 60 Hz，大概相当于每秒钟重绘 60 次，大多数浏览器都对重绘操作加以限制，不超过显示器的重绘频率，因为即使超过那个频率用户体验也不会有提升。

因此，最平滑动画的最佳循环间隔是 1000ms/60，约等于 17 ms。但无论是 setInterval() 还是 setTimeout() 都不十分精确，以毫秒表示的延迟时间并不代表到时候一定会执行动画代码，而仅代表到时候会把代码添加到任务队列中。如果 UI 线程繁忙，比如忙于处理用户操作，那么即使把代码加入队列也不会立即执行。

#### 循环间隔的问题
知道什么时候绘制下一帧是保证动画平滑的关键。

开发人员没有办法确保浏览器按时绘制下一帧。浏览器使用的计时器的精度并非精确到毫秒级别（且有差异）。浏览器都开始限制后台标签页或不活动标签页的计时器。

#### mozRequestAnimationFrame
Robert O'Callahan 提出：
- CSS 变换和动画的优势在于浏览器知道什么时候开始，因此会计算出正确的循环间隔，在恰当的时候刷新 UI
- 对于 JavaScript 动画，浏览器无从知晓什么时候开始。

=> mozRequestAnimationFrame() 方法告诉浏览器某些 JavaScript 代码将要执行动画，这样浏览器可以在运行某些代码后进行适当的优化

mozRequestAnimationFrame() 方法接收一个参数，即在重绘屏幕前调用的一个函数，这个函数负责改变下一次重绘时的 DOM 样式。


### Page Visibility API

### Geolocation API 

### File API

### Web 计时

### Web Workers

### 小结
