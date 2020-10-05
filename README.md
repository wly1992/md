# 随手记

# import

import * as xxx from 'xxx' 会将若干export导出的内容组合成一个对象返回

例如：

```
export const getStatus = () => {}

export const getStatus1 = () => {}

export const getStatus2 = () => {}
```

import xxx from 'xxx' 只会导出这个默认的对象作为一个对象

例如：

```
export default const getStatus = () => {}
```

import { getStatus } form 'xxx' 按需引入

例如：

```
export const getStatus = () => {}
export const getStatus1 = () => {}
```

# Set

```
let array = Array.from(new Set([1, 1, 1, 2, 3, 2, 4]));
```

# preload

 link元素的 rel 属性的属性值preload能够让你在你的HTML页面中 head 元素内部书写一些声明式的资源获取请求，可以指明哪些资源是在页面加载完成后即刻需要的。对于这种即刻需要的资源，你可能希望在页面加载的生命周期的早期阶段就开始获取，在浏览器的主渲染机制介入前就进行预加载。这一机制使得资源可以更早的得到加载并可用，且更不易阻塞页面的初步渲染，进而提升性能。

```
 <link rel="stylesheet" href="styles/main.css">
 <link rel="preload" href="sintel-short.mp4" as="video" type="video/mp4">
 <link rel="preload" href="fonts/cicle_fina-webfont.eot" as="font" type="application/vnd.ms-fontobject" crossorigin="anonymous">
 <link rel="preload" href="bg-image-narrow.png" as="image" media="(max-width: 600px)">
```

脚本化预加载
```
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

# HTML DOM classList 属性

- add

在元素中添加一个或多个类名，如果指定的类名已存在，则不会添加

```
document.getElementById("myDIV").classList.add("mystyle");
```

- contains

返回布尔值，判断指定的类名是否存在。

```
document.getElementById("myDIV").classList.contains("mystyle");
```

- item(index)

返回元素中索引值对应的类名。索引值从 0 开始。如果索引值在区间范围外则返回 null

```
document.getElementById("myDIV").classList.item(0);
```

- remove

移除元素中一个或多个类名。

> 注意： 移除不存在的类名，不会报错。

```
document.getElementById("myDIV").classList.remove("mystyle");
```

- toggle

在元素中切换类名。

```
document.getElementById("myDIV").classList.toggle("mystyle");
```