## 解析 URL Params 为对象

```js
let url = 'http://www.domain.com/?user=anonymous&id=123&id=456&city=%E5%8C%97%E4%BA%AC&enabled'; parseParam(url) 
/* 
结果 { user: 'anonymous', 
			id: [ 123, 456 ], // 重复出现的 key 要组装成数组，能被转成数字的就转成数字类型 
			city: '北京', // 中⽂需解码 
			enabled: true, // 未指定值得 key 约定为 true 
		}
*/
```

```js
function parseParam(url) { 
  const paramsStr = /.+\?(.+)$/.exec(url)[1]; // 将 ? 后⾯的字符串取出来 
  const paramsArr = paramsStr.split('&'); // 将字符串以 & 分割后存到数组中 
  let paramsObj = {}; 
  // 将 params 存到对象中 
  paramsArr.forEach(param => { 
    if (/=/.test(param)) { // 处理有 value 的参数 
      let [key, val] = param.split('='); // 分割 key 和 value 
      val = decodeURIComponent(val); // 解码 
      val = /^\d+$/.test(val) ? parseFloat(val) : val; // 判断是否转为数字 
      if (paramsObj.hasOwnProperty(key)) { // 如果对象有 key，则添加⼀个值 
        paramsObj[key] = [].concat(paramsObj[key], val); 
      } else { // 如果对象没有这个 key，创建 key 并设置值 
        paramsObj[key] = val; 
      } 
    } else { // 处理没有 value 的参数 
      paramsObj[param] = true; 
    } 
  })
  return paramsObj; 
}
```

## 模板引擎实现

```js
let template = '我是{{name}}，年龄{{age}}，性别{{sex}}'; 
let data = { 
  name: '姓名', 
  age: 18 
}
render(template, data); // 我是姓名，年龄18，性别undefined
```

```js
function render(template, data) {
  return template.replace(new RegExp('{{(.*?)}}', 'g'), (match, key) => data[key.trim()]);
}
```

## 转化为驼峰命名

```js
var f = function(s) { 
  return s.replace(/-\w/g, function(x) {
    return x.slice(1).toUpperCase(); 
  }) 
}
```

## 查找字符串中出现最多的字符和个数

```js
let str = "abcabcabcbbccccc"; 
let num = 0; let char = ''; // 使其按照⼀定的次序排列 
str = str.split('').sort().join(''); // "aaabbbbbcccccccc" 

// 定义正则表达式 
let re = /(\w)\1+/g; 
str.replace(re,($0,$1) => { 
  if(num < $0.length){ 
    num = $0.length; 
    char = $1; 
  } 
}); 
console.log(`字符最多的是${char}，出现了${num}次`);
```

## 实现千位分隔符

```js
// 保留三位⼩数 
parseToMoney(1234.56); // return '1,234.56' 
parseToMoney(123456789); // return '123,456,789' 
parseToMoney(1087654.321); // return '1,087,654.321'
```

```js
function parseToMoney(num) { 
  num = parseFloat(num.toFixed(3)); 
  let [integer, decimal] = String.prototype.split.call(num, '.'); 
  integer = integer.replace(/\d(?=(\d{3})+$)/g, '$&,'); 
  return integer + '.' + (decimal ? decimal : ''); 
}
```

正则表达式(运⽤了正则的前向声明和反前向声明):

```js
function parseToMoney(str){
  // 仅仅对位置进⾏匹配 
  let re = /(?=(?!\b)(\d{3})+$)/g; 
  return str.replace(re,','); 
}
```

## 判断是否是电话号码

```js
function isPhone(tel) { 
  var regx = /^1[34578]\d{9}$/; 
  return regx.test(tel); 
}

//验证是否是邮箱
var regx = /^([a-zA-Z0-9_\-])+@([a-zA-Z0-9_\-])+(\.[a-zA-Z0-9_\-])+$/;
//验证是否是身份证
var regx = /(^\d{15}$)|(^\d{18}$)|(^\d{17}(\d|X|x)$)/;
```


