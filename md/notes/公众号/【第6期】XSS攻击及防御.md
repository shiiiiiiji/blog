> 前端安全问题其实常见的也不多，一般有XSS攻击和CSRF攻击，今天早读课分享了一篇总结XSS攻击的文章。

## 一些相关背景
当网景公司最初推出JavaScript语言时，当时js是允许从另一个网页中取出数据的，这存在极大的安全风险，因此浏览器采用了**同源策略**——仅支持相同域名系统和使用相同协议的对象与网页之间的交互。但是这样无法完全避免这个数据被盗用的问题，还是能通过一些巧妙的方法想其他网页中注入脚本。

## XSS攻击是什么
全称`Cross-site scripting`，跨站脚本。XSS是一种网站应用程序的安全漏洞攻击，是代码注入的一种。通俗来说，就是恶意用户利用网页开发时留下的漏洞，通过巧妙的方法注入恶意指令代码到网页，使其他用户加载并执行攻击者恶意制造的网页程序（脚本）。

## XSS分类
常规分类中，XSS可分为三种：
- 存储型
数据库中存有数据中含有XSS攻击的数据，返回给客户端。若数据未经过任何转义。被浏览器渲染。就可能导致XSS攻击；

- 反射型
将用户输入的存在XSS攻击的数据，发送给后台，后台并未对数据进行存储，也未经过任何过滤，直接返回给客户端。被浏览器渲染。就可能导致XSS攻击；

- DOM based
纯粹发生在客户端的XSS攻击，如下例：
一个页面代码是这样的：
```javascript
<select>
	<script>
		document.write(
			"<option value=1>"
			+ document.location.href.substring( document.location.href.indexOf("default=")+8 )
			+ "</option>");
		document.write("<option value=2>English</option>");
	</script>
</select>
```
要实现的效果就是动态生成option选项的显示值，且依赖于query参数中default的值。

但是假如构造了一个链接如下，一旦点击这种链接后，就会触发其中的脚本代码：
```
...?default=<script>alert(document.cookie)</script>
```

但是现代浏览器，已经做了XSS过滤，一旦检测到XSS，会提示报错信息。（这里可能浏览器有差异，我发现Chrome会对URL编码的）

## XSS风险
- 会话劫持
将 cookie 或 token 等类似令牌数据上报给黑客，黑客就能以受害用户的权限进行操作

- 信息泄露

- 进一步的攻击
	- 运行恶意脚本的用户浏览器可能被操控继续攻击其他目标
	- 有黑客利用 XSS 嗅探内网服务，然后发起下一步的攻击

## XSS防御
- 转义
这是对XSS防御最常见的处理手段，可以对涉及到XSS攻击的特殊字符做转义处理。

转义编码参考如下：[https://dev.w3.org/html5/html-author/charref](https://dev.w3.org/html5/html-author/charref)

但是这种情况，一方面可能条件太强了，过滤了一些不需要转义的内容；另一方面，随着HTML的发展，可能会出现更多标签，如果我们过滤掉或者不过滤掉了会导致问题，而我们是没办法全部穷举的。


- 内容白名单
这种情况一般是在富文本场景下，允许一部分标签，然后又需要屏蔽其他标签时，单纯使用字符转义会产生全部封杀的情况。我们可以配置一个白名单库，出现在白名单内的标签、属性，我们就不过滤掉。

例：
```javascript
const xss = require("xss")
const options = {
    whiteList: {
        a: ["href", "title", "target"],
        p: [],
        span: [],
        h1: []
    }
}
const myxss = new xss.FilterXSS(options);
const result = myxss.process('<script>alert("xss");</script>')
```

- Content Security Policy
内容安全性策略（CSP）。这是从浏览器层就自动禁止外部注入恶意脚本，具体依据是开发者提供的一份配置。

使用方式有两种（配置信息放在里面，具体配置方式可自行查找资料）：
	- 一种是通过HTTP头信息的`Content-Security-Policy`字段
	- 一种是通过网页的`<meta>`标签

## 安全意识
> Always Remember Safety First
> 我们需要时刻保持安全意识。虽然安全始终是相对的，但如果我们通过提高开发中的安全意识，也就增加攻击者发起攻击的成本。


## 参考资料
- [https://developer.mozilla.org/zh-CN/docs/Web/HTTP/CSP](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/CSP)
- [【第1311期】浅析前端安全之 XSS](https://mp.weixin.qq.com/s?__biz=MjM5MTA1MjAxMQ%3D%3D&mid=2651229034&idx=1&sn=e05cd113bf615b4e1a34d6e22e7c8115#wechat_redirect)
- [【第849期】如何让前端更安全？——XSS攻击和防御详解](https://mp.weixin.qq.com/s?__biz=MjM5MTA1MjAxMQ==&mid=2651225720&idx=1&sn=780700316f7c3c245b6366269cc9257d&chksm=bd49a7fc8a3e2eeaccaff02f223f95fe249baa5523578a9dd3988e04caef472c29b9da47f289&scene=21#wechat_redirect)