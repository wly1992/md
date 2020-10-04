# 面试题总结

## 1.new 的实现原理

new 的实现原理:

创建一个空对象，构造函数中的 this 指向这个空对象。

这个新对象被执行 [[原型]] 连接。

执行构造函数方法，属性和方法被添加到 this 引用的对象中。

如果构造函数中没有返回其它对象，那么返回 this，即创建的这个的新对象，否则，返回构造函数中返回的对象。

在想要设置为标题的文字前面加#来表示

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

### this 是什么？
JavaScript this 关键词指的是它所属的对象。

它拥有不同的值，具体取决于它的使用位置：

在方法中，this 指的是所有者对象。
- 单独的情况下，this 指的是全局对象。
- 在函数中，this 指的是全局对象。
- 在函数中，严格模式下，this 是 undefined。
- 在事件中，this 指的是接收事件的元素。
像 call() 和 apply() 这样的方法可以将 this 引用到任何对象。

### 全局环境中的 this

浏览器环境：无论是否在严格模式下，在全局执行环境中（在任何函数体外部）this 都指向全局对象 window;

node 环境：无论是否在严格模式下，在全局执行环境中（在任何函数体外部），this 都是空对象 {};

### 是否是 new 绑定

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

### 函数是否通过 call,apply 调用，或者使用了 bind 绑定，如果是，那么this绑定的就是指定的对象【归结为显式绑定】

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

### 隐式绑定，函数的调用是在某个对象上触发的，即调用位置上存在上下文对象。典型的隐式调用为: `xxx.fn()`，此时`this`指向这个对象

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

### 默认绑定，在不能应用其它绑定规则时使用的默认规则，通常是独立函数调用。

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

### 箭头函数的情况：

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

### 深拷贝

  > 深拷贝复制变量值，对于非基本类型的变量，则递归至基本类型变量后，再复制。 深拷贝后的对象与原来的对象是完全隔离的，互不影响，对一个对象的修改并不会影响另一个对象。

### 浅拷贝

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

### 深拷贝实现

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

##  柯里化函数实现

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

## 如何让 `(a == 1 && a == 2 && a == 3)` 的值为`true`？

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

## 什么是BFC？BFC的布局规则是什么？如何创建BFC？

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

## 异步加载JS脚本的方式有哪些？

> <script> 标签中增加 async(html5) 或者 defer(html4) 属性,脚本就会异步加载。

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

## ES5有几种方式可以实现继承？分别有哪些优缺点？

