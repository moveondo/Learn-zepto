zepto源码解读
共七部分：

第一部分 2-38 ：立即执行函数 兼容部分内核bug

第二部分40-1069： 定义zepto并赋值给window 


第一部分 2-38 ：立即执行函数 兼容部分内核bug

第三部分 1074-1123：对Zepto继续进行扩展，主要是判断浏览器和手机内核

第四部分 1129-1456：接下来是事件处理;

第五部分 1462-1836:Ajax处理

第六部分 1839-1879：序列化表单

第七部分 1883-2007：CSS动画

第一部分 2-38 ：立即执行函数 兼容部分内核bug

javascript立即执行函数有两张写法：

1、 (function(形参){...})(实际传入参数);

2、 (function(形参){...}(实际传入参数));


zepto为了实现对模块化js，(只认识名词)，初始传入两个参数，并且第二个传入的参数是一个函数，

(function(形参1, 形参2){...}(实际传入参数1, function(){...}));
![image](https://github.com/moveondo/Learn-zepto/blob/master/images/1.png)
 
前面的;是为了压缩时的冲突

Zepto.js开始时一个立即执行函数，为了兼容一些浏览器bug，主要是trim和reduce
![image](https://github.com/moveondo/Learn-zepto/blob/master/images/2.png)

 
修复了IOS3.2的trim函数；以空格开始或者以空格结束时的全局替换
![image](https://github.com/moveondo/Learn-zepto/blob/master/images/3.png)

可见zepto.js中把undefined封装成void 0 了
 
len = t.length >>> 0,表示
1）将所有非数值转化为0,  
2）将所有大于等于0等数取整数部分；

第二部分40-1069： 定义zepto并赋值给window 

定义主体函数Zepto，依然是立即执行的函数，通过函数内部返回一个实际函数。 

然后将该Zepto函数赋值给全局的Zepto，即window.Zepto
![image](https://github.com/moveondo/Learn-zepto/blob/master/images/4.png)
  
细分：
![image](https://github.com/moveondo/Learn-zepto/blob/master/images/5.png)
 
定义写变量 及常用函数赋值（slice，filter） 
![image](https://github.com/moveondo/Learn-zepto/blob/master/images/6.png)

 定义一些正则表达式如ID，class选择器的正则；匹配非单独一个闭合标签的标签，类似将div标签不合并 等 
 
96-250：定义函数属性matches，函数type，isFunction，isWindow，isDocument，isObject，isArray，likeArray，compact，flatten，camelize，dasherize，uniq，classRE，maybeAddPx，defaultDisplay，children，fragment

![image](https://github.com/moveondo/Learn-zepto/blob/master/images/7.png)
 
在Zepto封装的即时执行函数内部定义了一个函数名为Z的构造函数 

这样$.fn上的方法就会被dom所继承 

将内部构造函数Z实例化，作为变量zepto的属性 

![image](https://github.com/moveondo/Learn-zepto/blob/master/images/8.png)
 
在初始化的方法中返回实例化之后的zepto.Z对象

1：这里首先判断如果没有传入参数，则返回新建的zepto对象

2：如果传入的是函数，$(function(){}) ,则执行$(document).ready(),即在文档加载完后执行自定义函数

3：如果selector本身就是一个zepto对象，则直接返回它自己

4：如果selector不是一个zepto对象，则根据不同类型的selector生产节点对象并赋值于dom

5：如果selector是数组，则将数组里的无效值去掉后赋值给dom

6：如果selector是对象

7：如果selector是html片段

8：如果selector是css选择字符串且context有值

9：如果selector是css选择字符串且context无值

10：调用zepto.Z，生产zepto对象

![image](https://github.com/moveondo/Learn-zepto/blob/master/images/9.png)

 
zepto初始化之后的对象赋值给$

325-478  之后定义$上的一些函数：$.extend，qsa，filtered，funcArg，className，deserializeValue，isEmptyObject，inArray，trim，map，each，grep

485-989 ：$.fn主要是针对DOM操作

992：$.fn.detach = $.fn.remove 赋值操作

996-1012：宽 高的取值

1021-1050：adjacencyOperators遍历

![image](https://github.com/moveondo/Learn-zepto/blob/master/images/10.png)

 after    => insertAfter；prepend  => prependTo；
before   => insertBefore；append   => appendTo

![image](https://github.com/moveondo/Learn-zepto/blob/master/images/11.png)

after    => insertAfter；prepend  => prependTo；
before   => insertBefore；append   => appendTo

  ![image](https://github.com/moveondo/Learn-zepto/blob/master/images/12.png)
 
把$.fn赋值给Z的原型，这样$.fn下面的函数Z也可以访问了
zepto下的函数赋值$.zepto也可以访问
$.fn与zepto.Z.prototype指向的是同一空间，这里达到了是扩展原型链的效果

![image](https://github.com/moveondo/Learn-zepto/blob/master/images/13.png)

 第三部分：
 
1074-1123：对Zepto继续进行扩展，主要是判断浏览器和手机内核

第四部分：

接下来是事件处理;
1129-1456: 事实上他还是调用的$.bind实现事件绑定，换个思维方式，其实整个zepto事件实现可以浓缩成这么几句话：
var eventSet = {
    el: {fnType: []}
};
function on(type, fn) {}
function off(type, fn) {}

第五部分：

1462-1836:Ajax处理

第六部分：

1839-1879：序列化表单

第七部分：

1883-2007：CSS动画

