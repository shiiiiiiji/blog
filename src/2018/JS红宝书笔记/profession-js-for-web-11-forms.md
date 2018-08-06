## 表单脚本
JavaScript 最初的一个应用，就是分担服务器处理表单的职责，打破处处依赖服务器的局面。

### 表单基础知识
- html —— `<form>`元素
- JavaScript —— `HTMLFormElement` 类型对象

#### 提交表单：
- 用户单机提交按钮或图像按钮时，就会提交表单
```html
<!-- generic submit button -->
<input type="submit" value="Submit Form">
<!-- custom submit button -->
<button type="submit">Submit Form</button>
<!-- image button -->
<input type="image" src="graphic.gif">
```
以这种方式提交表单时，浏览器会在将请求发送给服务器之前触发 `submit` 事件。 => 我们有机会验证表单数据，据决定是否允许表单提交（阻止这个事件的默认行为就可以取消表单提交）
```javascript
var form = document.getElementById("myForm");
	EventUtil.addHandler(form, "submit", function(event){
	// 取得事件对象
	event = EventUtil.getEvent(event);
	// 阻止默认事件
	EventUtil.preventDefault(event);
});
```

- 在 JavaScript 中，以编程方式调用 `submit()` 方法也可以提交表单
而且，这种方式无需表单包含提交按钮，任何时候都可以提交。但**调用 submit() 方法不会触发 submit 事件，因此在调用之前要先验证表单数据**

重复提交表单：
- 第一次提交表单后，就禁用提交按钮
- 利用 onsubmit 事件处理程序取消后续的表单提交操作

#### 重置表单
```html
<!-- generic reset button -->
<input type="reset" value="Reset Form">
<!-- custom reset button -->
<button type="reset">Reset Form</button>
```

```javascript
var form = document.getElementById(“myForm”);
//reset the form
form.reset();
```

与调用 submit() 方法不同，调用 reset() 方法会像单击重置按钮一样触发 reset 事件。


#### 表单字段
- 共有的表单字段属性
- 共有的表单字段方法
- 共有的表单字段事件

### 文本框脚本
- 选择文本：
	- select() 方法
	- setSelectionRange() 方法
	- 选择（select）事件
- 过滤输入
	- 屏蔽字符
	```javascript
	EventUtil.addHandler(textbox, "keypress", function(event){
		event = EventUtil.getEvent(event);
		var target = EventUtil.getTarget(event);
		var charCode = EventUtil.getCharCode(event);
		if (!/\d/.test(String.fromCharCode(charCode)) && charCode > 9 && !event.ctrlKey){
			EventUtil.preventDefault(event);
		}
	});
	```
	- 操作剪贴板
	- 自动切换焦点
	- HTML 5 约束验证 API（原生支持，无需 JavaScript）
		- 必填字段：required
		- 其他输入类型：email、url
		- 数值范围
		- 输入模式：pattern
		- 检测有效性：checkValidity()
		- 禁用验证：novalidate

### 选择框脚本

### 表单序列化

### 富文本编辑
又称为 WYSIWYG（What You See Is What You Get，所见即所得）

使用 contenteditable 属性