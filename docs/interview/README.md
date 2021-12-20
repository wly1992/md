# 面试题总结

## 1.new 的实现原理

new 的实现原理:

创建一个空对象，构造函数中的 this 指向这个空对象。

这个新对象被执行 [[原型]] 连接。

执行构造函数方法，属性和方法被添加到 this 引用的对象中。

如果构造函数中没有返回其它对象，那么返回 this，即创建的这个的新对象，否则，返回构造函数中返回的对象。

```
  function _new() {
    let target = {}; // 创建的新对象
    // 第一个参数是构造函数
    let [constructor, ...args] = [...arguments];
    // 执行 [[原型]] 连接 ;target 是 constructor 的实例
    target.__proto__ = constructor.prototype;
    // 执行构造函数，将属性或方法添加到创建的空对象上
    let result = constructor.apply(target, args);
    if (result && (typeof (result) == "object" || typeof (result) == "function")) {
      // 如果构造函数执行的结构返回的是一个对象，那么返回这个对象
      return result;
    }
    // 如果构造函数返回的不是一个对象，返回创建的新对象
    return target;
  }
```

## 2.this指向问题

#### this 是什么？
JavaScript this 关键词指的是它所属的对象。

它拥有不同的值，具体取决于它的使用位置：

在方法中，this 指的是所有者对象。
- 单独的情况下，this 指的是全局对象。
- 在函数中，this 指的是全局对象。
- 在函数中，严格模式下，this 是 undefined。
- 在事件中，this 指的是接收事件的元素。
像 call() 和 apply() 这样的方法可以将 this 引用到任何对象。

#### 全局环境中的 this

浏览器环境：无论是否在严格模式下，在全局执行环境中（在任何函数体外部）this 都指向全局对象 window;

node 环境：无论是否在严格模式下，在全局执行环境中（在任何函数体外部），this 都是空对象 {};

#### 是否是 new 绑定

如果是 `new` 绑定，并且构造函数中没有返回 function 或者是 object，那么 `this`指向这个新对象。如下:

> 构造函数返回值不是 function 或 object。new Super() 返回的是 this 对象。

```
function Super(age) {
  this.age = age;
}

let instance = new Super('26');
console.log(instance.age); //26
```
> 构造函数返回值是 function 或 object，new Super()是返回的是Super种返回的对象。

```
function Super(age) {
	this.age = age;
	let obj = {a: '2'};
	return obj;
}

let instance = new Super('hello');
console.log(instance);//{ a: '2' }
console.log(instance.age); //undefined
```

#### 函数是否通过 call,apply 调用，或者使用了 bind 绑定，如果是，那么this绑定的就是指定的对象【归结为显式绑定】

```
function info(){
	console.log(this.age);
}
var person = {
	age: 20,
	info
}
var age = 28;
var info = person.info;
info.call(person); //20
info.apply(person); //20
info.bind(person)(); //20
```


这里同样需要注意一种特殊情况，如果 `call`,`apply` 或者 `bind` 传入的第一个参数值是 `undefined` 或者 `null`，严格模式下 `this` 的值为传入的值 `null /undefined`。非严格模式下，实际应用的默认绑定规则，this 指向全局对象(node环境为global，浏览器环境为window)


```
function info(){
	//node 环境中: 非严格模式 global，严格模式为 null
	// 浏览器环境中: 非严格模式 window，严格模式为 null
	console.log(this);
	console.log(this.age);
}
var person = {
	age: 20,
	info
}
var age = 28;
var info = person.info;

// 严格模式抛出错误；
// 非严格模式，node 下输出 undefined（因为全局的 age 不会挂在 global 上）
// 非严格模式。浏览器环境下输出 28（因为全局的 age 会挂在 window 上）
info.call(null);

```

#### 隐式绑定，函数的调用是在某个对象上触发的，即调用位置上存在上下文对象。典型的隐式调用为: `xxx.fn()`，此时`this`指向这个对象

```
function info(){
	console.log(this.age);
}
var person = {
	age: 20,
	info
}
var age = 28;
person.info(); //20; 执行的是隐式绑定
```

#### 默认绑定，在不能应用其它绑定规则时使用的默认规则，通常是独立函数调用。

非严格模式： node环境，指向全局对象 `global`，浏览器环境，指向全局对象 `window`。
严格模式：执行 `undefined`

```
function info(){
	console.log(this.age);
}
var age = 28;
// 严格模式；抛错
// 非严格模式，node 下输出 undefined（因为全局的 age 不会挂在 global 上）
// 非严格模式。浏览器环境下输出 28（因为全局的 age 会挂在 window 上）
// 严格模式抛出，因为 this 此时是 undefined
info();
```

#### 箭头函数的情况：

> 箭头函数没有自己的 `this`，继承外层上下文绑定的 `this`。

```
let obj = {
	age: 20,
	info: function() {
		return () => {
			console.log(this.age); //this 继承的是外层上下文绑定的 this
		}
	}
}

let person = {age: 28};
let info = obj.info();
info(); //20

let info2 = obj.info.call(person);
info2(); //28
```

## 3.深拷贝和浅拷贝的区别是什么？实现一个深拷贝

深拷贝和浅拷贝是针对复杂数据类型来说的，浅拷贝只拷贝一层，而深拷贝是层层拷贝。

#### 深拷贝

> 深拷贝复制变量值，对于非基本类型的变量，则递归至基本类型变量后，再复制。 深拷贝后的对象与原来的对象是完全隔离的，互不影响，对一个对象的修改并不会影响另一个对象。

#### 浅拷贝

> 浅拷贝是会将对象的每个属性进行依次复制，但是当对象的属性值是引用类型时，实质复制的是其引用，当引用指向的值改变时也会跟着变化。

  可以使用 `for in`、 `Object.assign`、 扩展运算符 `...` 、`Array.prototype.slice()`、`Array.prototype.concat()` 等，例如:

  ```
  let obj = {
      name: 'wly',
      age: '28',
      hobbies: ['reading','basketball']
  }

  let obj2 = Object.assign({},obj);
  let obj3 = {...obj};

  obj.name = 'lilei';
  obj.hobbies.push('coding')

  console.log(obj)
  //{name: "lilei", age: "28", hobbies: ["reading", "basketball", "coding"]}
  console.log(obj2)
  //{name: "wly", age: "28", hobbies: ["reading", "basketball", "coding"]}
  console.log(obj3)
  //{name: "wly", age: "28", hobbies: ["reading", "basketball", "coding"]}
  ```

可以看出浅拷贝只最第一层属性进行了拷贝，当第一层的属性值是基本数据类型时，新的对象和原对象互不影响，但是如果第一层的属性值是复杂数据类型，那么新对象和原对象的属性值其指向的是同一块内存地址。

#### 深拷贝实现

> 1.深拷贝最简单的实现是: `JSON.parse(JSON.stringify(obj))`

`JSON.parse(JSON.stringify(obj))` 是最简单的实现方式，但是有一些缺陷：

1. 对象的属性值是函数时，无法拷贝。
2. 原型链上的属性无法拷贝
3. 不能正确的处理 Date 类型的数据
4. 不能处理 RegExp
5. 会忽略 symbol
6. 会忽略 undefined

> 2.实现一个 deepClone 函数

1. 如果是基本数据类型，直接返回
2. 如果是 RegExp 或者 Date 类型，返回对应类型
3. 如果是复杂数据类型，递归。
4. 考虑循环引用的问题

```
  function deepClone(obj, hash = new WeakMap()) { // 递归拷贝
    if (obj instanceof RegExp) return new RegExp(obj);
    if (obj instanceof Date) return new Date(obj);
    if (obj === null || typeof obj !== 'object') {
        // 如果不是复杂数据类型，直接返回
        return obj;
    }
    if (hash.has(obj)) {
        return hash.get(obj);
    }
    /**
     * 如果 obj 是数组，那么 obj.constructor 是 [Function: Array]
     * 如果 obj 是对象，那么 obj.constructor 是 [Function: Object]
     */
    let t = new obj.constructor();
    hash.set(obj, t);
    for (let key in obj) {
        // 递归
        if (obj.hasOwnProperty(key)) {// 是否是自身的属性
            t[key] = deepClone(obj[key], hash);
        }
    }
    return t;
  }
```

## 4.call/apply 的区别和实现原理

`call` 和 `apply` 的功能相同，都是改变 `this` 的执行，并立即执行函数。区别在于传参方式不同。


- `func.call(thisArg, arg1, arg2, ...)`：第一个参数是 this 指向的对象，其它参数依次传入。


- `func.apply(thisArg, [argsArray])`：第一个参数是 this 指向的对象，第二个参数是数组或类数组。


一起思考一下，如何模拟实现 `call` ？ 

首先，我们知道，函数都可以调用 `call`，说明 `call` 是函数原型上的方法，所有的实例都可以调用。即: `Function.prototype.call`。

- 在 `call` 方法中获取调用`call()`函数
- 如果第一个参数没有传入，那么默认指向 `window / global`(非严格模式)
- 传入 `call` 的第一个参数是 `this` 指向的对象，根据隐式绑定的规则，我们知道 `obj.foo()`, `foo()` 中的 `this` 指向 obj;因此我们可以这样调用函数 `thisArgs.func(...args)`
- 返回执行结果

```
Function.prototype.call = function() {
  let [thisArg, ...args] = [...arguments];
  if (!thisArg) {
      //context 为 null 或者是 undefined
      thisArg = typeof window === 'undefined' ? global : window;
  }
  //this 的指向的是当前函数 func (func.call)
  thisArg.func = this;
  // 执行函数
  let result = thisArg.func(...args);
  delete thisArg.func; //thisArg 上并没有 func 属性，因此需要移除
  return result;
}
```

`apply` 的实现思路和 `call` 一致，仅参数处理略有差别。如下：

```
Function.prototype.apply = function(thisArg,rest) {
  let result;
  if (!thisArg) {
      //context 为 null 或者是 undefined
      thisArg = typeof window === 'undefined' ? global : window;
  }
  //this 的指向的是当前函数 func (func.call)
  thisArg.func = this;
  if(!rest){
    result = thisArg.func()
  }else{
    result = thisArg.func(...rest)
  }
  delete thisArg.func; //thisArg 上并没有 func 属性，因此需要移除
  return result;
}
```

##  5.柯里化函数实现

在开始之前，我们首先需要搞清楚函数柯里化的概念。

> 函数柯里化是把接受多个参数的函数变换成接受一个单一参数（最初函数的第一个参数）的函数，并且返回接受余下的参数而且返回结果的新函数的技术。

```
const curry = (fn, ...args) => {
  // 参数长度不足时，重新柯里化该函数，等待接受新参数
  // 参数长度满足时，执行函数
  return args.length < fn.length ? (...arguments) => curry(fn, ...args, ...arguments) : fn(...args);
}      
function sumFn(a, b, c) {
  return a + b + c;
}
var sum = curry(sumFn);
console.log(sum(2))
console.log(sum(2)(3)(5));//10
console.log(sum(2, 3, 5));//10
console.log(sum(2)(3, 5));//10
console.log(sum(2, 3)(5));//10
```

> 函数柯里化的主要作用：

- 参数复用
- 提前返回 – 返回接受余下的参数且返回结果的新函数
- 延迟执行 – 返回新函数，等待执行

## 6.如何让 `(a == 1 && a == 2 && a == 3)` 的值为`true`？

> 1.利用隐式类型转换

`==` 操作符在左右数据类型不一致时，会先进行隐式转换。
`a == 1 && a == 2 && a == 3` 的值意味着其不可能是基本数据类型。因为如果 a 是 `null` 或者是 `undefined` `bool`类型，都不可能返回`true`。

因此可以推测 a 是复杂数据类型，JS 中复杂数据类型只有 `object`，回忆一下，Object 转换为原始类型会调用什么方法？

```
// 部署 [Symbol.toPrimitive] / valueOf/ toString 皆可
// 一次返回 1，2，3 即可。
let a = {
    [Symbol.toPrimitive]: (function(hint) {
            let i = 1;
            // 闭包的特性之一：i 不会被回收
            return function() {
                return i++;
            }
    })()
}
```

> 2.利用数据劫持(Proxy/Object.defineProperty)
```
let i = 1;
Object.defineProperty(window, 'a', {
    get: function() {
        return i++;
    }
});
```

```
let i = 1;
let a = new Proxy({}, {
    i: 1,
    get: function () {
        return () => this.i++;
    }
});
```

> 3.数组的 `toString` 接口默认调用数组的 `join` 方法，重写 `join` 方法

```
let a = [1, 2, 3];
// a.shift() == 1 && a.shift() == 2 && a.shift() == 3   true
a.join = a.shift;
```

> 4.利用with
```
let i = 0;

with ({
    get a() {
        return ++i;
    }
}) {
    console.log(a == 1 && a == 2 && a == 3); //true
}
```

## 7.什么是BFC？BFC的布局规则是什么？如何创建BFC？

Box 是 CSS 布局的对象和基本单位，页面是由若干个Box组成的。

元素的类型 和 `display` 属性，决定了这个 Box 的类型。不同类型的 Box 会参与不同的 Formatting Context。

> Formatting Context

Formatting Context 是页面的一块渲染区域，并且有一套渲染规则，决定了其子元素将如何定位，以及和其它元素的关系和相互作用。

Formatting Context 有 BFC (Block formatting context)，IFC (Inline formatting context)，FFC (Flex formatting context) 和 GFC (Grid formatting context)。FFC 和 GFC 为 CC3 中新增。

> BFC布局规则

- BFC内，盒子依次垂直排列。
- BFC内，两个盒子的垂直距离由 margin 属性决定。属于同一个BFC的两个相邻Box的margin会发生重叠【符合合并原则的- margin合并后是使用大的margin】
- BFC内，每个盒子的左外边缘接触内部盒子的左边缘（对于从右到左的格式，右边缘接触）。即使在存在浮动的情况下也是如此。除非创建新的BFC。
- BFC的区域不会与float box重叠。
- BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。反之也如此。
- 计算BFC的高度时，浮动元素也参与计算。

> 如何创建BFC

- 根元素
- 浮动元素（float 属性不为 none）
- position 为 absolute 或 fixed
- overflow 不为 visible 的块元素
- display 为 inline-block, table-cell, table-caption

> BFC 的应用

- 防止 margin  重叠 (同一个BFC内的两个两个相邻Box的 margin 会发生重叠，触发生成两个BFC，即不会重叠)
- 清除内部浮动 (创建一个新的 BFC，因为根据 BFC 的规则，计算 BFC 的高度时，浮动元素也参与计算)
- 自适应多栏布局 (BFC的区域不会与float box重叠。因此，可以触发生成一个新的BFC)

## 8.异步加载JS脚本的方式有哪些？

> `<script>` 标签中增加 async(html5) 或者 defer(html4) 属性,脚本就会异步加载。

defer 和 async 的区别在于：

- defer 要等到整个页面在内存中正常渲染结束（DOM 结构完全生成，以及其他脚本执行完成），在window.onload 之前执行；
- async 一旦下载完，渲染引擎就会中断渲染，执行这个脚本以后，再继续渲染。
- 如果有多个 defer 脚本，会按照它们在页面出现的顺序加载
- 多个 async 脚本不能保证加载顺序

> 动态创建 script 标签

动态创建的 `script` ，设置 src 并不会开始下载，而是要添加到文档中，JS文件才会开始下载。

```
let script = document.createElement('script');
script.src = 'XXX.js';
// 添加到 html 文件中才会开始下载
document.body.append(script);
```

> XHR 异步加载JS

```
let xhr = new XMLHttpRequest();
xhr.open("get", "js/xxx.js",true);
xhr.send();
xhr.onreadystatechange = function() {
    if (xhr.readyState == 4 && xhr.status == 200) {
        eval(xhr.responseText);
    }
}
```

## 9.ES5有几种方式可以实现继承？分别有哪些优缺点？

ES5 有 6 种方式可以实现继承，分别为：

1. 原型链继承

原型链继承的基本思想是利用原型让一个引用类型继承另一个引用类型的属性和方法。

```
function SuperType() {
    this.name = 'Yvette';
    this.colors = ['pink', 'blue', 'green'];
}
SuperType.prototype.getName = function () {
    return this.name;
}
function SubType() {
    this.age = 22;
}
SubType.prototype = new SuperType();
SubType.prototype.getAge = function() {
    return this.age;
}
SubType.prototype.constructor = SubType;
let instance1 = new SubType();
instance1.colors.push('yellow');
console.log(instance1.getName()); //'Yvette'
console.log(instance1.colors);//[ 'pink', 'blue', 'green', 'yellow' ]

let instance2 = new SubType();
console.log(instance2.colors);//[ 'pink', 'blue', 'green', 'yellow' ]
```


## 10.隐藏页面中的某个元素的方法有哪些？

> 隐藏类型

屏幕并不是唯一的输出机制，比如说屏幕上看不见的元素（隐藏的元素），其中一些依然能够被读屏软件阅读出来（因为读屏软件依赖于可访问性树来阐述）。为了消除它们之间的歧义，我们将其归为三大类：

- 完全隐藏：元素从渲染树中消失，不占据空间。
- 视觉上的隐藏：屏幕中不可见，占据空间。
- 语义上的隐藏：读屏软件不可读，但正常占据空。

> 完全隐藏

1.display 属性

```
display: none;
```

2.hidden 属性

HTML5 新增属性，相当于 display: none

```
<div hidden></div>
```

> 视觉上的隐藏

1.利用 position 和 盒模型(margin) 将元素移出可视区范围

2.利用 transfrom

- 缩放 transform: scale(0);
- 移动 translateX(-99999px);
- 旋转 rotateY(90deg);

3.设置其大小为0

```
height: 0;
width: 0;
font-size: 0;
```

4.设置透明度为0 opacity: 0;

5.visibility属性  visibility: hidden;

6.层级覆盖，z-index 属性,设置一个层级较高的元素覆盖在此元素上。

7.clip-path 裁剪

```
clip-path: polygon(0 0, 0 0, 0 0, 0 0);
```

> 语义上的隐藏

aria-hidden 属性

读屏软件不可读，占据空间，可见。

```
<div aria-hidden="true">
</div>
```

## 11.let、const、var 的区别有哪些？

声明方式|变量提升|暂时性死区|重复声明|块作用域有效
---|---|---|---|---
var|会|	不存在|	允许|	不是
let|	不会|	存在|	不允许|	是
const	|不会|	存在|	不允许|	是

1. let/const 定义的变量不会出现变量提升，而 var 定义的变量会提升。
2. 相同作用域中，let 和 const 不允许重复声明，var 允许重复声明。
3. const 声明变量时必须设置初始值
4. const 声明一个只读的常量，这个常量不可改变。

这里有一个非常重要的点即是：在JS中，复杂数据类型，存储在栈中的是堆内存的地址，存在栈中的这个地址是不变的，但是存在堆中的值是可以变得。有没有相当常量指针/指针常量~

```
const a = 20;
const b = {
  age: 18,
  star: 200
}
b.age = 22
```
一图胜万言，如下图所示，不变的是栈内存中 a 存储的 20，和 b 中存储的 0x0012ff21（瞎编的一个数字）。而 {age: 18, star: 200} 是可变的。

<img align=center src="https://user-gold-cdn.xitu.io/2019/6/26/16b8f715226b7944?imageslim">

## 12.说一说你对JS执行上下文栈和作用域链的理解？

在开始说明JS上下文栈和作用域之前，我们先说明下JS上下文以及作用域的概念。

### JS执行上下文

执行上下文就是当前 JavaScript 代码被解析和执行时所在环境的抽象概念， JavaScript 中运行任何的代码都是在执行上下文中运行。

执行上下文类型分为：

- 全局执行上下文
- 函数执行上下文

执行上下文创建过程中，需要做以下几件事:

- 创建变量对象：首先初始化函数的参数arguments，提升函数声明和变量声明。
- 创建作用域链（Scope Chain）：在执行期上下文的创建阶段，作用域链是在变量对象之后创建的。
- 确定this的值，即 ResolveThisBinding

### 作用域

作用域负责收集和维护由所有声明的标识符（变量）组成的一系列查询，并实施一套非常严格的规则，确定当前执行的代码对这些标识符的访问权限。—— 摘录自《你不知道的JavaScript》(上卷)
作用域有两种工作模型：词法作用域和动态作用域，JS采用的是词法作用域工作模型，词法作用域意味着作用域是由书写代码时变量和函数声明的位置决定的。(`with` 和 `eval` 能够修改词法作用域，但是不推荐使用，对此不做特别说明)

> 作用域分为：

- 全局作用域
- 函数作用域
- 块级作用域

JS执行上下文栈(后面简称执行栈)

执行栈，也叫做调用栈，具有 LIFO (后进先出) 结构，用于存储在代码执行期间创建的所有执行上下文。

> 规则如下：

- 首次运行JavaScript代码的时候,会创建一个全局执行的上下文并Push到当前的执行栈中，每当发生函数调用，引擎都会为该函数创建一个新的函数执行上下文并Push当前执行栈的栈顶。
- 当栈顶的函数运行完成后，其对应的函数执行上下文将会从执行栈中Pop出，上下文的控制权将移动到当前执行栈的下一个执行上下文。

```
function fun3() {
    console.log('fun3')
}

function fun2() {
    fun3();
}

function fun1() {
    fun2();
}

fun1();
```
`Global Execution Context` (即全局执行上下文) 首先入栈，过程如下：

<img align=center src="https://user-gold-cdn.xitu.io/2019/6/26/16b8f71525a2e359?imageslim" width="1368" height="422">

伪代码:

```
ECStack.push(globalContext);

//执行fun1();
ECStack.push(<fun1> functionContext);

//fun1中又调用了fun2;
ECStack.push(<fun2> functionContext);

//fun2中又调用了fun3;
ECStack.push(<fun3> functionContext);

//fun3执行完毕
ECStack.pop();

//fun2执行完毕
ECStack.pop();

//fun1执行完毕
ECStack.pop();

//javascript继续顺序执行下面的代码，但ECStack底部始终有一个 全局上下文（globalContext）;
```

### 作用域链

作用域链就是从当前作用域开始一层一层向上寻找某个变量，直到找到全局作用域还是没找到，就宣布放弃。这种一层一层的关系，就是作用域链。

如：

```
var a = 10;
function fn1() {
    var b = 20;
    console.log(fn2)
    function fn2() {
        a = 20
    }
    return fn2;
}
fn1()();
```

fn2作用域链 = [fn2作用域, fn1作用域，全局作用域]

<img style="-webkit-user-select: none;margin:0 auto;cursor: zoom-in; display: block" src="https://user-gold-cdn.xitu.io/2019/6/26/16b8f715263fc144?imageslim" width="520" height="320">

## 13.防抖函数的作用是什么？请实现一个防抖函数

> 防抖函数的作用

抖函数的作用就是控制函数在一定时间内的执行次数。防抖意味着N秒内函数只会被执行一次，如果N秒内再次被触发，则重新计算延迟时间。

举例说明： 小思最近在减肥，但是她非常吃吃零食。为此，与其男朋友约定好，如果10天不吃零食，就可以购买一个包(不要问为什么是包，因为包治百病)。但是如果中间吃了一次零食，那么就要重新计算时间，直到小思坚持10天没有吃零食，才能购买一个包。所以，管不住嘴的小思，没有机会买包(悲伤的故事)... 这就是 防抖。

> 防抖函数实现

- 事件第一次触发时，timeout 是 null，调用 later()，若 immediate 为true，那么立即调用 func.apply(this, params)；如果 immediate 为 false，那么过 wait 之后，调用 func.apply(this, params)
- 事件第二次触发时，如果 timeout 已经重置为 null(即 setTimeout 的倒计时结束)，那么流程与第一次触发时一样，若 timeout 不为 null(即 setTimeout 的倒计时未结束)，那么清空定时器，重新开始计时。

```
function debounce(func, wait, immediate = true) {
    let timeout, result;
    // 延迟执行函数
    const later = (context, args) => setTimeout(() => {
        timeout = null;// 倒计时结束
        if (!immediate) {
            // 执行回调
            result = func.apply(context, args);
            context = args = null;
        }
    }, wait);
    let debounced = function (...params) {
        if (!timeout) {
            timeout = later(this, params);
            if (immediate) {
                // 立即执行
                result = func.apply(this, params);
            }
        } else {
            clearTimeout(timeout);
            // 函数在每个等待时延的结束被调用
            timeout = later(this, params);
        }
        return result;
    }
    // 提供在外部清空定时器的方法
    debounced.cancel = function () {
        clearTimeout(timeout);
        timeout = null;
    };
    return debounced;
};
```

`immediate` 为 `true` 时，表示函数在每个等待时延的开始被调用。`immediate` 为 `false` 时，表示函数在每个等待时延的结束被调用。

> 防抖的应用场景


- 搜索框输入查询，如果用户一直在输入中，没有必要不停地调用去请求服务端接口，等用户停止输入的时候，再调用，设置一个合适的时间间隔，有效减轻服务端压力。
- 表单验证
- 按钮提交事件。
- 浏览器窗口缩放，resize事件(如窗口停止改变大小之后重新计算布局)等。

## 14.节流函数的作用是什么？有哪些应用场景，请实现一个节流函数

> 节流函数的作用

节流函数的作用是规定一个单位时间，在这个单位时间内最多只能触发一次函数执行，如果这个单位时间内多次触发函数，只能有一次生效。

> 节流函数实现

```
function throttle(func, wait, options = {}) {
    var timeout, context, args, result;
    var previous = 0;
    var later = function () {
        previous = options.leading === false ? 0 : (Date.now() || new Date().getTime());
        timeout = null;
        result = func.apply(context, args);
        if (!timeout) context = args = null;
    };

    var throttled = function () {
        var now = Date.now() || new Date().getTime();
        if (!previous && options.leading === false) previous = now;
        //remaining 为距离下次执行 func 的时间
        //remaining > wait，表示客户端系统时间被调整过
        var remaining = wait - (now - previous);
        context = this;
        args = arguments;
        //remaining 小于等于 0，表示事件触发的间隔时间大于设置的 wait
        if (remaining <= 0 || remaining > wait) {
            if (timeout) {
                // 清空定时器
                clearTimeout(timeout);
                timeout = null;
            }
            // 重置 previous
            previous = now;
            // 执行函数
            result = func.apply(context, args);
            if (!timeout) context = args = null;
        } else if (!timeout && options.trailing !== false) {
            timeout = setTimeout(later, remaining);
        }
        return result;
    };

    throttled.cancel = function () {
        clearTimeout(timeout);
        previous = 0;
        timeout = context = args = null;
    };

    return throttled;
}
```
禁用第一次首先执行，传递 `{leading: false}` ；想禁用最后一次执行，传递 `{trailing: false}`

> 节流的应用场景

- 按钮点击事件
- 拖拽事件
- onScoll
- 计算鼠标移动的距离(mousemove)

## 15.实现 Promise.all 方法

在实现 `Promise.all` 方法之前，我们首先要知道` Promise.all` 的功能和特点，因为在清楚了 `Promise.all` 功能和特点的情况下，我们才能进一步去写实现。

> Promise.all 功能

`Promise.all(iterable)` 返回一个新的 `Promise` 实例。此实例在 `iterable` 参数内所有的 `promise` 都 `fulfilled` 或者参数中不包含 `promise` 时，状态变成 `fulfilled`；如果参数中 `promise` 有一个失败`rejected`，此实例回调失败，失败原因的是第一个失败 `promise` 的返回结果。

`let p = Promise.all([p1, p2, p3]);`

p的状态由 p1,p2,p3决定，分成以下；两种情况：

1. 只有p1、p2、p3的状态都变成 fulfilled，p的状态才会变成 fulfilled，此时p1、p2、p3的返回值组成一个数组，传递给p的回调函数。
2. 只要p1、p2、p3之中有一个被 rejected，p的状态就变成 rejected，此时第一个被reject的实例的返回值，会传递给p的回调函数。

> Promise.all 的特点

- 如果传入的参数为空的可迭代对象，`Promise.all` 会 同步 返回一个已完成状态的 `promise`
- 如果传入的参数中不包含任何 `promise`,`Promise.all` 会 异步 返回一个已完成状态的 `promise`
- 其它情况下，Promise.all 返回一个 处理中`（pending）` 状态的 `promise`.

> Promise.all 返回的 promise 的状态

- 如果传入的参数中的 `promise` 都变成完成状态，`Promise.all` 返回的 `promise` 异步地变为完成。
- 如果传入的参数中，有一个 `promise` 失败，`Promise.all` 异步地将失败的那个结果给失败状态的回调函数，而不管其它 `promise` 是否完成
- 在任何情况下，`Promise.all` 返回的 `promise` 的完成状态的结果都是一个数组

> Promise.all 实现

```
Promise.all = function (promises) {
    //promises 是可迭代对象，省略参数合法性检查
    return new Promise((resolve, reject) => {
        //Array.from 将可迭代对象转换成数组
        promises = Array.from(promises);
        if (promises.length === 0) {
            resolve([]);
        } else {
            let result = [];
            let index = 0;
            for (let i = 0;  i < promises.length; i++ ) {
                // 考虑到 i 可能是 thenable 对象也可能是普通值
                Promise.resolve(promises[i]).then(data => {
                    result[i] = data;
                    if (++index === promises.length) {
                        // 所有的 promises 状态都是 fulfilled，promise.all 返回的实例才变成 fulfilled 态
                        resolve(result);
                    }
                }, err => {
                    reject(err);
                    return;
                });
            }
        }
    });
}
```

## 16.请实现一个 flattenDeep 函数，把嵌套的数组扁平化

例如:

`flattenDeep([1, [2, [3, [4]], 5]]); //[1, 2, 3, 4, 5]`

> 1. 利用 Array.prototype.flat

ES6 为数组实例新增了 flat 方法，用于将嵌套的数组“拉平”，变成一维的数组。该方法返回一个新数组，对原数组没有影响。

flat 默认只会 “拉平” 一层，如果想要 “拉平” 多层的嵌套数组，需要给 flat 传递一个整数，表示想要拉平的层数。

```
function flattenDeep(arr, deepLength) {
    return arr.flat(deepLength);
    // return arr.flat(Infinity);
}

console.log(flattenDeep([1, [2, [3, [4]], 5]], 3));
```
> 2. 利用 reduce 和 concat
```
function flattenDeep(arr){
    return arr.reduce((acc, val) => Array.isArray(val) ? acc.concat(flattenDeep(val)) : acc.concat(val), []);
}
console.log(flattenDeep([1, [2, [3, [4]], 5]]));
```

> 3. 使用 stack 无限反嵌套多层嵌套数组

```
function flattenDeep(input) {
    const stack = [...input];
    const res = [];
    while (stack.length) {
        // 使用 pop 从 stack 中取出并移除值
        const next = stack.pop();
        if (Array.isArray(next)) {
            // 使用 push 送回内层数组中的元素，不会改动原始输入 original input
            stack.push(...next);
        } else {
            res.push(next);
        }
    }
    // 使用 reverse 恢复原数组的顺序
    return res.reverse();
}
console.log(flattenDeep([1, [2, [3, [4]], 5]]));
```

## 17.可迭代对象有哪些特点

ES6 规定，默认的 `Iterator` 接口部署在数据结构的 `Symbol.iterator` 属性，换个角度，也可以认为，一个数据结构只要具有 `Symbol.iterator` 属性(`Symbol.iterator` 方法对应的是遍历器生成函数，返回的是一个遍历器对象)，那么就可以其认为是可迭代的。

可迭代对象的特点

- 具有 Symbol.iterator 属性，Symbol.iterator() 返回的是一个遍历器对象
- 可以使用 for ... of 进行循环
- 通过被 Array.from 转换为数组

```
let arry = [1, 2, 3, 4];
let iter = arry[Symbol.iterator]();
console.log(iter.next()); //{ value: 1, done: false }
console.log(iter.next()); //{ value: 2, done: false }
console.log(iter.next()); //{ value: 3, done: false }
```

原生具有 `Iterator` 接口的数据结构：
- Array
- Map
- Set
- String
- TypedArray
- 函数的 arguments 对象
- NodeList 对象

## 18.JSONP 的原理是什么？

尽管浏览器有同源策略，但是 `<script>` 标签的 `src` 属性不会被同源策略所约束，可以获取任意服务器上的脚本并执行。`jsonp` 通过插入 `script` 标签的方式来实现跨域，参数只能通过 `url` 传入，仅能支持 `get` 请求。

> 实现原理:

- Step1: 创建 callback 方法
- Step2: 插入 script 标签
- Step3: 后台接受到请求，解析前端传过去的 callback 方法，返回该方法的调用，并且数据作为参数传入该方法
- Step4: 前端执行服务端返回的方法调用

> jsonp源码实现

```
function jsonp({url, params, callback}) {
    return new Promise((resolve, reject) => {
        // 创建 script 标签
        let script = document.createElement('script');
        // 将回调函数挂在 window 上
        window[callback] = function(data) {
            resolve(data);
            // 代码执行后，删除插入的 script 标签
            document.body.removeChild(script);
        }
        // 回调函数加在请求地址上
        params = {...params, callback} //wb=b&callback=show
        let arrs = [];
        for(let key in params) {
            arrs.push(`${key}=${params[key]}`);
        }
        script.src = `${url}?${arrs.join('&')}`;
        document.body.appendChild(script);
    });
}
```

> 使用:

```
function show(data) {
    console.log(data);
}
jsonp({
    url: 'http://localhost:3000/show',
    params: {
        //code
    },
    callback: 'show'
}).then(data => {
    console.log(data);
});
```

> 服务端代码 (node):

```
//express 启动一个后台服务
let express = require('express');
let app = express();

app.get('/show', (req, res) => {
    let {callback} = req.query; // 获取传来的 callback 函数名，callback 是 key
    res.send(`${callback}('Hello!')`);
});
app.listen(3000);
```