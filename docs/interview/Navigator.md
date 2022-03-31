# 浏览器与新技术

## 浏览器对象模型 (BOM)

### Window 对象

所有 JavaScript 全局对象、函数以及变量均自动成为 window 对象的成员。
全局变量是 window 对象的属性。全局函数是 window 对象的方法。
甚至 HTML DOM 的 document 也是 window 对象的属性之一：

```js
window.document.getElementById("header");
```

### Window 尺寸:

1. window.innerHeight  
2. document.documentElement.clientHeight
3. document.body.clientHeight

### 其他 Window 方法

- window.open() - 打开新窗口
- window.close() - 关闭当前窗口
- window.moveTo() - 移动当前窗口
- window.resizeTo() - 调整当前窗口的尺寸

### Window属性

1. Window Screen

- screen.availWidth - 可用的屏幕宽度

2. Window Location

- location.hostname 返回 web 主机的域名
- location.pathname 返回当前页面的路径和文件名
- location.port 返回 web 主机的端口 （80 或 443）
- location.protocol 返回所使用的 web 协议（http: 或 https:）
- location.href 属性返回当前页面的 URL。

- window.location.assign(url) ： 加载 URL 指定的新的 HTML 文档。 就相当于一个链接，跳转到指定的url，当前页面会转为新页面内容，可以点击后退返回上一个页面。

- window.location.replace(url) ： 通过加载 URL 指定的文档来替换当前文档 ，这个方法是替换当前窗口页面，前后两个页面共用一个窗口，所以是没有后退返回上一页的

3. Window History

- history.back() - 与在浏览器点击后退按钮相同
- history.forward() - 与在浏览器中点击向前按钮相同
- history.go() - 这个方法来实现向前，后退的功能

4. Window Navigator

```html
<script>
txt = "<p>浏览器代号: " + navigator.appCodeName + "</p>";
txt+= "<p>浏览器名称: " + navigator.appName + "</p>";
txt+= "<p>浏览器版本: " + navigator.appVersion + "</p>";
txt+= "<p>启用Cookies: " + navigator.cookieEnabled + "</p>";
txt+= "<p>硬件平台: " + navigator.platform + "</p>";
txt+= "<p>用户代理: " + navigator.userAgent + "</p>";
txt+= "<p>用户代理语言: " + navigator.language + "</p>";
document.getElementById("example").innerHTML=txt;
</script>
```

5. JavaScript 弹窗

- window.confirm() 
- window.prompt() 
- window.alert()

6. JavaScript 计时事件

- setInterval() / clearInterval
- setTimeout() / clearTimeout

7. JavaScript Cookie

- document.cookie


## 全局变量和window属性上定义的变量

- 知识点1

全局变量和window属性上定义的变量的区别：
全局变量不能通过delete删除，而window属性上定义的变量可以通过delete删除

```js
var num=123;
window.str="string";
delete num;
delete str;
console.log(num); //123

console.log(str); //str is not defined
//全局变量不能通过 delete 删除，因为通过 var 定义全局变量会有一个名为 [Configurable] 的属性，默认值为 false，所以这样定义的属性不可以通过 delete 操作符删除
```

- 知识点2

尝试访问未声明的变量会报错，..is not defined
但是通过查询window查询，可以知道某个可能未声明的变量是否存在，不会报错，只会显示undefined

```js
var newValue=oldValue; // 报错：oldValue is not defined
var newValue=window.oldValue; // 不会报错
console.log(newValue); // undefined
```


## 常⻅的浏览器内核有哪些?

浏览器/RunTime|内核（渲染引擎）|JavaScript 引擎
:-----------:|:------------:|:---------------:
Chrome|Blink（28~）Webkit（Chrome 27）|V8
FireFox|Gecko|SpiderMonkey
Safari|Webkit|JavaScriptCore
Edge|EdgeHTML|Chakra(for JavaScript)
IE|Trident|Chakra(for JScript)
PhantomJS|Webkit|JavaScriptCore
Node.js|-|JavaScriptCore

## 浏览器的主要组成部分是什么？

1. ⽤户界⾯ - 包括地址栏、前进/后退按钮、书签菜单等。除了浏览器主窗⼝显示的您请求的⻚⾯外，其他显示的各个 部分都属于⽤户界⾯。 
2. 浏览器引擎 - 在⽤户界⾯和呈现引擎之间传送指令。 
3. 呈现引擎 - 负责显示请求的内容。如果请求的内容是 HTML，它就负责解析 HTML 和 CSS 内容，并将解析后的内 容显示在屏幕上。 
4. ⽹络 - ⽤于⽹络调⽤，⽐如 HTTP 请求。其接⼝与平台⽆关，并为所有平台提供底层实现。 
5. ⽤户界⾯后端 - ⽤于绘制基本的窗⼝⼩部件，⽐如组合框和窗⼝。其公开了与平台⽆关的通⽤接⼝，⽽在底层使⽤ 操作系统的⽤户界⾯⽅法。 
6. JavaScript 解释器。⽤于解析和执⾏ JavaScript 代码。 
7. 数据存储。这是持久层。浏览器需要在硬盘上保存各种数据，例如 Cookie。新的 HTML 规范 (HTML5) 定义了“⽹ 络数据库”，这是⼀个完整（但是轻便）的浏览器内数据库。

值得注意的是，和⼤多数浏览器不同，Chrome 浏览器的每个标签⻚都分别对应⼀个呈现引擎实例。每个标签⻚都是⼀ 个独⽴的进程。

## 浏览器是如何渲染UI的？

1. 浏览器获取HTML⽂件，然后对⽂件进⾏解析，形成DOM Tree 
2. 与此同时，进⾏CSS解析，⽣成Style Rules 
3. 接着将DOM Tree与Style Rules合成为 Render Tree 
4. 接着进⼊布局（Layout）阶段，也就是为每个节点分配⼀个应出现在屏幕上的确切坐标 
5. 随后调⽤GPU进⾏绘制（Paint），遍历Render Tree的节点，并将元素呈现出来

## 浏览器如何解析css选择器？

浏览器会『从右往左』解析CSS选择器。 

我们知道DOM Tree与Style Rules合成为 Render Tree，实际上是需要将Style Rules附着到DOM Tree上，因此需要根 据选择器提供的信息对DOM Tree进⾏遍历，才能将样式附着到对应的DOM元素上。

以下这段css为例

```css
.mod-nav h3 span {font-size: 16px;}
```

若从左向右的匹配，过程是：

1. 从 .mod-nav 开始，遍历⼦节点 header 和⼦节点 div 
2. 然后各⾃向⼦节点遍历。在右侧 div 的分⽀中 
3. 最后遍历到叶⼦节点 a ，发现不符合规则，需要回溯到 ul 节点，再遍历下⼀个 li-a，⼀颗DOM树的节点动不动上 千，这种效率很低。

如果从右⾄左的匹配：

1. 先找到所有的最右节点 span，对于每⼀个 span，向上寻找节点 h3 
2. 由 h3再向上寻找 class=mod-nav 的节点 
3. 最后找到根元素 html 则结束这个分⽀的遍历。

后者匹配性能更好，是因为从右向左的匹配在第⼀步就筛选掉了⼤量的不符合条件的最右节点（叶⼦节点）；⽽从左向 右的匹配规则的性能都浪费在了失败的查找上⾯。

## DOM Tree是如何构建的？

1. 转码: 浏览器将接收到的⼆进制数据按照指定编码格式转化为HTML字符串 
2. ⽣成Tokens: 之后开始parser，浏览器会将HTML字符串解析成Tokens 
3. 构建Nodes: 对Node添加特定的属性，通过指针确定 Node 的⽗、⼦、兄弟关系和所属 treeScope 
4. ⽣成DOM Tree: 通过node包含的指针确定的关系构建出DOM Tree

## 浏览器重绘与重排的区别？

- 重排: 部分渲染树（或者整个渲染树）需要重新分析并且节点尺⼨需要重新计算，表现为重新⽣成布局，重新排列 元素

- 重绘: 由于节点的⼏何属性发⽣改变或者由于样式发⽣改变，例如改变元素背景⾊时，屏幕上的部分内容需要更 新，表现为某些元素的外观被改变

单单改变元素的外观，肯定不会引起⽹⻚重新⽣成布局，但当浏览器完成重排之后，将会重新绘制受到此次重排影响的 部分

重排和重绘代价是⾼昂的，它们会破坏⽤户体验，并且让UI展示⾮常迟缓，⽽相⽐之下重排的性能影响更⼤，在两者⽆ 法避免的情况下，⼀般我们宁可选择代价更⼩的重绘。

『重绘』不⼀定会出现『重排』，『重排』必然会出现『重绘』。

## 如何触发重排和重绘？

任何改变⽤来构建渲染树的信息都会导致⼀次重排或重绘：

- 添加、删除、更新DOM节点 
- 通过display: none隐藏⼀个DOM节点-触发重排和重绘 
- 通过visibility: hidden隐藏⼀个DOM节点-只触发重绘，因为没有⼏何变化 
- 移动或者给⻚⾯中的DOM节点添加动画 
- 添加⼀个样式表，调整样式属性 
- ⽤户⾏为，例如调整窗⼝⼤⼩，改变字号，或者滚动。

## 如何避免重绘或者重排？

### 1. 集中改变样式

我们往往通过改变class的⽅式来集中改变样式

```js
// 判断是否是⿊⾊系样式 
const theme = isDark ? 'dark' : 'light'

// 根据判断来设置不同的class 
ele.setAttribute('className', theme)
```

### 2. 使⽤DocumentFragment

我们可以通过createDocumentFragment创建⼀个游离于DOM树之外的节点，然后在此节点上批量操作，最后插⼊ DOM树中，因此只触发⼀次重排

```js
var fragment = document.createDocumentFragment(); 
for (let i = 0;i<10;i++){ 
  let node = document.createElement("p"); 
  node.innerHTML = i; 
  fragment.appendChild(node); 
}

document.body.appendChild(fragment);
```

### 3. 提升为合成层

将元素提升为合成层有以下优点：

- 合成层的位图，会交由 GPU 合成，⽐ CPU 处理要快 
- 当需要 repaint 时，只需要 repaint 本身，不会影响到其他的层 
- 对于 transform 和 opacity 效果，不会触发 layout 和 paint

```css
#target { 
  will-change: transform; 
}
```

## 一些 DOM 对象常用方法

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

## 前端如何实现即时通讯？

### 1. 短轮询

短轮询的原理很简单，每隔⼀段时间客户端就发出⼀个请求，去获取服务器最新的数据，⼀定程度上模拟实现了即时通讯。

- 优点：兼容性强，实现⾮常简单 
- 缺点：延迟性⾼，⾮常消耗请求资源，影响性能

### 2. comet

comet有两种主要实现⼿段，⼀种是基于 AJAX 的⻓轮询（long-polling）⽅式，另⼀种是基于 Iframe 及 htmlfile 的流 （streaming）⽅式，通常被叫做⻓连接。

> 具体两种⼿段的操作⽅法请移步[Comet技术详解：基于HTTP⻓连接的Web端实时通信技术](http://www.52im.net/thread-334-1-1.html)

⻓轮询优缺点：

- 优点：兼容性好，资源浪费较⼩ 
- 缺点：服务器hold连接会消耗资源，返回数据顺序⽆保证，难于管理维护

⻓连接优缺点：

- 优点：兼容性好，消息即时到达，不发⽆⽤请求 
- 缺点：服务器维护⻓连接消耗资源

### 3. SSE

> 使⽤指南请看[Server-Sent Events 教程](https://www.ruanyifeng.com/blog/2017/05/server-sent_events.html)

SSE（Server-Sent Event，服务端推送事件）是⼀种允许服务端向客户端推送新数据的HTML5技术。

- 优点：基于HTTP⽽⽣，因此不需要太多改造就能使⽤，使⽤⽅便，⽽websocket⾮常复杂，必须借助成熟的库或框架
- 缺点：基于⽂本传输效率没有websocket⾼，不是严格的双向通信，客户端向服务端发送请求⽆法复⽤之前的连接，需要重新发出独⽴的请求

### 4. Websocket

> 使⽤指南请看[WebSocket 教程](http://www.ruanyifeng.com/blog/2017/05/websocket.html)

Websocket是⼀个全新的、独⽴的协议，基于TCP协议，与http协议兼容、却不会融⼊http协议，仅仅作为html5的⼀部 分，其作⽤就是在服务器和客户端之间建⽴实时的双向通信。

- 优点：真正意义上的实时双向通信，性能好，低延迟
- 缺点：独⽴于http的协议，因此需要额外的项⽬改造，使⽤复杂度⾼，必须引⼊成熟的库，⽆法兼容低版本浏览器

### 5. Web Worker

Web Worker 的作⽤，就是为 JavaScript 创造多线程环境，允许主线程创建 Worker 线程，将⼀些任务分配给后者运⾏

> [Web Worker教程](http://www.ruanyifeng.com/blog/2018/07/web-worker.html)

### 6. Service workers

Service workers 本质上充当Web应⽤程序与浏览器之间的代理服务器，也可以在⽹络可⽤时作为浏览器和⽹络间的代 理，创建有效的离线体验。

> [Service workers教程](https://developer.mozilla.org/zh-CN/docs/Web/API/Service_Worker_API)


## 什么是浏览器同源策略？

同源策略限制了从同⼀个源加载的⽂档或脚本如何与来⾃另⼀个源的资源进⾏交互。这是⼀个⽤于隔离潜在恶意⽂件的 重要安全机制。

同源是指"协议+域名+端⼝"三者相同，即便两个不同的域名指向同⼀个ip地址，也⾮同源。

浏览器中的⼤部分内容都是受同源策略限制的，但是以下三个标签可以不受限制：

- `<img src=XXX>` 
- `<link href=XXX>` 
- `<script src=XXX>`

## 如何实现跨域？

现在主要介绍三种⽐较主流的跨域⽅案，其余的⽅案 我们就不深⼊讨论了

### 1. 最经典的跨域⽅案jsonp

jsonp本质上是⼀个Hack，它利⽤ <script> 标签不受同源策略限制的特性进⾏跨域操作。

jsonp优点：实现简单 兼容性⾮常好

jsonp的缺点：

- 只⽀持get请求（因为 `<script>` 标签只能get）
- 有安全性问题，容易遭受xss攻击
- 需要服务端配合jsonp进⾏⼀定程度的改造

jsonp的实现： 

```js
function JSONP({url, params,callbackKey,callback}){
  //在参数里制定callback的名字
  params = params || {};
  params[callbackKey] = 'jsonCallback';
  //预留callback
  window.jsonCallback = callback
  // 拼接参数字符串
  const paramKey = Object.keys(params);
  const paramString = paramKey.map(key=>{
    return `${key}=${params[key]}`
  }).join('&')
  // 插⼊ DOM 元素
  const script = document.createElement('script') 
  script.setAttribute('src', `${url}?${paramString}`) 
  document.body.appendChild(script)
}

JSONP({ 
  url: 'http://s.weibo.com/ajax/jsonp/suggestion', 
  params: {
    key: 'test',
  },
  callbackKey: '_cb',
  callback(result) { 
    console.log(result.data) 
  }
})
```

### 2. 最流⾏的跨域⽅案cors

cors是⽬前主流的跨域解决⽅案，跨域资源共享(CORS) 是⼀种机制，它使⽤额外的 HTTP 头来告诉浏览器 让运⾏在⼀ 个 origin (domain) 上的Web应⽤被准许访问来⾃不同源服务器上的指定的资源。当⼀个资源从与该资源本身所在的服 务器不同的域、协议或端⼝请求⼀个资源时，资源会发起⼀个跨域 HTTP 请求。 

如果你⽤express，可以这样在后端设置

```js
var allowCrossDomain = function(req, res, next) { 
  res.header('Access-Control-Allow-Origin', 'http://example.com'); 
  res.header('Access-Control-Allow-Methods', 'GET,PUT,POST,DELETE'); 
  res.header('Access-Control-Allow-Headers', 'Content-Type'); next(); 
}
//... 
app.configure(function() { 
  app.use(express.bodyParser()); 
  app.use(express.cookieParser()); 
  app.use(express.session({ secret: 'cool beans' })); 
  app.use(express.methodOverride());
   app.use(allowCrossDomain); app.use(app.router); 
   app.use(express.static(__dirname + '/public')); 
});
```

### 最⽅便的跨域⽅案Nginx

nginx是⼀款极其强⼤的web服务器，其优点就是轻量级、启动快、⾼并发。

现在的新项⽬中nginx⼏乎是⾸选，我们⽤node或者java开发的服务通常都需要经过nginx的反向代理。

反向代理的原理很简单，即所有客户端的请求都必须先经过nginx的处理，nginx作为代理服务器再讲请求转发给node或 者java服务，这样就规避了同源策略。

### 其它跨域⽅案

1.  HTML5 XMLHttpRequest 有⼀个API，postMessage()⽅法允许来⾃不同源的脚本采⽤异步⽅式进⾏有限的通信， 可以实现跨⽂本档、多窗⼝、跨域消息传递。
2. WebSocket 是⼀种双向通信协议，在建⽴连接之后，WebSocket 的 server 与 client 都能主动向对⽅发送或接收数 据，连接建⽴好了之后 client 与 server 之间的双向通信就与 HTTP ⽆关了，因此可以跨域。 
3. window.name + iframe：window.name属性值在不同的⻚⾯（甚⾄不同域名）加载后依旧存在，并且可以⽀持⾮常 ⻓的 name 值，我们可以利⽤这个特点进⾏跨域。 
4. location.hash + iframe：a.html欲与c.html跨域相互通信，通过中间⻚b.html来实现。 三个⻚⾯，不同域之间利⽤ iframe的location.hash传值，相同域之间直接js访问来通信。 
5. document.domain + iframe： 该⽅式只能⽤于⼆级域名相同的情况下，⽐如 a.test.com 和 b.test.com 适⽤于该⽅ 式，我们只需要给⻚⾯添加 document.domain ='test.com' 表示⼆级域名都相同就可以实现跨域，两个⻚⾯都通过js 强制设置document.domain为基础主域，就实现了同域。

## h5和css3新特性旧浏览器不支持?

IE8以下不支持h5的新标签，解决:引入html5shiv.js

不支持HTML5的新标签，如`<header>`、`<nav>`等标签在IE8无法渲染。html5shiv.js可帮助IE6-8浏览器兼容HTML5语义化标签

## IE浏览器的条件注释针对IE5-9

```
<!--[if ！ie]>不是ie5-9版本的浏览器<！[endif]-->
<!--[if ie 5]>我是ie5才显示的内容<！[endif]--> 
<!--[if lt ie 9]>小于ie9版本的浏览器<！[endif]-->
<!--[if gt ie 7]>大于ie7版本的浏览器<！[endif]-->
<!--[if lte ie 6]>小于等于ie6版本的浏览器<！[endif]-->                       
<!--[if gte ie 6]>大于等于ie6版本的浏览器<！[endif]--> 
```

## meta标签的作用是什么?

meta是head区的一个辅助性标签。其主要作用有：搜索引擎优化（SEO），定义页面使用语言，自动刷新并指向新的页面，实现网页转换时的动态效果，控制页面缓冲，网页定级评价，控制网页显示的窗口等！

## html标签语义化的理解?

1. HTML语义化让页面的内容结构化，结构更清晰，便于对浏览器、搜索引擎解析；
2. 即使在没有样式CSS 的情况下也能以一种文档格式显示，并且是容易阅读的；
3. 搜索引擎的爬虫也依赖于HTML标记来确定上下文和各个关键字的权重，有利于SEO；
4. 使阅读源代码的人更容易将网站分块，便于阅读、维护和理解；

事件机制（事件捕获和事件冒泡 ）

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


