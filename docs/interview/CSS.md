# CSS面试题

## CSS选择器的优先级是怎样的？

CSS选择器的优先级是：内联 > ID选择器 > 类选择器 > 标签选择器

到具体的计算层⾯，优先级是由 A 、B、C、D 的值来决定的，其中它们的值计算规则如下：

- A 的值等于 1 的前提是存在内联样式, 否则 A = 0;
- B 的值等于 ID选择器 出现的次数; 
- C 的值等于 类选择器 和 属性选择器 和 伪类 出现的总次数; 
- D 的值等于 标签选择器 和 伪元素 出现的总次数 。

就⽐如下⾯的选择器，它不存在内联样式，所以A=0,不存在id选择器B=0,存在⼀个类选择器C=1,存在三个标签选择器 D=3，那么最终计算结果为: {0, 0, 1 ,3}

```css
ul ol li .red { ... }
```

按照这个结算⽅式，下⾯的计算结果为: {0, 1, 0, 0}

```css
#red { }
```

我们的⽐较优先级的⽅式是从A到D去⽐较值的⼤⼩，A、B、C、D权重从左到右，依次减⼩。判断优先级时，从左到 右，⼀⼀⽐较，直到⽐较出最⼤值，即可停⽌。

⽐如第⼆个例⼦的B与第⼀个例⼦的B相⽐，1>0,接下来就不需要⽐较了，第⼆个选择器的优先级更⾼。

## link和@import的区别？

- link属于XHTML标签，⽽@import是CSS提供的。 
- ⻚⾯被加载时，link会同时被加载，⽽@import引⽤的CSS会等到⻚⾯被加载完再加载。 
- import只在IE 5以上才能识别，⽽link是XHTML标签，⽆兼容问题。 
- link⽅式的样式权重⾼于@import的权重。 
- 使⽤dom控制样式时的差别。当使⽤javascript控制dom去改变样式的时候，只能使⽤link标签，因为@import不是 dom可以控制的。

## 有哪些⽅式（CSS）可以隐藏⻚⾯元素？

- `opacity:0` ：本质上是将元素的透明度将为0，就看起来隐藏了，但是依然占据空间且可以交互 
- `visibility:hidden` : 与上⼀个⽅法类似的效果，占据空间，但是不可以交互了 
- `overflow:hidden` : 这个只隐藏元素溢出的部分，但是占据空间且不可交互 
- `display:none` : 这个是彻底隐藏了元素，元素从⽂档流中消失，既不占据空间也不交互，也不影响布局 
`z-index:-9999` : 原理是将层级放到底部，这样就被覆盖了，看起来隐藏了 
`transform: scale(0,0)` : 平⾯变换，将元素缩放为0，但是依然占据空间，但不可交互

> 还有⼀些靠绝对定位把元素移到可视区域外，或者⽤clip-path进⾏裁剪的操作过于Hack，就不提了。

## em\px\rem区别？

- px：绝对单位，⻚⾯按精确像素展示。 
- em：相对单位，基准点为⽗节点字体的⼤⼩，如果⾃身定义了font-size按⾃身来计算（浏览器默认字体是 16px），整个⻚⾯内1em不是⼀个固定的值。 
- rem：相对单位，可理解为”root em”, 相对根节点html的字体⼤⼩来计算，CSS3新加属性，chrome/firefox/IE9+⽀ 持

## 如何理解层叠上下⽂？

层叠上下⽂是HTML元素的三维概念，这些HTML元素在⼀条假想的相对于⾯向（电脑屏幕的）视窗或者⽹⻚的⽤户的z 轴上延伸，HTML元素依据其⾃身属性按照优先级顺序占⽤层叠上下⽂的空间。

触发⼀下条件则会产⽣层叠上下⽂： 

- 根元素 (HTML), 
- z-index 值不为 "auto"的 绝对/相对定位， 
- ⼀个 z-index 值不为 "auto"的 flex 项⽬ (flex item)，即：⽗元素 display: flex|inline-flex， 
- opacity 属性值⼩于 1 的元素（参考 the specification for opacity）， 
- transform 属性值不为 "none"的元素， 
- mix-blend-mode 属性值不为 "normal"的元素， 
- filter值不为“none”的元素， 
- perspective值不为“none”的元素， 
- isolation 属性被设置为 "isolate"的元素， 
- position: fixed 
- 在 will-change 中指定了任意 CSS 属性，即便你没有直接指定这些属性的值（参考 这篇⽂章） 
- -webkit-overflow-scrolling 属性被设置 "touch"的元素

## 块级元素⽔平居中的⽅法？

## CSS有⼏种定位⽅式？

- static: 正常⽂档流定位，此时 top, right, bottom, left 和 z-index 属性⽆效，块级元素从上往下纵向排布，⾏级元素 从左向右排列。 

- relative：相对定位，此时的『相对』是相对于正常⽂档流的位置。 

- absolute：相对于最近的⾮ static 定位祖先元素的偏移，来确定元素位置，⽐如⼀个绝对定位元素它的⽗级、和祖 ⽗级元素都为relative，它会相对他的⽗级⽽产⽣偏移。 

- fixed：指定元素相对于屏幕视⼝（viewport）的位置来指定元素位置。元素的位置在屏幕滚动时不会改变，⽐如那 种回到顶部的按钮⼀般都是⽤此定位⽅式。 

- sticky：粘性定位，特性近似于relative和fixed的合体，其在实际应⽤中的近似效果就是IOS通讯录滚动的时候的 『顶屁股』。

## 如何理解z-index？

CSS 中的z-index属性控制重叠元素的垂直叠加顺序，默认元素的z-index为0，我们可以修改z-index来控制元素的图层 位置，⽽且z-index只能影响设置了position值的元素。 我们可以把视图上的元素认为是⼀摞书的层叠，⽽⼈眼是俯视的视⻆，设置z-index的位置，就如同设置某⼀本书在这摞 书中的位置。

## 清除浮动有哪些⽅法？

## 你对媒体查询的理解？

媒体查询由⼀个可选的媒体类型和零个或多个使⽤媒体功能的限制了样式表范围的表达式组成，例如宽度、⾼度和颜 ⾊。媒体查询，添加⾃CSS3，允许内容的呈现针对⼀个特定范围的输出设备⽽进⾏裁剪，⽽不必改变内容本身,⾮常适 合web⽹⻚应对不同型号的设备⽽做出对应的响应适配。

媒体查询包含⼀个可选的媒体类型和，满⾜CSS3规范的条件下，包含零个或多个表达式，这些表达式描述了媒体特 征，最终会被解析为true或false。如果媒体查询中指定的媒体类型匹配展示⽂档所使⽤的设备类型，并且所有的表达式 的值都是true，那么该媒体查询的结果为true.那么媒体查询内的样式将会⽣效。

```css
<!-- link元素中的CSS媒体查询 --> 
<link rel="stylesheet" media="(max-width: 800px)" href="example.css" />

<!-- 样式表中的CSS媒体查询 --> 
<style> 
@media (max-width: 600px) { 
  .facet_sidebar { display: none; } 
  }
</style>
```

## 谈谈对BFC的理解

书⾯解释：BFC(Block Formatting Context)这⼏个英⽂拆解

- Box: CSS布局的基本单位，Box 是 CSS 布局的对象和基本单位， 直观点来说，就是⼀个⻚⾯是由很多个 Box 组 成的，实际就是上个问题说的盒模型
- Formatting context：块级上下⽂格式化，它是⻚⾯中的⼀块渲染区域，并且有⼀套渲染规则，它决定了其⼦元素 将如何定位，以及和其他元素的关系和相互作⽤

简⽽⾔之，它是⼀块独⽴的区域，让处于BFC内部的元素与外部的元素互相隔离

BFC触发条件:

- 根元素，即HTML元素 
- position: fixed/absolute 
- float 不为none 
- overflow不为visible 
- display的值为inline-block、table-cell、table-caption

作⽤是什么: 

1. 防⽌margin发⽣重叠
2. 两栏布局，防⽌⽂字环绕等
3. 防⽌元素塌陷

## 为什么有时候⼈们⽤translate来改变位置⽽不是定位？

translate()是transform的⼀个值。改变transform或opacity不会触发浏览器重新布局（reflow）或重绘（repaint），只会 触发复合（compositions）。⽽改变绝对定位会触发重新布局，进⽽触发重绘和复合。transform使浏览器为元素创建⼀ 个 GPU 图层，但改变绝对定位会使⽤到 CPU。 因此translate()更⾼效，可以缩短平滑动画的绘制时间。 ⽽translate改变位置时，元素依然会占据其原始空间，绝对定位就不会发⽣这种情况。

## 伪类和伪元素的区别是什么？

伪类（pseudo-class） 是⼀个以冒号(:)作为前缀，被添加到⼀个选择器末尾的关键字，当你希望样式在特定状态下才被 呈现到指定的元素时，你可以往元素的选择器后⾯加上对应的伪类。

伪元素⽤于创建⼀些不在⽂档树中的元素，并为其添加样式。⽐如说，我们可以通过::before来在⼀个元素前增加⼀些 ⽂本，并为这些⽂本添加样式。虽然⽤户可以看到这些⽂本，但是这些⽂本实际上不在⽂档树中。

## 你对flex的理解？

[flex语法](https://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)

## 关于CSS的动画与过渡问题

[深⼊理解CSS动画animation](https://www.cnblogs.com/xiaohuochai/p/5391663.html)

[深⼊理解CSS过渡transition](https://www.cnblogs.com/xiaohuochai/p/5347930.html)
