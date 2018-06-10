## 引用类型
- Date类型
    - 创建一个日期对象：使用Date构造函数
        - 不传入参数 => 自动获得当前日期和时间
        - 传入该日期的毫秒数（从UTC时间1970年1月1日零时开始经过的毫秒数）,毫秒数获取方法
            - Date.parse()：接收一个**表示日期的字符串参数**（ECMA-262并没有定义应该支持哪些日期格式，因此会因实现而异，通常表现为因地区而异），然后尝试根据这个字符串返回相应日期的毫秒数。否则返回NaN。通常此过程在构造函数中可自动转化
            - Date.UTC()
    - Date.now()：返回调用这个方法时的日期和时间的毫秒数。**使用+操作符把Date对象转换为字符串**
    - 继承的方法
        - toLocaleString()
        - toString()
        - valueOf()：返回日期的毫秒表示 => 可以直接使用比较操作符来比较日期值
    - 日期格式化方法和日期/时间组件方法(复杂)
        - moment库：http://momentjs.cn/

- RegExp类型
    - 图形化正则表达式工具：https://regexper.com/
    - 创建正则表达式
        - 字面量
        - 构造函数
    - RegExp实例属性
        - global
        - ignoreCase
        - lastIndex
        - multiline
        - source
    - RegExp实例方法
        - exec()
        - test()
        - 继承的toString()和toString()会返回正则表达式的字面量
    - RegExp构造函数属性
        - “静态属性”