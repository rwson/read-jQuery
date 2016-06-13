### 构造jQuery对象

如果在调用jQuery或者$传入的参数不同,jQuery对应的处理逻辑也就不同,根据传入的参数,一共有下面这几种用法:


参数 | 意义/执行逻辑
---|---
$(selector[,context]) | 接受一个选择器和可选的context,返回一个包含匹配选择器的DOM元素的jQuery对象
$(htmlStr[,owner])/$(htmlStr[,props]) | 用于创建HTML,给创建出来的DOM标签赋相关的属性值
$(elems)/$([elems]) | 接受HTMLDOMELEMEN( list),封装DOM元素为jQuery对象
$(plainObject) | 封装普通javascript对象为jQuery对象
$(function(){}); | 接受一个匿名函数,在DOM结构加载完成后执行
$(jQuery Object) | 接受一个jQuery对象,并且返回该jQuery对象的拷贝副本
$() | 创建一个空的jQuery对象

    (function(window, undefined) {
    
        //  构造jQuery对象
        var jQuery = (function(selector, context) {
        
            var jQuery = (function() {
            
                return new jQuery.fn.init(selector, context, rootjQuery);
                
            })();
            
            jQuery.fn = jQuery.prototype = {
                constructor: jQuery,
                
                init: function(selector, context, rootjQuery) {
                    //  ...
                },
                
                //  一些原型方法
                
            };
            
            jQuery.fn.init.prototype = jQuery.fn;
            
            jQuery.extend = jQuery.fn.extend = function (){
            
                //  实现拓展函数
                
            };
            
            jQuery.extend({
            
                //  一些静态属性和方法
                
            });
            
            return jQuery;
            
        })();
        
    })(window);

这边在构造器中,用了一个new操作符,就像下面这样:

    var jQuery = (function() {
    
        return new jQuery.fn.init(selector, context, rootjQuery);
        
    })();

这可以达到在调用jQuery时无new操作符的目的,比如下面定义一个自定义类,对它进行实例化的时候,就需要用new+构造方法([args,...])来声明

    function Person(name, age) {
        this.name = name;
        this.age = age;
    }
    
    Person.staticMethod1 = function() {
        //  ...
    }
    
    //  ...
    
    Person.prototype = {
    
        constructor: Person,
        
        classMethod1: function() {
            //  ...
        },
        
        //...
    };
    
    //  实例化
    var p = new Person("小宋", 23);
    
    //  进行一些操作

但是如果改成下面的写法,就可以在实例化的时候不需要new操作符:

    function Person(name, age) {
    
        var Person = function() {
            this.name = name;
            this.age = age;
        }
        
        return new Person();
    }
    
    //  ...

    //  进行实例化操作
    
    var p = Person("小宋", 23);
    
这样就省去了new操作符,在构造方法里面已经帮我们做好了

构造函数(jQuery.fn.init)会根据传入的参数(selector,context)类型,做出不同的解析,从而做出不同的逻辑处理,一共有下面的一些情况:


selector | context | 示例
---|---|---
可以转换成false的 | - | $()
DOM元素 | - | $(document.body)
"body" | - | $("body")
单独标签 | - | $("div")
复杂HTML字符串 | - | $(document.body)
选择器表达式 | - | $("#id")
选择器表达式 | 选择器表达式/jQuery对象 | $(".class", ".context")/$(".class", $(".context"))
选择器表达式 | - | $("#id").click(function(){ $("span",this).css("color", "red") });
匿名函数 | - | $(function(){  });
jQuery对象 | - | $($("div"));
其他任意值 | - | $({"a": "1","b": "2"});

