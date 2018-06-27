- 继承
    - 传统OO语言都支持两种继承方式：
        - 接口继承：只继承方法签名（**因为ECMAScript的函数没有签名，所以无法实现接口继承**）
        - 实现继承：ECMAScript只支持实现继承，主要依靠**原型链**实现。
    - 原型链
        - 构造函数、原型和实例的关系：**每个构造函数都有一个原型对象，原型对象都包含一个指向构造函数的指针，而实例都包含一个指向原型对象的内部指针**
            - 每个构造函数（其实是每个函数都有这个属性）都有一个`prototype属性`，这个属性默认指向的是一个对象（即`原型对象`）。构造函数本质也是函数，会包含一个指向`Function.prototype`的内部属性`[[Prototype]]`
            - `默认指向的原型对象`会默认包含一个`constructor属性`，并指向构造函数。原型对象会包含一个指向`Object.prototype`的内部属性`[[Prototype]]`
            - 通过new调用构造函数创建的实例对象会包含一个指向原型对象的内部属性`[[Prototype]]`
            - 这里需要说明的是：
                - 所有对象都会默认包含一个指向其原型对象的内部属性`[[Prototype]]`
                - 只有函数对象才会默认包含`prototype属性`
                - 只有**默认**的原型对象才会默认包含`constructor属性`
            - 关系如下图所示：
                ![](https://user-images.githubusercontent.com/3774016/39959479-d324c69e-5644-11e8-83a1-8f774d936bf9.jpg)
        - 原型链怎么产生？**假设我们让原型对象等于另一个类型的实例**，那么
            - 此时的原型对象将包含一个指向另一个原型的指针
            - 相应另一个原型中也包含着一个指向另一个构造函数的指针
            - 如此层层递进，便构成了*实例与原型的链条*。
        - 原型链实现的本质就是**重写原型对象，代之以一个新类型的实例**，这样原型对象就拥有了新类型实例对象的所有属性与方法了
        - 实现原型链的基本模式，代码如下：
        ```javascript
        function SuperType(){
            this.property = true;
        }
        SuperType.prototype.getSuperValue = function(){
            return this.property;
        }
        function SubType(){
            this.subProperty = false;
        }

        // 继承了 SuperType
        SubType.prototype = new SuperType();

        SubType.prototype.getSubValue = function(){
            return this.subProperty;
        }

        var instance = new SubType();
        alert(instance.getSuperValue());
        ```
        - 实例、构造函数、原型关系图示：
            ![](https://user-images.githubusercontent.com/3774016/39959492-0deef4c0-5645-11e8-804e-690ec5f15619.jpg)
        - 个人OS：
            - 一开始会觉得这样做很奇怪，实例当原型，但是想想构造函数默认的原型对象，不也是Object的一个实例嘛，只不过默认多了一个constructor属性。那我们只需要在重写原型对象之后，再对他添加指向构造函数的contructor属性即可。
            - 所谓类型，在js中是通过函数来定义的，从理论上讲，所有函数应当都是一个自定义类型。而充当了自定义类型的函数，我们称之为构造函数。只有当函数是以new操作符调用时，才体现出构造函数的一面的作用。一般构造函数以大写字母开头命名。





