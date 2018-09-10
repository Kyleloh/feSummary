# 前端碎片知识总结

## 手写事件模型及事件代理/委托
1. 捕获(低版本IE不支持捕获阶段)
2. 目标
3. 冒泡


### 绑定事件的方法:
`target.addEventListener(eType, listener, useCapture);`<br>
useCapture一般为false。为true时在捕获阶段触发。

`target.attachEvent(eType, listener); `(仅IE8及以下版本浏览器)


### 事件委托
> 可以大量节省内存占用，减少事件注册

> 可以实现当新增子对象时无需再次对其绑定事件，对于动态内容部分尤为合适

#### 原生JS事件委托
关键代码
```javascript
obj.onclick = function(e){
    var ev = e || window.event;
    var target = ev.target || ev.srcElement;    //具体点到的目标，最内层
    var cTarget = currentTarget || this;    //对象this始终等于currentTarget
}
```


### 实现事件模型
即写一个类或是一个模块，有两个函数，一个bind一个trigger，分别实现绑定事件和触发事件，核心需求就是可以对某一个事件名称绑定多个事件响应函数，然后触发这个事件名称时，依次按绑定顺序触发相应的响应函数。
```javascript
var Demo = (function(){
    var dirc = {};
    function bind(eType, func){
        if(typeof eType == "string" && typeof func == "function"){
            if(typeof dirc[eType] != "undefined"){
                dirc[eType].push(func);
            }else{
                dirc[eType] = [func];
            }
        }else{
            alert("bind传参有误");
        }
        console.log(dirc);
    }

    function trigger(eType){
        if(typeof dirc[eType] != "undefined"){
            for(var i=0; i < dirc[eType].length; i++){
                dirc[eType][i]();
            }
        }
    }

    function unbind(eType){
        if(typeof dirc[eType] != "undefined"){
            delete dirc[eType];
        }
    }

    return {
        bind : bind,
        trigger: trigger,
        unbind : unbind
    }
}());

Demo.bind("hello",function(){alert("hello")});
Demo.bind("hello",function(){alert("bbb")});
Demo.trigger("hello");
Demo.unbind("hello");
Demo.trigger("hello");
```


## 前端性能优化
- CDN
- 压缩静态文件
    - CSS Sprite
    - 图片压缩
- CSS靠前/JS靠后
- DOM操作放在一起
- lazyload
- 提高js的性能


## 闭包原理及应用
```javascript
function fun(n,o) {
  console.log(o)
  return {
    fun:function(m){
      return fun(m,n);
    }
  };
}
var a = fun(0);  a.fun(1);  a.fun(2);  a.fun(3);    //undefined / ? / ? / ?
var b = fun(0).fun(1).fun(2).fun(3);    //undefined / ? / ? / ?
var c = fun(0).fun(1);  c.fun(2);  c.fun(3);    //undefined / ? / ? / ?
```
[链接](http://www.cnblogs.com/xxcanghai/p/4991870.html)


## 手写Function.bind函数
```javascript
if (!Function.prototype.bind) {
    Function.prototype.bind = function(oThis) {
        if (typeof this !== "function") {
            // closest thing possible to the ECMAScript 5
            // internal IsCallable function
            throw new TypeError("TypeError");
        }

        var aArgs = Array.prototype.slice.call(arguments, 1),
            fToBind = this,
            fNOP = function() {},
            fBound = function() {
                return fToBind.apply(this instanceof fNOP ? this : oThis || this,
                    aArgs.concat(Array.prototype.slice.call(arguments)));
            };

        fNOP.prototype = this.prototype;
        fBound.prototype = new fNOP();

        return fBound;
    };
}
```


## 排序算法

### 快速排序(去重)
```javascript
//第一种:
//找到一个基准值，左边找到大于基准值，右边找到小于基准值的同时，左右换位。继续遍历。
function sort(arr){
    return quickSort2(arr,0,arr.length-1);
    function quickSort2(arr,l,r){            
        if(l<r){         
            var mid=arr[parseInt((l+r)/2)],i=l-1,j=r+1;         
            while(true){
                while(arr[++i]<mid);
                while(arr[--j]>mid);             
                if(i>=j)break;
                var temp=arr[i];
                arr[i]=arr[j];
                arr[j]=temp;
            }       
            quickSort2(arr,l,i-1);
            quickSort2(arr,j+1,r);
        }
        return arr;
    }
}

```


### 冒泡排序
```javascript
function bubbleSort(arr){
    var result = arr,
        len = result.length;
    for(var i=0; i < len; i++){
        for(var j=0; j < len-1-i; j++){
            console.log(++sss);
            if(result[j]>result[j+1]){
                var temp = result[j];
                result[j] = result[j+1];
                result[j+1] = temp;
            }
        }
    }
    return result;
}
```


### 去重
```javascript
function unique(arr){
    var result=[],
        record = {};    //记录每个元素出现的次数
    for(var i=0; i< arr.length; i++){
        if(record[arr[i]]){
            record[arr[i]]++;
        }else{
            result.push(arr[i]);
            record[arr[i]]=1;
        }
    }
    return record;
}
```


## JS的定义提升
```javascript
function Foo() {
    getName = function() {
        alert(1);
    };
    return this;
}
Foo.getName = function() {
    alert(2);
};
Foo.prototype.getName = function() {
    alert(3);
};
var getName = function() {
    alert(4);
};

function getName() {
    alert(5);
}

//请写出以下输出结果：
Foo.getName();
getName();
Foo().getName();
getName();
new Foo.getName();
new Foo().getName();
new new Foo().getName();
```
[链接](http://www.cnblogs.com/xxcanghai/p/5189353.html)


## 跨域问题

[JavaScript跨域总结与解决办法](http://www.cnblogs.com/rainman/archive/2011/02/20/1959325.html)

[跨域-知识](http://www.cnblogs.com/scottckt/archive/2011/11/12/2246531.html)

[跨域资源共享的10种方式](http://www.cnblogs.com/cat3/archive/2011/06/15/2081559.html)

### 1、document.domain+iframe的设置
对于主域相同而子域不同的例子，试用该方法。

xxx.a.com上的a.html
```javascript
document.domain = 'a.com';
var ifr = document.createElement('iframe');
ifr.src = 'http://script.a.com/b.html';
ifr.style.display = 'none';
document.body.appendChild(ifr);
ifr.onload = function(){
    var doc = ifr.contentDocument || ifr.contentWindow.document;
    // 在这里操纵b.html
    alert(doc.getElementsByTagName("h1")[0].childNodes[0].nodeValue);
};
```

script.a.com上的b.html
```javascript

document.domain = 'a.com';
```

问题：

1、安全性，当一个站点（b.a.com）被攻击后，另一个站点（c.a.com）会引起安全漏洞。<br>
2、如果一个页面中引入多个iframe，要想能够操作所有iframe，必须都得设置相同domain。

### 2、JSONP
思路

动态创建script标签,src带参数请求其它地址。<br>
返回后进行操作。<br>
最后要销毁。

### 3、利用iframe和location.hash
    a.html和c.html同域
    b.html不同域
    实现a.html与b.html通信

    a.html > b.html > c.html


### HTML5 postMessage
```javascript
//a.html
<iframe id="ifr" src="b.com/index.html"></iframe>
<script type="text/javascript">
window.onload = function() {
    var ifr = document.getElementById('ifr');
    var targetOrigin = 'http://b.com';  // 若写成'http://b.com/c/proxy.html'效果一样
                                        // 若写成'http://c.com'就不会执行postMessage了
    ifr.contentWindow.postMessage('I was there!', targetOrigin);
};
</script>

//b.html
<script type="text/javascript">
    window.addEventListener('message', function(event){
        // 通过origin属性判断消息来源地址
        if (event.origin == 'http://a.com') {
            alert(event.data);    // 弹出"I was there!"
            alert(event.source);  // 对a.com、index.html中window对象的引用
                                  // 但由于同源策略，这里event.source不可以访问window对象
        }
    }, false);
</script>
```


### window.name跨域
a,b,c三个页面. a,c同域, b不同域<br>
a里面添加iframe,设置src为b页面.b页面里面修改window.name,也就是iframe里面的window.name, <br>
第一次onload时,将iframe里的src改成c的地址.此时c跟a同域,可以传递window.name.

### document.domain+iframe的设置 (用于主域相同，子域不同的情况)


## 正则表达式
方法
```javascript
    var reg = /\w+/g;
    reg.test(str);
    str.match(reg);
```

规则
```
/d    [0-9]                   匹配数字
/D    [^0-9]                  匹配非数字字符 
/s    [ /n/r/t/f/x0B]         匹配一个空白字符 
/S    [^ /n/r/t/f/x0B]        匹配一个非空白字符 
/w    [a-zA-Z0-9_]            匹配字母数字和下划线 
/W    [^a-zA-Z0-9_]           匹配除字母数字下划线之外的字符 


*     匹配前面的子表达式零次或多次。例如，zo* 能匹配 "z" 以及 "zoo"。 * 等价于{0,}。 
+     匹配前面的子表达式一次或多次。例如，'zo+' 能匹配 "zo" 以及 "zoo"，但不能匹配 "z"。+ 等价于 {1,}。 
?     匹配前面的子表达式零次或一次。例如，"do(es)?" 可以匹配 "do" 或 "does" 中的"do" 。? 等价于 {0,1}。 
{n}   n 是一个非负整数。匹配确定的 n 次。例如，'o{2}' 不能匹配 "Bob" 中的 'o'，但是能匹配 "food" 中的两个 o。 
{n,}  n 是一个非负整数。至少匹配n 次。例如，'o{2,}' 不能匹配 "Bob" 中的 'o'，但能匹配 "foooood" 中的所有 o。
      'o{1,}' 等价于 'o+'。'o{0,}' 则等价于 'o*'。
{n,m} m 和 n 均为非负整数，其中n <= m。最少匹配 n 次且最多匹配 m 次。刘， "o{1,3}" 将匹配 "fooooood" 中的前三个 o。
    'o{0,1}' 等价于 'o?'。请注意在逗号和两个数之间不能有空格。
```


## 字符串/数组 操作

### 字符串 操作
| 方法          | 描述                                                                               |
|---------------|------------------------------------------------------------------------------------|
| concat()      | 将两个或多个字符的文本组合起来，返回一个新的字符串。                               |
| indexOf()     | 返回字符串中一个子串第一处出现的索引。如果没有匹配项，返回 -1 。                   |
| charAt()      | 返回指定位置的字符。                                                               |
| lastIndexOf() | 返回字符串中一个子串最后一处出现的索引，如果没有匹配项，返回 -1 。                 |
| match()       | 检查一个字符串是否匹配一个正则表达式。                                             |
| substring()   | 返回字符串的一个子串。传入参数是起始位置和结束位置。                               |
| substr()      | 返回字符串的一个子串。传入参数是起始位置和字符串长度。(不建议使用)                 |
| replace()     | 用来查找匹配一个正则表达式的字符串，然后使用新字符串代替匹配的字符串。             |
| search()      | 执行一个正则表达式匹配查找。如果查找成功，返回字符串中匹配的索引值。否则返回 -1 。 |
| slice()       | 提取字符串的一部分，并返回一个新字符串。                                           |
| split()       | 通过将字符串划分成子串，将一个字符串做成一个字符串数组。                           |
| length        | 返回字符串的长度，所谓字符串的长度是指其包含的字符的个数。                         |
| toLowerCase() | 将整个字符串转成小写字母。                                                         |
| toUpperCase() | 将整个字符串转成大写字母。                                                         |


### 数组操作
| 方法       | 描述                                                          |
|------------|---------------------------------------------------------------|
| concat()   | 连接两个或更多的数组，并返回结果。                            |
| join()     | 把数组的所有元素放入一个字符串。元素通过指定的分隔符进行分隔。|
| pop()      | 删除并返回数组的最后一个元素                                  |
| push()     | 向数组的末尾添加一个或更多元素，并返回新的长度。              |
| reverse()  | 颠倒数组中元素的顺序。                                        |
| shift()    | 删除并返回数组的第一个元素                                    |
| unshift()  | 向数组的开头添加一个或更多元素，并返回新的长度。              |
| slice()    | 从某个已有的数组返回选定的元素                                |
| sort()     | 对数组的元素进行排序                                          |
| splice()   | 删除元素，并向数组添加新元素。                                |
| toSource() | 返回该对象的源代码。                                          |
| toString() | 把数组转换为字符串，并返回结果。                              |
| valueOf()  | 返回数组对象的原始值                                          |


### 字符串和数组操作应用
```javascript
//获取url参数
var getUrlPara = function(){
    var arr = window.location.search.substring(1).split("&"),
        result = {};
    for(var i=0; i < arr.length; i++){
        result[arr[i].split("=")[0]] = arr[i].split("=")[1];
    }
    return result;
}
```


## 设计模式
- 构造器模式
- 模块模式
- 揭示模块
- 观察者模式
- 发布/订阅模式
- 中介者模式
- 单例模式

## 函数节流
通过setTimeout和clearTimeout对于频繁执行的函数进行延时处理。<br>
例如: onresize, onscroll 还有drag之类的。(为避免由于频率过高导致一直都不执行，可以设置一个必然触发执行的时间间隔)
```javascript
var throttle = function(fn, delay){
    var timer = null;
    return function(){
        var context = this, args = arguments;
        clearTimeout(timer);
        timer = setTimeout(function(){
            fn.apply(context, args);
        }, delay);
    };
 };


var throttleV2 = function(fn, delay, mustRunDelay){
    var timer = null;
    var t_start;
    return function(){
        var context = this, args = arguments, t_curr = +new Date();
        clearTimeout(timer);
        if(!t_start){
            t_start = t_curr;
        }
        if(t_curr - t_start >= mustRunDelay){
            fn.apply(context, args);
            t_start = t_curr;
        }
        else {
            timer = setTimeout(function(){
                fn.apply(context, args);
            }, delay);
        }
    };
 };
```


## JS运算符优先级
| 运算符                             | 描述                                         |
|------------------------------------|----------------------------------------------|
| . [] ()                            | 字段访问、数组下标、函数调用以及表达式分组   |
| ++ -- - ~ ! delete new typeof void | 一元运算符、返回数据类型、对象创建、未定义值 |
| * / %                              | 乘法、除法、取模                             |
| + - +                              | 加法、减法、字符串连接                       |
| << >> >>>                          | 移位                                         |
| < <= > >= instanceof               | 小于、小于等于、大于、大于等于、instanceof   |
| == != === !==                      | 等于、不等于、严格相等、非严格相等           |
| &                                  | 按位与                                       |
| ^                                  | 按位异或                                     |
| \|                                 | 按位或                                       |
| &&                                 | 逻辑与                                       |
| \|\|                               | 逻辑或                                       |
| ?:                                 | 条件                                         |
| = oP=                              | 赋值、运算赋值                               |
| ,                                  | 多重求值                                     |


## CSS 

### CSS选择器
着重注意CSS3的伪类
[链接](http://www.w3school.com.cn/cssref/css_selectors.asp)

### CSS垂直居中
`top:50%; margin-top:-with/2;`

`flexbox`

