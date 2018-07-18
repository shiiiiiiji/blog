## 前端监控上报

### 项目上线前的checklist
https://github.com/HarrisJT/web-launch-checklist

上线前必加的上报：pv、页面加载成功、页面加载失败、页面重试成功、页面重试失败、所有接口的请求/成功/失败上报、脚本错误上报pv（如果有）、图片加载失败上报（主视觉强依赖图片的页面）

### 监控上报
考虑到上报不care回包，一般采用new Image()
```javascript
new Image().src = `${your_report_url}`;
```

#### 腾讯的做法
- monitor：一切可以可以用累积量量(count++)表示的上报（PV/接口成功or失败/是否支持websocket/是否离线包访问/…）
- huatuo：测速上报、性能上报
- badjs： 脚本错误上报
- tdbank： 用户行为上报

### 性能监控上报
https://developer.mozilla.org/zh-CN/docs/Web/API/PerformanceTiming

### 埋点上报

**方法论：除了了可以实时查看数据，还需沉淀。**即，得到的数据如何利用。

=> 为什么要做这件事？因为实验室数据 !== 生产环境数据

=> 好处：
- 解决被动反馈问题的窘境：发现潜在问题，验证修复
- 验证业务优化方案，积累数据
- 优化方案的提出和选型，也是基于生产环境数据

如果你还不清楚积累数据的重要性，那你应该是还没写过晋级答辩 or 向老板汇报的PPT

### 用户行为上报
- 关注用户行为
- 了解用户操作习惯
- 了解用户的兴趣点
- 用户访问链路路上报
- 借力第三方统计（Google Analytics，百度统计，MTA等）

[showcase4](https://github.com/FE-star/showcase4)

### 页面关闭前上报
https://developer.mozilla.org/zh-CN/docs/Web/API/Navigator/sendBeacon

### 错误监控上报
- https://developer.mozilla.org/zh-CN/docs/Web/API/GlobalEventHandlers/onerror
- https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/try...catch
- https://developer.mozilla.org/zh-CN/docs/Web/Events/unhandledrejection
- 了解一下unhandledrejection和uncaughtException的区别

对nodejs感兴趣的同学可以了解一下如何利用try…catch/process.on(‘uncaughtException’,cb)/domain模块处理uncaughtException

https://github.com/BetterJS/badjs-report

为啥会出现Script error？
- **跨域**的JavaScript文件通过<script>标签引入，且内部发生脚本错误
- 通过window.onerror注册监听脚本错误事件
- 现代浏览器器：firefox、chrome、safari
- native调用js代码时发生脚本错误
- https://stackoverflow.com/questions/5913978/cryptic-script-error-reported-in-javascript-in-chrome-and-firefox
- https://blog.sentry.io/2016/05/17/what-is-script-error

方案一：
- 跨域JavaScript文件的服务端响应设置cors
- script标签设置crossorigin属性

方案二：
- 对入口和异步方案全部用try…catch包裹
- ajax和websocket的callback
- setTimout和setInterval
- Promise
- define和require
- 事件监听
- 主入口

**两套方案不冲突，可以一起用。**

- 根据错误上报趋势及时发现问题，验证修复效果
- 纳入绩效考核，推动大家积极解决错误

https://cloud.tencent.com/developer/article/1004564

### 利用AOP将上报与业务分离