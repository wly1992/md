# HTML面试题

## doctype的作⽤是什么？

DOCTYPE是html5标准⽹⻚声明，且必须声明在HTML⽂档的第⼀⾏。来告知浏览器的解析器⽤什么⽂档标准解析这个 ⽂档，不同的渲染模式会影响到浏览器对于 CSS 代码甚⾄ JavaScript 脚本的解析

⽂档解析类型有：

- BackCompat：怪异模式，浏览器使⽤⾃⼰的怪异模式解析渲染⻚⾯。（如果没有声明DOCTYPE，默认就是这个 模式）
- CSS1Compat：标准模式，浏览器使⽤W3C的标准解析渲染⻚⾯。

#### 这三种模式的区别是什么？

- 标准模式(standards mode)：⻚⾯按照 HTML 与 CSS 的定义渲染 
- 怪异模式(quirks mode)模式： 会模拟更旧的浏览器的⾏为 
- 近乎标准(almost standards)模式： 会实施了⼀种表单元格尺⼨的怪异⾏为（与IE7之前的单元格布局⽅式⼀致）， 除此之外符合标准定义

## HTML、XHTML、XML有什么区别

- HTML(超⽂本标记语⾔): 在html4.0之前HTML先有实现再有标准，导致HTML⾮常混乱和松散 
- XML(可扩展标记语⾔): 主要⽤于存储数据和结构，可扩展，⼤家熟悉的JSON也是相似的作⽤，但是更加轻量⾼ 效，所以XML现在市场越来越⼩了 
- XHTML(可扩展超⽂本标记语⾔): 基于上⾯两者⽽来，W3C为了解决HTML混乱问题⽽⽣，并基于此诞⽣了 HTML5，开头加⼊ `<!DOCTYPE html>` 的做法因此⽽来，如果不加就是兼容混乱的HTML，加了就是标准模式。

## 什么是data-属性？

HTML的数据属性，⽤于将数据储存于标准的HTML元素中作为额外信息,我们可以通过js访问并操作它，来达到操作数据的⽬的。

```html
<article id="electriccars" data-columns="3" data-index-number="12314" data-parent="cars"> ... </article>
```

> 前端框架出现之后，这种⽅法已经不流⾏了

## 你对HTML语义化的理解？

语义化是指使⽤恰当语义的html标签，让⻚⾯具有良好的结构与含义，⽐如 `<p>` 标签就代表段落， `<article>` 代表正⽂ 内容等等。

语义化的好处主要有两点：

- 开发者友好：使⽤语义类标签增强了可读性，开发者也能够清晰地看出⽹⻚的结构，也更为便于团队的开发和维护 
- 机器友好：带有语义的⽂字表现⼒丰富，更适合搜索引擎的爬⾍爬取有效信息，语义类还可以⽀持读屏软件，根据⽂章可以⾃动⽣成⽬录

这对于简书、知乎这种富⽂本类的应⽤很重要，语义化对于其⽹站的内容传播有很⼤的帮助，但是对于功能性的web软件重要性⼤打折扣，⽐如⼀个按钮、Skeleton这种组件根本没有对应的语义，也不需要什么SEO。

## HTML5与HTML4的不同之处

- ⽂件类型声明（<!DOCTYPE>）仅有⼀型：`<!DOCTYPE HTML>`。 
- 新的解析顺序：不再基于SGML。 
- 新的元素：`section, video, progress, nav, meter, time, aside, canvas, command, datalist, details, embed, figcaption, figure, footer, header, hgroup, keygen, mark, output, rp, rt, ruby, source, summary, wbr`。 
- input元素的新类型：date, email, url等等。 
- 新的属性：ping（⽤于a与area）, charset（⽤于meta）, async（⽤于script）。 
- 全域属性：id, tabindex, repeat。 
- 新的全域属性：contenteditable, contextmenu, draggable, dropzone, hidden, spellcheck。 
- 移除元素：acronym, applet, basefont, big, center, dir, font, frame, frameset, isindex, noframes, strike, tt

## 有哪些常⽤的meta标签？

meta标签由name和content两个属性来定义，来描述⼀个HTML⽹⻚⽂档的属性，例如作者、⽇期和时间、⽹⻚描述、 关键词、⻚⾯刷新等，除了⼀些http标准规定了⼀些name作为⼤家使⽤的共识，开发者也可以⾃定义name。

- charset，⽤于描述HTML⽂档的编码形式

```html
<meta charset="UTF-8" >
```

- http-equiv，顾名思义，相当于http的⽂件头作⽤,⽐如下⾯的代码就可以设置http的缓存过期⽇期

```html
<meta http-equiv="expires" content="Wed, 20 Jun 2019 22:33:00 GMT">
```

- viewport，移动前端最熟悉不过，Web开发⼈员可以控制视⼝的⼤⼩和⽐例

```html
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1">
```

- apple-mobile-web-app-status-bar-style,开发过PWA应⽤的开发者应该很熟悉，为了⾃定义评估⼯具栏的颜⾊。

```html
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
```

## src和href的区别？

- src是指向外部资源的位置，指向的内容会嵌⼊到⽂档中当前标签所在的位置，在请求src资源时会将其指向的资源 下载并应⽤到⽂档内，如js脚本，img图⽚和frame等元素。当浏览器解析到该元素时，会暂停其他资源的下载和处 理，知道将该资源加载、编译、执⾏完毕，所以⼀般js脚本会放在底部⽽不是头部。

- href是指向⽹络资源所在位置（的超链接），⽤来建⽴和当前元素或⽂档之间的连接，当浏览器识别到它他指向的 ⽂件时，就会并⾏下载资源，不会停⽌对当前⽂档的处理。

## 知道img的srcset的作⽤是什么？

可以设计响应式图⽚，我们可以使⽤两个新的属性srcset 和 sizes来提供更多额外的资源图像和提示，帮助浏览器选择 正确的⼀个资源。

srcset 定义了我们允许浏览器选择的图像集，以及每个图像的⼤⼩。
sizes 定义了⼀组媒体条件（例如屏幕宽度）并且指明当某些媒体条件为真时，什么样的图⽚尺⼨是最佳选择。

所以，有了这些属性，浏览器会：

- 查看设备宽度 
- 检查 sizes 列表中哪个媒体条件是第⼀个为真 
- 查看给予该媒体查询的槽⼤⼩ 
- 加载 srcset 列表中引⽤的最接近所选的槽⼤⼩的图像

> srcset提供了根据屏幕条件选取图⽚的能⼒

```html
<img src="clock-demo-thumb-200.png" 
    alt="Clock" 
    srcset="clock-demo-thumb-200.png 200w, clock-demo-thumb-400.png 400w" 
    sizes="(min-width: 600px) 200px, 50vw">
```

## 还有哪⼀个标签能起到跟srcset相似作⽤？

`<picture> `元素通过包含零或多个 `<source>` 元素和⼀个 `<img>`元素来为不同的显示/设备场景提供图像版本。浏览器 会选择最匹配的⼦ `<source>` 元素，如果没有匹配的，就选择 `<img>` 元素的 src 属性中的URL。然后，所选图像呈现 在 `<img>` 元素占据的空间中

> picture同样可以通过不同设备来匹配不同的图像资源

```html
<picture>
  <source srcset="/media/examples/surfer-240-200.jpg" media="(min-width: 800px)"> 
		<img src="/media/examples/painted-hand-298-332.jpg" /> 
	</source>
</picture> 
```

## script标签中defer和async的区别？

- defer：浏览器指示脚本在⽂档被解析后执⾏，script被异步加载后并不会⽴刻执⾏，⽽是等待⽂档被解析完毕后执 ⾏。
- async：同样是异步加载脚本，区别是脚本加载完毕后⽴即执⾏，这导致async属性下的脚本是乱序的，对于script 有先后依赖关系的情况，并不适⽤。

## 有⼏种前端储存的⽅式？及区别

cookies、localstorage、sessionstorage、Web SQL、IndexedDB

- cookies： 在HTML5标准前本地储存的主要⽅式，优点是兼容性好，请求头⾃带cookie⽅便，缺点是⼤⼩只有4k， ⾃动请求头加⼊cookie浪费流量，每个domain限制20个cookie，使⽤起来麻烦需要⾃⾏封装

- localStorage：HTML5加⼊的以键值对(Key-Value)为标准的⽅式，优点是操作⽅便，永久性储存（除⾮⼿动删 除），⼤⼩为5M，兼容IE8+

- sessionStorage：与localStorage基本类似，区别是sessionStorage当⻚⾯关闭后会被清理，⽽且与cookie、 localStorage不同，他不能在所有同源窗⼝中共享，是会话级别的储存⽅式

- Web SQL：2010年被W3C废弃的本地数据库数据存储⽅案，但是主流浏览器（⽕狐除外）都已经有了相关的实 现，web sql类似于SQLite，是真正意义上的关系型数据库，⽤sql进⾏操作，当我们⽤JavaScript时要进⾏转换， 较为繁琐。

- IndexedDB： 是被正式纳⼊HTML5标准的数据库储存⽅案，它是NoSQL数据库，⽤键值对进⾏储存，可以进⾏快 速读取操作，⾮常适合web场景，同时⽤JavaScript进⾏操作会⾮常⽅便。

## 提高网站性能的方式有哪些?

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