## 最佳实践
### 可维护性

可维护性：
- 可理解性
- 直观性
- 可适应性
- 可扩展性
- 可调式性

代码约定：
- 可读性
    - 函数和方法
    - 大段代码
    - 复杂的算法
    - Hack
- 变量和函数命名
    - 变量名应为名词
    - 函数名应以动词开始，返回布尔类型值得函数一般以 is 开头
    - 合乎逻辑，长度可处理和压缩
- 变量类型透明
    - 初始化：初始化一个特定的数据类型，缺点是无法用于函数声明中的函数参数
    - 匈牙利标记法：在变量名之前加上一个或多个字符表示数据类型。
        - “o”：代表对象
        - “s”：代表字符串
        - “i”：代表整数
        - “f”：代表浮点数
        - “b”：代表布尔值
    - 使用类型注释
    ```javascript
    var found /*:Boolean*/ = false;
    var count  /*:int*/    = 10;
    var name   /*:String*/ = "Nicholas";
    var person /*:Object*/ = null;
    ```

松散耦合：
- 解耦 HTML（数据） / JavaScript（行为）
- 解耦 HTML / CSS
- 解耦 应用逻辑 / 事件处理程序

编程实践：
- 尊重对象所有权
    - 不要为实例或原型添加属性
    - 不要为实例或原型添加方法
    - 不要重定义已存在的方法
- 避免全局量
    - 将功能组合在一个的对象，叫做命名空间，如 YUI、$
- 避免与 null 进行比较
    ```javascript
    // 代码中的 null 比较越少，就越容易确定代码的目的，并消除不必要的错误
    values != null; // 避免
    values instanceof Array; // 推荐
    ```
    - 如果看到了与 null 比较的代码，可以尝试：
    - 如果值应为一个引用类型，使用 instanceof 操作符检查其构造函数
    - 如果值应为一个基本类型，使用 typeof 检查其类型
    - 如果是希望对象包含某个特定的方法名，使用 typeof 操作符确保指定名字的方法存在于对象上
- 使用常量
    - 重复值
    - 用户界面字符串：应被抽取出来以方便国际化
    - URLs：在 Web 应用中，资源位置很容易变更，所以推荐用一个公共地方存放所有 URL
    - 任何可能会更改的值

### 性能
JavaScript 解释型语言 => Chrome 开始实现编译执行

#### 注意作用域
随着作用域链中的作用域数量的增加，访问当前作用域意外的变量的时间也在增加。

- 避免全局查找
```javascript
function updateUI(){
    var imgs = document.getElementsByTagName("img");
    for (var i=0, len=imgs.length; i < len; i++){
            imgs[i].title = document.title + " image " + i;
    }
    var msg = document.getElementById("msg");
    msg.innerHTML = "Update complete.";
}

function updateUI(){
    var doc = document; // 减少全局 document 对象的引用
    var imgs = doc.getElementsByTagName("img");
    for (var i=0, len=imgs.length; i < len; i++){
            imgs[i].title = doc.title + " image " + i;
    }
    var msg = doc.getElementById("msg");
    msg.innerHTML = "Update complete.";
}
```

将在一个函数中会用到多次的全局变量存储为局部变量总是没错的。

- 避免 with 语句
```javascript
function updateBody(){
    with(document.body){
       alert(tagName);
       innerHTML = "Hello world!";
    }
 }

function updateBody(){
    var body = document.body;    // 使用局部变量消除额外的字符
    alert(body.tagName);
    body.innerHTML = "Hello world!";
}
```

#### 选择正确方法
- 避免不必要的属性查找
算法复杂度使用 O 符号表示。最简单、快捷的算法是常数值 O(1)。
    - O(1)：常数。不管有多少值，执行的时间都是恒定的。一般表示简单值和存储在变量中的值。如获取常量值、**访问数组元素**
    - O(log n)：对数。总的执行时间和值的数量有关，但是要完成算法并不一定要获取每个值。如：二分查找
    - O(n)：线性。总执行时间和值的数量直接相关。如：遍历数组所有元素、**访问对象**（因为必须在原型链中对拥有该名称的属性进行一次搜索，属性查找越多，执行时间就越长。**一旦多次用到对象属性，应该将其存储在局部变量中**，这样第一次访问是 O(n)，后续都是 O(1)。**尽可能多地使用局部变量将属性查找替换为值查找**，如果既可以用数字化的数组位置进行访问，也可以使用命名属性，那么使用数字位置）
    - O(n^2)：平方。总执行时间和值的数量有关，每个值至少要获取 n 次，如插入排序
- 优化循环
    - 减值迭代 —— 大多数循环使用一个从 0 开始、增加到某个特定值的迭代器。在很多情况下，从最大值开始，在循环中不断减值的迭代器更加高效。
    - 简化终止条件 —— 由于每次循环过程都会计算终止条件，所以必须保证它尽可能快。也就是说避免属性查找或其他 O(n) 的操作
    - 简化循环体 —— 循环体是执行最多的，所以要确保其被最大限度地优化。确保没有某些可以被很容易移除循环的密集计算
    - 使用后测试循环 —— do-while 可以避免最初终止条件的计算，因此运行更快
```javascript
// 1
for (var i=0; i < values.length; i++){
    process(values[i]);
}

// 2
// 终止条件从 value.length 的 O(n) 调用简化成了 0 的 O(1) 调用
for (var i=values.length -1; i >= 0; i--){
    process(values[i]);
}

// 3
// 改为后测试循环
// 可以避免空数组导致多余的一次循环
var i=values.length -1;
if (i > -1){
    do {
        process(values[i]);
    }while(--i >= 0);
}
```
- 展开循环：当循环的次数是确定的，消除循环并使用多次函数调用往往更快。可以提升 **大数据集** 的处理速度。但对于小数据集，额外的开销则可能得不偿失。
```javascript
// 4
// 消除循环，消除建立循环和处理终止条件的额外开销
process(values[0]); 
process(values[1]); 
process(values[2]);
```

如果循环中迭代次数不能事先确定，可以使用一种叫做 Duff 装置的技术，基本概念是通过计算迭代次数是否为 8 的倍数将一个循环展开为一系列语句：
```javascript
// credit: Jeff Greenberg for JS implementation of Duff’s Device
//􏵽􏵾 假设 values.length > 0
var iterations = Math.ceil(values.length / 8);
var startAt = values.length % 8;    // 循环需要多少次迭代
var i = 0;
do {
    switch(startAt){    // 检查 startAt 变量看有需要多少额外调用
        case 0: process(values[i++]);
        case 7: process(values[i++]);
        case 6: process(values[i++]);
        case 5: process(values[i++]);
        case 4: process(values[i++]);
        case 3: process(values[i++]);
        case 2: process(values[i++]);
        case 1: process(values[i++]);
    }
    startAt = 0;    // 重置为 0，这样之后的每次循环都会调用 8 次 process()
} while (--iterations > 0);
```

```javascript
//credit: Speed Up Your Site (New Riders, 2003)
var iterations = Math.floor(values.length / 8);
var leftover = values.length % 8;
var i = 0;
if (leftover > 0){  // 剩余的部分不会再实际循环中处理
    do {
        process(values[i++]);
    } while (--leftover > 0);
}
do {    // 8次 process() 的主循环，比原始的 Duff 装置事先快上 40%
    process(values[i++]);
    process(values[i++]);
    process(values[i++]);
    process(values[i++]);
    process(values[i++]);
    process(values[i++]);
    process(values[i++]);
    process(values[i++]);
} while (--iterations > 0);
```

- 避免双重解释：eval、Function 构造函数、setTimeout() 传入第一个字符串参数，需要 **解析包含了 JavaScript 代码的字符串**，这个操作是不能再初始的解析过程中完成的，需要在 JavaScript 代码运行的同时必须新启动一个解析器来解析新的代码。**实例化一个新的解析器有不容忽视的开销**
- 其他注意事项
    - 原生方法较快，原生方法是用 C/C++ 之类的编译型语言事先的
    - Switch 语句较快
    - 位运算符较快：诸如取模、逻辑与/或都可以考虑用位运算来替换

#### 最小化语句数
完成多个操作的单个语句要比完成单个操作的多个语句快 => 找出可以组合在一起的语句，以减少脚本整体的执行时间。
- 多个变量声明
```javascript
// 4 个语句，很浪费
var count = 5;
var color = "blue"; 
var values = [1,2,3]; 
var now = new Date();

// 1 个语句
var count = 5，
    color = "blue"， 
    values = [1,2,3]， 
    now = new Date();
```
- 插入迭代值
当使用迭代值（在不同的位置进行增加或减少的值）的时候，尽可能合并语句。
```javascript
var name = values[i];
i++;

var name = values[i++];
```

- 使用数组和对象字面量
使用构造函数总是要用到更多的语句来插入元素或定义属性。
```javascript
// 用 4 个语句创建和初始化数组——浪费
var values = new Array();
values[0] = 123;
values[1] = 456;
values[2] = 789;

// 用 4 个语句创建和初始化对象——浪费
var person = new Object();
person.name = "Nicholas";
person.age = 29;
person.sayName = function(){
    alert(this.name);
};

var values = [123, 456, 789];
var person = {
    name: "Nicholas",
    age: 29,
    sayName: function(){
        alert(this.name);
    }
}
```

#### 优化 DOM 交互
- 最小化现场更新
一旦你需要访问的 DOM 部分是已经显示的页面的一部分，那么就是在进行一个 现场更新（需要立即/现场对页面对用户的显示进行更新）。每一个更改，不管是插入单个字符，还是移除整个片段，都有一个性能惩罚。

一般有两个方法：
    - 移除、最后进行更新并插回同样的位置
    - 使用文档结构

- 使用 innerHTML（创建节点）
- 使用事件代理
- 注意 HTMLCollection
任何时候要访问 HTMLCollection，不管是一个属性还是一个方法，都是在文档上进行一个查询，这个查询开销很昂贵。因此，需要知道何时返回 HTMLCollection 对象，这样才可以最小化对其的访问：
    - 进行了对 getElementsByTagName() 的调用
    - 获取了元素的 childNodes 属性
    - 获取了元素的 attributes 属性
    - 访问了特殊的集合，如 document.forms、document.images 等


### 部署
#### 构建过程
完备 JavaScript 代码可以用于部署的一件很重要的事情，就是给它开发某些类型的构建过程。

软件开发的典型模式是：写代码——编译——测试，首先书写好代码，将其编译通过，然后运行并确保其正常工作。由于 JavaScript 并非一个编译型语言，模式变成了“写代码——测试”，这里你写的代码就是你要在浏览器中测试的代码。这个方法的问题在于它不是最优的，你写的代码不应该原封不动地放入浏览器中，理由如下：
- 知识产权问题：如果把带有完整注释的代码放到线上，那别人就更容易知道你的意图，对它再利用，并且可能找到安全漏洞。
- 文件大小：书写代码要保证容易阅读，才能更好的维护，但是这对于性能是不利的，浏览器并不能从额外的空白字符或者冗长的函数名和变量名中获得什么好处。
- 代码组织：组织代码要考虑到可维护性，但并不一定是传送给浏览器的最好方式。

基于这些原因，最好给 JavaScript 文件定义一个构建过程。

构建过程始于在源控制中定义用于存储文件的逻辑结构。A build process starts by defining a logical structure for storing your files in source control.

最好避免使用一个文件存放所有的 JavaScript，遵循以下面向对象语言中的典型模式：**将每个对象或自定义类型分别放入其单独的文件中。**这样可以确保每个文件包含最少量的代码，使其在不引入错误的情况下更容易修改。而且，在版本控制时，也减少了合并操作产生冲突的风险。

但需要记住将代码分离成多个文件只是为了提高可维护性，并非为了部署。要进行部署的时候，需要将这些源代码合并为一个或几个归并文件。推荐 Web 应用中尽可能使用最少的 JavaScript 文件，是因为 HTTP 请求是 Web 中的主要性能瓶颈之一。记住通过 script 引入 JavaScript 文件是一个阻塞操作，当代码下载并运行时会停止其他所有的下载，因此，尽量从逻辑上将 JavaScript 代码分组成部署文件。

一旦组织好文件和目录结构，并确定哪些要出现在部署文件中，就可以创建构建系统了。

早期的构建工具：[Ant](http://www.julienlecomte.net/blog/2007/09/16/)，由于其简便的文件处理能力而非常适合 JavaScript 编译系统（如可以很方便地获取目录中所有文件的列表，然后将其合并为一个文件）

```xml
<!-- build.xml -->
<project name=”JavaScript Project” default=”js.concatenate”>
    <!-- the directory to output to -->
    <!-- 输出的目录 -->
    <property name=”build.dir” value=”./js” />
    <!-- the directory containing the source files -->
    <!-- 包含源文件的目录 -->
    <property name=”src.dir” value=”./dev/src” />
    <!-- Target to concatenate all JS files -->
    <!-- Credit: Julien Lecomte, http://www.julienlecomte.net/blog/2007/09/16/ --> 
    <!-- 合并所有 JS 文件的目标 -->
    <target name=”js.concatenate”>
        <concat destfile=”${build.dir}/output.js”>
            <filelist dir=”${src.dir}/js” files=”a.js, b.js”/>
            <fileset dir=”${src.dir}/js” includes=”*.js” excludes=”a.js, b.js”/>
        </concat> 
    </target>
</project>
```
该 build.xml 文件定义了两个属性：输出最终文件的构建目录、JavaScript 源文件所在的源目录。
目标 js.concatenate 使用了 concat 元素来指定需要进行合并的文件的列表以及结果文件所要输出的位置。

#### 验证
大多数问题要在浏览器中运行代码以检查其语法，但：
- 验证过程难以自动化或者在不同系统间直接移植
- 除了语法错误外，很多问题只有在执行代码时才会遇到

有些工具可以帮助确定 JavaScript 代码中潜在的问题。JSLint 可以查找 JavaScript 代码中的语法错误以及常见的编码错误。

http://www.jslint.com/（在线版本、命令行模式）

**可以使用 Ant 将 JSLint 作为构建过程的一部分。**

给开发周期添加代码验证这个环节有助于避免将来可能出现的一些错误。

#### 压缩
JavaScript 文件压缩：
- 代码长度：浏览器所需解析的字节数
- 配重（Wire weight）：实际从服务器传送到浏览器的字节数

早期，这两个值几乎一样，因为是从服务端到客户端原封不动地传递了源文件。

##### 文件压缩
因为 JavaScript 并非编译为字节码，而是按照源代码发送的。代码文件通常包含浏览器执行所不需要的额外的信息和格式。注释、额外的留白，以及常常的变量名和函数名虽然提高了可读性，但却是传送给浏览器时不必要的字节。不过我们可以使用压缩工具减少文件的大小。

压缩器一般进行如下一些步骤：
- 删除额外的空包（包括换行）
- 删除所有注释
- 缩短变量名

http://yui.github.com/yuicompressor/

YUI 压缩器也可以通过直接调用 Java 可执行文件在 Ant 中使用。

所有的 JavaScript 文件在部署到生产环境之前，都应该使用 YUI 压缩器或者类似的工具进行压缩，给构建过程添加一个压缩 JavaScript 文件的环节以确保每次都进行这个操作。

##### HTTP 压缩
配重指的是实际从服务器传送给浏览器的字节数，现在的服务器和浏览器都有压缩功能，这个字节数不一定和代码长度一样，所有的 5 大 Web 浏览器都支持对所接收的资源进行客户端解压缩。这样服务器端就可以使用服务器相关功能来压缩 JavaScript 文件。

一个指定了文件使用了给定格式进行了压缩的 HTTP 头包含在了服务器响应中，接着浏览器会查看该 HTTP 头确定文件是否已被压缩，然后使用合适的格式进行解压缩。结果是和原来的代码量相比在网络中传递的字节数量大大减少了。

对于 Apache Web 服务器，有两个模块可以进行 HTTP 压缩：mod_gzip 和 mod_deflate。