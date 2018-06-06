## 3-1mongodb模式模型设计及编码

> ch3-项目数据库实现

### 设计数据库模型


#### Schema - 模式定义
可以定义字段类型等

#### Model - 编译模型
对传入的模式Schema进行编译，生成构造函数

#### Documents - 文档实例化
调用构造函数，传入数据便可以存储到mongodb中

#### Documents - 数据库查询
1.批量查询：find({})
2.单条查询：findOne({\_id:id}) //传入一个特定的key
3.单条删除：remove({\_id:id},callback)

### 调整目录结构

```
- imooc/
    -- node_modules/
    -- bower_components/
    -- views/
        --- *.jade
    -- models/
        --- movie.js
    -- schemas/
        --- movie.js
    -- app.js
```

### 数据库模式模型的编码

- 模式-schemas/movie.js

```
var mongoose = require('mongoose');

var MovieSchema = new mongoose.Schema({
    doctor: String,
    title: String,
    language: String,
    country: String,
    summary: String,
    flash: String,
    poster: String,
    year: Number,
    meta:{
        createAt: {
            type: Date,
            default: Date.now()
        },
        updateAt: {
            type: Date,
            default: Date.now()
        }
    }
});

MovieSchema.pre('save', function(next){ //每次执行save操作会首先执行这个函数
    if(this.isNew){
        this.meta.createAt = this.meta.updateAt = Date.now();
    }else{
        this.meta.updateAt = Date.now();
    }

    next();
});

MovieSchema.statics = {
    fetch: function(cb){
        return this
            .find({})
            .sort('meta.updateAt');
        exec(cb);
    },
    findById: function(id, cb){
        return this
            .findOne({_id: id});
        exec(cb);
    }
}

module.exports = MovieSchema;
```

- 模型-models/movie.js

```
var mongoose = require('mongoose');
var MovieSchema = require('../schemas/movie');
var Movie = mongoose.model('Movie', MovieSchema);   //编译模式 -→ 模型（构造函数）

module.exports = Movie;
```