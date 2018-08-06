## 模板引擎&webpack
对于前端而言，所谓的模板，最原始、最基础的其实就是HTML。对于后台来说，有 Velocity 模板引擎。

在前端领域，是怎么渲染数据？（模板：在固定结构内容上渲染数据）

1、在没有模板的时候，我们是怎么将数据渲染到模板（即HTML或DOM节点）上？

```html
<div id="container">
    我们正处于刀耕火种的石器时代
</div>
```

- 方法一：通过DOM API
```javascript
var newTxt = "石器时代需要自己撸工具，摩擦摩擦，似魔鬼的步伐...";
var container = document.getElementById('container');
var title = document.createElement('h1');
var txt = document.createTextNode(newTxt);
title.appendChild(txt);
// 或
title.innerText = newTxt;
container.replaceChild(title, container.childNodes[0]);
```

仅因为数据是动态的（不是写死在代码里），我们需要多写上面6行代码。
=> DOM API 操作繁琐

- 方法二：innerHTML
```javascript
var newTxt = "石器时代需要自己撸工具，摩擦摩擦，似魔鬼的步伐...";
var template = '<h1>' + newTxt + '</h1>';
var container = document.getElementById('container');
container.innerHTML = template;
```

可读性和可维护性差！而且`innerHTML`在生产环境一般严禁使用的，尽量使用innerText，前者会将其当做代码执行的（如果有js脚本的话）。
=> innerHTML 可读性和可维护性差。

2、jQuery 作者：JavaScript Micro-templating

https://johnresig.com/blog/javascript-micro-templating/

```
<%=表达式%>
<%语句（分支、循环）%>
```

```javascript
// 性能
.split("<%").join("\t")
// 等价于
.replace("<%", "\t")
```

```
模板字符串 ———(编译)———> 模板函数 ———（传入数据）———> HTML String
```

出现模板引擎的本质原因是：**DOM API 虽然强大灵活，但设计得很难用**

前端做了那么多 View 相关的库本质上就是为了屏蔽底层 DOM 操作

这些模板引擎又可以细分为2类：功能 vs 性能
- 功能
功能很强大，除了HTML，还可以写CSS、JavaScript等，可扩展。
http://handlebarsjs.com/

Blocks

- 性能
满足最简单、最本质的需求，但扩展性不好、功能也不强大。https://github.com/olado/doT/blob/master/doT.js、https://github.com/jashkenas/underscore/blob/master/underscore.js#L1525（https://github.com/jashkenas/underscore/blob/master/underscore.js#L1557）

1. 体积小（为什么文件体积小，网络传输会快，TCP的拥塞窗口、rtt时间）
2. 支持线下预编译，成为静态js文件

线下编译的时候就把模板字符串编译成js文件，在浏览器中给数据，return出HTML字符串。（因为数据往往只能动态加载的，需要模板字符串往往也是ajax加载的动态数据）

gzip压缩原理？（压缩算法思想）

- [高性能JavaScript模板引擎原理解析](http://cdc.tencent.com/2012/06/15/%E9%AB%98%E6%80%A7%E8%83%BDjavascript%E6%A8%A1%E6%9D%BF%E5%BC%95%E6%93%8E%E5%8E%9F%E7%90%86%E8%A7%A3%E6%9E%90/)
error.stack

- [doT 源码](https://github.com/olado/doT/blob/master/doT.js)

前端的模板字符串仅仅是为了提升开发效率而已，真正提高性能的或者性能最好的话就是**服务端渲染**（直出），但是**增加了调试成本、服务端维护成本（不好调试）**，对于开发效率来说，只需要前端一个人在前端完成了所有工作。

3、MVVM 和 React.js 提供了更好的方案
在开发模板（UI）这一块，提供了 DOM Template：HTML 是非常强大的，如果真的可以在DOM标签上写逻辑代码的话（模板引擎语法）。再配合 MVVM 双向绑定，DOM Template 具有强大灵活的表征能力。

现在大方向上的 React、Vue 用到了虚拟DOM，本质上只是字符串而已，转换成js可理解的Object去比较差异更新，jsx。追求“同构”的概念。

JSX：all in JS（逻辑+内容+样式(可选)都写在一起，但样式一般会单独拆出来）=>利于组件化 https://ant.design/components/button-cn/#header

组件化，写业务代码同时，要做组件化方案，肯定首先要推动视觉统一视觉规范。组件化后也利于维护。

4、未来的Web Component
https://developer.mozilla.org/zh-CN/docs/Web/Web_Components

MDN的一套规范，但其实一直没有推行起来。因为随着MVVM和React等框架的兴起，大家对Web Components需求反而不太高了，因为前者已经把后者想做的事情已经做到了，只不过是用js实现的，而且并不是浏览器规范。而Web components是浏览器标准，以后可原生支持，目前还不能很好的使用。

Component = HTML（内容=模板+数据） + CSS（样式） + JavaScript（逻辑）

生命周期

5、Polymer

Web Component 实现

Polymer = MVVM + Web Component

https://github.com/Polymer/polymer

https://github.com/ionic-team/stencil


webpack

grunt/gulp

四个核心概念：
- 入口：entry、依赖图，按需打包所有依赖
- 出口：output
- loader：处理非js文件
- 插件plugins：需要改变源文件，做特定修改

```javascript
var TARGET = process.env.npm_lifecycle_event

// require('webpack-merge') === Object.assign()
// https://github.com/survivejs/webpack-merge/blob/master/src/index.js
```

- extract-text-webpack-plugin 插件的使用
- CommonsChunkPlugin 插件的使用

为什么 loader 报错？windows下路径问题

showcase和exercise
- https://github.com/FE-star/showcase5
- https://github.com/FE-star/showcase8
- https://github.com/FE-star/exercise11