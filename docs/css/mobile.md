# 移动端

## 常用的移动端适配方案是什么

Rem适配(重点)
rem是相对单位,,以html的根节点的fontsize为基准,通过监测浏览器的宽度.动态改变fontsize的大小,达到适配不同宽度的效果.
onresize事件当窗口或者框架被调整大小时发生的事件;

```
<script>
    var fontSize = 50;
    var _w = document.body.clientWidth;//窗口的大小
    document.getElementsByTagName("html")[0].style.fontSize = parseInt(_w*50/375*10)/10+"px";
    window.onresize = function(){
        document.getElementsByTagName("html")[0].style.fontSize=parseInt(_w*50/375*10)/10+"px";
    }
</script>
```


## Vue 移动端适配

通过`px2rem-loader`和`amfe-flexible`两个依赖

1. 下载
```
npm install  px2rem-loader --save
 
npm install amfe-flexible --save
```
2. 在main.js引入

`import "amfe-flexible"`

3. 将px2rem-loader添加到cssLoaders

在postcss.config.js文件中添加

```
module.exports = {
  plugins: {
    // "autoprefixer": { browsers: ["> 1%", "ie > 8", 'last 5 version'] },
    "postcss-px2rem":{
        remUnit:'75'
    }
  },
};
```

4. 设置根元素设置的font-size

```
html {
    font-size: 10vw;
    //font-size: 62.5%;;
}
```

## 动态设置html元素的font-size（页面使用rem做单位）

1. 设置页面的viewport视口

```
<meta name="viewport" content="width=device-width,user-scalable=no,initial-scale=1.0,minimum-scale=1.0,maximum-scale=1.0" />
```

2. 我们以iPhone6设计稿来进行移动端的开发（设计稿是750px）

在JavaScript中，通过window.devicePixelRatio来获取设备的像素比(device pixel ratio)简称DPR

那么我们得到的deviceWidth = 375px; 为了方便在开发的时候量尺寸好算，我们设置html的font-size为50px。
那么本来根据设计稿量出来的div是80px的话我们要先除以2，得到实际的宽度px，然后要转换为rem，我们还要除以html的font-size，即再除以50，即可得到单位rem的数值。
也就是说：拿到一个iPhone6设计图，量到的px像素值来除以100，那么得到的值就是多少rem。

```
deviceWidth = 320，font-size = 320 / 7.5 = 42.6667px
deviceWidth = 375，font-size = 375 / 7.5 = 50px
deviceWidth = 414，font-size = 414 / 7.5 = 55.2px
deviceWidth = 500，font-size = 500 / 7.5 = 66.6667px
```

动态设置HTML元素的font-size代码如下：
```
var html = document.documentElement; //获取到html元素
var hWidth = html.getBoundingClientRect().width;//获取到html的宽度
if(hWidth > 640) hWidth = 640; // 当hWidth大于640时，则物理分辨率大于1280（这就看设备的devicePixelRatio这个值了），应该去访问pc网站了
html.style.fontSize = hWidth/7.5 + "px"; //设置HTML的字体大小 font-size = 50px，1rem = 50px
```

如果页面的字体不想使用rem作为单位，那么就使用媒体查询来设置body的font-size。

```
@media screen and (min-width: 320px) {
    body {font-size: 14px;}
}
 
@media screen and (min-width: 360px) {
    body {font-size: 16px;}
}
 
@media screen and (min-width: 400px) {
    body {font-size: 18px;}
}
 
@media screen and (min-width: 440px) {
    body {font-size: 20px;}
}
 
@media screen and (min-width: 480px) {
    body {font-size: 22px;}
}
 
@media screen and (min-width: 640px) and (max-width: 640px) {
    body {font-size: 28px;}
}
```

4. 学习得来的各屏幕的rem适配（终极方案）------- sass语法

```
$baseDevice: 750; // 默认iphone6设计稿
$device: $baseDevice / 2; // 375 ，iphone6设备宽度
$baseFontSize: 100px;
@function px2rem($px, $base-font-size: $baseDevice / $device * $baseFontSize) {
  // unitless() 函数只是用来判断一个值是否带有单位，如果不带单位返回的值为 true，带单位返回的值为 false
  // unit() 函数主要是用来获取一个值所使用的单位，碰到复杂的计算时，其能根据运算得到一个“多单位组合”的值，不过只充许乘、除运算
  @if (unitless($px)) { 
    @warn 'Assuming #{$px} to be in pixels';
    @return px2rem($px + 0px)
  }
  @else if (unit($px) == rem) { 
    @return $px
  }
  @return ($px / $base-font-size) * 1rem
}

html {
  font-size: $baseFontSize;
}
@media screen and (min-width: 320px) {
  html {
    font-size: (320 /  $device) * $baseFontSize;
  }
}
@media screen and (min-width: 360px) {
  html {
    font-size: (360 /  $device) * $baseFontSize; // 96px
  }
}
@media screen and (min-width: 375px) {
  html {
    font-size: (375 /  $device) * $baseFontSize; // 100px
  }
}
@media screen and (min-width: 480px) {
  html {
    font-size: (480 /  $device) * $baseFontSize; // 128px
  }
}
@media screen and (min-width: 640px) {
  html {
    font-size: (640 /  $device) * $baseFontSize;
  }
}
@media screen and (min-width: 750px) {
  html {
    font-size: (750 /  $device) * $baseFontSize;
  }
}
// 示例使用
div {
  font-size: px2rem(28px); // 750px的设计稿量出来是多少px就直接扔进去自动换算为rem
}
```

5. 淘宝的H5移动端适配方案flexible.js

https://github.com/amfe/article/issues/17#

flexible的实质

flexible实际上就是能过JS来动态改写meta标签，代码类似这样：

```
var metaEl = doc.createElement('meta');
var scale = isRetina ? 0.5:1;
metaEl.setAttribute('name', 'viewport');
metaEl.setAttribute('content', 'initial-scale=' + scale + ', maximum-scale=' + scale + ', minimum-scale=' + scale + ', user-scalable=no');
if (docEl.firstElementChild) {
    document.documentElement.firstElementChild.appendChild(metaEl);
} else {
    var wrap = doc.createElement('div');
    wrap.appendChild(metaEl);
    documen.write(wrap.innerHTML);
}
```
事实上他做了这几样事情：

- 动态改写`<meta>`标签
- 给`<html>`元素添加data-dpr属性，并且动态改写data-dpr的值
- 给`<html>`元素添加font-size属性，并且动态改写font-size的值