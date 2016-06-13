### Sizzle选择器引擎

在$()中可以传入css选择器表达式,得益于强大的Sizzle选择器的支撑。

Sizzle是一套纯javascript实现的选择器引擎,可单独使用。几乎支持所有的css3系列选择器,HTML5新增的选择器方法(querySelector/querySelectorAll)。在调用$("div.class")时,jQuery会先通过Sizzle选取满足匹配条件的元素,然后再由jQuery进行操作。

精简下Sizzle代码,大概如下结构:

    (function(){
        
        //  定义一些变量,正则等等
        
        //  选择器入口
        var Sizzle = function(selector, context, results, seed) {
            //   ...
        };
        
        //  排序,去除重复的项
        Sizzle.uniqueSort = function (results) {
            //  ...
        };
        
        //  判断某个元素node是否匹配选择器表达式expr
        Sizzle.matchesSelector = function (node, expr) {
            //  ...
        };
        
        //  对块表达式进行查找
        Sizzle.find = function (expr, context, isXML) {
            //  ...
        };
        
        //  用块表达式过滤元素集合
        Sizzle.filter = function (expr, set, inplace, not) {
            //  ...
        };
        
        //  异常
        Sizzle.error = function (msg) {};
        
        //  获取DOM元素集合的文本内容
        var getText = Sizzle.getText = function (elem) {
            //  ...
        };
        
        var Expr = Sizzle.selectors = {
            
            //  块表达式
            order: ["ID", "NAME", "TAG"],
            
            //  正则表达式集,用于匹配和解析表达式(map)
            match: {ID, CLASS, NAME, ATTR, TAG, CHILD, POS, PSEUDO},
            
            //  属性名修正函数(map)
            attrMap: {"class", "for"},
            
            //  属性值读取函数(map)
            attrHandle: {href, type},
            
            //  块间关系表达式(map)
            relative: {"+", ">", "~", ""},
            
            //  块表达式查找函数(map)
            find: {ID, NAME, TAG},
        
            //  块级表达式预过滤集(map)
            preFilter: {CLASS, ID, TAG, CHILD, ATTR, PSEUDO, POS},
            
            //  伪类过滤函数集(map)
            filters: {enabled, disableb, checked, selected, parent, empty, has, header, text, radio, checkbox, file, password, submit, image, reset, button, input, focus},
            
            //  位置伪类过滤函数集(map)
            setFilters: {first, last, even, odd, lt, gt, nth, eq},
            
            //  块表达式过滤函数集(map)
            filter: {PSEUDO, CHILD, ID, TAG, CLASS, ATTR, POS}
        
        };
        
        //  浏览器支持HTML5
        if(document.querySelectorAll) {
            //  ...
        }
        
        //  ...
        
        //  工具方法以及实现和jQuery之间的关联
        Sizzle.attr = jQuery.attr;
        Sizzle.selectors.attrMap = {};
        jQuery.find = Sizzle;
        jQuery.expr = Sizzle.selectors;
        jQuery.expr[":"] = jQuery.expr.filters;
        jQuery.unique = Sizzle.uniqueSort;
        jQuery.text = Sizzle.getText;
        jQuery.isXMLDoc = Sizzle.isXML;
        jQuery.contains = Sizzle.contains;
    
    })();

在Sizzle开头,发现这样一段代码:

    var hasDuplicate = false;

    [0, 0].sort(function () {
        baseHasDuplicate = false;
        return 0;
    });

不知道为什么这样写,然后复制代码到Google查了下,原因是Chrome引擎(某些版本)对于sort的优化

    var a = 0, b = 0;
    [0, 0].sort(function() {
        a = 1;
        return 0;
    });
    
    [0, 1].sort(function() {
        b = 1;
        return 0;
    });
    alert(a === b);
    
上面的代码在Chrome会得到一个false的结果,但是在本机Chrome上运行下,结果和Firefox并无差异,所以这个不是存在所有的Chrome版本中的。

HTML原生提供了4个基础选取页面中DOM元素的方法:

- document.getElementById("idStr") *IE 6+, Firefox 3+, Safari 3+, Chrome 4+, and Opera 10+*
- document.getElementsByName("nameAttrValue") *IE 6+, Firefox 3+, Safari 3+,Chrome 4+, and Opera 10+*
- document.getElementsByClassName("className") *IE 9+, Firefox 3+, Safari 4+,Chrome 4+, and Opera 10+*
- document.getElementsByTagName("tagName") *IE 6+, Firefox 3+, Safari 3+,Chrome 4+, and Opera 10+*

在HTML5中提供了两个高级API来选取页面中DOM元素,即document.querySelector/document.querySelectorAll,可以传入复杂css表达式作为过滤条件来选取页面中的内容,但是这两个方法的只能兼容到IE9+等浏览器,低版本浏览器,无法使用,要想使用,还得用之前的几个基础方法来,但是在Sizzle中,给我们封装了一套统一的API,通过传入选择器表达式来选取页面中的元素。

下面盗一张图来说明下选择器表达式😁:

![选择器表达式](/images/Sizzle-1.png)

当jQuery判断调用构造函数时传入的是一个css选择器的时候,才会调用Sizzle,否咋还是实现自己的逻辑。


