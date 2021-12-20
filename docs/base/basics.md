# js基础知识总结

## 1.JS 的基本数据类型和引用数据类型有哪些？

基本数据类型：undefined、null、boolean、number、string、symbol（ES6）
引用数据类型：object、array、function

> `Symbol`表示独一无二的值，最大的用法是用来定义对象的唯一属性名

> Symbol 值作为属性名时，该属性是公有属性不是私有属性，可以在类的外部访问。但是不会出现在 for...in 、 for...of 的循环中，也不会被 Object.keys() 、 Object.getOwnPropertyNames() 返回。如果要读取到一个对象的 Symbol 属性，可以通过 Object.getOwnPropertySymbols() 和 Reflect.ownKeys() 取到。

## 2、重绘和回流（重排）的区别和关系？

**重绘**：当渲染树中的元素外观（如：颜色）发生改变，不影响布局时，产生重绘

**回流**：当渲染树中的元素的布局（如：尺寸、位置、隐藏/状态状态、border、字体大小）发生改变时，产生重绘回流

> 注意：JS 获取 Layout 属性值（如：offsetLeft、scrollTop、getComputedStyle 等）也会引起回流。因为浏览器需要通过回流计算最新值

回流必将引起重绘，而重绘不一定会引起回流

## 3.JavaScript 中的作用域与变量声明提升

1. JavaScript 作用域：

- 全局作用域
- 函数作用域
- 块级作用域（ES6新增）

2. JavaScript 变量声明提升：

- 在 JavaScript中，函数声明与变量声明经常被 JavaScript 引擎隐式地提升到当前作用域的顶部。
声明语句中的赋值部分并不会被提升，只有名称被提升

- 函数声明的优先级高于变量，如果变量名跟函数名相同且未赋值，则函数声明会覆盖变量声明
如果函数有多个同名参数，那么最后一个参数（即使没有定义）会覆盖前面的同名参数

## 4.Javascript 作用域链是什么概念？

全局函数无法查看局部函数的内部细节，但局部函数可以查看其上层的函数细节，直至全局细节
如果当前作用域没有找到属性或方法，会向上层作用域查找，直至全局函数，这种形式就是作用域链

## 5.请描述一下 cookies，sessionStorage 和 localStorage 的区别？

- cookie 是网站为了标示用户身份而储存在用户本地终端（Client Side）上的数据（通常经过加密）
- cookie 数据始终在同源的 http 请求中携带（即使不需要），记会在浏览器和服务器间来回传递。
- sessionStorage 和 localStorage 不会自动把数据发给服务器，仅在本地保存。

> 存储大小：

1. cookie 数据大小不能超过 4k。

2. sessionStorage 和 localStorage 虽然也有存储大小的限制，但比 cookie 大得多，可以达到 5M 或更大。

> 有效期（生命周期）：

1. localStorage: 存储持久数据，浏览器关闭后数据不丢失除非主动删除数据；

2. sessionStorage: 数据在当前浏览器窗口关闭后自动删除。

3. cookie: 设置的 cookie 过期时间之前一直有效，即使窗口或浏览器关闭

> document.cookie = "username=; expires=Thu, 01 Jan 1970 00:00:00 UTC; path=/;";

> sessionStorage.setItem  sessionStorage.getItem

> localStorage.setItem  localStorage.getItem

> localStorage.clear() 

## 6.数组方法
1. toString() (逗号分隔)
2. join()
3. push()
4. pop()
5. shift()
6. unshift()
7. splice()
8. concat()
9. slice()
10. reverse()
11. sort()

### ES6 新增

1. find()
2. findIndex()
3. fill()
4. flat()

## 7.字符串方法

1. indexOf()
2. lastIndexOf()
3. search()
4. slice()
5. substring()
6. substr()
7. replace()
8. toUpperCase()
9. toLowerCase()
10. concat()
11. trim()
12. charAt()
13. charCodeAt()
14. split()

### ES6 新增

1. includes()
2. repeat()
3. padStart()
4. 字符串模板

## 8.http状态码是什么?

HTTP状态码是网页HTTP请求的状态,200代表请求成功,404请求资源不存在,500服务器错误.

- 1xx：1开头的是信息状态码
- 2xx：2开头的是请求成功
- 3xx：3开头的是重定向
- 4xx：4开头的是客户端错误
- 5xx：5开头的是服务器错误

## 9.js的垃圾回收机制GC

JS具有自动垃圾回收机制。垃圾收集器会按照固定的时间间隔周期性的执行。

> JS中最常见的垃圾回收方式是标记清除。

工作原理：是当变量进入环境时，将这个变量标记为“进入环境”。当变量离开环境时，则
将其标记为“离开环境”。标记“离开环境”的就回收内存。

工作流程：

1. GC在运行时给内存中所有变量都加上标记
2. 去掉环境中的变量以及被引用的变量的标记
3. 再被加上的标记会被视为准备删除的变量
4. GC完成内存清除工作，销毁那些带标记的值，回收其所占内存

> 引用计数方式

工作原理：跟踪记录每个值被引用的次数。

## 10.什么是事件委托?

事件委托(事件代理)就是利用事件冒泡原理，只指定一个事件处理程序,就可以管理某一类的所有事件.

```
$('ul').on('click','li',function(){
   console.log($(this).text())      //this指向li
})
```

## 11.什么是闭包？闭包的作用是什么？

闭包的定义

《JavaScript高级程序设计》:
> 闭包是指有权访问另一个函数作用域中的变量的函数
《JavaScript权威指南》：
> 从技术的角度讲，所有的JavaScript函数都是闭包：它们都是对象，它们都关联到作用域链。
《你不知道的JavaScript》
> 当函数可以记住并访问所在的词法作用域时，就产生了闭包，即使函数是在当前词法作用域之外执行。

创建一个闭包

```
function foo() {
    var a = 2;
    return function fn() {
        console.log(a);
    }
}
let func = foo();
func(); // 输出 2
```

闭包使得函数可以继续访问定义时的词法作用域。拜 fn 所赐，在 foo() 执行后，foo 内部作用域不会被销毁。

### 闭包的作用

1. 能够访问函数定义时所在的词法作用域(阻止其被回收)。

2. 私有化变量

```
function base() {
    let x = 10; // 私有变量
    return {
        getX: function() {
            return x;
        }
    }
}
let obj = base();
console.log(obj.getX()); //10
```

3. 模拟块级作用域

```
var a = [];
for (var i = 0; i < 10; i++) {
    a[i] = (function(j){
        return function () {
            console.log(j);
        }
    })(i);
}
a[6](); // 6
```

4. 创建模块

```
function coolModule() {
    let name = 'Yvette';
    let age = 20;
    function sayName() {
        console.log(name);
    }
    function sayAge() {
        console.log(age);
    }
    return {
        sayName,
        sayAge
    }
}
let info = coolModule();
info.sayName(); //'Yvette'
```

模块模式具有两个必备的条件(来自《你不知道的JavaScript》)

- 必须有外部的封闭函数，该函数必须至少被调用一次(每次调用都会创建一个新的模块实例)

- 封闭函数必须返回至少一个内部函数，这样内部函数才能在私有作用域中形成闭包，并且可以访问或者修改私有的状态。

## 12.请实现一个 uniq 函数，实现数组去重

> 法1: 利用ES6新增数据类型 Set

```
function uniq(arry) {
    return [...new Set(arry)];
    // return Array.form(new Set(arry)
}
```

> 法 2: 利用 indexOf

```
function uniq(arry) {
    var result = [];
    for (var i = 0; i < arry.length; i++) {
        if (result.indexOf(arry[i]) === -1) {
            // 如 result 中没有 arry[i], 则添加到数组中
            result.push(arry[i])
        }
    }
    return result;
}
```
> 法 3: 利用 includes
```
function uniq(arry) {
    var result = [];
    for (var i = 0; i < arry.length; i++) {
        if (!result.includes(arry[i])) {
            // 如 result 中没有 arry[i], 则添加到数组中
            result.push(arry[i])
        }
    }
    return result;
}
```
> 法 4：利用 reduce
```
function uniq(arry) {
    return arry.reduce((prev, cur) => prev.includes(cur) ? prev : [...prev, cur], []);
}
```
> 法 5：利用 Map
```
function uniq(arry) {
    let map = new Map();
    let result = new Array();
    for (let i = 0; i < arry.length; i++) {
        if (map.has(arry[i])) {
            map.set(arry[i], true);
        } else {
            map.set(arry[i], false);
            result.push(arry[i]);
        }
    }
    return result;
}
```

## 13.数组排序

> 1. sort排序

```
var arr=[1,5,7,9,16,2,4];
arr.sort(function(a,b){
    return b-a;  //降序排列，return a-b; —>升序排列
})  
```

> 2. 希尔排序（性能最好的排序）

```
function xier(arr){
    var interval = parseInt(arr.length / 2);  //分组间隔设置
    while(interval > 0){
        for(var i = 0 ; i < arr.length ; i ++){
            var n = i;
            while(arr[n] < arr[n - interval] && n > 0){
                var temp = arr[n];
                arr[n] = arr[n - interval];
                arr[n - interval] = temp;
                n = n - interval;
            }
        }
        interval = parseInt(interval / 2);
    }
    return arr;
}
xier([12,9,38,44,7,98,35,59,49,88,38]);
```

> 3. 插入排序

```
var arr=[45,1,32,21,56,87,43,12,34,45];
    for(var i=0;i<arr.length;i++){
    var n=i;
    while(arr[n]>arr[n+1] && n>=0){
 	    var temp=arr[n];
	    arr[n]=arr[n+1];
 	    arr[n+1]=temp;
	    n--;
    }
}
```

> 4. 快速排序（一拆为二）

```
function quickSort(arr){
    if(arr.length <= 1){
        return arr;
    }
    var left = [];
    var right = [];
    var midIndex = parseInt(arr.length / 2);
    var mid = arr[midIndex];
    for(var i = 0 ; i < arr.length ; i++){
        if(i == midIndex) continue;
        if( arr[i] < mid){
            left.push(arr[i])
        }else{
            right.push(arr[i]);
        }
    }
    return quickSort(left).concat([mid],quickSort(right));
}
 
[1,2].concat([3],[4,5]);   //[1, 2, 3, 4, 5]
```
> 冒泡排序
```
//性能一般
var arr=[1,5,7,9,16,2,4];
//冒泡排序,每一趟找出最大的,总共比较次数为arr.length-1次,每次的比较次数为arr.length-1次，依次递减
var temp;
for(var i=0;i<arr.length-1;i++){
    for(var j=0;j<arr.length-1;j++){
        if(arr[j]>arr[j+1]){
            temp=arr[j];
            arr[j]=arr[j+1];
            arr[j+1]=temp;
        }
    }
}
```
## 14.使用模块化加载时，模块加载的顺序是怎样的?

CMD 推崇依赖就近，AMD 推崇依赖前置

AMD 提前执行，不管是否调用模块，先解析所有模块 requirejs 速度快 有可能浪费资源

CMD 延迟执行，在真正需要使用(依赖)模块时才解析该模块 seajs 按需解析

## 15.JavaScript 全局对象

### 顶层函数（全局函数）

decodeURI()	解码某个编码的 URI。

decodeURIComponent() 解码一个编码的 URI 组件。

encodeURI()	把字符串编码为 URI。

encodeURIComponent() 把字符串编码为 URI 组件。

escape() 对字符串进行编码。

eval()	计算 JavaScript 字符串，并把它作为脚本代码来执行。

getClass()	返回一个 JavaObject 的 JavaClass。

isFinite()	检查某个值是否为有穷大的数。

isNaN()	检查某个值是否是数字。

Number()	把对象的值转换为数字。

parseFloat()	解析一个字符串并返回一个浮点数。

parseInt()	解析一个字符串并返回一个整数。

String()	把对象的值转换为字符串。

unescape()	对由 escape() 编码的字符串进行解码。


### 顶层属性（全局属性）

Infinity	代表正的无穷大的数值。
java	代表 java.* 包层级的一个 JavaPackage。
NaN	指示某个值是不是数字值。
Packages	根 JavaPackage 对象。
undefined	指示未定义的值。

## 16.瀑布流实现

先通过计算出一排能够容纳几列元素，然后寻找各列之中所有元素高度之和的最小者，并将新的元素添加到该列上，然后继续寻找所有列的各元素之和的最小者，继续添加至该列上，如此循环下去，直至所有元素均能够按要求排列为止；

详情：https://blog.csdn.net/weixin_44135121/article/details/98629830

## 17.思考:return与break与continue的区别?

- return: 函数中(返回值并且结束函数  return后的代码不会执行)
- break: 循环语句(结束循环)   switch语句中(结束switch语句防止穿透)
- continue: 循环语句(结束本次循环,进入下一次循环)

## 18.一些 DOM 对象常用方法

getElementById()  返回带有指定 ID 的元素。

getElementsByTagName()	返回包含带有指定标签名称的所有元素的节点列表（集合/节点数组）。

getElementsByClassName()	返回包含带有指定类名的所有元素的节点列表。

appendChild()	把新的子节点添加到指定节点。

removeChild()	删除子节点。

replaceChild()	替换子节点。

insertBefore()	在指定的子节点前面插入新的子节点。

createAttribute()	创建属性节点。

createElement()	创建元素节点。

createTextNode()	创建文本节点。

getAttribute()	返回指定的属性值。

setAttribute()	把指定属性设置或修改为指定的值。


## 19.事件机制（事件捕获和事件冒泡 ）

重点：事件捕获，事件冒泡，执行顺序是先捕获，后冒泡。事件委托，DOM事件

事件捕获：通俗的理解就是，当鼠标点击或者触发dom事件时，浏览器会从根节点开始由外到内进行事件传播，即点击了子元素，如果父元素通过事件捕获方式注册了对应的事件的话，会先触发父元素绑定的事件。

事件冒泡：与事件捕获恰恰相反，事件冒泡顺序是由内到外进行事件传播，直到根节点。

dom标准事件流的触发的先后顺序为：先捕获再冒泡，即当触发dom事件时，会先进行事件捕获，捕获到事件源之后通过事件传播进行事件冒泡

事件绑定的方法：
`addEventListener(event, listener, useCapture)`
 三个参数:
 - 代表事件
 - 监听函数
 - 是否在捕获阶段进行事件捕捉，默认false，在事件冒泡阶段进行捕捉。

> 通过dom.onclick = function(){} 在事件冒泡阶段进行捕捉

`attachEvent(event,listener) `主要用于ie10以下的ie浏览器

> 阻止冒泡的方法是:event.stopPropagation();阻止默认事件:event.preventDefault()

## 20.浏览器渲染页面的步骤

**HTML渲染过程：**

- 解析HTML
- 构建DOM树
- DOM树与CSS样式进行附着构造呈现树(render)
- 布局
- 绘制

## 21.h5和css3新特性旧浏览器不支持?

IE8以下不支持h5的新标签，解决:引入html5shiv.js

不支持HTML5的新标签，如`<header>`、`<nav>`等标签在IE8无法渲染。html5shiv.js可帮助IE6-8浏览器兼容HTML5语义化标签

## 22.IE浏览器的条件注释针对IE5-9

```
<!--[if ！ie]>不是ie5-9版本的浏览器<！[endif]-->
<!--[if ie 5]>我是ie5才显示的内容<！[endif]--> 
<!--[if lt ie 9]>小于ie9版本的浏览器<！[endif]-->
<!--[if gt ie 7]>大于ie7版本的浏览器<！[endif]-->
<!--[if lte ie 6]>小于等于ie6版本的浏览器<！[endif]-->                       
<!--[if gte ie 6]>大于等于ie6版本的浏览器<！[endif]--> 
```

## 23.html标签语义化的理解?

1. HTML语义化让页面的内容结构化，结构更清晰，便于对浏览器、搜索引擎解析；
2. 即使在没有样式CSS 的情况下也能以一种文档格式显示，并且是容易阅读的；
3. 搜索引擎的爬虫也依赖于HTML标记来确定上下文和各个关键字的权重，有利于SEO；
4. 使阅读源代码的人更容易将网站分块，便于阅读、维护和理解；

## 24.meta标签的作用是什么?

meta是head区的一个辅助性标签。其主要作用有：搜索引擎优化（SEO），定义页面使用语言，自动刷新并指向新的页面，实现网页转换时的动态效果，控制页面缓冲，网页定级评价，控制网页显示的窗口等！

## 25.图片懒加载的原理是什么?

首先用一张空白图片的src取代所有需要懒加载的图片;当页面滚动时,遍历当前页面需要进行懒加载的图片,判断图片是否在可视区内,如果在可视区内就取存放在伪属性中的真实src替代当前的src;

判断是否在可是区 

`document.body.clientHeight + document.body.scrollTop >= element.offsetTop`

## 26.img 和 background-image 优劣比较

**一. 简单来说，img是内容部分的东西，css的background-image是修饰性的东西。**

- img：从页面元素来说，如果是页面中的图片是作为内容出现的，比如广告图片，比如产品图片，那么这个必然是用img了，因为这个是页面元素内容。页面元素内容最关键的一点就是，当页面没有样式的时候，还是能一眼看过去就知道什么是什么

- background-image：背景图片，修饰性的内容，在页面中可有可无。有，是为了让页面中视觉感受上更美；无，并不影响用户浏览网页获取内容。

**二. 区别**

1. background-image是背景图片,是css的一个样式；`<img/>`是一个块状的元素，是html的一个标签

2. `<img/>`是一个dom对象，可以使用js进行元素的操作。背景图片不占位置。

3. 一般来说，如果是装饰性的图片就使用background-img，如果和文体内容很相关就使用`<img/>`。

4. 在网页中的加载顺序不一样，html中的标签`<img/>`是网页结构（内容）的一部分会在加载结构的过程中加载，**以css背景图存在的图片background-image会等到dom结构加载完成（网页的内容标签全部显示以后）才开始加载**，  既网页会先加载标签`<img/>`的内容，再加载背景图片background-image。如果你用引入了一个很大的图片，那么在 这个图片下载完成之前，img后的内容都不会显示。而如果用css来引入同样的图片，网页结构和内容加载完成之后，才开始加载背景图片，不会影响你浏览网页内容。

5. 选择的时候也要兼顾seo和标签语义化，logo最好同时使用背景图和<img/>标签。

**三.js操作**

操作标签`<img/>`

1. 设置属性document.getElementsByTagName("img")[0].setAttribute("src","newSrc"); 获取属性document.getElementsByTagName("img")[0].getAttribute("src");

或者：设置document.getElementsByTagName("img")[0].src="newSrc"; 获取document.getElementsByTagName("img")[0].src;

获取与设置宽高:document.getElementsByTagName("img")[0].style.width;document.getElementsByTagName  ("img")[0].style.width = "320px";

2. 图片居中：一般来说可以用CSS中的①“text-align:center属性，②margin:0 auto或③定位属性”(position)就可以居中。 但是主要看是看你的页面布局是怎样的，是否用了定位。根据这些来判断到顶使用怎样的方法来使元素居中。 设置background属性，详细参考

　　1、background-image:url("");//设置图片的背景图片

　　2、background-color:#***||colorName||rgba（，，，）;//设置背景色

　　3、background-position:center;//设置背景图片在父元素中的显示位置，如果您仅规定了一个关键词，那么第二个值将是"center"。接受三种值，百分比||描述值||像素值;

　　4、background-repeat:repeat||repeat-x||repeat-y||no-repeat||inherit;

　　5、background-origin: padding-box|border-box|content-box;//规定 background-position 属性相对于什么位置来定位。

　　6、background-clip: border-box|padding-box|content-box;//background-clip 属性规定背景的绘制区域。剪裁的依据。

　　 7、background-attachment:scroll||fixed||inherit;//属性设置背景图像是否固定或者随着页面的其余部分滚动。

　　 8、background-size:cover;//设置了图片背景no-repeat之后，使用这个属性可以让图片平铺。详细讲解background-size

## 27.强缓存与协商缓存

### 强缓存

到底什么是强缓存？强在哪？其实强是强制的意思。当浏览器去请求某个文件的时候，服务端就在respone header里面对该文件做了缓存配置。缓存的时间、缓存类型都由服务端控制，具体表现为：
respone header 的cache-control，常见的设置是max-age public private no-cache no-store等

强缓存总结

cache-control: max-age=xxxx，public

客户端和代理服务器都可以缓存该资源；
客户端在xxx秒的有效期内，如果有请求该资源的需求的话就直接读取缓存,statu code:200 ，如果用户做了刷新操作，就向服务器发起http请求

cache-control: max-age=xxxx，private
只让客户端可以缓存该资源；代理服务器不缓存
客户端在xxx秒内直接读取缓存,statu code:200

cache-control: max-age=xxxx，immutable
客户端在xxx秒的有效期内，如果有请求该资源的需求的话就直接读取缓存,statu code:200 ，即使用户做了刷新操作，也不向服务器发起http请求

cache-control: no-cache
跳过设置强缓存，但是不妨碍设置协商缓存；一般如果你做了强缓存，只有在强缓存失效了才走协商缓存的，设置了no-cache就不会走强缓存了，每次请求都回询问服务端。

cache-control: no-store
不缓存，这个会让客户端、服务器都不缓存，也就没有所谓的强缓存、协商缓存了。

### 协商缓存

上面说到的强缓存就是给资源设置个过期时间，客户端每次请求资源时都会看是否过期；只有在过期才会去询问服务器。所以，强缓存就是为了给客户端自给自足用的。而当某天，客户端请求该资源时发现其过期了，这是就会去请求服务器了，而这时候去请求服务器的这过程就可以设置协商缓存。这时候，协商缓存就是需要客户端和服务器两端进行交互的。

发请求-->看资源是否过期-->过期-->请求服务器-->服务器对比资源是否真的过期-->没过期-->返回304状态码-->客户端用缓存的老资源。

这就是一条完整的协商缓存的过程。

当然，当服务端发现资源真的过期的时候，会走如下流程：

发请求-->看资源是否过期-->过期-->请求服务器-->服务器对比资源是否真的过期-->过期-->返回200状态码-->客户端如第一次接收该资源一样，记下它的cache-control中的max-age、etag、last-modified等。

用法：
index.html文件采用协商缓存，理由就是要用户每次请求index.html不拿浏览器缓存，直接请求服务器，这样就保证资源更新了，用户能马上访问到新资源，如果服务端返回304，这时候再拿浏览器的缓存的index.html，切记不要设置强缓存！！！



