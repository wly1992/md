## ES6 对象 <!-- {docsify-ignore} -->

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