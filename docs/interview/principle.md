
## 1.new 的实现原理

new 的实现原理:
1. 创建一个空对象，构造函数中的 this 指向这个空对象。
2. 这个新对象被执行 [[原型]] 连接。
3. 执行构造函数方法，属性和方法被添加到 this 引用的对象中。
4. 如果构造函数中没有返回其它对象，那么返回 this，即创建的这个的新对象，否则，返回构造函数中返回的对象。

```js
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

```js
function Super(age) {
  this.age = age;
}

let instance = new Super('26');
console.log(instance.age); //26
```
> 构造函数返回值是 function 或 object，new Super()是返回的是Super种返回的对象。

```js
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

```js
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


```js
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

```js
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

```js
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

```js
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

```js
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

```js
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

```js
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

```js
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

```js
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

```js
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
```js
let i = 1;
Object.defineProperty(window, 'a', {
    get: function() {
        return i++;
    }
});
```

```js
let i = 1;
let a = new Proxy({}, {
    i: 1,
    get: function () {
        return () => this.i++;
    }
});
```

> 3.数组的 `toString` 接口默认调用数组的 `join` 方法，重写 `join` 方法

```js
let a = [1, 2, 3];
// a.shift() == 1 && a.shift() == 2 && a.shift() == 3   true
a.join = a.shift;
```

> 4.利用with
```js
let i = 0;

with ({
    get a() {
        return ++i;
    }
}) {
    console.log(a == 1 && a == 2 && a == 3); //true
}
```

## 9.ES5有几种方式可以实现继承？分别有哪些优缺点？

ES5 有 6 种方式可以实现继承，分别为：

1. 原型链继承

原型链继承的基本思想是利用原型让一个引用类型继承另一个引用类型的属性和方法。

```js
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
## 13.防抖函数的作用是什么？请实现一个防抖函数

> 防抖函数的作用

抖函数的作用就是控制函数在一定时间内的执行次数。防抖意味着N秒内函数只会被执行一次，如果N秒内再次被触发，则重新计算延迟时间。

举例说明： 小思最近在减肥，但是她非常吃吃零食。为此，与其男朋友约定好，如果10天不吃零食，就可以购买一个包(不要问为什么是包，因为包治百病)。但是如果中间吃了一次零食，那么就要重新计算时间，直到小思坚持10天没有吃零食，才能购买一个包。所以，管不住嘴的小思，没有机会买包(悲伤的故事)... 这就是 防抖。

> 防抖函数实现

- 事件第一次触发时，timeout 是 null，调用 later()，若 immediate 为true，那么立即调用 func.apply(this, params)；如果 immediate 为 false，那么过 wait 之后，调用 func.apply(this, params)
- 事件第二次触发时，如果 timeout 已经重置为 null(即 setTimeout 的倒计时结束)，那么流程与第一次触发时一样，若 timeout 不为 null(即 setTimeout 的倒计时未结束)，那么清空定时器，重新开始计时。

```js
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

```js
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

```js
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

```js
flattenDeep([1, [2, [3, [4]], 5]]); //[1, 2, 3, 4, 5]
```

> 1. 利用 Array.prototype.flat

ES6 为数组实例新增了 flat 方法，用于将嵌套的数组“拉平”，变成一维的数组。该方法返回一个新数组，对原数组没有影响。

flat 默认只会 “拉平” 一层，如果想要 “拉平” 多层的嵌套数组，需要给 flat 传递一个整数，表示想要拉平的层数。

```js
function flattenDeep(arr, deepLength) {
    return arr.flat(deepLength);
    // return arr.flat(Infinity);
}

console.log(flattenDeep([1, [2, [3, [4]], 5]], 3));
```
> 2. 利用 reduce 和 concat
```js
function flattenDeep(arr){
    return arr.reduce((acc, val) => Array.isArray(val) ? acc.concat(flattenDeep(val)) : acc.concat(val), []);
}
console.log(flattenDeep([1, [2, [3, [4]], 5]]));
```

> 3. 使用 stack 无限反嵌套多层嵌套数组

```js
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
## 请实现一个 uniq 函数，实现数组去重

> 法1: 利用ES6新增数据类型 Set

```js
function uniq(arry) {
    return [...new Set(arry)];
    // return Array.form(new Set(arry)
}
```

> 法 2: 利用 indexOf

```js
function uniq(arry) {
    var result = [];
    for (var i = 0; i < arry.length; i++) {
        if (result.indexOf(arry[i]) === -1) {
            // 如 result 中没有 arry[i], 则添加到数组中
            result.push(arry[i])
        }
    }
    return result;
}
```

> 法 3: 利用 includes
```js
function uniq(arry) {
    var result = [];
    for (var i = 0; i < arry.length; i++) {
        if (!result.includes(arry[i])) {
            // 如 result 中没有 arry[i], 则添加到数组中
            result.push(arry[i])
        }
    }
    return result;
}
```

> 法 4：利用 reduce
```js
function uniq(arry) {
    return arry.reduce((prev, cur) => prev.includes(cur) ? prev : [...prev, cur], []);
}
```

> 法 5：利用 Map
```js
function uniq(arry) {
    let map = new Map();
    let result = new Array();
    for (let i = 0; i < arry.length; i++) {
        if (map.has(arry[i])) {
            map.set(arry[i], true);
        } else {
            map.set(arry[i], false);
            result.push(arry[i]);
        }
    }
    return result;
}
```

## 数组排序

> 1. sort排序

```js
var arr=[1,5,7,9,16,2,4];
arr.sort(function(a,b){
  return b-a;  //降序排列，return a-b; —>升序排列
})  
```

> 2. 希尔排序（性能最好的排序）

```js
function xier(arr){
    var interval = parseInt(arr.length / 2);  //分组间隔设置
    while(interval > 0){
        for(var i = 0 ; i < arr.length ; i ++){
            var n = i;
            while(arr[n] < arr[n - interval] && n > 0){
                var temp = arr[n];
                arr[n] = arr[n - interval];
                arr[n - interval] = temp;
                n = n - interval;
            }
        }
        interval = parseInt(interval / 2);
    }
    return arr;
}
xier([12,9,38,44,7,98,35,59,49,88,38]);
```

> 3. 插入排序

```js
var arr=[45,1,32,21,56,87,43,12,34,45];
    for(var i=0;i<arr.length;i++){
    var n=i;
    while(arr[n]>arr[n+1] && n>=0){
 	    var temp=arr[n];
	    arr[n]=arr[n+1];
 	    arr[n+1]=temp;
	    n--;
    }
}
```

> 4. 快速排序（一拆为二）

```js
function quickSort(arr){
    if(arr.length <= 1){
        return arr;
    }
    var left = [];
    var right = [];
    var midIndex = parseInt(arr.length / 2);
    var mid = arr[midIndex];
    for(var i = 0 ; i < arr.length ; i++){
        if(i == midIndex) continue;
        if( arr[i] < mid){
            left.push(arr[i])
        }else{
            right.push(arr[i]);
        }
    }
    return quickSort(left).concat([mid],quickSort(right));
}
 
[1,2].concat([3],[4,5]);   //[1, 2, 3, 4, 5]
```
> 冒泡排序
```js
//性能一般
var arr=[1,5,7,9,16,2,4];
//冒泡排序,每一趟找出最大的,总共比较次数为arr.length-1次,每次的比较次数为arr.length-1次，依次递减
var temp;
for(var i=0;i<arr.length-1;i++){
    for(var j=0;j<arr.length-1;j++){
        if(arr[j]>arr[j+1]){
            temp=arr[j];
            arr[j]=arr[j+1];
            arr[j+1]=temp;
        }
    }
}
```

## instanceof的实现代码:

```js
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