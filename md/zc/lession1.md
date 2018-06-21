## js基础知识

### 从设计模式说起
- https://coolshell.cn/articles/4535.html
- 软件工程设计原则
    - 高内聚
    - 低耦合
- 设计模式的历史
    - [https://zh.wikipedia.org/wiki/设计模式_(计算机)](https://zh.wikipedia.org/wiki/设计模式_(计算机))\
    - [https://addyosmani.com/resources/essentialjsdesignpatterns/book/](https://addyosmani.com/resources/essentialjsdesignpatterns/book/)
    - [https://design-patterns.readthedocs.io/zh_CN/latest/index.html](https://design-patterns.readthedocs.io/zh_CN/latest/index.html)
- 发布/订阅模式 vs 观察者模式
    - [https://www.cnblogs.com/lovesong/p/5272752.html](https://www.cnblogs.com/lovesong/p/5272752.html)
    - [练习](https://github.com/FE-star/exercise17)

### 事件、事件模型、事件处理机制
- addEventListener
    - [https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/addEventListener](https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/addEventListener)
    - [ShowCase9](https://github.com/FE-star/showcase9)
- 标准
    - [https://www.w3.org/TR/DOM-Level-3-Events/#dom-event-architecture](https://www.w3.org/TR/DOM-Level-3-Events/#dom-event-architecture)
    - [https://developer.mozilla.org/en-US/docs/Web/API/Event](https://developer.mozilla.org/en-US/docs/Web/API/Event)
- 关系
    - Event（事件）：被观察者
    - 事件模型：观察者模式具体实现
    - 事件处理机制：事件传播的方式，捕获&冒泡
- 历史&兼容问题
    - HTML的`on-`属性
    - JS元素节点对象的事件属性
    - IE下的attachEvent()：因为IE不支持事件捕获，所以为了兼容大多数都是基于事件冒泡实现的。但是现在移动端基本上都会同时支持时间捕获和冒泡
    - 浏览器兼容写法与[caniuse](https://caniuse.com/#search=EventTarget)
- 浏览器兼容
    - 渐进增强
    - 优雅降级
- 自定义事件
    - 如何自定义事件？
        - [https://developer.mozilla.org/zh-CN/docs/Web/Guide/Events/Creating_and_triggering_events](https://developer.mozilla.org/zh-CN/docs/Web/Guide/Events/Creating_and_triggering_events)
    - 为什么要自定义事件？
    - 应用场景有哪些？
        - 弹窗登录
        - toast
- 事件代理和委托
    - [https://developer.mozilla.org/zh-CN/docs/Web/Guide/Events/Creating_and_triggering_events](https://developer.mozilla.org/zh-CN/docs/Web/Guide/Events/Creating_and_triggering_events)
    -jQuery的`$.delegate`、`$.live`、`$.on`
    - 优缺点：
        - 减少内存占用
        - 一次委托、终身受用
        - 有一定的局限性
- Event vs Event Loop
    - 不是同一个东西
    - [https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick/](https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick/)
    - [https://cnodejs.org/topic/57d68794cb6f605d360105bf](https://cnodejs.org/topic/57d68794cb6f605d360105bf)
    - [http://menzhongxin.com/2017/05/18/node事件循环(EventLoop)/](http://menzhongxin.com/2017/05/18/node事件循环(EventLoop)/)

### Ajax
- [https://developer.mozilla.org/zh-CN/docs/Web/Guide/AJAX](https://developer.mozilla.org/zh-CN/docs/Web/Guide/AJAX)
- Ajax vs $.ajax
    - ajax != jQuery.ajax
    - ajax 2005.2.18. Google
    - [Ajax: A New Approach to Web Applications](https://courses.cs.washington.edu/courses/cse490h/07sp/readings/ajax_adaptive_path.pdf)
    - jQuery 2006.8.26 John Resig
- XHR - XMLHttpRequest
- Fetch
- 有xhr为什么还要fetch？

### 异步编程
- 同步 vs 异步
    - sync
    - async
    - [ShowCase10](https://github.com/FE-star/showcase10)
- 回调（callback）
    - 回调地狱（callback hell）
    - 可读性&可维护性
- Generator + function*
    - 过渡阶段的产物
- Promise + async/await
    - 新的标准
- RxJS
    - 响应式编程
    - 观察者模式+迭代器模式+Stream

### Unix设计思想 - 管道 & stream
- [https://zh.wikipedia.org/wiki/Unix哲学](https://zh.wikipedia.org/wiki/Unix哲学)
- [https://coolshell.cn/articles/7236.html](https://coolshell.cn/articles/7236.html)
- [https://nodejs.org/api/stream.html](https://nodejs.org/api/stream.html)
- [https://medium.freecodecamp.org/node-js-streams-everything-you-need-to-know-c9141306be93](https://medium.freecodecamp.org/node-js-streams-everything-you-need-to-know-c9141306be93)