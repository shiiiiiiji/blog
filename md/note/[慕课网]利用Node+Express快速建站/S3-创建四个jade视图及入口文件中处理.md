## 2-2创建四个jade视图及入口文件中处理

> ch2-项目前后端流程打通

### 代码结构
![](http://om739linv.bkt.clouddn.com/codes.png)

### 具体代码
app.js
```
var express = require('express');
var port = process.env.PORT || 3000;
var app = express();

app.set('views', './views');
app.set('view engine', 'jade');
app.listen(port);

console.log('imooc started on port ' + port);

//路由
//index page
app.get('/', function(req, res){    //路由规则和URL地址相匹配
    res.render('index', {   //渲染模板
        title: 'imooc 首页'
    });
});

//detail page
app.get('/movie/:id', function(req, res){
    res.render('detail', {
        title: 'imooc 详情页'
    });
});

//admin page
app.get('/admin/movie', function(req, res){
    res.render('admin', {
        title: 'imooc 录入页'
    });
});

//list page
app.get('/admin/list/', function(req, res){
    res.render('list', {
        title: 'imooc 列表页'
    });
});
```

index.jade、detail.jade、admin.jade、list.jade
```
doctype
html
    head
        meta(charset="utf-8")
        title #{title}
    body
        h1 #{title}
```

### 测试
![](http://om739linv.bkt.clouddn.com/index.png)
![](http://om739linv.bkt.clouddn.com/detail.png)
![](http://om739linv.bkt.clouddn.com/moviein.png)
![](http://om739linv.bkt.clouddn.com/list.png)

