# js基础知识总结

## 1.http状态码是什么?

HTTP状态码是网页HTTP请求的状态,200代表请求成功,404请求资源不存在,500服务器错误.

- 1xx：1开头的是信息状态码
- 2xx：2开头的是请求成功
- 3xx：3开头的是重定向
- 4xx：4开头的是客户端错误
- 5xx：5开头的是服务器错误

## 2.js的垃圾回收机制GC

Js具有自动垃圾回收机制。垃圾收集器会按照固定的时间间隔周期性的执行。

> S中最常见的垃圾回收方式是标记清除。

工作原理：是当变量进入环境时，将这个变量标记为“进入环境”。当变量离开环境时，则
将其标记为“离开环境”。标记“离开环境”的就回收内存。

工作流程：
1. GC在运行时给内存中所有变量都加上标记
2. 去掉环境中的变量以及被引用的变量的标记
3. 再被加上的标记会被视为准备删除的变量
4. GC完成内存清除工作，销毁那些带标记的值，回收其所占内存

> 引用计数方式

工作原理：跟踪记录每个值被引用的次数。

## 3.什么是事件委托?

事件委托(事件代理)就是利用事件冒泡原理，只指定一个事件处理程序,就可以管理某一类的所有事件.

```
$('ul').on('click','li',function(){
   console.log($(this).text())      //this指向li
})
```

##  4.什么是闭包？闭包的作用是什么？

闭包的定义

《JavaScript高级程序设计》:
> 闭包是指有权访问另一个函数作用域中的变量的函数
《JavaScript权威指南》：
> 从技术的角度讲，所有的JavaScript函数都是闭包：它们都是对象，它们都关联到作用域链。
《你不知道的JavaScript》
>当函数可以记住并访问所在的词法作用域时，就产生了闭包，即使函数是在当前词法作用域之外执行。

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

### 闭包的作用

1. 能够访问函数定义时所在的词法作用域(阻止其被回收)。

2. 私有化变量

```
function base() {
    let x = 10; // 私有变量
    return {
        getX: function() {
            return x;
        }
    }
}
let obj = base();
console.log(obj.getX()); //10
```

3. 模拟块级作用域

```
var a = [];
for (var i = 0; i < 10; i++) {
    a[i] = (function(j){
        return function () {
            console.log(j);
        }
    })(i);
}
a[6](); // 6
```

4. 创建模块

```
function coolModule() {
    let name = 'Yvette';
    let age = 20;
    function sayName() {
        console.log(name);
    }
    function sayAge() {
        console.log(age);
    }
    return {
        sayName,
        sayAge
    }
}
let info = coolModule();
info.sayName(); //'Yvette'
```

模块模式具有两个必备的条件(来自《你不知道的JavaScript》)

- 必须有外部的封闭函数，该函数必须至少被调用一次(每次调用都会创建一个新的模块实例)
- 封闭函数必须返回至少一个内部函数，这样内部函数才能在私有作用域中形成闭包，并且可以访问或者修改私有的状态。

## 5.请实现一个 uniq 函数，实现数组去重

> 法1: 利用ES6新增数据类型 Set

```
function uniq(arry) {
    return [...new Set(arry)];
    // return Array.form(new Set(arry)
}
```

> 法 2: 利用 indexOf

```
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
```
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
```
function uniq(arry) {
    return arry.reduce((prev, cur) => prev.includes(cur) ? prev : [...prev, cur], []);
}
```
> 法 5：利用 Map
```
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

## 6.数组排序

> 1. sort排序

```
var arr=[1,5,7,9,16,2,4];
arr.sort(function(a,b){
    return b-a;  //降序排列，return a-b; —>升序排列
})  
```

> 2. 希尔排序（性能最好的排序）

```
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

```

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

```
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
```
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