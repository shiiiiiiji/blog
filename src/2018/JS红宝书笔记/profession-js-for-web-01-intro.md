Code：[http://www.wrox.com/WileyCDA/WroxTitle/Professional-JavaScript-for-Web-Developers-3rd-Edition.productCd-1118026691,descCd-DOWNLOAD.html](http://www.wrox.com/WileyCDA/WroxTitle/Professional-JavaScript-for-Web-Developers-3rd-Edition.productCd-1118026691,descCd-DOWNLOAD.html)

## 简介
JavaScript （web）主要包含以下内容
- ECMAScript - 核心
宿主环境：不仅提供基本的ECMAScript实现，同时也会提供该语言的扩展（如DOM、BOM等），以便语言与环境之间的对接交互。

- DOM - 文档对象模型
文档对象模型（DOM，Document Object Model）是针对XML但经过扩展用于HTML的应用程序编程接口（API，Application Programming Interface）。

DOM级别与W3C标准。

- BOM - 浏览器对象模型
浏览器对象模型（BOM，Browser Object Model）。

BOM标准？

- How use JavaScript in HTML
借助**`<script>`元素**, 使用`<script>`元素的方式有两种：
- 直接在页面嵌入JavaScript代码
    - 只须为`<script>`指定`type属性`为`text/javascript`，然后把js代码直接放入元素内即可
    - 从上到下依次解释（解释器）
    - 在完成之前，页面中的其余内容都不会被浏览器加载或显示（阻塞）
- 包含外部JavaScript文件
    - 指定`src属性`为外部js文件链接
    - 会被加载到当前页面中（然后就和方式一相同）
    - 指定src属性会忽略标签包含的代码
    - 可以指定来自**外部域**的js文件

执行顺序：
- 除非指定了`defer属性`或`async属性`，否则浏览器会按照**`<script>`元素在页面中出现的顺序**对它们依次解析
- defer：延迟脚本。表示脚本可以延迟到文档完全被解析和显示之后再执行（Q：下载是否会阻塞页面？）。立即下载，延迟顺序执行（理论上，但实际可能并不会按照顺序）
- async：异步脚本。表示立即下载脚本，但不妨碍页面中的其他操作（N：异步体现在同时页面可进行其他操作）。立即下载，延迟无序执行
- （延迟脚本和异步脚本，下载、执行与load事件、DOMContentLoaded事件的关系？）

`<script>`标签的位置：一般应当放在`<body>`内容的后面（用户体验）

尽量使用外部文件引入的方式，而不要嵌入代码的原因：
- 可维护
- 可缓存：两个页面都是用同一个文件，就只需下载一次
- 适应未来：嵌入代码语法在传统HTML内需要兼容，而引入外部文件的语法是都支持的