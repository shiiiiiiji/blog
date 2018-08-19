## 离线应用与客户端存储
支持离线 Web 应用开发 —— HTML 5。

开发离线 Web 应用需要几个步骤：
- 首先确保应用知道设备是否能上网，以便下一步执行正确的操作 —— 离线检测
- 应用必须能够访问一定的资源（图像、JavaScript、CSS等），只有这样才能正常工作 —— 应用缓存
- 最后，必须有一块本地空间用于保存数据，无论能否上网都不妨碍读写 —— 数据存储

### 离线检测
一个属性：

`navigator.onLine` 属性，虽然有浏览器差异（当时），但在请求发生错误的情况下，检测这个属性仍然使管用的。

```javascript
if (navigator.onLine){
    // 正常工作
} else {
    // 执行离线状态时的任务
}
```

两个事件：（当网络从离线变为在线或者从在线变为离线时，分别在 window 对象上触发这两个事件）
- online
- offline

```javascript
EventUtil.addHandler(window, 'online', function(){
    alert('Online');
});
EventUtil.addHandler(window, 'offline', function(){
    alert('Offline');
});
```

为了检测应用是否离线，在页面加载后，最好先通过 navigator.onLine 取得初时的状态。然后，通过上述两个事件来确定网络连接状态是否变化。当上述事件触发时，navigator.onLine 属性的值也会改变，不过必须要手工轮询这个属性才能检测到网络状态的变化。


### 应用缓存
HTML 5 的应用缓存，application cache，或者简称为 appcache，是专门为开发离线 Web 应用而设计的。Appcache 就是从浏览器的缓存中分出来的一块缓存区，要想在这个缓存中保存数据，可以使用一个 **描述文件**（mainfest file），列出要下载和缓存的资源。示例如下：

```
CACHE MAINFEST
#Comment

file.js
file.css
```

在最简单的情况下，描述文件中列出都是需要下载的资源，以备离线时使用。

http://html5doctor.com/go-offline-with-application-cache/

要将描述文件与页面关联起来，可以在 html 中的 manifest 属性中指定这个文件的路径。

```html
<html mainfest="/offline.mainfest">
```

以上代码告诉页面，/offline.mainfest 中包含这描述文件。这个文件的 MIME 类型必须是 text/cache-mainfest（描述文件的扩展名以前推荐时用 mainfest，现在推荐的是 appcache）

另外，`applicationCache`对象的`status`属性，表示应用缓存的当前状态：
- 0：无缓存，即没有与页面相关的应用缓存
- 1：闲置，即应用缓存未得到更新
- 2：检查中，即正在下载描述文件并检查更新
- 3：下载中，即应用缓存正在下载描述文件中指定的资源
- 4：更新完成，即应用缓存已经更新了资源，而且所有资源都已下载完毕，可以通过 swapCache() 来使用了
- 5：废弃，即应用缓存的描述文件已经不存在了，因此页面无法再访问应用缓存

应用缓存还有很多相关的事件（checking、error、noupdate、downloading、progress、progress、cached等），表示其状态的改变。另外，还有 update()、swapCache() 等方法。

### 数据存储
随着 Web 应用程序的出现，也产生了对于能够直接在客户端上存储用户信息能力的要求。无论是登录信息、偏好设定活其他数据，开发者在找各种方式将数据存在客户端。

#### Cookie
[PERSISTENT CLIENT STATE HTTP COOKIES](http://curl.haxx.se/rfc/cookie_spec.html)标准对 cookie 机制的阐述 —— 持久客户端状态：HTTP Cookies

HTTP Cookie，通常直接叫做 cookie，最初是在客户端用于存储会话信息的。该标准要求服务器对任意 HTTP 请求发送 Set-Cookie HTTP 头作为响应的一部分，其中包含会话信息。例如，这种服务器响应的头可能如下：

```
HTTP/1.1 200 OK
Content-type: text/html
Set-Cookie: name=value
Other-header: other-header-value
```

这个 HTTP 响应设置以 name 为名称，以 value 为值的一个 cookie，名称和值在传送时都必须时 URL 编码的。浏览器会存储这样的会话信息，并在这之后，通过为每个请求添加 Cookie HTTP 头将信息发送回服务器，如下：

```
GET /index.html HTTP/1.1
Cookie: name=value
Other-header: other-headerv-value
```

发送回服务器的额外信息可以用于唯一验证客户来自于发送的哪个请求。

##### 限制
cookie 在性质上时绑定在特定的域名下的，当设定一个 cookie 后，再给创建它的域名发送请求时，都会包含这个 cookie。这个限制确保了储存在 cookie 中的信息只能让批准的接受者访问，而无法被其他域访问。

- 数量限制
由于 cookie 是存在客户端计算机上的，还加入了一些限制确保 cookie 不会被恶意使用，同时不会占据太多磁盘空间。**每个域的 cookie 总数是有限的，不过浏览器之间各有不同。**当超过单个域名限制之后还要再设置 cookie，会清除以前设置的 cookie（浏览器之间选择清除的逻辑不同），所以考虑 cookie 限制非常重要，以免出现不可预期的后果。

- 尺寸限制
浏览器中对于 cookie 的尺寸也有限制，大多数浏览器都有大约 4096B（±1），尺寸限制影响到一个域下所有的 cookie，而非每个 cookie 单独限制。

##### cookie 的构成
cookie 由浏览器保存的以下几块信息构成：
- 名称：不区分大小写（实践中最好看作区分大小写），必须经过 URL 编码的
- 值：储存在 cookie 中的字符串值，必须经过 URL 编码的
- 域：cookie 对于哪个域有效。所有向该域发送的请求中都会包含这个 cookie 信息。这个值可以包含子域，也可以不包含（？）。如果没有明确设定，会认作来自设置 cookie 的那个域
- 路径：对于指定域中的那个路径，应该向服务器发送 cookie。例如，你可以指定 cookie 只有从 http://www.wrox.com/books/ 中才能访问，那么 http://www.wrox.com 的页面就不会发送 cookie 信息，即使请求都是来自同一个域的。
- 失效时间：表示 cookie 何时应该被删除的时间戳（也就是，何时应该停止向服务器发送这个 cookie）。默认情况下，浏览器会话结束时即将所有 cookie 删除；不过也可以自己设置删除时间，这个值是个 GMT 格式的日期，用于指定应该删除 cookie 的准确时间。因此，cookie 可在浏览器关闭后依然保存在用户的机器上。如果你设置的失效日期是个以前的时间，则 cookie 会被立刻删除。
- 安全标志：指定后，cookie 只有在使用 SSL 连接的时候才发送到服务器。例如，cookie 信息只能发送给 https://www.wrox.com，而 http://www.wrox.com 的请求则不能发送 cookie。

每一段信息都作为 Set-Cookie 头的一部分，使用分号加空格分隔每一段：
```

// ...
Set-Cookie: name=value; expires=Mon, 22-Jan-07 07:10:24 GMT; domain=.wrox.com; path=/; secure
// ...

```

需要注意的是，域、路径、失效时间和 secure 标志都是服务器给浏览器的指示，以指定何时应该发送 cookie。这些参数并不会作为发送到服务器的 cookie 信息的一部分，只有名值对儿才会被发送。

##### JavaScript 中的 cookie
document.cookie 属性获取当前页面可用的所有 cookie 的字符串，一系列由分号隔开的名值对儿。所有名字和值都是经过 URL 编码的，所以必须使用 decodeURIComponent() 来解码。

设置 cookie 的格式如下，和 Set-Cookie 头中使用的格式一样：
```
name=value; expires=expiration_time; path=domain_path; domain=domain_name; secure
```
其中，只有 cookie 的名字和值时必需的。

```javascript
document.cookie = encodeURIComponent("name") + "=" + ncodeURIComponent("Nicholas") + "; domain=.wrox.com; path=/";
```

由于 JavaScript 中读写 cookie 不是非常直观，需要封装一些函数来简化 cookie 的功能，基本 cookie 操作有三种：读取、写入和删除：

```javascript
var CookieUtil = {

    get: function (name){
        var cookieName = encodeURIComponent(name) + "=",
            cookieStart = document.cookie.indexOf(cookieName),
            cookieValue = null,
            cookieEnd;
            
        if (cookieStart > -1){
            cookieEnd = document.cookie.indexOf(";", cookieStart);
            if (cookieEnd == -1){
                cookieEnd = document.cookie.length;
            }
            cookieValue = decodeURIComponent(document.cookie.substring(cookieStart + cookieName.length, cookieEnd));
        } 

        return cookieValue;
    },
    
    set: function (name, value, expires, path, domain, secure) {
        var cookieText = encodeURIComponent(name) + "=" + encodeURIComponent(value);
    
        if (expires instanceof Date) {
            cookieText += "; expires=" + expires.toGMTString();
        }
    
        if (path) {
            cookieText += "; path=" + path;
        }
    
        if (domain) {
            cookieText += "; domain=" + domain;
        }
    
        if (secure) {
            cookieText += "; secure";
        }
    
        document.cookie = cookieText;
    },
    
    unset: function (name, path, domain, secure){
        this.set(name, "", new Date(0), path, domain, secure);  // 没有删除已有 cookie 的直接方法。所以需要使用相同的路径、域和安全选项再次设置 cookie，并将失效时间设置为过去的时间（1970年1月1日，初始化为 0 ms 的 Date 对象的值）
    }

};
```

##### 子 cookie
为了绕开浏览器的单域名 cookie 数限制，子 cookie 是存放在单个 cookie 中的更小段数据，也就是使用 cookie 值来存储多个名称值对儿，如：
```
name=name1=value1&name2=value2&name3=value3&name4=value4&name5=value5
```

##### 关于 cookie 的思考
还有一类 cookie 被称之为“HTTP 专有 cookie”，可从浏览器或者服务器设置，但是只能从服务器读取，JavaScript 无法获取 HTTP 专有 cookie 的值。

由于所有的 cookie 都会由浏览器作为请求头发送，所以在 cookie 中存储大量信息会影响到特定域的请求性能。cookie 信息越大，完成对服务器请求的时间也就越长，最好尽可能在 cookie 中少存储信息，以避免影响性能。

cookie 的性质和它的局限使得其不能作为存储大量信息的理想手段。且一定不要在 cookie 中存储重要和敏感的数据（信用卡号、个人地址等），cookie 数据并非存储在一个安全环境中，其中包含的任何数据都可以被他人访问。


#### IE 用户数据
自定义行为引入持久化用户数据

#### Web 存储机制
Web Storage

数据需要被严格控制在客户端上时，无须持续地将数据发回服务器。

Web Storage 的两个主要目标是：
- 提供一种在 cookie 之外存储会话数据的途径
- 提供一种存储大量可以跨会话存在的数据的机制

最初的 Web Storage 规范包含了两种对象的定义（都是以 window 对象属性的形式存在）：
- sessionStorage
- globalStorage

##### Storage 类型
Storage 类型提供最大的存储空间（因浏览器而异）来存储名值对儿，有如下方法：
- clear()：删除所有值
- getItem(name)：根据指定的名字 name 获取对应的值
- key(index)：获取 index 位置处的值的名字
- removeItem(name)：删除由 name 指定的名值对儿
- setItem(name, value)：为制定的 name 设置一个对应的值

其中，getItem、removeItem、setItem 方法可以直接调用，也可通过 Storage 对象间接调用，因为每个项目都是作为属性存储在该对象上的（所有可以通过点语法或者方括号语法访问属性来读取或设置，也可以使用 delete 操作符。但是不建议，以免会意外重写该对象上已经存在的成员）

还可以使用 length 属性来判断有多少名值对儿存放在 Storage 对象中，但一般无法判断所有数据的大小。另外，Storage 类型只能存储字符串，非字符串的数据在存储之前会被转换成字符串。

##### sessionStorage 对象
sessionStorage 对象存储特定于某个会话的数据，也就是该数据只保持到浏览器关闭。这个对象就像会话 cookie，也会在浏览器关闭后消失。存储在 sessionStorage 中的数据可以跨越页面刷新而存在，同时浏览器崩溃并重启后依然可用（如果浏览器支持）。

因为 sessionStorage 对象绑定于某个服务器会话，所以当文件在本地运行的时候是不可用的。存储在 sessionStorage 中的数据只能由最初给对象存储数据的页面访问到，所以对多页面应用有限制。

sessionStorage 对象是 Storage 的一个实例，可以使用 setItem() 或者直接设置新的属性来存储数据：

```javascript
// 使用方法存储/获取/删除数据
sessionStorage.setItem("name", "Nicholas");
sessionStorage.getItem("name");
sessionStorage.removeItem("name");

// 使用属性存储/获取/删除数据
sessionStorage.book = "Professional JavaScript";
var book = sessionStorage.book;
delete sessionStorage.name; //  在 Webkit 中可能无效
```

注意不同浏览器写入数据方面略有不同：同步 vs 异步。

可以结合 length 属性和 key() 方法来迭代 sessionStorage 中的值。
```javascript
for(var i=0, len = sessionStorage.length; i < len; i++){
    var key = sessionStorage.key(i);
    var value = sessionStorage.getItem(key);
    alert(key + '=' + value);
}
```

或者 for-in 循环迭代 sessionStorage：
```javascript
for(var key in sessionStorage){
    var value = sessionStorage.getItem(key);
    alert(key + '=' + value);
}
```

sessionStorage 对象应该主要用于仅对会话的小段数据的存储，如果需要跨域会话存储数据 => globalStorage/localStorage

sessionStorage 严格用于在一个浏览器会话中存储数据，因为数据在浏览器关闭后会立即删除。

##### globalStorage 对象
这个对象的目的是跨越会话存储数据，但有特定的访问限制。要使用 globalStorage，首先需要指定哪些域可以访问。可以通过方括号标记使用属性来实现：

```javascript
// 保存数据
globalStorage["wrox.com"].name = "Nicholas";

// 获取数据
var name = globalStorage["wrox.com"].name
```

在这里，访问的是针对域名 wrox.com 的存储空间。globalStorage 对象不是 Storage 的实例，而具体的 globalStorage["wrox.com"] 才是。这个存储空间对于 wrox.com 及其所有子域都是可以访问的。

避免使用可宽泛访问的数据存储，以防止出现潜在的安全问题。所以当使用 globalStorage 的时候一定要指定一个域名。对 globalStorage 空间的访问，是根据发起请求的页面的域名、协议和端口来限制的。

globalStorage 的每个属性都是 Storage 的实例。如果事先不能确定域名，可以使用 location.host 作为属性名比较安全。

如果不使用 removeItem() 或者 delete 删除，或者用户未清除浏览器缓存，存储在 globalStorage 属性中的数据会一直保留在磁盘中。这让 globalStorage 非常适合在客户端存储文档或者长期保存用户偏好设置。

##### localStorage 对象
localStorage 对象在修订过的 HTML 5 规范中作为持久保存客户端数据的方案取代了 globalStorage。与之不同，不能给 localStorage 指定任何访问规则 => 或者说，要访问同一个 localStorage 对象，页面必须来自同一个域名（子域名无效），使用同一种协议，在同一个端口上，相当于  globalStorage[location.host]

localStorage 是 Storage 的实例。

如果浏览器不支持 localStorage，可以：
```javascript
function getLocalStorage(){
    if (typeof localStorage == "object"){
        return localStorage;
    } else if (typeof globalStorage == "object"){
        return globalStorage[location.host];
    } else {
        throw new Error("Local storage not available.");
    }
}
```

localStorage 用于跨会话持久化数据并遵循跨域安全策略。

##### Storage 事件
对 Storage 对象进行任何修改，都会在文档上触发 storage 事件。这个事件的 event 对象有以下属性：
- domain：发生变化的存储空间的域名
- key：设置或删除的键名
- newValue：设置 —— 新值；删除 —— null
- oldValue：键被更改之前的值

##### 限制
与其他客户端数据存储方案类似，Web Storage 同样也有限制，且因浏览器而异。一般来说，对存储空间大小的限制都是以每个来源（协议、域和端口）为单位的。

http://dev-test.nemikor.com/web-storage/support-test/

#### IndexedDB
Indexed Database API，简称为 IndexedDB，是在浏览器中保存结构化数据的一种数据库。思想是创建一套 API，方便保存和读取 JavaScript 对象，同时还支持查询及搜索。

indexedDB 设计的操作完全是异步进行的。

1、数据库

IndexedDB 就是一个数据库，最大的特色是使用对象保存数据，而不是使用表来保存数据。一个 IndexedDB 数据库，就是一组位于相同命名空间下的对象的集合。

```javascript
var indexedDB = window.indexedDB || window.msIndexedDB || window.mozIndexedDB || window.webkitIndexedDB,    // 浏览器差异
    request,
    database;
    
request = indexedDB.open("admin");  // 如果传入的数据库已经存在，就发送一个打开它的请求；否则，发送一个创建并打开它的请求。返回一个 IDBRequest 对象，在这个对象上可以添加 onerror 和 onsuccess 事件处理程序
request.onerror = function(event){
    alert("Something bad happened while trying to open: " + event.target.errorCode);    // event.target === request
};
request.onsuccess = function(event){
    database = event.target.result;
    initializeDatabase();
};    
```

2、对象存储空间

可想象成表。

add()/put()

3、事务

4、使用游标查询

5、键范围

6、设定游标方向

7、索引

8、并发问题

9、限制
- IndexedDB 数据库只能由同源（协议、域名和端口）页面操作，因此不同跨域共享信息 => www.wrox.com 和 p2p.wrox.com 是完全独立的。
- 每个来源的数据库占用的磁盘空间也有限制
- 部分浏览器不允许本地文件访问 IndexedDB