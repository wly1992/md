# ES6小结

## Symbol
ES6 引入了一种新的原始数据类型 Symbol ，表示独一无二的值，最大的用法是用来定义对象的唯一属性名。

ES6 数据类型除了 Number 、 String 、 Boolean 、 Object、 null 和 undefined ，还新增了 Symbol 。

```
let sy = Symbol("KK");
console.log(sy);   // Symbol(KK)
typeof(sy);        // "symbol"
 
// 相同参数 Symbol() 返回的值不相等
let sy1 = Symbol("kk"); 
sy === sy1;       // false
```

> Symbol 值作为属性名时，该属性是公有属性不是私有属性，可以在类的外部访问。但是不会出现在 for...in 、 for...of 的循环中，也不会被 Object.keys() 、 Object.getOwnPropertyNames() 返回。如果要读取到一个对象的 Symbol 属性，可以通过 Object.getOwnPropertySymbols() 和 Reflect.ownKeys() 取到。

## Map 与 Set

### Map

Map 对象保存键值对。任何值(对象或者原始值) 都可以作为一个键或一个值。

#### Maps 和 Objects 的区别

- 一个 Object 的键只能是字符串或者 Symbols，但一个 Map 的键可以是任意值。
- Map 中的键值是有序的（FIFO 原则），而添加到对象中的键则不是。
- Map 的键值对个数可以从 size 属性获取，而 Object 的键值对个数只能手动计算。
- Object 都有自己的原型，原型链上的键名有可能和你自己在对象上的设置的键名产生冲突。

#### Map 的迭代

`for...of` 和 `forEach`

#### Map 对象的操作

1. Map 与 Array的转换

```
var kvArray = [["key1", "value1"], ["key2", "value2"]];
 
// Map 构造函数可以将一个 二维 键值对数组转换成一个 Map 对象
var myMap = new Map(kvArray);
 
// 使用 Array.from 函数可以将一个 Map 对象转换成一个二维键值对数组
var outArray = Array.from(myMap);
```

2. Map 的克隆

```
ar myMap1 = new Map([["key1", "value1"], ["key2", "value2"]]);
var myMap2 = new Map(myMap1);
 
console.log(original === clone); 
// 打印 false。 Map 对象构造函数生成实例，迭代出新的对象。
```

3. Map 的合并

```
var first = new Map([[1, 'one'], [2, 'two'], [3, 'three'],]);
var second = new Map([[1, 'uno'], [2, 'dos']]);
 
// 合并两个 Map 对象时，如果有重复的键值，则后面的会覆盖前面的，对应值即 uno，dos， three
var merged = new Map([...first, ...second]);
```
### Set

Set 对象允许你存储任何类型的唯一值，无论是原始值或者是对象引用。

```
let mySet = new Set();
 
mySet.add(1); // Set(1) {1}
mySet.add(5); // Set(2) {1, 5}
mySet.add(5); // Set(2) {1, 5} 这里体现了值的唯一性
mySet.add("some text"); 
// Set(3) {1, 5, "some text"} 这里体现了类型的多样性
var o = {a: 1, b: 2}; 
mySet.add(o);
mySet.add({a: 1, b: 2}); 
// Set(5) {1, 5, "some text", {…}, {…}} 
// 这里体现了对象之间引用不同不恒等，即使值相同，Set 也能存储
```

#### Set 对象作用

1. 数组去重

```
var mySet = new Set([1, 2, 3, 4, 4]);
[...mySet]; // [1, 2, 3, 4]
```

2. 并集

```
var a = new Set([1, 2, 3]);
var b = new Set([4, 3, 2]);
var union = new Set([...a, ...b]); // {1, 2, 3, 4}
```

3. 交集

```
var a = new Set([1, 2, 3]);
var b = new Set([4, 3, 2]);
var intersect = new Set([...a].filter(x => b.has(x))); // {2, 3}
```

4. 差集

```
var a = new Set([1, 2, 3]);
var b = new Set([4, 3, 2]);
var intersect = new Set([...a].filter(x => !b.has(x))); // {2, 3}
```

## Reflect 与 Proxy

## ES6 字符串方法

1. 子串的识别

- includes()：返回布尔值，判断是否找到参数字符串。
- startsWith()：返回布尔值，判断参数字符串是否在原字符串的头部。
- endsWith()：返回布尔值，判断参数字符串是否在原字符串的尾部。

2. 字符串重复

repeat()：返回新的字符串，表示将字符串重复指定次数返回。

3. 字符串补全

- padStart：返回新的字符串，表示用参数字符串从头部（左侧）补全原字符串。
- padEnd：返回新的字符串，表示用参数字符串从尾部（右侧）补全原字符串。

以上两个方法接受两个参数，第一个参数是指定生成的字符串的最小长度，第二个参数是用来补全的字符串。如果没有指定第二个参数，默认用空格填充。

```
console.log("h".padStart(5,"o"));  // "ooooh"
console.log("h".padEnd(5,"o"));    // "hoooo"
console.log("h".padStart(5));      // "    h"
```

4. 模板字符串

模板字符串相当于加强版的字符串，用反引号 `,除了作为普通字符串，还可以用来定义多行字符串，还可以在字符串中加入变量和表达式。

```
let name = "Mike";
let age = 27;
let info = `My Name is ${name},I am ${age+1} years old next year.`
console.log(info);
// My Name is Mike,I am 28 years old next year.

function f(){
  return "have fun!";
}
let string2= `Game start,${f()}`;
console.log(string2);  // Game start,have fun!
```

> 模板字符串中的换行和空格都是会被保留的


## ES6 数值

常量

> Number.EPSILON

Number.EPSILON 属性表示 1 与大于 1 的最小浮点数之间的差。

它的值接近于 2.2204460492503130808472633361816E-16，或者 2-52。

测试数值是否在误差范围内:

```
0.1 + 0.2 === 0.3; // false
// 在误差范围内即视为相等
equal = (Math.abs(0.1 - 0.3 + 0.2) < Number.EPSILON); // true
```

### Number 对象新方法

> Number.isFinite()

#### 从全局移植到 Number 对象的方法

> Number.parseInt() Number.parseFloat()  Number.isNaN()

### Math 对象的扩展

1. `Math.cbrt`用于计算一个数的立方根。

2. `Math.hypot`用于计算所有参数的平方和的平方根。

3. `Math.clz32` 用于返回数字的32 位无符号整数形式的前导0的个数。

4. `Math.trunc`用于返回数字的整数部分。

5. `Math.fround`用于获取数字的32位单精度浮点数形式。

6. `Math.sign`判断数字的符号（正、负、0）。

7. `Math.expm1()`用于计算 e 的 x 次方减 1 的结果，即 Math.exp(x) - 1 。

8. `Math.log1p(x)`用于计算1 + x 的自然对数，即 Math.log(1 + x) 。

9. `Math.log10(x)`用于计算以 10 为底的 x 的对数。

10. `Math.log2()`用于计算 2 为底的 x 的对数。

#### 指数运算符

> **

```
// 右结合，从右至左计算
2 ** 2 ** 3; // 256
// **=
let exam = 2;
exam ** = 2; // 4
```

## ES6 对象

### 对象字面量

属性的简洁表示法

ES6允许对象的属性直接写变量，这时候属性名是变量名，属性值是变量值。

```
const age = 12;
const name = "Amy";
const person = {age, name};
person   //{age: 12, name: "Amy"}
//等同于
const person = {age: age, name: name}
```

```
const person = {
  sayHi(){
    console.log("Hi");
  }
}
person.sayHi();  //"Hi"
//等同于
const person = {
  sayHi:function(){
    console.log("Hi");
  }
}
person.sayHi();//"Hi"
```

### 对象的拓展运算符

```
let person = {name: "Amy", age: 15};
let someone = { ...person };
someone;  //{name: "Amy", age: 15}

let age = {age: 15};
let name = {name: "Amy"};
let person = {...age, ...name};
person;  //{age: 15, name: "Amy"}

let person = {name: "Amy", age: 15};
let someone = { ...person, name: "Mike", age: 17};
someone;  //{name: "Mike", age: 17}
```

### 对象的新方法

> 1. Object.assign(target, source_1, ···)

用于将源对象的所有可枚举属性复制到目标对象中。

> #### assign 的属性拷贝是浅拷贝:

> 2. Object.is(value1, value2)

用来比较两个值是否严格相等，与（===）基本类似。

```
Object.is("q","q");      // true
Object.is(1,1);          // true
Object.is([1],[1]);      // false
Object.is({q:1},{q:1});  // false
```

与（===）的区别

```
//一是+0不等于-0
Object.is(+0,-0);  //false
+0 === -0  //true
//二是NaN等于本身
Object.is(NaN,NaN); //true
NaN === NaN  //false
```

## ES6 数组

### 数组创建

> 1. Array.of()

```
console.log(Array.of(1, 2, 3, 4)); // [1, 2, 3, 4]
 
// 参数值可为不同类型
console.log(Array.of(1, '2', true)); // [1, '2', true]
 
// 参数为空时返回空数组
console.log(Array.of()); // []
```

> 2. Array.from()

```
// 参数为数组,返回与原数组一样的数组
console.log(Array.from([1, 2])); // [1, 2]
 
// 参数含空位
console.log(Array.from([1, , 3])); // [1, undefined, 3]
```

> Array.from(arrayLike[, mapFn[, thisArg]])

- arrayLike
> `console.log(Array.from([1, 2, 3])); // [1, 2, 3]`
- mapFn
> `console.log(Array.from([1, 2, 3], (n) => n * 2)); // [2, 4, 6]`
- thisArg
```
let map = {
    do: function(n) {
        return n * 2;
    }
}
let arrayLike = [1, 2, 3];
console.log(Array.from(arrayLike, function (n){
    return this.do(n);
}, map)); // [2, 4, 6]
```
### 类数组对象

一个类数组对象必须含有 length 属性，且元素属性名必须是数值或者可转换为数值的字符。

```
let arr = Array.from({
  0: '1',
  1: '2',
  2: 3,
  length: 3
});
console.log(); // ['1', '2', 3]
 
// 没有 length 属性,则返回空数组
let array = Array.from({
  0: '1',
  1: '2',
  2: 3,
});
console.log(array); // []
 
// 元素属性名不为数值且无法转换为数值，返回长度为 length 元素值为 undefined 的数组  
let array1 = Array.from({
  a: 1,
  b: 2,
  length: 2
});
console.log(array1); // [undefined, undefined]
```

#### 转换可迭代对象

- 转换map
```
let map = new Map();
map.set('key0', 'value0');
map.set('key1', 'value1');
console.log(Array.from(map)); // [['key0', 'value0'],['key1',
// 'value1']]
```
- 转换set
```
let arr = [1, 2, 3];
let set = new Set(arr);
console.log(Array.from(set)); // [1, 2, 3]
```
- 转换字符串
```
let str = 'abc';
console.log(Array.from(str)); // ["a", "b", "c"]
```

### 扩展的方法

> 1. find()

查找数组中符合条件的元素,若有多个符合条件的元素，则返回第一个元素。
```
let arr = Array.of(1, 2, 3, 4);
console.log(arr.find(item => item > 2)); // 3
 
// 数组空位处理为 undefined
console.log([, 1].find(n => true)); // undefined
```

> 2. findIndex()
查找数组中符合条件的元素索引，若有多个符合条件的元素，则返回第一个元素索引。
```
let arr = Array.of(1, 2, 1, 3);
// 参数1：回调函数
// 参数2(可选)：指定回调函数中的 this 值
console.log(arr.findIndex(item => item = 1)); // 0
 
// 数组空位处理为 undefined
console.log([, 1].findIndex(n => true)); //0
```

> 3. fill()
将一定范围索引的数组元素内容填充为单个指定的值。
```
let arr = Array.of(1, 2, 3, 4);
// 参数1：用来填充的值
// 参数2：被填充的起始索引
// 参数3(可选)：被填充的结束索引，默认为数组末尾
console.log(arr.fill(0,1,2)); // [1, 0, 3, 4]
```

#### 嵌套数组转一维数组

> flat()

```
console.log([1 ,[2, 3]].flat()); // [1, 2, 3]
 
// 指定转换的嵌套层数
console.log([1, [2, [3, [4, 5]]]].flat(2)); // [1, 2, 3, [4, 5]]
 
// 不管嵌套多少层
console.log([1, [2, [3, [4, 5]]]].flat(Infinity)); // [1, 2, 3, 4, 5]
 
// 自动跳过空位
console.log([1, [2, , 3]].flat());<p> // [1, 2, 3]
```

## ES6 迭代器

## ES6 Class 类

## ES6 模块

## ES6 Promise 对象

是异步编程的一种解决方案。

从语法上说，Promise 是一个对象，从它可以获取异步操作的消息。

### Promise 状态
#### 状态的特点
Promise 异步操作有三种状态：pending（进行中）、fulfilled（已成功）和 rejected（已失败）。除了异步操作的结果，任何其他操作都无法改变这个状态。

Promise 对象只有：从 pending 变为 fulfilled 和从 pending 变为 rejected 的状态改变。只要处于 fulfilled 和 rejected ，状态就不会再变了即 resolved（已定型）

```
const p1 = new Promise(function(resolve,reject){
    resolve('success1');
    resolve('success2');
}); 
const p2 = new Promise(function(resolve,reject){  
    resolve('success3'); 
    reject('reject');
});
p1.then(function(value){  
    console.log(value); // success1
});
p2.then(function(value){ 
    console.log(value); // success3
});
```

#### 状态的缺点
无法取消 Promise ，一旦新建它就会立即执行，无法中途取消。

如果不设置回调函数，Promise 内部抛出的错误，不会反应到外部。

当处于 pending 状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。

#### then 方法
then 方法接收两个函数作为参数，第一个参数是 Promise 执行成功时的回调，第二个参数是 Promise 执行失败时的回调，两个函数只会有一个被调用。

#### then 方法的特点

在 JavaScript 事件队列的当前运行完成之前，回调函数永远不会被调用。

```
const p = new Promise(function(resolve,reject){
  resolve('success');
});
 
p.then(function(value){
  console.log(value);
});
 
console.log('first');
// first
// success
```

## ES6 Generator 函数

## ES6 async 函数