# ES6 面试题

## async/await是什么？

async 函数，就是 Generator 函数的语法糖，它建⽴在Promises上，并且与所有现有的基于Promise的API兼容。

1. Async—声明⼀个异步函数(async function someName(){...})
2. ⾃动将常规函数转换成Promise，返回值也是⼀个Promise对象
3. 只有async
4. 异步函数内部可以使⽤await

1. Await—暂停异步的功能执⾏(var result = await someAsyncCall();)
2. 放置在Promise调⽤之前，await强制其他代码等待，直到Promise完成并返回结果
3. 只能与Promise⼀起使⽤，不适⽤与回调
4. 只能在async函数内部使⽤

## async/await相⽐于Promise的优势？

- 代码读起来更加同步，Promise虽然摆脱了回调地狱，但是then的链式调⽤也会带来额外的阅读负担 
- Promise传递中间值⾮常麻烦，⽽async/await⼏乎是同步的写法，⾮常优雅 
- 错误处理友好，async/await可以⽤成熟的try/catch，Promise的错误捕获⾮常冗余 
- 调试友好，Promise的调试很差，由于没有代码块，你不能在⼀个返回表达式的箭头函数中设置断点，如果你在⼀个`.then`代码块中使⽤调试器的步进(step-over)功能，调试器并不会进⼊后续的`.then`代码块，因为调试器只能跟踪 同步代码的『每⼀步』。

## 可迭代对象有哪些特点

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