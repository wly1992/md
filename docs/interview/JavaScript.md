# JavaScript基础

## 解释下变量提升？

JavaScript引擎的⼯作⽅式是，先解析代码，获取所有被声明的变量，然后再⼀⾏⼀⾏地运⾏。这造成的结果，就是所有的变量的声明语句，都会被提升到代码的头部，这就叫做变量提升（hoisting）。

```js
console.log(a) // undefined 
var a = 1 
function b() { 
  console.log(a) 
}

b() // 1
```

上⾯的代码实际执⾏顺序是这样的:

第⼀步： 引擎将 var a = 1 拆解为 var a = undefined 和 a = 1 ，并将 var a = undefined 放到最顶端， a = 1 还在原 来的位置

这样⼀来代码就是这样:

```js
var a = undefined 
console.log(a) // undefined 
a = 1 

function b() { 
	console.log(a) 
}

b() // 1
```

## let、const、var 的区别有哪些？

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

```js
const a = 20;
const b = {
  age: 18,
  star: 200
}
b.age = 22
```
一图胜万言，如下图所示，不变的是栈内存中 a 存储的 20，和 b 中存储的 0x0012ff21（瞎编的一个数字）。而 {age: 18, star: 200} 是可变的。

<img align=center src="https://user-gold-cdn.xitu.io/2019/6/26/16b8f715226b7944?imageslim">

## 闭包是什么？闭包有什么作⽤？

MDN的解释：闭包是函数和声明该函数的词法环境的组合。

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

闭包的作⽤:

闭包最⼤的作⽤就是隐藏变量，闭包的⼀⼤特性就是内部函数总是可以访问其所在的外部函数中声明的参数和变量，即使在其外部函数被返回（寿命终结）了之后

基于此特性，JavaScript可以实现私有变量、特权变量、储存变量等

我们就以私有变量举例，私有变量的实现⽅法很多，有靠约定的（变量名前加_）,有靠Proxy代理的，也有靠Symbol这 种新数据类型的。

但是真正⼴泛流⾏的其实是使⽤闭包。

```js
function Person(){ 
	var name = 'cxk'; 
	this.getName = function(){ 
		return name; 
	}
	this.setName = function(value){ 
		name = value; 
	} 
}

const cxk = new Person() 
console.log(cxk.getName()) //cxk 
cxk.setName('jntm') 
console.log(cxk.getName()) //jntm 
console.log(name) //name is not defined
```

函数体内的 var name = 'cxk' 只有 getName 和 setName 两个函数可以访问，外部⽆法访问，相对于将变量私有化。


## 说一说你对JS执行上下文栈和作用域链的理解？

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

```js
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

```js
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

```js
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


## ES6模块与CommonJS模块有什么区别？

- CommonJS是对模块的浅拷⻉，ES6 Module是对模块的引⽤,即ES6 Module只存只读，不能改变其值，具体点就 是指针指向不能变，类似const
- import的接⼝是read-only（只读状态），不能修改其变量值，即不能修改其变量的指针指向，但可以改变变量内部指针指向，不过，这种写法很难查错，建议凡是输入的变量，都当作完全只读，不要轻易改变它的属性。可以对commonJS引用重新赋值（改变指针指向），但是对ES6 Module赋值会编译报错。

ES6 Module和CommonJS模块的共同点：

- CommonJS和ES6 Module都可以对引⼊的对象进⾏赋值，即对对象内部属性的值进⾏改变。

## js有哪些类型？

JavaScript的类型分为两⼤类，⼀类是原始类型，⼀类是复杂(引⽤）类型。

原始类型: 

- boolean 
- null 
- undefined 
- number 
- string 
- symbol

还有⼀个没有正式发布但即将被加⼊标准的原始类型BigInt。

复杂类型:

- Object (Object,Array,Function)

> `Symbol`表示独一无二的值，最大的用法是用来定义对象的唯一属性名

> Symbol 值作为属性名时，该属性是公有属性不是私有属性，可以在类的外部访问。但是不会出现在 for...in 、 for...of 的循环中，也不会被 Object.keys() 、 Object.getOwnPropertyNames() 返回。如果要读取到一个对象的 Symbol 属性，可以通过 Object.getOwnPropertySymbols() 和 Reflect.ownKeys() 取到。

## 为什么会有BigInt的提案？

JavaScript中Number.MAX_SAFE_INTEGER表示最⼤安全数字,计算结果是9007199254740991，即在这个数范围内不 会出现精度丢失（⼩数除外）。

但是⼀旦超过这个范围，js就会出现计算不准确的情况，这在⼤数计算的时候不得不依靠⼀些第三⽅库进⾏解决，因此 官⽅提出了BigInt来解决此问题。

## null与undefined的区别是什么？

null表示为空，代表此处不应该有值的存在，⼀个对象可以是null，代表是个空对象，⽽null本身也是对象。

undefined表示『不存在』，JavaScript是⼀⻔动态类型语⾔，成员除了表示存在的空值外，还有可能根本就不存在（因 为存不存在只在运⾏期才知道），这就是undefined的意义所在。

## 请描述一下 cookies，sessionStorage 和 localStorage 的区别？

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

## 0.1+0.2为什么不等于0.3？

JS 的 `Number` 类型遵循的是 IEEE 754 标准，使⽤的是 64 位固定⻓度来表示。

IEEE 754 浮点数由三个域组成，分别为 sign bit (符号位)、exponent bias (指数偏移值) 和 fraction (分数值)。64 位中， sign bit 占 1 位，exponent bias 占 11 位，fraction 占 52 位。

通过公式表示浮点数的值 `value = sign x exponent x fraction`

当⼀个数为正数，sign bit 为 0，当为负数时，sign bit 为 1.

以 0.1 转换为 IEEE 754 标准表示为例解释⼀下如何求 exponent bias 和 fraction。转换过程主要经历 3 个过程：

1. 将 0.1 转换为⼆进制表示 
2. 将转换后的⼆进制通过科学计数法表示 
3. 将通过科学计数法表示的⼆进制转换为 IEEE 754 标准表示

## 谈谈你对原型链的理解？

这个问题关键在于两个点，⼀个是原型对象是什么，另⼀个是原型链是如何形成的

### 原型对象

绝⼤部分的函数(少数内建函数除外)都有⼀个 prototype 属性,这个属性是原型对象⽤来创建新对象实例,⽽所有被创建的 对象都会共享原型对象,因此这些对象便可以访问原型对象的属性。

例如 `hasOwnProperty()` ⽅法存在于Obejct原型对象中,它便可以被任何对象当做⾃⼰的⽅法使⽤

> ⽤法： `object.hasOwnProperty( propertyName )`
> `hasOwnProperty()` 函数的返回值为 Boolean 类型。如果对象 object 具有名称为 propertyName 的属性，则返 回 true ，否则返回 false 。

```js
var person = { 
	name: "Messi", 
	age: 29, 
	profession: "football player" 
}; 
console.log(person.hasOwnProperty("name")); //true 
console.log(person.hasOwnProperty("hasOwnProperty")); //false 
console.log(Object.prototype.hasOwnProperty("hasOwnProperty")); //true
```

由以上代码可知, hasOwnProperty() 并不存在于 person 对象中,但是 person 依然可以拥有此⽅法

所以 person 对象是如何找到 Object 对象中的⽅法的呢?靠的是原型链

原因是每个对象都有` __proto__` 属性，此属性指向该对象的构造函数的原型。

对象可以通过 `__proto__` 与上游的构造函数的原型对象连接起来，⽽上游的原型对象也有⼀个 `__proto__` ，这样就形成 了原型链。

## 如何判断是否是数组？

es6中加⼊了新的判断⽅法

```js
if(Array.isArray(value)}{ return true; }
```

在考虑兼容性的情况下可以⽤toString的⽅法

```js
if(!Array.isArray){ 
	Array.isArray = function(arg){ 
		return Object.prototype.toString.call(arg)==='[object Array]' 
	} 
}
```

## 谈⼀谈你对this的了解？

this的指向不是在编写时确定的,⽽是在执⾏时确定的，同时，this不同的指向在于遵循了⼀定的规则。 

⾸先，在默认情况下，this是指向全局对象的，⽐如在浏览器就是指向window。

```js
name = "Bale"; 
function sayName () { 
	console.log(this.name); 
};
sayName(); //"Bale"
```

其次，如果函数被调⽤的位置存在上下⽂对象时，那么函数是被隐式绑定的。

```js
function f() { 
	console.log( this.name ); 
}

var obj = { 
	name: "Messi", 
	f: f 
};

obj.f(); //被调⽤的位置恰好被对象obj拥有，因此结果是Messi
```

再次，显示改变this指向，常⻅的⽅法就是call、apply、bind

以bind为例:

```js
function f() { 
	console.log( this.name ); 
}
var obj = { 
	name: "Messi", 
};
var obj1 = { 
	name: "Bale" 
};

f.bind(obj)(); //Messi ,由于bind将obj绑定到f函数上后返回⼀个新函数,因此需要再在后⾯加上括号进⾏执⾏,这是bind与apply和call的 区别
```

最后，也是优先级最⾼的绑定 new 绑定。

⽤ new 调⽤⼀个构造函数，会创建⼀个新对象, 在创造这个新对象的过程中,新对象会⾃动绑定到Person对象的this上， 那么 this ⾃然就指向这个新对象。

```js
function Person(name) { 
	this.name = name; 
	console.log(name); 
}
var person1 = new Person('Messi'); //Messi
```

> 绑定优先级: new绑定 > 显式绑定 >隐式绑定 >默认绑定

## 那么箭头函数的this指向哪⾥？

箭头函数不同于传统JavaScript中的函数,箭头函数并没有属于⾃⼰的this,它的所谓的this是捕获其所在上下⽂的 this 值，作为⾃⼰的 this 值,并且由于没有属于⾃⼰的this,⽽箭头函数是不会被new调⽤的，这个所谓的this也不会被改变

我们可以⽤Babel理解⼀下箭头函数:

```js
// ES6 
const obj = { 
	getArrow() { 
		return () => { console.log(this === obj); };
	} 
}
```

转化后

```js
// ES5，由 Babel 转译 
var obj = { 
	getArrow: function getArrow() { 
		var _this = this; 
		return function () { 
			console.log(_this === obj); 
		}; 
	} 
};
```

## JavaScript的参数是按照什么⽅式传递的？

1. 基本类型传递⽅式

由于js中存在复杂类型和基本类型,对于基本类型⽽⾔,是按值传递的.

```js
var a = 1; 
function test(x) { 
	// var x = a
	x = 10; 
	console.log(x); 
}

test(a); // 10 
console.log(a); // 1
```

虽然在函数 test 中 

2. 复杂类型按引⽤传递?

我们将外部 a 作为⼀个对象传⼊ test 函数.

```js
var a = { 
	a: 1, 
	b: 2 
};
function test(x) { 
	// var x = a;
	x.a = 10; 
	console.log(x); 
}
test(a); // { a: 10, b: 2 } 
console.log(a); // { a: 10, b: 2 }
```

可以看到,在函数体内被修改的 a 对象也同时影响到了外部的 a 对象,可⻅复杂类型是按引⽤传递的.

可是如果再做⼀个实验:

```js
var a = { a: 1, b: 2 };
function test(x) { x = 10; console.log(x); }
test(a); // 10 
console.log(a); // { a: 1, b: 2 }
```
外部的 a 并没有被修改,如果是按引⽤传递的话,由于共享同⼀个堆内存, a 在外部也会表现为 10 才对. 此时的复杂类型 同时表现出了 按值传递 和 按引⽤传递 的特性.

按共享传递

复杂类型之所以会产⽣这种特性,原因就是在传递过程中,对象 a 先产⽣了⼀个 副本a ,这个 副本a 并不是深克隆得到的 副 本a , 副本a 地址同样指向对象 a 指向的堆内存.

因此在函数体中修改 x=10 只是修改了 副本a , a 对象没有变化. 但是如果修改了 x.a=10 是修改了两者指向的同⼀堆内 存,此时对象 a 也会受到影响.

有⼈讲这种特性叫做传递引⽤,也有⼀种说法叫做按共享传递.

## 聊⼀聊如何在JavaScript中实现不可变对象？

实现不可变数据有三种主流的⽅法

1. 深克隆，但是深克隆的性能⾮常差，不适合⼤规模使⽤ 
2. Immutable.js，Immutable.js是⾃成⼀体的⼀套数据结构，性能良好，但是需要学习额外的API 
3. immer，利⽤Proxy特性，⽆需学习额外的api，性能良好

## JavaScript的基本类型和复杂类型是储存在哪⾥的？

- 基本类型储存在栈中，但是⼀旦被闭包引⽤则成为常住内存，会储存在内存堆中。

- 复杂类型会储存在内存堆中。

## 讲讲JavaScript垃圾回收是怎么做的？

## 数组方法
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

> ES6 新增

1. find()
2. findIndex()
3. fill()
4. flat()

## 字符串方法

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

> ES6 新增

1. includes()
2. repeat()
3. padStart()
4. 字符串模板

## JavaScript 全局对象
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

## 思考:return与break与continue的区别?

- return: 函数中(返回值并且结束函数  return后的代码不会执行)
- break: 循环语句(结束循环)   switch语句中(结束switch语句防止穿透)
- continue: 循环语句(结束本次循环,进入下一次循环)

## 瀑布流实现

先通过计算出一排能够容纳几列元素，然后寻找各列之中所有元素高度之和的最小者，并将新的元素添加到该列上，然后继续寻找所有列的各元素之和的最小者，继续添加至该列上，如此循环下去，直至所有元素均能够按要求排列为止；

[详情](https://blog.csdn.net/weixin_44135121/article/details/98629830)

## 使用模块化加载时，模块加载的顺序是怎样的?

CMD 推崇依赖就近，AMD 推崇依赖前置

AMD 提前执行，不管是否调用模块，先解析所有模块 requirejs 速度快 有可能浪费资源

CMD 延迟执行，在真正需要使用(依赖)模块时才解析该模块 seajs 按需解析

## 图片懒加载的原理是什么?

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

## 异步加载JS脚本的方式有哪些？

> `<script>` 标签中增加 async(html5) 或者 defer(html4) 属性,脚本就会异步加载。

defer 和 async 的区别在于：

- defer 要等到整个页面在内存中正常渲染结束（DOM 结构完全生成，以及其他脚本执行完成），在window.onload 之前执行；
- async 一旦下载完，渲染引擎就会中断渲染，执行这个脚本以后，再继续渲染。
- 如果有多个 defer 脚本，会按照它们在页面出现的顺序加载
- 多个 async 脚本不能保证加载顺序

> 动态创建 script 标签

动态创建的 `script` ，设置 src 并不会开始下载，而是要添加到文档中，JS文件才会开始下载。

```js
let script = document.createElement('script');
script.src = 'XXX.js';
// 添加到 html 文件中才会开始下载
document.body.append(script);
```

> XHR 异步加载JS

```js
let xhr = new XMLHttpRequest();
xhr.open("get", "js/xxx.js",true);
xhr.send();
xhr.onreadystatechange = function() {
    if (xhr.readyState == 4 && xhr.status == 200) {
        eval(xhr.responseText);
    }
}
```