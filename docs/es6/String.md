## ES6 字符串方法 <!-- {docsify-ignore} -->

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