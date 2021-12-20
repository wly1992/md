
## 优化/提升性能

### 提高网站性能的方式有哪些?

1. 压缩源码和图片

2. 选择合适的图片格式(颜色数多用jpg，颜色少用png，矢量图：字体图标，如 font-awesome；svg 等) 

3. 合并静态资源(减少HTTP请求:图片懒加载,sprite雪碧图,c3特效代替图片) 

4. 把多个css合并为一个css，把图片组合成雪碧图

5. 使用CDN(对公开库，能和其他网站共享缓存)

6. 把css放页面头部，js放底部(这样不会阻塞页面渲染，让页面出现长时间的空白) 

（1）减少http请求次数：CSS Sprites, JS、CSS源码压缩、图片大小控制合适；网页Gzip，CDN托管，data缓存，图片服务器。
（2） 前端模板 JS+数据，减少由于HTML标签导致的带宽浪费，前端用变量保存AJAX请求结果，每次操作本地变量，不用请求，减少请求次数

（3） 用innerHTML代替DOM操作，减少DOM操作次数，优化javascript性能。

（4） 当需要设置的样式很多时设置className而不是直接操作style。

（5） 少用全局变量、缓存DOM节点查找的结果。减少IO读取操作。

（6） 避免使用CSS Expression（css表达式)又称Dynamicproperties(动态属性)。

（7） 图片预加载，将样式表放在顶部，将脚本放在底部  加上时间戳。

（8） 避免在页面的主体布局中使用table，table要等其中的内容完全下载之后才会显示出来，显示比div+css布局慢。


## preload

 link元素的 rel 属性的属性值preload能够让你在你的HTML页面中 head 元素内部书写一些声明式的资源获取请求，可以指明哪些资源是在页面加载完成后即刻需要的。对于这种即刻需要的资源，你可能希望在页面加载的生命周期的早期阶段就开始获取，在浏览器的主渲染机制介入前就进行预加载。这一机制使得资源可以更早的得到加载并可用，且更不易阻塞页面的初步渲染，进而提升性能。

```html
 <link rel="stylesheet" href="styles/main.css">
 <link rel="preload" href="sintel-short.mp4" as="video" type="video/mp4">
 <link rel="preload" href="fonts/cicle_fina-webfont.eot" as="font" type="application/vnd.ms-fontobject" crossorigin="anonymous">
 <link rel="preload" href="bg-image-narrow.png" as="image" media="(max-width: 600px)">
```

脚本化预加载
```javascript
var preloadLink = document.createElement("link");
preloadLink.href = "myscript.js";
preloadLink.rel = "preload";
preloadLink.as = "script";
document.head.appendChild(preloadLink);
```
浏览器兼容性:

Chrome|Firefox |Internet Explorer|Opera|Safari
:--:|:--:|:--:|:--:|:--:
50.0|	56 (56)|未实现|47|11

## HTML DOM classList 属性

- add

在元素中添加一个或多个类名，如果指定的类名已存在，则不会添加

```javascript
document.getElementById("myDIV").classList.add("mystyle");
```

- contains

返回布尔值，判断指定的类名是否存在。

```javascript
document.getElementById("myDIV").classList.contains("mystyle");
```

- item(index)

返回元素中索引值对应的类名。索引值从 0 开始。如果索引值在区间范围外则返回 null

```javascript
document.getElementById("myDIV").classList.item(0);
```

- remove

移除元素中一个或多个类名。

> 注意： 移除不存在的类名，不会报错。

```javascript
document.getElementById("myDIV").classList.remove("mystyle");
```

- toggle

在元素中切换类名。

```javascript
document.getElementById("myDIV").classList.toggle("mystyle");
```

## 判断数据类型
```javascript
Object.prototype.toString.call()
```

## 运算符instance 与 typeof

首先 typeof 能够正确的判断基本数据类型，但是除了 null, typeof null输出的是对象。
但是对象来说，typeof 不能正确的判断其类型， typeof 一个函数可以输出 'function',而除此之外，输出的全是 object,这种情况下，我们无法准确的知道对象的类型。

instanceof可以准确的判断复杂数据类型，但是不能正确判断基本数据类型。

instanceof是通过原型链判断的，A instanceof B, 在A的原型链中层层查找，是否有原型等于B.prototype，如果一直找到A的原型链的顶端(null;即`Object.__proto__.__proto__`),仍然不等于B.prototype，那么返回false，否则返回true.

instanceof的实现代码:

```javascript
// L instanceof R
function instance_of(L, R) {//L 表示左表达式，R 表示右表达式
    var O = R.prototype;// 取 R 的显式原型
    L = L.__proto__;    // 取 L 的隐式原型
    while (true) { 
        if (L === null) //已经找到顶层
            return false;  
        if (O === L)   //当 O 严格等于 L 时，返回 true
            return true; 
        L = L.__proto__;  //继续向上一层原型链查找
    } 
}
```
## 取数组的最大值（ES5、ES6）

```javascript
// ES5 的写法
Math.max.apply(null, [14, 3, 77, 30]);

// ES6 的写法
Math.max(...[14, 3, 77, 30]);

// reduce
[14,3,77,30].reduce((accumulator, currentValue)=>{
    return accumulator = accumulator > currentValue ? accumulator : currentValue
});
```