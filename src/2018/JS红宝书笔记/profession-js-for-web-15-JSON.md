## JSON
JavaScript Object Notation，JavaScript 对象表示法。是一种数据格式。

JSON 是 JavaScript 的一个严格的子集，利用了 JavaScript 中的一些模式来表示结构化数据。

### 语法
JSON 的语法可以表示以下三种类型的值：
- 简单值，使用与 JavaScript 相同的语法，可以在 JSON 中表示字符串、数值、布尔值和 null，但不支持 JavaScript 中的特殊值 undefined。
- 对象，复杂数据类型，表示的是一组无序的键值对儿。每个键值对儿中的值可以是简单值，也可以是复杂数据类型的值。
- 数组，复杂数据类型，表示的是一组有序的键值对儿，可以通过数组索引来访问其中的值，数组的值也可以是任意类型——简单值、对象或数组。

#### 简单值
```json
5
true
null
"Hello, World!"
```

**JSON 字符串必须使用双引号。**

#### 对象
JSON 中对象的属性需要加引号。

```json
{
    "name": "Nicholas",
    "age": 30
}
```

#### 数组
```json
[
    25,
    "hi",
    true
]
```


```json
[
    {
        "title": "Professional JavaScript",
        "author": "Nicholas C. Zakas",
        "edition": 1
    },
    {
        "title": "Professional JavaScript",
        "author": "Nicholas C. Zakas",
        "edition": 1
    },
    {
        "title": "Professional JavaScript",
        "author": "Nicholas C. Zakas",
        "edition": 1
    },
    {
        "title": "Professional JavaScript",
        "author": "Nicholas C. Zakas",
        "edition": 1
    }
]
```

对象和数组通常是 JSON 数据格式的最外层形式，利用它们能够创造各种各样的数据结构。

### 解析和序列化
JSON 数据结构 **能够很方便的**解析为 有用的 JavaScript 对象。

JSON 对象（不支持的浏览器，可以使用一个 shim：https://github.com/douglascrockford/JSON-js）

[JavaScript术语：shim 和 polyfill](http://www.css88.com/archives/tag/shim)

ECMAScript 5 定义了一个原生的 JSON 对象，可以用来将对象序列化为 JSON 字符串或者将 JSON 数据解析为 JavaScript 对象。JSON 对象有两个方法分别用来实现上述两项功能，这两个方法都有一些选项，通过它们可以改变过滤的方法，或者改变序列化的过程。

#### stringify()

把 JavaScript 对象序列化为 JSON 字符串。
```javascript
var book = {
    title: "Professional JavaScript",
    authors: [
        "Nicholas C. Zakas"
    ],
    edition: 3,
    year: 2011
}
var jsonText = JSON.stringify(book);
```

**默认情况下，输出的 JSON 字符串不包含任何空格字符或缩进。**

jsonText 中的字符串：
```json
{"title":"Professional JavaScript","authors":["Nicholas C. Zakas"],"edition":3,"year":2011}
```

**在序列化 JavaScript 对象时，所有函数及原型成员都会被有意忽略，不体现在结果中。**，**值为 undefined 的任何属性也都会被跳过**。结果中最终都是值为有效 JSON 数据类型的实例属性。

[JSON.stringify() - JavaScript | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)

```javascript
// JSON.stringify(value[, replacer[, space]])

// 1. 过滤结果
// 数组
var book = {
    title: "Professional JavaScript", 
    authors: [
        "Nicholas C. Zakas"
    ],
    edition: 3,
    year: 2011
};
var jsonText = JSON.stringify(book, ["title", "edition"]);
// jsonText
// {"title":"Professional JavaScript","edition":3}

// 函数
var jsonText = JSON.stringify(book, function(key, value){
    switch(key){
        case "authors":
            return value.join(',');
        case "year":
            return 5000;
        case "edition":
            return undefined;
        default:
            return value;
    }
}); 
// jsonText
// {"title":"Professional JavaScript","authors":"Nicholas C. Zakas","year":5000}


// 2. 字符串缩进
var jsonText = JSON.stringify(book, null, 4);
// jsonText
// {
//     "title": "Professional JavaScript",
//     "authors": [
//         "Nicholas C. Zakas"
//     ],
//     "edition": 3,
//     "year": 2011
// }

var jsonText = JSON.stringify(book, null, " - -");
// jsonText
// {
//  - -"title": "Professional JavaScript",
//  - -"authors": [
//  - - - -"Nicholas C. Zakas"
//  - -],
//  - -"edition": 3,
//  - -"year": 2011
// }

```

toJSON() 方法：有时候，JSON.stringify() 还是不能满足对某些对象进行自定义序列化的需求。=>在这些情况下，可以给对象定义 toJSON() 方法，返回其自身的 JSON 数据格式。
```javascript
var book = {
    title: "Professional JavaScript", 
    authors: [
        "Nicholas C. Zakas"
    ],
    edition: 3,
    year: 2011,
    toJSON: function(){
        return this.title;
    }
};
var jsonText = JSON.stringify(book);
// jsonText
// "Professional JavaScript"
```

可以让 toJSON() 方法返回任何值，如果返回 undefined => 1. 如果包含它的对象嵌入在另一个对象中，会导致它的值变成 null， 2. 如果它是顶级对象，那序列化返回的结果是 undefined。

toJSON() 作为函数过滤器的补充，理解 **序列化（JSON.stringify）的内部顺序** 十分重要：
1. 如果存在 toJSON() 方法而且能通过它取得有效的值，则调用该方法。否则，返回对象本身；
2. 如果提供第二个参数，应用这个函数过滤器，传入函数过滤器的值是第 1 步返回的值；
3. 对第 2 步返回的每个值进行相应的序列化；
4. 如果提供第三个参数，执行相应的格式化。


#### parse()

把 JSON 字符串解析为原生 JavaScript 值。将 JSON 字符串直接传递给 JSON.parse() 就可以得到相应的 JavaScript 值。但注意：序列化前和解析后的对象是相互独立、没有任何关系的对象。如果传给 JSON.parse() 的字符串不是有效的 JSON，会**抛出错误**（一般会结合 try-catch）。

[JSON.parse() - JavaScript | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse)

```javascript
var book = {
    title: "Professional JavaScript", 
    authors: [
        "Nicholas C. Zakas"
    ],
    edition: 3,
    year: 2011,
    releaseDate: new Date(2011, 11, 1)
};
var jsonText = JSON.stringify(book);

var bookCopy = JSON.parse(jsonText, function(key, value){
    if(key == "releaseDate"){
        return new Date(value);
    }else{
        return value;
    }
});
alert(bookCopy.releaseDate.getFullYear());  // 2011
```