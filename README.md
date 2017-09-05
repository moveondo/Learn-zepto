zepto源码解读
共七部分：

第一部分 2-38 ：立即执行函数 兼容部分内核bug

第二部分40-1069： 定义zepto并赋值给window 


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

 第三部分：
 
1074-1123：对Zepto继续进行扩展，主要是判断浏览器和手机内核

![image](https://github.com/moveondo/Learn-zepto/blob/master/images/13.png)

第四部分1129-1456：事件处理;

事实上他还是调用的$.bind实现事件绑定，换个思维方式，其实整个zepto事件实现可以浓缩成这么几句话：

var eventSet = {
    el: {fnType: []}
};

function on(type, fn) {}

function off(type, fn) {}

第五部分：1462-1836:Ajax处理

流程：

1：根据$.ajaxSettings 完善settings参数

2：触发全局ajaxStart ,document.trgger("ajaxStart")事件

3：判断是否跨域 ，确定settings.crossDomain的值

这里有一个获取url的协议和host的简单方法：

  urlAnchor = document.createElement('a')
  
  //如果没有设置请求地址，则取当前页面地址
  
  urlAnchor.href = settings.url
  
  // cleans up URL for .href (IE only), see https://github.com/madrobby/zepto/pull/1049
  
  urlAnchor.href = urlAnchor.href
  
  无论settings.url带不带host和protocol,最后由urlAnchor.host和urlAnchor.protocol会指出当前settings.url的主机名和协议名

4：如果没有设置settings.url  ，则默认window.location，即当前url

5：判断setting.url后面是否有#号，有则去掉#后面的

6：option.processData为true(默认为true，即默认以form-data格式来传值),且option.data为对象。则$.param(option.data);若为get请求， 则url后面添上序列化的option.data。

7：根据url判断dataType是否为jsonP类型。/\?.+=\?/.test

8：如果设置了缓存settings.catch === false，则url后面添加Date.now() ---->随机数Date.now() === new Date().getTime()　

9：如果是jsonp类型，则$.ajaxJson();

10：声明变量mine,header,setHeader(用来设置header对象),protocol(用来设置协议http),xhr,nativeSetHeader(xhr.setRequestHeader),abortTimeout

11：deferred.promise(xhr)，xhr继承deferred

12：判断settings.crossDomain，若为true，则代表跨域同步请求，否则为Ajax异步，默认为异步请求

13：设置头部Accept，信息先存入headers，默认为任何类型,此字段是告诉服务器，客户端接受指定的数据类型

accepts: {
  script: 'text/javascript, application/javascript, application/x-javascript',
  json:   jsonType,
  xml:    'application/xml, text/xml',
  html:   htmlType,
  text:   'text/plain'
},
var mime = settings.accepts[dataType], //媒体类型

setHeader('Accept', mime || '*/*')  //默认接受任何类型

14：判断有无mineType，有则强制服务器的头部覆盖返回类型即mineType字段加入到头部，mineType主要是用来兼容浏览器用的

15：根据method，设置content-type，于headers中，在get类型的请求头部中是不需要content-type的在request中的content-type表示请求的数据格式，在response中的content-type表示返回的数据格式

16：将settings.header中的属性加入到headers中去

17：设置xhr.onreadystatechange

　　1：如果状态为4，则设置onreadystatechange为empty，清除计时器，
  
　　2：读取dataType，获取响应值，格式化返回的数据，触发ajaxSuccess事件
  
18：触发ajax发送之前的操作，若settings.beforeSend返回false，或者全局ajaxBeforeSend返回false，则触发ajaxError("abort")，停止操作并返回。

19：xhrField设置，设置跨域凭证，xhr[name]=settings.xhrFields[name];

20：xhr.open

21：统一根据headers，设置请求头部

22：设置timeout，请求超时

23：xhr.send

24：返回xhr(其实也是个promise)


![image](https://github.com/moveondo/Learn-zepto/blob/master/images/14.png)

这里的parseArguments方法是一个很好的对函数参数校验的技巧，这个技巧在zepto里面很常见到。一般的方法是对各个参数是否为null进行校验，利用if，else针对不同的情况调用不同的参数。但这里的parseArguments根据不同的情况调整参数的值。然后统一的调用函数。


第六部分：

1839-1879：序列化表单

第七部分：

1883-2007：CSS动画

