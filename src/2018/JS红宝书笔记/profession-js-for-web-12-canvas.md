## Canvas
画布

### 基本用法
```html
<canvas id="drawing" width=" 200" height="200">A drawing of something.</canvas>
```

要在这块画布上绘图，需要取得绘图上下文（对象） => `getContext()`

```javascript
var drawing = document.getElementById("drawing");
//􏵆 确定浏览器支持 􏶚􏶛􏱭􏶞􏶟<canvas>􏶆􏶇 元素
if (drawing.getContext){
    var context = drawing.getContext("2d"); // 获得绘图上下文对象
    // ...
}
```

使用 toDataURL() 方法，可以导出 <canvas> 元素上绘制的对象。

```javascript
// 取得图像的数据 URI
var imgURI = drawing.toDataURL('image/png');

// 显示图像
var image = document.createElement('img');
image.src = imgURI;
document.body.appendChild(image);
```

需要注意的是：
- 默认情况下，浏览器会将图像编码为 PNG 格式
- 如果绘制到画布上的图像源自不同的域，此方法会报错（跨域）

### 2D 上下文
使用 2D 绘图上下文提供的方法，可以绘制简单的 2D 图像，如矩形、弧线、路径等。

#### 填充和描边
2D 上下文的坐标开始于 <canvas> 元素的左上角，原点坐标是(0, 0)。基本绘图操作：**填充和描边**，其结果取决于：
- fillStyle
- strokeStyle

属性值可以是**字符串、渐变对象或模式对象**，默认值“#000000”，设置后所有涉及描边和填充的操作都将使用这两个样式，直至重新设置这两个值。

#### 绘制矩形
矩形是唯一一种可以直接在 2D 上下文中绘制的形状。这三个方法都能接收 4 个参数：矩形的 x 坐标、y 坐标、宽度、高度（单位：像素）
- fillRect()
- strokeRect()
- clearRect()

```javascript
//􏵿􏵣􏸪􏴀􏷴􏶉 绘制红色矩形
context.fillStyle = "#ff0000"; context.fillRect(10, 10, 50, 50);
//􏵿􏵣􏸫􏸬􏴾 绘制半透明的蓝色矩形
context.fillStyle = "rgba(0,0,255,0.5)"; context.fillRect(30, 30, 50, 50);

//􏵿􏵣􏸪􏴀􏸆􏸇􏷴􏶉 绘制红色描边矩形
context.strokeStyle = "#ff0000";
context.strokeRect(10, 10, 50, 50);
//􏵿􏵣􏸫􏸬􏴾 绘制半透明的蓝色描边矩形
context.strokeStyle = "rgba(0,0,255,0.5)";
context.strokeRect(30, 30, 50, 50);
􏲳􏵉􏸻􏶩􏰲􏳾􏷇􏷴􏶉
context.clearRect(40, 40, 10, 10);
```

- 描边线条的宽度：lineWidth
- 线条末端的形状：lineCap
- 线条相交的方式：lineJoin

#### 绘制路径
2D 上下文支持很多在画布上绘制路径的方法，通过路径可以创造出复杂的形状和线条。

绘制路径，首先必须调用 beginPath() 方法，表示要开始绘制新路径，然后再通过调用下列方法来实际绘制路径：
- arc(x, y, radius, startAngle, endAngle, counterclockwise)
- arcTo(x1, y1, x2, y2, radius)
- bezierCurveTo(c1x, c1y, c2x, c2y, x, y)
- lineTo(x, y)
- moveTo(x, y)：绘图游标
- quadraticCurveTo(cx, cy, x, y)
- rect(x, y, width, height)

绘制路径后，接下来有几种可能的选择：
- 如果想绘制一条连接到路径起点的线条，可以调用 closePath()
- 如果路径已经完成，可以调用 fill() 方法填充（fillStyle）或者 stroke() 方法描边（strokeStyle）
- 最后还可以调用 clip() 在路径上创建一个剪切区域

在 2D 绘图上下文中，路径是一种主要的绘图方式，因为路径能为要绘制的图形提供更多的控制。由于路径的使用很频繁，可以使用 isPointInPath(x, y) 方法在路径关闭之前确定画布上的某一点是否位于路径上。

```javascript
if (context.isPointInPath(100, 100)){
    alert("Point (100, 100) is in the path.");
}
```

绘制时钟表盘（不带数字） demo：
```javascript
var drawing = document.getElementById(“drawing”);
//make sure <canvas> is completely supported if (drawing.getContext){
var context = drawing.getContext(“2d”);
//start the path
context.beginPath();
//draw outer circle
context.arc(100, 100, 99, 0, 2 * Math.PI, false);
//draw inner circle
context.moveTo(194, 100);
context.arc(100, 100, 94, 0, 2 * Math.PI, false);
//draw minute hand
context.moveTo(100, 100);
context.lineTo(100, 15);
//draw hour hand
context.moveTo(100, 100);
context.lineTo(35, 100);
//stroke the path
context.stroke();
}
```

#### 绘制文本
- fillText()
- strokeText()

接受 4 个参数：要绘制的文本字符串、x 坐标、y 坐标、最大像素宽度（可选）。另外，这两个方法以下列 3 个属性为基础：
- font
- textAlign
- textBaseline

measureText() 方法

#### 绘制图像
drawImage()，注意跨域限制。

[CanvasRenderingContext2D.drawImage()](https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D/drawImage)

#### 阴影
- shadowColor
- shadowOffsetX
- shadowOffsetY
- shadowblur

#### 渐变
- CanvasGradient 实例

#### 模式
模式就是重复的图像，可以用来**填充**或**描边**图形。

---
笔者注：渐变和模式都可用来**填充**或**描边**图形

#### 使用图像数据
getImageData() 取得原始图像数据，返回 ImageData 的实例

#### 合成
- globalAlpha
- globalCompositionOperation

### WebGL