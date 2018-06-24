> 刚入门前端的时候，很容易搞不清楚`ECMAScript`和`JavaScript`之间的区别，今天早读课推送的文章介绍ES2018（ES9）的新特性，借此机会查阅资料总结一下：

关键词：`JavaScript`、`ECMAScript`

## JavaScript历史

《JavaScript高级程序设计（第3版）》里开篇就介绍了JavaScript的发展历史，简单概括如下：

- 1995年，诞生。为了解决浏览器表单验证问题，网景公司的布兰登·艾奇开发了一个叫LiveScript语言。为了蹭Java热点，改名JavaScript。并后来发布JavaScript 1.1版本。
- 1996年，JavaScript和JScript两版本并存。微软的JScript的JavaScript实现。
- 1997年，标准化。以JavaScript 1.1 为蓝本被提交给了**“欧洲计算机制作者协会”**（ECMA，European Computer Manufacturers Association），后改名“ECMA国际”（ECMA International）是一个技术标准制定的协会。
	- 该协会指定由“Netscape、Sun、微软、Borland及其他关注脚本语言发展的公司的程序员”组成的39号技术委员会（TC39， Technical Committee #39）负责“标准化一种通用、跨平台、供应商中立的脚本语言的语法和语义”。
	- 该协会当年完成了**ECMA-262**标准。且该标准定义了一种名为**ECMAScript**的新脚本语言的标准。（与浏览器、Node等具体宿主环境没有任何关系，这个版本是ECMAScript第1版）

由上述历史可以看出：JavaScript是ECMAScript（语言标准）的具体实现之一。

## ECMAScript的版本

> 我们经常听到ES6、ES7、ES9等说法，这和ECMAScript的版本有什么关系呢？

- ECMAScript第1版（ES1）：ECMAScript 1，1997年，即标准产生
- ECMAScript第2版（ES2）：ECMAScript 2，1998年。
- ECMAScript第3版（ES3）：ECMAScript 3，1999年。
- ECMAScript第4版（ES4）：ECMAScript 4，因为跨度太大，被废弃。
- ECMAScript第5版（ES5）：ECMAScript 3.1，因为ECMAScript 4 跨度太大，故称为ECMAScript第5版,2009年，ECMAScript 5.1，修正版 2011年。
- ECMAScript第6版（ES6）：ECMAScript 2015,2015年（**因为内部流程的问题，自ES6出来后，精简了提案的修订流程，并使用了Github pull requests**）。
- ECMAScript第7版（ES7）：ECMAScript 2016,2016年。
- ECMAScript第8版（ES8）：ECMAScript 2017,2017年。
- ECMAScript第9版（ES9）：ECMAScript 2018,2018年。

## 提案新流程
一般会经历一下几个Stage：
- Stage 0: strawman——最初想法的提交。
- Stage 1: proposal（提案）——由TC39至少一名成员倡导的正式提案文件，该文件包括API事例。
- Stage 2: draft（草案）——功能规范的初始版本，该版本包含功能规范的两个实验实现。
- Stage 3: candidate（候选）——提案规范通过审查并从厂商那里收集反馈
- Stage 4: finished（完成）——提案准备加入ECMAScript，**但是到浏览器或者Nodejs中可能需要更长的时间**

## Web浏览器对ECMAScript的支持
因为ECMAScript标准提出后，浏览器一般都不会第一时间实现对新标准的支持，所以即使新标准提出了一些新特性，也无法即时在浏览器中运行，Node.js也是如此。

而在实际工程中，我们一般都会采用对代码进行编译（俗称“打包”）后才被浏览器引入，整个过程其实把新特性语法编译成浏览器支持的语法实现（一般是ES5，因为五大浏览器都支持ES5）。

## 参考资料
- 《JavaScript高级程序设计（第3版）》
- [https://en.wikipedia.org/wiki/ECMAScript#Versions](https://en.wikipedia.org/wiki/ECMAScript#Versions)
- [https://medium.com/@justjavac/tc39-ecmascript-proposals-future-of-javascript-386b12149880](https://medium.com/@justjavac/tc39-ecmascript-proposals-future-of-javascript-386b12149880)