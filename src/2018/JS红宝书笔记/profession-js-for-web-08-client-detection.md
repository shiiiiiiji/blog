## 客户端检测
能力检测（又称特性检测）：目标不是识别特定的浏览器，而是识别浏览器的能力。

```javascript
// 能力检测基本模式
if(object.propertyInQuestion){
    // 使用 object.propertyInQuestion
}
```

更严谨的能力检测：typeof
```javascript
function isSortable(object){
    return typeof object.sort == "function";
}
```

怪癖检测：识别浏览器的特殊行为

用户代理检测：检测用户代理字符串来确定实际使用的浏览器。在每一次 HTTP 请求过程中，用户代理字符串是作为响应首部发送的，而且该字符串可以通过 JavaScript 的 `navigator.userAgent` 属性访问。

用户代理字符串的历史：HTTP 规范（包括1.0和1.1版）明确规定，浏览器应该发送简短的用户代理字符串，指明浏览器的名称和版本号。