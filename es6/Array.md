## ES6 数组 <!-- {docsify-ignore} -->

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