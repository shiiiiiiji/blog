## DOM
The Document Object Model，文档对象模型。描绘了一个层次化的节点树。

### 节点层次
DOM 可以将任何 HTML 或 XML 文档描绘成一个由多层**节点**构成的结构。

节点分为几种不同的类型，表示文档中不同的信息/标记。每个节点都拥有各自的特点、数据和方法。节点与其他节点存在某种关系，构成了层次。而所有页面标记则表现为一个以特定节点为根节点的树形结构。

#### NODE 类型

基类型

每个节点都有一个 nodeType 属性，用于表明节点的类型。
- Node.ELEMENT_NODE(1)
- Node.ATTRIBUTE_NODE(2)
- Node.TEXT_NODE(3)
- Node.CDATA_SECTION_NODE(4)
- Node.ENTITY_REFERENCE_NODE(5)
- Node.ENTITY_NODE(6)
- Node.PROCESSING_INSTRUCTION_NODE(7)
- Node.COMMENT_NODE(8)
- Node.DOCUMENT_NODE(9)
- Node.DOCUMENT_TYPE_NODE(10)
- Node.DOCUMENT_FRAGMENT_NODE(11)
- Node.NOTATION_NODE(12)

##### nodeName 和 nodeValue 属性

##### 节点关系

childNodes 属性，其中保存着一个 NodeList 对象：
- 类数组对象，用于保存一组有序的节点
- length
- 基于 DOM 结构动态执行的结果，因此 DOM 结构的变化能够自动反映在 NodeList 对象上
- 索引（方括号）或使用 item() 方法访问
- Array.prototype.slice.call()

parentNode 属性，指向文档树中的父节点

previousSibling 和 nextSibling 属性，可以访问同一列表的其他节点，没有值为 null

firstChild 和 lastChild 属性。父节点的属性，分别指向其 childNodes 列表中的第一个和最后一个节点，即：
```javascript
someNode.firstNode === someNode.childNodes[0];  // true
someNode.lastNode === someNode.childNodes[someNode.childNodes.length - 1];    // true
```

hasChildNodes()

ownerDocument 属性：指向表示整个文档的文档节点

##### 操作节点
因为关系节点都是只读的，所以 DOM 提供了一些操作节点的方法。

appendChild()，用于向 childNodes 列表的末尾添加一个节点，返回新增的节点。如果已经是文档的一部分了，结果就是将该节点从原来的位置转移到新位置。

insertBefore()，将节点放在 childNodes 列表中某个特定的位置上，接受两个参数：要插入的节点和作为参照的节点，插入节点会变成参照节点的前一个同胞节点（previousSibling），同时被返回。如果参照节点是 null，则与 appendChild() 执行相同的操作。

replaceChild()，接受两个参数：要插入的节点和要替换的节点。插入节点的所有关系指针都会从被替换节点复制过来，技术上，被替换节点仍然还在文档中，但在文档中已经没有了位置。

removeChild()，要移除的节点，返回被移除的节点。同样还在文档中，但没有位置了

注意：上面四个方法操作的都是某个节点的子节点，使用必须先取得父节点。如果在不支持子节点的节点上调用了这些方法，将会导致错误发生。

##### 其他方法
cloneNode()，用于创建调用这个方法的节点的一个完全相同的副本，接受一个布尔值参数，表示是否执行深复制（是否复制子节点树）。复制后返回的节点副本属于文档所有，但并没有为它指定父节点。注意：**cloneNode() 不会复制添加到 DOM 节点中的 JavaScript 属性**

normalize()，唯一作用处理文档树中的文本节点。

#### Document 类型
Document 类型表示文档。

在浏览器中，document 对象是 HTMLDocument （继承自 Document） 类型的一个实例，表示整个 HTML 页面。

documetElement 属性：始终指向 HTML 页面中的 `<html>` 元素。
```javascript
document.documentElement === document.childNodes[0];    // true
```

body 属性：直接指向 HTML 页面中的 `<body>` 元素。

文档信息：
- document.title：**可读写**，修改当前页面的标题并反映在浏览器的标题栏上。
- document.URL
- document.domain
- document.referrer

查找元素（返回结果都是“动态”集合）：
- document.getElementById()
- document.getElementsByTagName()：返回一个“动态”集合
```javascript
// 取得文档中的所有元素
document.getElementsByTagName("*");
```
- document.getElementsByName()

特殊集合：
- document.anchors，包含文档中所有带 name 特性的 a 元素
- document.forms， === document.getElementsByTagName('form');
- document.images， === document.getElementsByTagName('img')
- document.links，包含文档中所有带 href 特性的 a 元素

文档写入：输入流写入到网页中。
- document.write()
- document.writeln()
- document.open()
- document.close()

```html
<!-- 在页面呈现过程中直接向其中输出内容 -->
<script type="text/javascript">
    // 利用write/writeln动态包含外部资源
    document.write("<script type=\"text/javascript\" src=\"file.js\">" + "<\/script>");
</script>
<!-- 页面加载完全之后执行write会重写整个页面内容 -->
```
#### Element 类型
所有 HTML 元素都由 HTMLElement 类型表示（或子类型），HTMLElement 类型直接继承于 Element 类型，并添加了一些属性：id、title、lang、dir、className等（可通过对象属性访问和设置特性值）。

nodeName属性、tagName属性（注意大小写）

操作特性：
- element.getAttribute()：除默认特性外，也可以取得自定义特性。特性名称不区分大小写。自定义特性应该加上`data-`前缀以便验证
- element.setAttribute()：设置特性名会被统一转换为小写形式
- element.removeAttribute()：不仅清除特性的值，也会从元素中完全删除特性

对象属性名 vs ...Attribute()
- style 属性
.style返回一个对象，而 getAttribute() 会返回CSS文本
- onclick 等事件处理程序
.onclick 返回 JavaScript 代码（函数，没有为null），而 getAttribute() 会返回相应代码的字符串

element.attributes 属性，包含一个 NamedNodeMap，也是一个“动态”集合。

创建元素：document.createElement()

元素的子节点：element.getElementsByTagName()，搜索起点是当前元素

#### Text 类型

nodeValue 和 data 属性

- appendData(text)
- deleteData(offset, count)
- insertData(offset, text)
- replaceData(offset, count, text)
- splitText(offset)
- substringData(offset, count)

length 属性

在向 DOM 文档中插入文本之前，先对其进行 HTML 编码的一种有效方式。（修改文本节点时，会经过 HTML 编码）

创建文本节点：document.createTextNode()，作为参数的文本也将按照 HTML/XML 格式进行编码

规范化文本节点：element.normalize()（将相邻文本节点合并，在父元素上调用）

分隔文本节点：splitText()

#### Comment 类型
#### CDATASection 类型
#### DocumentType 类型
#### DcoumentFragment 类型
在所有节点类型中，只有 DocumentFragment 在文档中没有对应的标记。“轻量级”的文档，可以包含和控制节点，但不会像完整的文档那样占用额外的资源。

document.createDocumentFragment()

文档片段继承了 Node 的所有方法，通常用于执行那些针对文档的 DOM 操作。如果将文档中的节点添加到文档片段中，就会从文档树中移除该节点，也不会从浏览器中再看到该节点，添加到文档片段中的新节点同样也不属于文档树。可以通过 appendChild/insertBefore 将文档片段中内容添加到文档中。在将文档片段作为参数传递给这两个方法时，实际只会将文档片段的所有子节点添加到相应位置，文档片段本身永远不会成为文档树的一部分。（可以避免多次直接操作DOM）

```javascript
var fragment = document.createDocumentFragment();
var ul = document.getElementById("myList");
var li = null;
for (var i=0; i < 3; i++){
    li = document.createElement("li");
    li.appendChild(document.createTextNode("Item " + (i+1)));
    fragment.appendChild(li);
}
ul.appendChild(fragment);
```

#### Attr 类型
元素的特性在 DOM 中以 Attr 类型表示。

不建议直接访问特性节点，实际上使用getAttribute()、element.setAttribute()、element.removeAttribute()方法更加方便。

### DOM 操作
#### 动态脚本
页面加载时不存在，但在将来的某一时刻通过修改 DOM 动态添加的脚本。创建动态脚本的两种方式：插入外部文件和直接插入 JavaScript 代码。

动态加载的外部 JavaScript 文件能够立即运行。
```html
<script type="text/javascript" src="client.js"></script>
```

创建这个节点的 DOM 代码：
```javascript
var script = document.createElement('script');
script.type = 'text/javascript';
script.src = 'client.js';
document.body.appendChild(script);  // 在执行最后一行代码把<script>元素添加到页面中之前，是不会下载外部文件的。
```

但是浏览器目前没有标准方式知道脚本加载完成。

动态直接插入 js 代码：
```javascript
var script = document.createElement('script');
script.type = "text/javascript";
script.appendChild(document.createTextNode("function sayHi(){alert('hi');}"));
// script.text = "function sayHi(){alert('hi');}"; // IE 中
document.body.appendChild(script);
```

#### 动态样式
```javascript
var link = document.createElement("link");
link.rel = "stylesheet";
link.type = "text/css";
link.href = "style.css";
var head = document.getElementsByTagName("head")[0];
head.appendChild(link);
```

加载外部样式的过程是异步的。

#### 操作表格

#### 使用 NodeList
NodeList、NamedNodeMap、HTMLCollection，都是“动态”集合

### 小结
理解 DOM 的关键，就是理解 DOM 对性能的影响。DOM 操作往往是 JavaScript 程序中开销最大的部分，而因访问 NodeList 导致的问题最多，因为 NodeList 对象都是“动态”的，每次访问都会运行一次查询。=>尽量减少 DOM 操作。


## DOM 扩展
### 选择符 API
让浏览器原生支持 CSS 查询。

Document 及 Element 类型的实例调用：
- querySelector()：接收一个 CSS 选择符（不全被支持），返回与该模式匹配的第一个元素，没有返回 null
- querySelectorAll()：返回一个 NodeList 的实例，否则是空的 NodeList
- matchesSelector()：调用元素与选择符匹配，返回 true，否则为 false

### 元素遍历
- childElementCount
- firstElementChild
- lastElementChild
- previousElementSibling
- nextElementSibling

### HTML 5
- getElementsByClassName()
- classList 属性
    - add(value)
    - contains(value)
    - remove(value)
    - toggle(value)
- 焦点管理：document.activeElement、document.hasFocus()
- HTMLDocument
    - document.readyState
    - document.compatMode
    - document.head
- 字符集属性：document.charset，document.defaultCharset
- 自定义数据属性：
    - `data-`前缀
    - dataset 属性
- 插入标记
    - innerHTML：可读写，设置 innerHTML 后读取 HTML 字符串，是根据原始 HTML 字符串创建的 DOM 树经过序列化之后的结果。
    - outerHTML：返回调用它的元素及所有子节点的 HTML 标签，写模式下会根据指定的 HTML 字符串创建新的 DOM 子树，然后用这个 DOM 子树完全替换调用元素。
    - insertAdjacentHTML()，接受两个参数：插入位置和要插入的 HTML 文本
    - 内存与性能问题
        - 在插入大量新 HTML 标记时，innerHTML 属性与通过多次 DOM 操作先创建节点再指定它们之间的关系相比，效率要高很多。因为在设置 innerHTML 时，会创建一个 HTML 解析器，这个解析器是在浏览器级别的代码基础上运行的，因此比执行 JavaScript 快得多。
        - 不管如何，创建和销毁 HTML 解析器也会带来性能损失，所以应当将设置 innerHTML 等操作次数控制的合理范围内。
- scrollIntoView()：可以在所有 HTML 元素上调用，通过滚动浏览器窗口或某个容器元素，调用元素就可以出现在视口中。如果传 true 或不传参数，那么窗口滚动之后会让调用元素的顶部与视口顶部尽可能平齐。如果传 false，调用元素会尽可能全部出现在视口中（可能的话，调用元素的底部会与视口顶部平齐）

### 专有扩展
#### 文档模式
#### children 属性
#### contains()
#### 插入文本
- innerText
    - 读取时，按照由浅入深的顺序，将子文档树中的所有文本拼接起来
    - 写入时，会删除元素所有子节点，插入包含相应文本值的文本节点。对文本中存在的 HTML 语法字符进行编码。
```javascript
div.innerText = div.innerText;  // 去掉所有 HTML 标签
```
- textContent
- outerText

#### 滚动
- scrollIntoViewIfNeeded(alignCenter)：只有当前元素在视口中不可见的情况下，才滚动浏览器窗口或容器元素，最终让它可见，否则不做处理。
- scrollByLines(lineCount)：将元素的内容滚动指定的行高，lineCount可正可负
- scrollByPages(pageCount)：将原色的内容滚动指定的页面高度，具体高度由元素的高度决定

scrollIntoView 和 scrollIntoViewIfNeeded 的作用对象是元素的容器，scrollByLines 和 scrollByPages 影响的则是元素自身。

## DOM 2 和 DOM 3
DOM 1 级主要定义的是 HTML 和 XML 文档的底层结构。DOM 2 级和DOM 3 级则是在这个结构的基础上引入了更多的交互能力，为此，分为许多模块，分别描述了 DOM 的某个非常具体的子集。
- DOM 2 级核心（DOM Level 2 Core）：在1级核心基础上构建，为节点添加了更多方法和属性
- DOM 2 级视图（DOM Level 2 Views）：为文档定义了基于样式信息的不同视图
- DOM 2 级事件（DOM Level 2 Events）：说明了如何使用事件与 DOM 文档交互
- DOM 2 级样式（DOM Level 2 Style）：定义了如何以编程方式来访问和改变 CSS 样式信息
- DOM 2 级遍历和范围（DOM Level 2 Traversal and Range）：引入了遍历 DOM 文档和选择其特定部分的新接口