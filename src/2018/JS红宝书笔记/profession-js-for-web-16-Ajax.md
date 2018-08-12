## Ajax
[Ajax: A New Approach to Web Applications](http://experiencezen.com/wp-content/uploads/2007/04/adaptive-path-ajax-a-new-approach-to-web-applications1.pdf)

Asynchronous JavaScript + XML

能够向服务器请求额外数据而无需卸载页面 => 改变 Web 诞生以来一直沿用的“单击，等待”的交互模式。

---
浏览器发起请求的几种方式（有什么区别）：
- 输入链接
- src
- ajax

浏览器获取数据的方式：
- 请求
- 本地存储（包括缓存（被动缓存）、loaclStorage、cookie等（主动缓存））
---

XHR 的出现将浏览器原生的通信能力提供给了开发人员。其实有很多中方法可以实现这种浏览器和服务器的通信：隐藏的框架或内嵌框架、Java Applet、Flash等

### XMLHttpRequest 对象

#### XHR 的用法

new XMLHttpRequest()

```javascript
function createXHR() {
    if (typeof XMLHttpRequest != "undefined") {
        return new XMLHttpRequest();
    } else if (typeof ActiveXObject != "undefined") {
        if (typeof arguments.callee.activeXString != "string") {
            var versions = ["MSXML2.XMLHttp.6.0", "MSXML2.XMLHttp.3.0", "MSXML2.XMLHttp"],
                i, len;

            for (i = 0, len = versions.length; i < len; i++) {
                try {
                    new ActiveXObject(versions[i]);
                    arguments.callee.activeXString = versions[i];
                    break;
                } catch (ex) {
                    //􏰋􏰌 跳过
                }
            }
        }
        return new ActiveXObject(arguments.callee.activeXString);
    } else {
        throw new Error("No XHR object available.");
    }
}
var xhr = createXHR();
```

xhr.open()

接受三个参数（要发送请求的类型（get/post等）、请求的URL、表示是否异步发送请求的布尔值）
```javascript
xhr.open("get", "example.php", false);
// 1. URL 相对于执行代码的当前界面
// 2. 调用 open() 方法并不会真正发送请求，而只是启动一个请求以备发送
```
**注意跨域**

XHR.send()

接受一个参数（作为请求主体发送的数据，没有必须传入 null。因为这个参数对于有些浏览器是必须的。**调用 send() 之后，请求就会被分配到服务器**）

```javascript
xhr.send(null);
```

如果请求是同步的，那代码会等到服务器响应之后再继续执行。收到响应后，响应的数据会自动填充 XHR 对象的属性，相关属性有：
- responseText：作为响应主体被返回的文本
- responseXML：如果响应的内容类型是“text/xml”或“application/xml”，这个属性将保存包含这响应数据的 XML DOM 文档
- status：相应的 HTTP 状态。一般用其判断请求结果，决定下一步的操作
- statusText：HTTP 状态的说明。因为在跨浏览器使用此属性不可靠，因此不作为判断请求结果。

在接收到响应后，第一步是检查 status 属性，以确定响应已经成功返回。一般来说，可以将 HTTP 状态代码为 200 作为成功的标志。此时，responseText 属性内容已经就绪。此外，状态代码为 304 表示请求的资源并没有被修改，可以直接使用浏览器中缓存的版本。当然，也意味着响应是有效的：
```javascript
xhr.open("get", "example.txt", false);
xhr.send(null);
if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304){
    alert(xhr.responseText);
} else {
    alert("Request was unsuccessful: " + xhr.status);
}
```

如果请求是异步的（大多数情况下），此时可以检测 XHR 对象的 readyState 属性，该属性表示请求/响应过程的当前活动阶段。
- 0：未初始化。尚未调用 open() 方法
- 1：启动。已经调用了 open() 方法，但尚未调用 send() 方法
- 2：发送。已经调用了 send() 方法，但尚未接收到响应
- 3：接收。已经接收到部分响应数据
- 4：完成。已经接收到全部响应数据，而且已经可以在客户端使用了

**只要 readyState 属性的值变化时，都会触发一次 readystatechange 事件。**可以利用这个事件来检测每次状态变化后 readyState 的值。不过必须在调用 open() 之前指定 readystatechange 事件处理程序才能确保跨浏览器兼容性。

```javascript
var xhr = createXHR();
    xhr.onreadystatechange = function(){
        if (xhr.readyState == 4){   // 没有使用 this，避免产生错误
            if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304){
                  alert(xhr.responseText);
        } else {
             alert("Request was unsuccessful: " + xhr.status);
        }
    }
};
xhr.open("get", "example.txt", true);
xhr.send(null);
```

以上利用 DOM 0 级方法为 XHR 对象添加了时间处理程序，原因是并非所有浏览器都支持 DOM 2 级方法。

另外，在 **接收到响应之前** 还可以调用 abort() 方法来取消异步请求。
```javascript
xhr.abort();
```

调用这个方法之后，XHR 对象会停止触发事件，而且也不再允许访问任何与响应有关的对象属性。在终止请求之后，还应该对 XHR 对象进行 **解引用** 操作。

由于内存原因，不建议重用 XHR 对象。

#### HTTP 头部信息
每个 HTTP 请求和响应都会带有响应的头部信息。

默认情况下，在发送 XHR 请求的同时，还会发送下列头部信息。
- Accept：浏览器能够处理的内容类型
- Accept-Charset：浏览器能够显示的字符集
- Accept-Encoding：浏览器能够处理的压缩编码
- Accept-Language：浏览器当前设置的语言
- Connection：浏览器与服务器之间连接的类型
- Cookie：当前页面设置的任何 Cookie
- Host：发出请求的页面所在的域
- Referer：发出请求的页面的 URI（HTTP规范写错了，正确应该是 referrer）
- User-Agent：浏览器的用户代理字符串

setRequestHeader() 可以设置自定义的请求头部信息，接受两个参数：头部字段的名称和头部字段的值。要成功发送请求头部信息，必须在调用 open() 方法之后且调用 send() 方法之前调用 setRequestHeader()：
```javascript
var xhr = createXHR();
xhr.onreadystatechange = function () {
    if (xhr.readyState == 4) {
        if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
            alert(xhr.responseText);
        } else {
            alert("Request was unsuccessful: " + xhr.status);
        }
    }
};
xhr.open("get", "example.php", true);
xhr.setRequestHeader("MyHeader", "MyValue");
xhr.send(null);
```

服务器在接收到这种自定义的头部信息之后，可以执行响应的后续操作。尽量不要重写默认的头部信息，有的浏览器不允许这样做。

getResponseHeader() 方法并传入头部字段名称，可以取得相应的响应头部信息，而调用 getAllResponseHeaders() 方法则可以取得一个包含所有头部信息的长字符串。

```javascript
var myHeader = xhr.getResponseHeader("MyHeader");
var allHeaders = xhr.getAllResponseHeaders();
```

#### GET 请求
GET 是最常见的请求类型，最常用于向服务器查询某些信息。必要时，可以将查询字符串参数追加到 URL 的末尾，以便将信息发送给服务器。对 XHR 而言，位于传入 open() 方法的 URL 末尾的查询字符串必须经过正确的编码才行。

使用 GET 请求经常会发生的一个错误，就是查询字符串的格式有问题。查询字符串中的每个参数的名称和值都必须使用 `encodeURIComponent()` 进行编码，然后才能放到 URL 的末尾；而且所有 名-值对儿 都必须由和号（&）分隔。

```javascript
xhr.open("get", "example.php?name1=value1&name2=value2", true);
```

```javascript
function addURLParam(url, name, value){
    url += (url.indexOf("?") == -1 ? "?" : "&");
    url += encodeURIComponent(name) + "=" + encodeURIComponent(value);
    return url;
}
```

```javascript
var url = "example.php";

// 添加参数
url = addURLParam(url, "name", "Nicholas");
url = addURLParam(url, "book", "Professional Javascript");

// 初始化请求
xhr.open("get", url, false);
```

---
你能举出多少种使用 GET 请求的场景吗？

#### POST 请求
使用频次仅次于 GRT 的是 POST 请求，通常用于向服务器发送应该被保存的数据。POST 请求应该把数据作为请求的主体提交，而 GET 请求传统上不是这样。POST 请求的主体可以包含非常多的数据。

（GET VS POST 区别）

（？有种错觉是 POST 比 GET 更常用，是因为很多操作其实本质上都是 GET 请求，如：访问服务器的第一个请求（可能是直接访问，也可能是跳转，但 SPA 可能就第一个页面是 GET 请求，后面只是改变了 URL）、各种外部资源的引用（带有 src 属性的元素，img/link/script/audio/video/iframe/...）、主动发起的 GET 请求（Ajax 等）...）

```javascript
xhr.open("post", "example.php", true);
xhr.send(); // 发送 POST 请求的第二步就是向 send() 方法传入某些数据
```

注意：
1. **Content-Type**头部信息设置与服务端解析请求数据方法( $_POST / $HTTP_RAW_POST_DATA )
2. POST 请求与 form 表单，以及如何用 POST 请求模拟表单提交

```javascript
function submitData(){
    var xhr = createXHR();
    xhr.onreadystatechange = function(){
        if(xhr.readyState == 4){
            if((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304){
                alert(xhr.responseText);
            }else{
                alert("Request was unsuccessful: " + xhr.status);
            }
        }
    }

    xhr.open("post", "postexample.php", true);
    xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
    var form = document.getElementById("user-info");
    xhr.send(serialize(form));
}
```


### XMLHttpRequest 2 级
#### FormData
表单数据的序列化 => XMLHttpRequest 2 级定义了 FormData 类型，可直接传入 XHR 的 send() 方法。

#### 超时设定
XHR 对象的 timeout 属性，表示请求在等待响应多少毫秒之后就终止。超时规定事件内浏览器还没有接收到响应，就会触发 timeout 事件，进而会调用 onetimeout 事件处理程序。

```javascript
var xhr = createXHR();
xhr.onreadystatechange = function(){
    if(xhr.readyState == 4){
        try{
            if((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304){
                alert(xhr.responseText);
            }else{
                alert("Request was unsuccessful: " + xhr.status);
            }
        }catch(ex){
            // 假设由 ontimeout 事件处理程序处理
            // 当超时时，请求会自动终止，会调用 ontimeout 事件处理程序
            // 但此时 readyState 可能已经改变为 4 了 => 会调用 onreadystatechange 事件处理程序
            // 但在超时终止请求之后再访问 status 属性，会导致错误 => try-catch
        }
    }
}

xhr.open("get", "timeout.php", true);
xhr.timeout = 1000; // 将超时设置为 1 秒
xhr.ontimeout = function(){
    alert("Request did not return in a second.");
};
xhr.send(null);
```

#### overrideMimeType() 方法
重写 XHR 响应的 MIME 类型。

因为返回响应的 MIME 类型决定了 XHR 对象如何处理它，所以提供一种方法能够重写服务器返回的 MIME 类型很有用。但调用此方法必须在 send() 方法之前，才能保证重写响应的 MIME 类型。

```javascript
var xhr = createXHR();
xhr.open("get", "text.php", true);
xhr.overrideMimeType("text/xml");
xhr.send(null);
```

### 进度事件
Poogress Event 规范定义了与客户端服务器通信有关的事件，后来也被其他 API 借鉴。
- loadstart：在接收到响应数据的第一个字节时触发
- progress：在接收响应期间持续不断地触发
- error：在请求发生错误时触发
- abort：在因为调用 abort() 方法而终止连接时触发
- load：在接收到完整的响应数据时触发
- loadend：在通信完成或者触发 error、abort 或 load 事件后触发

loadstart -> progress -> ... -> progress -> error/abort/load -> loadend

每个请求都从触发 loadstart 事件开始，接下来是一或多个 progress 事件，然后触发 error、abort 或 load 事件中的一个，最后以触发 loadend 事件结束。

load 事件理论是可替代 readystatechange 事件（没必要检查 readyState 属性了），但因为并非所有浏览器都为这个事件实现了适当的事件对象（event，其 target 属性指向 XHR 对象实例），所以还是需要使用 XHR 对象变量。

```javascript
var xhr = createXHR();
xhr.onload = function(){    // 只要浏览器接收到服务器的响应，不管其状态如何，都会触发 load 事件。
    // 但必须要检查 status 属性，才能确定数据是否真的已经可用了
    if((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304){
        alert(xhr.responseText);
    }else{
        alert("Request was unsuccessful: " + xhr.status);
    }
}
xhr.open("get", "altevents.php", true);
xhr.send(null);
```

progress 事件，会在浏览器接收新数据期间周期性触发。

onprogress 事件处理程序会接收到一个 event 对象，其 target 属性是 XHR 对象，但包含着三个额外的属性：
- lengthComputable：表示进度信息是否可用的布尔值
- position：已经接收的字节数
- totalSize：根据 Content-Length 响应头部确定的预期字节数

创建进度指示器：
```javascript
var xhr = createXHR();
xhr.onload = function(){
    if((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304){
        alert(xhr.responseText);
    }else{
        alert("Request was unsuccessful: " + xhr.status);
    }
}

xhr.onprogress = function(event){   // 必须在调用 open() 方法之前添加 onprogress 事件处理程序
    var divStatus = document.getElementById('status');
    if(event.lengthComputable){
        divStatus.innerHTML = "Received " + event.position + " of " + event.totalSize + " bytes";
    }
}

xhr.open("get", "altevents.php", true);
xhr.send(null);
```


### 跨域资源共享
何谓“跨域”？

通过 XHR 实现 Ajax 通信的一个主要限制，来源于跨域安全策略。默认情况下，XHR 对象只能访问与包含它的页面位于同一个域的资源（从字面上来看，很容易认为跨域是由服务端控制？但其实不然）。这种安全策略可以预防某些恶意行为，但是实现合理的跨域请求对开发某些浏览器应用程序也是至关重要的。

CORS（Cross-Origin Resource Sharing，跨域资源共享）是 W3C 的一个工作草案，定义了在必须访问跨源资源时，浏览器与服务器应该如何沟通。CORS 背后的基本思想，就是使用自定义的 HTTP 头部让浏览器与服务器进行沟通，从而决定请求或响应是应该成功，还是应该失败。（能否实现一个不受“同源策略”限制的浏览器？）

比如一个简单的使用 GET 或 POST 发送的请求，它没有自定义的头部，而主体内容是 `text/plain`，在发送改请求时，需要给它附加一个额外的 Origin 头部，其中包含请求页面的源信息（协议、域名和端口），以便服务器根据这个头部信息来决定是否给予响应。

```
Origin: http://www.nczonline.net
```

如果服务器认为这个请求可以接受，就在 `Access-Control-ALlow-Origin` 头部中回发相同的源信息（如果是公共资源，可以回发`“*”`）

```
Access-Control-ALlow-Origin: http://www.nczonline.net
```

如果没有这个头部，或者有这个头部但源信息不匹配，浏览器就会驳回请求。

跨域 XHR 对象有一些限制（主要为了安全考虑）：
- 不能使用 setRequestHeader() 设置自定义头部
- 不能发送和接收 cookie
- 调用 getAllResponseHeaders() 方法总会返回空字符串

由于无论同源请求还是跨源请求都使用相同的接口，因此对于本地资源，最好使用相对 URL，在访问远程资源时再使用绝对 URL，这样做能消除歧义，避免出现限制访问头部或本地 cookie 信息等问题。

Prefighted Requests：CORS 通过一种叫做 Prefighted Requests 的透明服务器验证机制支持开发人员使用自定义的头部、GET 或 POST 之外的方法，以及不同类型的主体内容。

带凭据的请求：默认情况下，跨源请求不提供凭据（cookie、HTTP 认证及客户端 SSL 证明等）。通过将 withCredentials 属性设置为 true，可以指定某个请求应该发送凭据，并以下面的 HTTP 头部来响应：

```
Access-Control-Allow-Credentials: true
```

如果发送的是带凭据的请求，但是服务器的响应中没有包含这个头部，那么浏览器就不会把响应交给 JavaScript，于是：
- responseText 为空字符串
- status 为0
- 还会调用 onerror() 事件处理程序

另外，服务器还可以在 Prefight 响应中发送这个 HTTP 头部，表示允许源发送带凭据的请求。

跨浏览器的 CORS：其实浏览器对 CORS 的支持成都并不都一样，但所有浏览器都支持简单的（非 Prefighte 和不带凭据的）请求。因此有必要实现一个跨浏览器的方案，检测 XHR 是否支持 CORS 的最简单方式，就是检查是否存在 withCredentials 属性，再结合检测 XDomainRequest 对象是否存在（IE），就可以兼顾所有浏览器了（？此处存疑，不知当前是什么情况）。

### 其他跨域技术
在 CORS 出现以前，要实现跨域 Ajax 通信颇费周折，开发人员想出一些办法，利用 **DOM 中能够执行跨域请求的功能，在不依赖 XHR 对象的情况下也能发送某种请求。**

虽然 CORS 技术已经无处不在，但开发人员自己发明的这些技术仍然被广泛使用，毕竟这样不需要修改服务器端代码。

#### 图像 Ping
`<img>`标签。

`图像 Ping`是与服务器进行，简单、单向的跨域通信的一种方式。请求的数据是通过查询字符串形式发送的，而响应可以是任意内容，但通常是像素图或 204 响应。通过图像 Ping，浏览器得不到任何具体的数据，但通过侦听 load 和 error 事件，它能知道响应是什么时候接收到的。

```javascript
var img = new Image();
img.onload = img.onerror = function(){
    alert("Done!");
}
img.src = "http://www.example/com/test?name=Nicholas";
```

图像 Ping 最常用于跟踪用户点击页面或动态广告曝光次数。

图像 Ping 有两个主要的缺点：
- 只能发送 GET 请求
- 无法访问服务器的响应文本

因此，**图像 Ping 只能用户浏览器与服务器间的单向通信。**
