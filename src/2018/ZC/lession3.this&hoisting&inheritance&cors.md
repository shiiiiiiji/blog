## js基础难点（二）

### this
- this是什么？this总是指向调用它所在方法的对象
- this的指向与所在方法的调用位置有关，而与方法的声明位置无关
- 在浏览器中，调用方法时没有明确对象的，this指向window
- 在浏览器中，setTimeout、setInterval和匿名函数执行时的当前对象是全局对象window
- 而在Node中，这种情况，this是指向Global吗？https://nodejs.org/zh-cn/docs/
	- In Node.js this is different. The top-level scope is not the global scope; var something inside a Node.js module will be local to that **module**.
	- 但是在Node CLI下，与浏览器的行为保持一致
- eval等同于在声明位置填入代码
- apply和call能够强制改变函数执行时的当前对象，让this指向其他对象。
	- 如何利用call或者apply实现bind？
	- eval也能使用call
- 因为js的this太古怪，所以ES6开始，lamda表达式，或者有些人称作箭头函数，是在声明时候绑定this的
	- 有兴趣可以去看看Babel是怎么转的～
- 但是在use strict模式下，this的绑定规则有点不一样：https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Strict_mode

### JavaScript声明提升
- 函数表达式不会提升（具名的也不行）
- 函数声明优先于变量声明提升
```javascript
foo();
var foo; // 重复定义会被忽略
function foo(){
	console.log(1);
}
foo = function(){
	console.log(2);
}

// 执行顺序如下：
function foo(){
	console.log(1);
}
// var foo; // 重复定义会被忽略
foo();
foo = function(){
	console.log(2);
}
```
- 后面出现的函数声明可以覆盖前面的（千万不要这么做）
- 声明提升不会被条件判断所控制（那什么情况会被“控制”呢？）
```javascript
// 下面代码不会报错
if(someVar === undefined){
	var someVar = 1;
	alert("someVar未定义");
}
```

**理解js执行顺序**，js引擎 和 作用域 是一对好基友。
```javascript
function(){
	var a;
	a=5;
	console.log(a);	// 查找变量，js引擎问作用域：请把a变量的值给我？ 作用域：test作用域下就有，值是5
	console.log(this.a); // js引擎问作用域：请把this变量的值给我。 作用域：test作用域下就有，值是window。 js引擎：this变量的a属性值是多少？ 作用域：有的，值为10
}
```

### JavaScript继承
- ES6 class 也是 prototype 语法糖，可以借助babel看看是怎么转化的。
- OOP三大特性：封装、继承、多态
	- 凡是不希望别人知道内部实现的进行封装，内外隔离
	- 凡是系统需要归一化，为了处理方便对所处理的对象有统一要求，则使用继承和多态
	- 注意规则不是银弹，更多的可能需要在自己的编程过程中体验了
- 前人使用了很多黑魔法来实现继承，我们看看ES6怎么继承
	- ES6 - extends
		- https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Classes
	- 各种黑魔法（前人实现）
		- https://github.com/FE-star/lianxi5

### 跨域解决方案
- 同源策略
	- Netscape 最开始为了Cookie而创建的规则。
		- 协议相同
		- 域名相同
		- 端口相同
	- 导致：
		- Cookie、LocalStorage、Indexed DB 无法读取
		- DOM 无法获得
		- Ajax 请求无法发出 => 出于数据安全考虑，不同源的网站，不应当能获取其数据
- 解决方案https://github.com/FE-star/showcase1