## 2-1Node入口文件分析和目录初始化

> ch2-项目前后端流程打通

### 项目结构的初始化

```
- imooc/
    > npm install express
    > npm install jade
    > npm install mongoose
    > npm install bower -g
    > bower install bootstrap 
```

### 入口文件编码

app.js
```
var express = require('express');
var app = express();

app.set('view engine', 'jade');
app.set('port', 3000);

app.get('/', function(req, res){
    res.render('index', {title: 'imooc'});  //index → index.jade
});
```

index.jade
```
...
    title #{title}
...
```

### 初始时网站目录结构
```
- imooc/
    -- node_modules/            //node依赖模块
    -- bower_components/        //静态资源安装目录
    -- views/                   //主要的视图文件（测试）
        --- index.jade
        --- detail.jade
        --- admin.jade
        --- list.jade
    -- app.js                   //入口文件
```

### 测试前端流程
```
- localhost:3000/               //首页
- localhost:3000/movie/1        //电影详情页
- localhost:3000/admin/movie    //后台录入页
- localhost:3000/admin/list     //后台电影管理列表页
```






