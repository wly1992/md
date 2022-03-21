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

## 闭包是什么？闭包有什么作⽤？

MDN的解释：闭包是函数和声明该函数的词法环境的组合。

举个简单的例⼦:

```js
(function() { 
	var a = 1; 
	function add() { 
		var b = 2 
		var sum = b + a 
		console.log(sum); // 3 
	}
	add() 
})()
```

add 函数本身，以及其内部可访问的变量，即 a = 1 ，这两个组合在⼀起就被称为闭包，仅此⽽已。

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

## JavaScript的作⽤域链理解吗？

JavaScript属于静态作⽤域，即声明的作⽤域是根据程序正⽂在编译时就确定的，有时也称为词法作⽤域。

其本质是JavaScript在执⾏过程中会创造可执⾏上下⽂，可执⾏上下⽂中的词法环境中含有外部词法环境的引⽤，我们 可以通过这个引⽤获取外部词法环境的变量、声明等，这些引⽤串联起来⼀直指向全局的词法环境，因此形成了作⽤域 链。

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

## 为什么会有BigInt的提案？

JavaScript中Number.MAX_SAFE_INTEGER表示最⼤安全数字,计算结果是9007199254740991，即在这个数范围内不 会出现精度丢失（⼩数除外）。

但是⼀旦超过这个范围，js就会出现计算不准确的情况，这在⼤数计算的时候不得不依靠⼀些第三⽅库进⾏解决，因此 官⽅提出了BigInt来解决此问题。

## null与undefined的区别是什么？

null表示为空，代表此处不应该有值的存在，⼀个对象可以是null，代表是个空对象，⽽null本身也是对象。

undefined表示『不存在』，JavaScript是⼀⻔动态类型语⾔，成员除了表示存在的空值外，还有可能根本就不存在（因 为存不存在只在运⾏期才知道），这就是undefined的意义所在。

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